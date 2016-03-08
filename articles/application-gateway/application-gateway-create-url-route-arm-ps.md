<properties
   pageTitle="Erstellen eines Application Gateways mit URL-Routingregeln | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen und Konfigurieren eines Azure Application Gateways mit URL-Routingregeln."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# Erstellen eines Application Gateways mit Routing auf URL-Basis 

Mit Routing auf URL-Pfadbasis können Sie Routen basierend auf URL-Pfad oder Http-Anforderung zuordnen. Es wird überprüft, ob eine Route zu einem Backend-Pool für die URL-Listen im Application Gateway konfiguriert ist, und der Netzwerkverkehr wird an den definierten Back-End-Pool gesendet. Eine übliche Verwendung für Routing auf URL-Basis ist der Lastenausgleich von Anforderungen für verschiedene Inhaltstypen auf verschiedene Back-End-Serverpools.

Mit Routing auf URL-Basis wird ein neuer Regeltyp für das Application Gateway eingeführt. Das Application Gateway hat 2 Regeltypen: Basisrouting und PathBasedRouting. Der Basisregeltyp bietet einen Roundrobin-Dienst für die Back-End-Pools, während PathBasedRouting neben der Round-Robin-Verteilung auch Pfadmuster der Anforderungs-URL beim Auswählen des Back-End-Pools berücksichtigt.

>[AZURE.IMPORTANT] PathPattern: Die Liste der abzustimmenden Pfadmuster. Jedes muss mit „/“ beginnen, und ein „*“ ist nur auf ein „/“ folgend zulässig. Die Zeichenfolge, die dem Pfadabgleicher eingegeben wird, enthält keinen Text nach dem ersten „?“ oder „#“, und diese Zeichen sind nicht zulässig.

## Szenario
Im folgenden Beispiel verarbeitet das Application Gateway Datenverkehr für „contoso.com“ mit zwei Back-End-Pools: Video-Serverpool und Image-Serverpool.

Anforderungen für http://contoso.com/image* werden an den Image-Serverpool (pool1) und Anforderungen für http://contoso.com/video* an den Video-Serverpool (pool2) weitergeleitet. Ein Standard-Serverpool (pool1) wird ausgewählt, wenn keines der Pfadmuster zutrifft.

![URL-Route](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## Voraussetzungen

1. Installieren Sie mit dem Webplattform-Installer die aktuelle Version der Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt **Windows PowerShell** der Seite [Downloads](https://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Sie erstellen ein virtuelles Netzwerk und Subnetz für das Application Gateway. Stellen Sie sicher, dass keine virtuellen Maschinen oder Cloudbereitstellungen das Subnetz verwenden. Das Application Gateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie dem Back-End-Pool zur Verwendung des Application Gateways hinzugefügt haben, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden, oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.



## Was ist zum Erstellen eines Application Gateways erforderlich?


- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein.
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, jeweils mit Beachtung der Groß-/Kleinschreibung) und dem Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird).
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden soll, wenn er einen bestimmten Listener erreicht.

## Erstellen eines neuen Anwendungsgateways

Der Unterschied zwischen dem klassischen Azure-Modell und Azure-Ressourcen-Manager besteht in der Reihenfolge, in der Sie ein Application Gateway und die Elemente erstellen, die konfiguriert werden müssen.

Beim Ressourcen-Manager werden alle Elemente, die ein Application Gateway bilden, einzeln konfiguriert und anschließend zusammengesetzt, um die Application Gateway-Ressource zu erstellen.


Hier sind die erforderlichen Schritte zum Erstellen eines Application Gateways angegeben:

1. Erstellen einer Ressourcengruppe für den Ressourcen-Manager
2. Erstellen eines virtuelles Netzwerks, Subnetzes und einer öffentlichen IP-Adresse für das Application Gateway
3. Erstellen eines Konfigurationsobjekts für das Anwendungsgateway
4. Erstellen einer Application Gateway-Ressource

## Erstellen einer Ressourcengruppe für den Ressourcen-Manager

Stellen Sie sicher, dass Sie die neueste Version von Azure PowerShell verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](../powershell-azure-resource-manager.md).

### Schritt 1
Melden Sie sich bei Azure Login-AzureRmAccount an.

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 2
Überprüfen Sie die Abonnements für das Konto.

		Get-AzureRmSubscription

### Schritt 3
Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Schritt 4
Erstellen Sie eine neue Ressourcengruppe (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
Alternativ können Sie auch Tags für eine Ressourcengruppe für das Application Gateway erstellen:
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im obigen Beispiel haben wir eine Ressourcengruppe namens „appgw-RG“ mit dem Standort „USA, Westen“ erstellt.

>[AZURE.NOTE] Falls Sie einen benutzerdefinierten Test für Ihr Application Gateway konfigurieren müssen, helfen Ihnen die entsprechenden Informationen unter [Erstellen eines Application Gateways mit benutzerdefinierten Tests mithilfe von PowerShell](application-gateway-create-probe-ps.md) weiter. Weitere Informationen finden Sie unter [Benutzerdefinierte Tests und Systemüberwachung](application-gateway-probe-overview.md).

## Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### Schritt 1
Weisen Sie den Adressbereich 10.0.0.0/24 der Subnetzvariablen zu, die zum Erstellen eines virtuelles Netzwerks verwendet wird.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Schritt 2
Erstellen Sie ein virtuelles Netzwerk mit dem Namen „appgwvnet“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“ mit dem Präfix „10.0.0.0/16“ und dem Subnetz „10.0.0.0/24“.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Schritt 3
Weisen Sie eine Subnetzvariable für die nächsten Schritte zu. Damit wird ein Application Gateway erstellt.

	$subnet=$vnet.Subnets[0]

## Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration
Erstellen Sie eine öffentliche IP-Ressource namens „publicIP01“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Eine IP-Adresse wird dem Application Gateway beim Starten des Diensts zugewiesen.

## Erstellen einer Application Gateway-Konfiguration

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Application Gateway erstellen. Die folgenden Schritten erstellen die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.

### Schritt 1
Erstellen Sie eine IP-Konfiguration für das Application Gateway mit dem Namen „gatewayIP01“. Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Schritt 2
Konfigurieren Sie die Back-End-IP-Adresspools „pool01“ und „pool02“ mit den IP-Adressen „134.170.185.46, 134.170.188.221, 134.170.185.50“ für „pool01“ und „134.170.186.46, 134.170.189.221,134.170.186.50“ für „pool02“.


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

Dieses Beispiel enthält zwei Back-End-Pools zum Weiterleiten von Netzwerkverkehr auf URL-Pfadbasis. Ein Pool empfängt Datenverkehr vom URL-Pfad „/video“, und andere Pools empfangen Verkehr vom Pfad „/image“. Sie müssen die obigen IP-Adressen mit Ihren eigenen IP-Adressendpunkten der Anwendung ersetzen.

### Schritt 3
Konfigurieren Sie die Application Gateway-Einstellungen „poolsetting01“ und „poolsetting02“ für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool. In diesem Beispiel konfigurieren Sie verschiedene Back-End-Pool-Einstellungen für die Back-End-Pools. Jeder Back-End-Pool kann eine eigene Back-End-Pool-Einstellung aufweisen.

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Schritt 4
Konfigurieren Sie die Front-End-IP-Adresse mit dem öffentlichen IP-Endpunkt.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Schritt 5 
Konfigurieren Sie den Front-End-Port für ein Application Gateway.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### Schritt 6
Konfigurieren Sie den Listener. Dieser Schritt konfiguriert den Listener für die öffentliche IP-Adresse und den Port zum Empfangen von eingehendem Netzwerkverkehr.
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### Schritt 7 
Konfigurieren Sie URL-Regelpfade für die Back-End-Pools. Dieser Schritt konfiguriert den relativen Pfad, der vom Application Gateway verwendet wird, um die Zuordnung zwischen URL-Pfad und dem Back-End-Pool zu definieren, der zum Verarbeiten des eingehenden Datenverkehrs zugewiesen werden soll.

Im folgenden Beispiel werden zwei Regeln erstellt: eine für den „/image/“-Pfad, der Datenverkehr an Back-End „pool1“ leitet, und eine andere für den „/video/“-Pfad, der Datenverkehr an Back-End „pool2“ leitet.
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting01

Die Konfiguration der Regelpfadzuordnung konfiguriert auch dann einen Standard-Back-End-Adresspool, wenn der Pfad keiner der vordefinierten Pfadregeln entspricht.

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### Schritt 8 
Erstellen Sie eine Regeleinstellung. Dieser Schritt konfiguriert das Application Gateway zur Verwendung des Routings auf URL-Pfadbasis.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### Schritt 9 
Konfigurieren Sie die Anzahl der Instanzen und die Größe für das Application Gateway.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Erstellen eines Application Gateways
Erstellen Sie ein Application Gateway mit allen Konfigurationsobjekten aus den vorangegangenen Schritten.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Abrufen des Application Gateways
	$getgw =  Get-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname

<!---HONumber=AcomDC_0224_2016-->