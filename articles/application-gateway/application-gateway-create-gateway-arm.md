<properties 
   pageTitle="Erstellen, Starten oder Löschen eines Application Gateways mit Azure-Ressourcen-Manager | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure Application Gateways mit Azure-Ressourcen-Manager."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# Erstellen, Starten oder Löschen eines Application Gateways mit Azure-Ressourcen-Manager

Application Gateway verwendet einen Lastenausgleich auf der Schicht 7 (Anwendungsschicht). Application Gateway bietet ein Failover sowie ein schnelles Routing von HTTP-Anforderungen zwischen verschiedenen Servern in der Cloud und der lokalen Umgebung. Application Gateways weisen folgende Anwendungsbereitstellungsfunktionen auf: HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität und SSL-Auslagerung.


> [AZURE.SELECTOR]
- [Azure Classic PowerShell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template ](application-gateway-create-gateway-arm-template.md)


<BR>


Dieser Artikel führt Sie durch die Schritte zum Erstellen und Konfigurieren, Starten und Löschen eines Anwendungsgateways.


>[AZURE.IMPORTANT]Bevor Sie mit Azure-Ressourcen arbeiten, sollten Sie wissen, dass Azure derzeit über zwei Bereitstellungsmodelle verfügt: die Bereitstellung mit dem Ressourcen-Manager und die klassische Bereitstellung. Stellen Sie sicher, dass Sie die [Bereitstellungsmodelle und -tools](azure-classic-rm.md) verstanden haben, bevor Sie mit Azure-Ressourcen arbeiten. Sie können die Dokumentation für verschiedene Tools anzeigen, indem Sie auf die Registerkarten oben in diesem Artikel klicken. Dieses Dokument behandelt das Erstellen eines Application Gateways mit dem Azure-Ressourcen-Manager. Um die klassische Version zu verwenden, wechseln Sie zu [Erstellen einer klassischen Application Gateway-Bereitstellung mithilfe von PowerShell](application-gateway-create-gateway.md).



## Voraussetzungen

1. Installieren Sie die neueste Version der Azure PowerShell-Cmdlets mit dem Webplattform-Installer. Sie können die neueste Version im Abschnitt **Windows PowerShell** der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Sie erstellen ein virtuelles Netzwerk und Subnetz für das Application Gateway. Stellen Sie sicher, dass keine virtuellen Computer oder Cloudbereitstellungen das Subnetz verwenden. Das Application Gateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Application Gateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## Was ist zum Erstellen eines Application Gateways erforderlich?
 

- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein. 
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, bei beiden muss die Groß-/Kleinschreibung beachtet werden) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird). 
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden sollen, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.


 
## Erstellen eines neuen Application Gateways

Der Unterschied zwischen Azure Classic und Azure-Ressourcen-Manager besteht in der Reihenfolge, in der Sie das Application Gateway und die Elemente erstellen, die konfiguriert werden müssen.

Beim Ressourcen-Manager werden alle Elemente, die ein Application Gateway bilden, einzeln konfiguriert und anschließend zusammengesetzt, um die Application Gateway-Ressource zu erstellen.


Es folgen die erforderlichen Schritte zum Erstellen eines Application Gateways:

1. Erstellen einer Ressourcengruppe für den Ressourcen-Manager
2. Erstellen eines virtuelles Netzwerks, Subnetzes und einer öffentlichen IP-Adresse für das Application Gateway
3. Erstellen eines Konfigurationsobjekts für das Application Gateway
4. Erstellen einer Application Gateway-Ressource


## Erstellen einer Ressourcengruppe für den Ressourcen-Manager

Stellen Sie sicher, dass Sie die neueste Version von Azure PowerShell verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).

### Schritt 1

		Login-AzureRmAccount



### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

		Get-AzureRmSubscription 

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 3 

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im oben stehenden Beispiel haben wir eine Ressourcengruppe namens "appgw-RG" mit dem Standort "USA, Westen" erstellt.


>[AZURE.NOTE]Wenn Sie einen benutzerdefinierten Test für ein Application Gateway konfigurieren müssen, lesen Sie den Artikel [Erstellen eines Application Gateways mit benutzerdefinierten Tests mithilfe von PowerShell](application-gateway-create-probe-ps.md). Weitere Informationen finden Sie unter [Benutzerdefinierte Tests und Systemüberwachung](application-gateway-probe-overview.md).



## Erstellen eines virtuelles Netzwerks und Subnetzes für das Application Gateway

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### Schritt 1	
	
Dieser Befehl weist den Adressbereich 10.0.0.0/24 der Subnetzvariablen zu, die zum Erstellen eines virtuelles Netzwerks verwendet wird.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Schritt 2	

Ein virtuelles Netzwerk mit dem Namen „appgwvnet“ wird in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“ mit dem Präfix 10.0.0.0/16 und dem Subnetz 10.0.0.0/24 erstellt.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Schritt 3
	
Weisen Sie eine Subnetzvariable zu, damit die nächsten Schritte beim Erstellen eines Application Gateways ausgeführt werden können.

	$subnet=$vnet.Subnets[0]

## Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

Dieser Befehl dient zum Erstellen der öffentlichen IP-Ressource „publicIP01“ in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Erstellen eines Konfigurationsobjekts für das Application Gateway

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Application Gateway erstellen. Mit den folgenden Schritten erstellen Sie die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.

### Schritt 1

Dieser Befehl erstellt eine IP-Konfiguration für das Anwendungsgateway mit dem Namen „gatewayIP01“. Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine IP-Adresse verwendet.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Schritt 2

Dieser Schritt dient zum Konfigurieren des Back-End-IP-Adresspools "pool01" mit den IP-Adressen 134.170.185.46, 134.170.188.221 und 134.170.185.50. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Adressendpunkt empfangen werden. Ersetzen Sie die obigen IP-Adressen durch Ihre eigenen IP-Adressendpunkte der Anwendung.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Schritt 3

Dient zum Konfigurieren der Application Gateway-Einstellungen "poolsetting01" für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Schritt 4

Dieser Befehl konfiguriert den Front-End-IP-Port mit dem Namen "frontendport01" für den öffentlichen IP-Adressendpunkt.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80


### Schritt 5

Hiermit wird die Front-End-IP-Adresskonfiguration namens „fipconfig01“ erstellt und die öffentliche IP-Adresse zugewiesen.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Schritt 6

Dieser Befehl dient zum Erstellen des Listeners „listener01“ und zum Zuweisen des Front-End-Ports zur Front-End-IP-Konfiguration.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Schritt 7 

Erstellt die Load Balancer-Routingregel „rule01“ und konfiguriert das Load Balancer-Verhalten.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Schritt 8

Dieser Befehl konfiguriert die Instanzgröße des Application Gateways.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Application Gateways mit dem Cmdlet „New-AzureRmApplicationGateway“

Mit diesem Cmdlet wird ein Application Gateway mit allen Konfigurationselementen aus den vorangegangenen Schritten erstellt. Im Beispiel heißt das Application Gateway "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Löschen eines Application Gateways

Um ein Application Gateway zu löschen, müssen Sie die folgenden Schritte in der angegebenen Reihenfolge ausführen:

1. Verwenden Sie das Cmdlet `Stop-AzureRmApplicationGateway` zum Beenden des Gateways. 
2. Verwenden Sie das Cmdlet `Remove-AzureRmApplicationGateway` zum Entfernen des Gateways.
3. Überprüfen Sie mit dem Cmdlet `Get-AzureRmApplicationGateway`, ob das Gateway entfernt wurde.

### Schritt 1

Rufen Sie das Application Gateway-Objekt ab, und ordnen Sie es der Variablen "$getgw" zu:
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Schritt 2
	 
Verwenden Sie `Stop-AzureRmApplicationGateway`, um das Application Gateway zu beenden:

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Sobald das Application Gateway beendet wurde, verwenden Sie das Cmdlet `Remove-AzureRmApplicationGateway`, um den Dienst zu entfernen.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]Mit dem optionalen Switch "-force" kann diese Bestätigungsmeldung unterdrückt werden.


Mithilfe des Cmdlets `Get-AzureRmApplicationGateway` können Sie sicherstellen, dass der Dienst entfernt wurde. Dieser Schritt ist nicht erforderlich.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	
## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, lesen Sie [Konfigurieren eines Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md).

Wenn Sie ein Anwendungsgateway für ILB konfigurieren möchten, lesen Sie [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md).

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0107_2016-->