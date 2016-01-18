<properties 
   pageTitle="Erstellen eines benutzerdefinierten Tests für ein Application Gateway mithilfe von PowerShell im Ressourcen-Manager | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie einen benutzerdefinierten Test für Application Gateways mithilfe von PowerShell im Ressourcen-Manager erstellen."
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Erstellen eines benutzerdefinierten Tests für ein Application Gateway mithilfe von PowerShell für den Azure-Ressourcen-Manager

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Schritt 1 

Authentifizieren mithilfe des Cmdlets Login-AzureRmAccount

	Login-AzureRmAccount

### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

		get-AzureRmSubscription 

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 3 

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im oben stehenden Beispiel haben wir eine Ressourcengruppe namens "appgw-RG" mit dem Standort "USA, Westen" erstellt.

## Erstellen eines virtuelles Netzwerks und Subnetzes für das Application Gateway

Anhand der folgenden Schritte erstellen Sie ein virtuelles Netzwerk und ein Subnetz für das Application Gateway.

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
 

## Erstellen eines Konfigurationsobjekts für das Application Gateway mit benutzerdefiniertem Test 

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Application Gateway erstellen. Mit den folgenden Schritten erstellen Sie die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.


### Schritt 1

Dieser Befehl erstellt eine IP-Konfiguration für das Anwendungsgateway mit dem Namen „gatewayIP01“. Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine IP-Adresse verwendet.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Schritt 2


Dieser Schritt dient zum Konfigurieren des Back-End-IP-Adresspools "pool01" mit den IP-Adressen 134.170.185.46, 134.170.188.221 und 134.170.185.50. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Adressendpunkt empfangen werden. Ersetzen Sie die obigen IP-Adressen durch Ihre eigenen IP-Adressendpunkte der Anwendung.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Schritt 3


In diesem Schritt wird der benutzerdefinierte Test konfiguriert.

Verwendete Parameter:

- **-Interval**: Konfiguriert die Intervalle für die Prüfungen des Tests (in Sekunden). 
- **-Timeout**: Definiert das Timeout des Tests für eine HTTP-Antwortprüfung.
- **-Hostname und -path**: Vollständiger URL-Pfad, der vom Application Gateway aufgerufen wird, um die Integrität der Instanz zu ermitteln. Beispiel: Bei der Website http://contoso.com/ können Sie den benutzerdefinierten Test für „http://contoso.com/path/custompath.htm“ konfigurieren, damit die HTTP-Antwort bei den Prüfungen des Tests erfolgreich ist. 
- **UnhealthyThreshold**: Die Anzahl der Fehler bei HTTP-Antworten, ab der die Back-End-Instanz als *fehlerhaft* gekennzeichnet wird.



	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Schritt 4

Dient zum Konfigurieren der Application Gateway-Einstellungen „poolsetting01“ für den Datenverkehr im Back-End-Pool. Dieser Schritt umfasst auch eine Timeoutkonfiguration, die für die Back-End-Pool-Antwort auf eine Application Gateway-Anforderung gilt. Wenn eine Back-End-Antwort ein Timeoutlimit erreicht, bricht das Application Gateway die Anforderung ab. Dies unterscheidet sich vom Timeout des Tests, das nur für die Back-End-Antwort auf Testprüfungen gilt.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Schritt 5

Dieser Befehl konfiguriert den Front-End-IP-Port mit dem Namen "frontendport01" für den öffentlichen IP-Adressendpunkt.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Schritt 6

Hiermit wird die Front-End-IP-Adresskonfiguration namens „fipconfig01“ erstellt und die öffentliche IP-Adresse zugewiesen.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Schritt 7

Dieser Befehl dient zum Erstellen des Listeners „listener01“ und zum Zuweisen des Front-End-Ports zur Front-End-IP-Konfiguration.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Schritt 8 

Erstellt die Load Balancer-Routingregel „rule01“ und konfiguriert das Load Balancer-Verhalten.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Schritt 9

Dieser Befehl konfiguriert die Instanzgröße des Application Gateways.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Application Gateways mit dem Cmdlet New-AzureRmApplicationGateway

Mit diesem Cmdlet wird ein Application Gateway mit allen Konfigurationselementen aus den vorangegangenen Schritten erstellt. Im Beispiel heißt das Application Gateway "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Hinzufügen eines Tests zu einem vorhandenen Application Gateway

Zum Hinzufügen eines benutzerdefinierten Tests zu einem vorhandenen Application Gateway sind vier Schritte erforderlich.

### Schritt 1 

Laden Sie die Application Gateway-Ressource mithilfe von `Get-AzureRmApplicationGateway` in eine PowerShell-Variable.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Schritt 2

Fügen Sie der vorhandenen Gateway-Konfiguration einen Test hinzu.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

   
Im genannten Beispiel ist der benutzerdefinierte Test so konfiguriert, dass der URL-Pfad „contoso.com/path/custompath.htm“ alle 30 Sekunden überprüft wird. Ein Timeoutschwellenwert mit 120 Sekunden und maximal acht erfolglosen Suchanfragen wird konfiguriert.

### Schritt 3

Fügen Sie den Test mithilfe von `-Set-AzureRmApplicationGatewayBackendHttpSettings` der Einstellungskonfiguration des Back-End-Pools und dem Timeout hinzu.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Schritt 4

Speichern Sie die Konfiguration im Application Gateway mithilfe von `Set-AzureRmApplicationGateway`.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Entfernen eines Tests aus einem vorhandenen Application Gateway

Hier sind die Schritte zum Entfernen eines benutzerdefinierten Tests aus einem vorhandenen Application Gateway aufgeführt.

### Schritt 1 

Laden Sie die Application Gateway-Ressource mithilfe von `Get-AzureRmApplicationGateway` in eine PowerShell-Variable.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Schritt 2 

Entfernen Sie die Testkonfiguration mithilfe von `Remove-AzureRmApplicationGatewayProbeConfig` aus dem Application Gateway.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Schritt 3 

Aktualisieren Sie mithilfe von `-Set-AzureRmApplicationGatewayBackendHttpSettings` die Back-End-Pool-Einstellung, um die Einstellung für den Test und das Timeout zu entfernen


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Schritt 4
	
Speichern Sie die Konfiguration im Application Gateway mithilfe von `Set-AzureRmApplicationGateway`.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0107_2016-->