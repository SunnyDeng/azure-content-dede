<properties
   pageTitle="Erstellen, Starten oder Löschen eines Application Gateways mit Azure-Ressourcen-Manager | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure Application Gateways mit Azure-Ressourcen-Manager."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>


# Erstellen, Starten oder Löschen eines Application Gateways mit Azure-Ressourcen-Manager

Azure Application Gateway verwendet einen Load Balancer auf der Schicht 7 (Anwendungsschicht). Das Application Gateway ermöglicht ein Failover sowie schnelles Routing von HTTP-Anforderungen zwischen verschiedenen Servern in der Cloud und der lokalen Umgebung. Application Gateways weisen folgende Anwendungsbereitstellungsfunktionen auf: HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität und Secure Sockets Layer-Auslagerung (SSL).


> [AZURE.SELECTOR]
- [Schritte für die klassische Azure PowerShell-Bereitstellung](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager-Vorlage](application-gateway-create-gateway-arm-template.md)


<BR>


In diesem Artikel werden Sie durch die Schritte zum Erstellen, Konfigurieren, Starten und Löschen eines Application Gateways geführt.


>[AZURE.IMPORTANT] Bevor Sie mit Azure-Ressourcen arbeiten, sollten Sie wissen, dass Azure derzeit über zwei Bereitstellungsmodelle verfügt: die Bereitstellung mit dem Ressourcen-Manager und die klassische Bereitstellung. Stellen Sie sicher, dass Sie die [Bereitstellungsmodelle und -tools](../azure-classic-rm.md) verstehen, bevor Sie mit Azure-Ressourcen arbeiten. Zum Anzeigen der Dokumentation für verschiedene Tools klicken Sie auf die Registerkarten oben in diesem Artikel. In diesem Dokument erfahren Sie alles über das Erstellen eines Application Gateways mit Azure-Ressourcen-Manager Um die klassische Version zu verwenden, wechseln Sie zu [Erstellen einer klassischen Application Gateway-Bereitstellung mithilfe von PowerShell](application-gateway-create-gateway.md).



## Voraussetzungen

1. Installieren Sie mit dem Webplattform-Installer die aktuelle Version der Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt **Windows PowerShell** der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Sie erstellen ein virtuelles Netzwerk und Subnetz für das Application Gateway. Stellen Sie sicher, dass keine virtuellen Maschinen oder Cloudbereitstellungen das Subnetz verwenden. Das Application Gateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Application Gateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## Was ist zum Erstellen eines Application Gateways erforderlich?


- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein.
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, jeweils mit Beachtung der Groß-/Kleinschreibung) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird).
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

Stellen Sie sicher, dass Sie die neueste Version von Azure PowerShell verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../powershell-azure-resource-manager.md).

### Schritt 1
Melden Sie sich bei Azure Login-AzureRmAccount an.

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>
### Schritt 2
Überprüfen Sie die Abonnements für das Konto.

		Get-AzureRmSubscription

### Schritt 3
Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Schritt 4
Erstellen Sie eine neue Ressourcengruppe (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im obigen Beispiel haben wir eine Ressourcengruppe namens „appgw-RG“ mit dem Standort „USA, Westen“ erstellt.

>[AZURE.NOTE] Falls Sie einen benutzerdefinierten Test für Ihr Application Gateway konfigurieren müssen, helfen Ihnen die entsprechenden Informationen unter [Erstellen eines Application Gateways mit benutzerdefinierten Tests mithilfe von PowerShell](application-gateway-create-probe-ps.md) weiter. Weitere Informationen finden Sie unter [Benutzerdefinierte Tests und Systemüberwachung](application-gateway-probe-overview.md).



## Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### Schritt 1

Weisen Sie den Adressbereich 10.0.0.0/24 der Subnetzvariablen zu, die zum Erstellen eines virtuelles Netzwerks verwendet wird.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Schritt 2

Erstellen Sie ein virtuelles Netzwerk mit dem Namen „appgwvnet“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“ mit dem Präfix „10.0.0.0/16“ und dem Subnetz „10.0.0.0/24“.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Schritt 3

Weisen Sie eine Subnetzvariable für die nächsten Schritte zu. Damit wird ein Application Gateway erstellt.

	$subnet=$vnet.Subnets[0]

## Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

Erstellen Sie eine öffentliche IP-Ressource namens „publicIP01“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Erstellen eines Konfigurationsobjekts für das Anwendungsgateway

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Application Gateway erstellen. Die folgenden Schritten erstellen die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.

### Schritt 1

Erstellen Sie eine IP-Konfiguration für das Application Gateway mit dem Namen „gatewayIP01“. Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Schritt 2

Konfigurieren Sie den Back-End-IP-Adresspool „pool01“ mit den IP-Adressen „134.170.185.46“, „134.170.188.221“ und „134.170.185.50“. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Endpunkt empfangen werden. Ersetzen Sie die obigen IP-Adressen durch Ihre eigenen IP-Adressendpunkte der Anwendung.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Schritt 3

Konfigurieren Sie die Application Gateway-Einstellung „poolsetting01“ für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Schritt 4

Konfigurieren Sie den Front-End-IP-Port mit dem Namen „frontendport01“ für den öffentlichen IP-Adressendpunkt.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Schritt 5

Erstellen Sie die Front-End-IP-Adresskonfiguration namens „fipconfig01“ und ordnen Sie die öffentliche IP-Adresse der Front-End-IP-Konfiguration zu.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Schritt 6

Erstellen Sie den Listener „listener01“ und ordnen Sie den Front-End-Port der Front-End-IP-Konfiguration zu.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Schritt 7

Erstellen Sie die Load Balancer-Routingregel „rule01“, welche das Load Balancer-Verhalten konfiguriert.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Schritt 8

Konfigurieren Sie die Instanzgröße des Application Gateways.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Application Gateways mit dem Cmdlet „New-AzureRmApplicationGateway“

Erstellen Sie ein Application Gateway mit allen Konfigurationselementen aus den vorangegangenen Schritten. In diesem Beispiel heißt das Application Gateway „appgwtest.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Löschen eines Application Gateways

So löschen Sie ein Application Gateway:

1. Verwenden Sie das **Stop-AzureRmApplicationGateway**-Cmdlet, um das Gateway zu beenden.
2. Verwenden Sie das **Remove-AzureRmApplicationGateway**-Cmdlet, um das Gateway zu entfernen.
3. Stellen Sie sicher, dass das Gateway entfernt wurde, indem Sie das **Get-AzureRmApplicationGateway**-Cmdlet verwenden.

### Schritt 1

Rufen Sie das Application Gateway-Objekt ab, und ordnen Sie es einer „$getgw“-Variablen zu.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Schritt 2

Verwenden Sie das **Stop-AzureRmApplicationGateway**-Cmdlet, um das Application Gateway zu beenden.

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Verwenden Sie das **Remove-AzureRmApplicationGateway**-Cmdlet, um den Dienst zu entfernen, sobald das Application Gateway beendet wurde.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] Mit dem optionalen Switch **-force** kann diese Bestätigungsmeldung unterdrückt werden.


Um sicherzustellen, dass der Dienst entfernt wurde, können Sie das **Get-AzureRmApplicationGateway**-Cmdlet verwenden. Dieser Schritt ist nicht erforderlich.


	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, ist es ratsam, den Abschnitt [Konfigurieren eines Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md) zu lesen.

Wenn Sie ein Application Gateway für die Verwendung mit einem internen Load Balancer konfigurieren möchten, ist es ratsam, den Abschnitt [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md) zu lesen.

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0302_2016-->