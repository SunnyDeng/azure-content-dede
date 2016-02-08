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

# Erstellen eines benutzerdefinierten Tests für Azure Application Gateway mithilfe von PowerShell für Azure-Ressourcen-Manager

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Schritt 1

Verwenden Sie das Cmdlet „Login-AzureRmAccount“, um sich zu authentifizieren.

	Login-AzureRmAccount

### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

		get-AzureRmSubscription

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Schritt 4

Erstellen Sie eine neue Ressourcengruppe (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im obigen Beispiel haben wir eine Ressourcengruppe mit dem Namen „appgw-RG“ und dem Standort „USA, Westen“ erstellt.

## Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway.

Mit den folgenden Schritten erstellen Sie ein virtuelles Netzwerk und ein Subnetz für das Application Gateway.

### Schritt 1


Weisen Sie dem Adressbereich 10.0.0.0/24 eine Subnetzvariable zu, die zum Erstellen eines virtuellen Netzwerks verwendet wird.

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


## Erstellen eines Konfigurationsobjekts für das Application Gateway mit benutzerdefiniertem Test

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Application Gateway erstellen. Die folgenden Schritten erstellen die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.


### Schritt 1

Erstellen Sie eine IP-Konfiguration für das Application Gateway mit dem Namen „gatewayIP01“. Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Schritt 2


Konfigurieren Sie den Back-End-IP-Adresspool „pool01“ mit den IP-Adressen „134.170.185.46“, „134.170.188.221“ und „134.170.185.50“. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Endpunkt empfangen werden. Ersetzen Sie die obigen IP-Adressen durch Ihre eigenen IP-Adressendpunkte der Anwendung.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Schritt 3


In diesem Schritt wird der benutzerdefinierte Test konfiguriert.

Verwendete Parameter:

- **-Interval**: Konfiguriert die Intervalle für die Prüfungen des Tests (in Sekunden).
- **-Timeout**: Definiert das Timeout des Tests für eine HTTP-Antwortprüfung.
- **-Hostname und -path**: Vollständiger URL-Pfad, der vom Application Gateway aufgerufen wird, um die Integrität der Instanz zu ermitteln. Beispiel: Bei der Website http://contoso.com/ können Sie den benutzerdefinierten Test für „http://contoso.com/path/custompath.htm“ konfigurieren, damit die HTTP-Antwort bei den Prüfungen des Tests erfolgreich ist.
- **UnhealthyThreshold**: Die Anzahl der Fehler bei HTTP-Antworten, ab der die Back-End-Instanz als *fehlerhaft* gekennzeichnet wird.

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Schritt 4

Konfigurieren Sie die Application Gateway-Einstellung „poolsetting01“ für den Datenverkehr im Back-End-Pool. Dieser Schritt umfasst auch eine Timeoutkonfiguration, die für die Back-End-Pool-Antwort auf eine Application Gateway-Anforderung gilt. Wenn eine Back-End-Antwort ein Timeoutlimit erreicht, bricht das Application Gateway die Anforderung ab. Dieses Timeoutlimit unterscheidet sich vom Timeout des Tests, das nur für die Back-End-Antwort auf Testprüfungen gilt.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Schritt 5

Konfigurieren Sie den Front-End-IP-Port mit dem Namen „frontendport01“ für den öffentlichen IP-Adressendpunkt.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Schritt 6

Erstellen Sie die Front-End-IP-Adresskonfiguration namens „fipconfig01“ und ordnen Sie die öffentliche IP-Adresse der Front-End-IP-Konfiguration zu.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Schritt 7

Erstellen Sie den Listener „listener01“ und ordnen Sie den Front-End-Port der Front-End-IP-Konfiguration zu.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Schritt 8

Erstellen Sie die Load Balancer-Routingregel „rule01“, welche das Load Balancer-Verhalten konfiguriert.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Schritt 9

Konfigurieren Sie die Instanzgröße des Application Gateways.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Application Gateways mit dem Cmdlet „New-AzureRmApplicationGateway“

Erstellen Sie ein Application Gateway mit allen Konfigurationselementen aus den vorangegangenen Schritten. In diesem Beispiel heißt das Application Gateway „appgwtest“.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Hinzufügen eines Tests zu einem vorhandenen Application Gateway

Zum Hinzufügen eines benutzerdefinierten Tests zu einem vorhandenen Application Gateway sind vier Schritte erforderlich.

### Schritt 1

Laden Sie die Application Gateway-Ressource mit **Get-AzureRmApplicationGateway** in eine PowerShell-Variable.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Schritt 2

Fügen Sie der vorhandenen Gateway-Konfiguration einen Test hinzu.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


Im genannten Beispiel ist der benutzerdefinierte Test so konfiguriert, dass der URL-Pfad „contoso.com/path/custompath.htm“ alle 30 Sekunden überprüft wird. Ein Timeoutschwellenwert mit 120 Sekunden und maximal acht erfolglosen Testanfragen wird konfiguriert.

### Schritt 3

Fügen Sie mithilfe von **-Set-AzureRmApplicationGatewayBackendHttpSettings** den Test den Einstellungen für die Back-End-Poolkonfiguration und dem Timeout hinzu.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Schritt 4

Speichern Sie die Konfiguration mit **Set-AzureRmApplicationGateway** ins Application Gateway.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Entfernen eines Tests aus einem vorhandenen Application Gateway

Hier sind die Schritte zum Entfernen eines benutzerdefinierten Tests aus einem vorhandenen Application Gateway aufgeführt.

### Schritt 1

Laden Sie die Application Gateway-Ressource mit **Get-AzureRmApplicationGateway** in eine PowerShell-Variable.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Schritt 2

Entfernen Sie die Testkonfiguration mit **Remove-AzureRmApplicationGatewayProbeConfig** aus dem Application Gateway.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Schritt 3

Verwenden Sie **-Set-AzureRmApplicationGatewayBackendHttpSettings**, um die Back-End-Pool-Einstellungen zu aktualisieren und damit den Test und die Timeouteinstellung zu entfernen.


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Schritt 4

Speichern Sie die Konfiguration mit **Set-AzureRmApplicationGateway** ins Application Gateway.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0128_2016-->