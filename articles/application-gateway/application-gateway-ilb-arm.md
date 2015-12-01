<properties 
   pageTitle="Erstellen und Konfigurieren eines Application Gateways mit internem Lastenausgleich (ILB) mit Azure-Ressourcen-Manager | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure Application Gateways mit internem Lastenausgleich (ILB) mit Azure-Ressourcen-Manager."
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


# Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB) mit dem Azure-Ressourcen-Manager

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)

Ein Application Gateway kann mit einer VIP mit Internetzugriff oder mit einem internen Endpunkt konfiguriert werden, der nicht über das Internet erreichbar ist. Dies wird auch als Endpunkt für internen Lastenausgleich (Internal Load Balancer, ILB) bezeichnet. Das Konfigurieren des Gateways mit einem ILB ist für interne Branchenanwendungen nützlich, die nicht für das Internet verfügbar gemacht werden. Es ist auch hilfreich für die Dienste/Ebenen in einer Anwendung mit mehreren Ebenen, die sich innerhalb einer Sicherheitsgrenze befindet und nicht für das Internet verfügbar gemacht wird, aber dennoch eine Round-Robin-Lastverteilung, Sitzungsbindungen oder SSL-Beendigung erfordert. Dieser Artikel führt Sie durch die Schritte zum Konfigurieren eines Application Gateways mit einem ILB.

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

Der Unterschied zwischen Azure Classic und Azure-Ressourcen-Manager besteht in der Reihenfolge, in der Sie das Application Gateway und die Elemente erstellen, die konfiguriert werden müssen. Beim Ressourcen-Manager werden alle Elemente, die ein Application Gateway bilden, einzeln konfiguriert und anschließend zusammengesetzt, um die Application Gateway-Ressource zu erstellen.


Es folgen die erforderlichen Schritte zum Erstellen eines Application Gateways:

1. Erstellen einer Ressourcengruppe für den Ressourcen-Manager
2. Erstellen eines virtuelles Netzwerks und Subnetzes für das Application Gateway
3. Erstellen eines Konfigurationsobjekts für das Application Gateway
4. Erstellen einer Application Gateway-Ressource


## Erstellen einer Ressourcengruppe für den Ressourcen-Manager

Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die ARM-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).

### Schritt 1

		PS C:\> Login-AzureRmAccount

### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

		PS C:\> get-AzureRmSubscription 

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 3 

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im oben stehenden Beispiel haben wir eine Ressourcengruppe namens "appgw-rg" mit dem Standort "USA, Westen" erstellt.

## Erstellen eines virtuelles Netzwerks und Subnetzes für das Application Gateway

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### Schritt 1	
	
	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Dieser Befehl weist den Adressbereich 10.0.0.0/24 der Subnetzvariablen zu, die zum Erstellen eines virtuelles Netzwerks verwendet wird.

### Schritt 2
	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Ein virtuelles Netzwerk mit dem Namen "appgwvnet" wird in der Ressourcengruppe "appw-rg" für die Region "USA, Westen" mit dem Präfix 10.0.0.0/16 und dem Subnetz 10.0.0.0/24 erstellt.
	
### Schritt 3

	$subnet=$vnet.subnets[0]

Weist der Variablen "$subnet" das Subnetzobjekt für die nächsten Schritte zu.
 

## Erstellen eines Konfigurationsobjekts für das Application Gateway

### Schritt 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Dieser Befehl erstellt eine IP-Konfiguration für das Application Gateway mit dem Namen "gatewayIP01". Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine IP-Adresse verwendet.
 
### Schritt 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.10,10.0.0.11,10.0.0.12

In diesem Schritt konfigurieren Sie den Back-End-IP-Adresspool „pool01“ mit den IP-Adressen 10.0.0.10, 10.0.0.11 und 10.0.0.12. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Endpunkt empfangen. Ersetzen Sie die obigen IP-Adressen durch Ihre eigenen IP-Adressendpunkte der Anwendung.

### Schritt 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Dient zum Konfigurieren der Application Gateway-Einstellungen "poolsetting01" für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool.

### Schritt 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Dieser Befehl konfiguriert den Front-End-IP-Port mit dem Namen "frontendport01" für den ILB.

### Schritt 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Hiermit wird die Front-End-IP-Adresskonfiguration „fipconfig01“ erstellt und eine private IP-Adresse im aktuellen Subnetz des virtuellen Netzwerks zugewiesen.

### Schritt 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Dieser Befehl dient zum Erstellen des Listeners „listener01“ und zum Zuweisen des Front-End-Ports zur Front-End-IP-Konfiguration.

### Schritt 7 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Erstellt die Load Balancer-Routingregel „rule01“ und konfiguriert das Load Balancer-Verhalten.

### Schritt 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Dieser Befehl konfiguriert die Instanzgröße des Application Gateways.

>[AZURE.NOTE]Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Application Gateways mit dem Cmdlet "New-AzureApplicationGateway"

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Mit diesem Cmdlet wird ein Application Gateway mit allen Konfigurationselementen aus den vorangegangenen Schritten erstellt. Im Beispiel heißt das Application Gateway "appgwtest".



## Starten des Gateways

Sobald das Gateway konfiguriert wurde, verwenden Sie das `Start-AzureRmApplicationGateway`-Cmdlet, um das Gateway zu starten. Die Abrechnung für ein Application Gateway beginnt, nachdem das Gateway erfolgreich gestartet wurde.


**Hinweis:** Bis zum Abschluss des `Start-AzureRmApplicationGateway`-Cmdlets können bis zu 15 – 20 Minuten vergehen.

Im folgenden Beispiel heißt das Anwendungsgateway „appgwtest“ und die Ressourcengruppe „app-rg“:


### Schritt 1

Rufen Sie das Application Gateway-Objekt ab, und ordnen Sie es der Variablen "$getgw" zu:
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Schritt 2
	 
Verwenden Sie `Start-AzureRmApplicationGateway`, um das Application Gateway zu starten:

	PS C:\> Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureRmApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Überprüfen des Application Gateway-Status

Verwenden Sie das `Get-AzureRmApplicationGateway`-Cmdlet zum Überprüfen des Status des Gateways. Wenn *Start-AzureApplicationGateway* im vorherigen Schritt erfolgreich ausgeführt wurde, sollte der Status *Wird ausgeführt* lauten.


## Löschen eines Application Gateways

Um ein Application Gateway zu löschen, müssen Sie die folgenden Schritte in der angegebenen Reihenfolge ausführen:

1. Verwenden Sie das Cmdlet `Stop-AzureRmApplicationGateway` zum Beenden des Gateways. 
2. Verwenden Sie das Cmdlet `Remove-AzureRmApplicationGateway` zum Entfernen des Gateways.
3. Überprüfen Sie mit dem Cmdlet `Get-AzureApplicationGateway`, ob das Gateway entfernt wurde.

Dieses Beispiel zeigt das Cmdlet `Stop-AzureRmApplicationGateway` in der ersten Zeile, gefolgt von der Ausgabe.

### Schritt 1

Rufen Sie das Application Gateway-Objekt ab, und ordnen Sie es der Variablen "$getgw" zu:
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Schritt 2
	 
Verwenden Sie `Stop-AzureRmApplicationGateway`, um das Application Gateway zu beenden:

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Sobald das Application Gateway beendet wurde, verwenden Sie das Cmdlet `Remove-AzureRmApplicationGateway`, um den Dienst zu entfernen.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]Mit dem optionalen Switch "-force" kann diese Bestätigungsmeldung unterdrückt werden.
>

Mithilfe des Cmdlets `Get-AzureRmApplicationGateway` können Sie sicherstellen, dass der Dienst entfernt wurde. Dieser Schritt ist nicht erforderlich.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, lesen Sie [Konfigurieren eines Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md).

Wenn Sie ein Anwendungsgateway für ILB konfigurieren möchten, lesen Sie [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md).

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1125_2015-->