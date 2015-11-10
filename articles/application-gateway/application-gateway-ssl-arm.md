<properties 
   pageTitle="Konfigurieren eines Application Gateways für SSL-Auslagerung mit Azure-Ressourcen-Manager | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen eines Application Gateways mit SSL-Auslagerung mit Azure-Ressourcen-Manager."
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
   ms.date="10/28/2015"
   ms.author="joaoma"/>

# Konfigurieren eines Application Gateways für SSL-Auslagerung mit Azure-Ressourcen-Manager


 Application Gateway kann so konfiguriert werden, dass damit die SSL-Sitzung auf dem Gateway beendet wird. Auf diese Weise wird die aufwändige SSL-Entschlüsselung auf der Webfarm vermieden. Die SSL-Auslagerung vereinfacht zudem die Einrichtung und Verwaltung der Webanwendung auf dem Front-End-Server.


>[AZURE.IMPORTANT]Bevor Sie mit Azure-Ressourcen arbeiten, sollten Sie wissen, dass Azure derzeit über zwei Bereitstellungsmodelle verfügt: die Bereitstellung mit dem Ressourcen-Manager und die klassische Bereitstellung. Stellen Sie sicher, dass Sie die [Bereitstellungsmodelle und -tools](azure-classic-rm.md) verstanden haben, bevor Sie mit Azure-Ressouren arbeiten. Sie können die Dokumentation für verschiedene Tools anzeigen, indem Sie auf die Registerkarten oben in diesem Artikel klicken. Dieses Dokument behandelt das Erstellen eines Application Gateways mit dem Azure-Ressourcen-Manager. Um die klassische Version zu verwenden, wechseln Sie zu [Konfigurieren eines Application Gateway für SSL-Auslagerung mithilfe von PowerShell (klassisch)](application-gateway-ssl.md).



## Voraussetzungen

1. Installieren Sie die neueste Version der Azure PowerShell-Cmdlets mit dem Webplattform-Installer. Sie können die neueste Version im Abschnitt **Windows PowerShell** der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Sie erstellen ein virtuelles Netzwerk und Subnetz für das Application Gateway. Stellen Sie sicher, dass keine virtuellen Computer oder Cloudbereitstellungen das Subnetz verwenden. Das Application Gateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Application Gateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## Was ist zum Erstellen eines Application Gateways erforderlich?
 

- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein. 
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, bei beiden muss die Groß-/Kleinschreibung beachtet werden) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird). 
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden soll, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.

**Zusätzliche Konfigurationshinweise:**

Für die Konfiguration von SSL-Zertifikaten sollte das Protokoll in **HttpListener** in *Https* (Groß-/Kleinschreibung beachten) geändert werden. Das Element **SslCertificate** muss **HttpListener** mit dem Variablenwert hinzugefügt werden, der für das SSL-Zertifikat konfiguriert wurde. Der Front-End-Port sollte auf 443 aktualisiert werden.

**So aktivieren Sie cookiebasierte Affinität**: Ein Application Gateway kann konfiguriert werden, um sicherzustellen, dass die Anforderung von einer Clientsitzung immer an denselben virtuellen Computer in der Webfarm weitergeleitet wird. Dies erfolgt durch Einfügen eines Sitzungscookies, sodass das Gateway den Datenverkehr entsprechend weiterleiten kann. Legen Sie zum Aktivieren der cookiebasierten Affinität **CookieBasedAffinity** im **BackendHttpSettings**-Element auf *Enabled* fest.

 
## Erstellen eines neuen Application Gateways

Der Unterschied zwischen Azure Classic und Azure-Ressourcen-Manager besteht in der Reihenfolge, in der Sie das Application Gateway und die Elemente erstellen, die konfiguriert werden müssen.

Beim Ressourcen-Manager werden alle Elemente, die ein Application Gateway bilden, einzeln konfiguriert und anschließend zusammengesetzt, um die Application Gateway-Ressource zu erstellen.


Es folgen die erforderlichen Schritte zum Erstellen eines Application Gateways:

1. Erstellen einer Ressourcengruppe für den Ressourcen-Manager
2. Erstellen eines virtuelles Netzwerks, Subnetzes und einer öffentlichen IP-Adresse für das Application Gateway
3. Erstellen eines Konfigurationsobjekts für das Application Gateway
4. Erstellen einer Application Gateway-Ressource


## Erstellen einer Ressourcengruppe für den Ressourcen-Manager

Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die ARM-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).

### Schritt 1

    Switch-AzureMode -Name AzureResourceManager

### Schritt 2

Melden Sie sich beim Azure-Konto an.


    Add-AzureAccount

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.


### Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

    Select-AzureSubscription -SubscriptionName "MySubscription"

Um eine Liste der verfügbaren Abonnements anzuzeigen, verwenden Sie das Cmdlet „Get-AzureSubscription“.


### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    New-AzureResourceGroup -Name appgw-rg -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Application Gateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im oben stehenden Beispiel haben wir eine Ressourcengruppe namens "appgw-RG" mit dem Standort "USA, Westen" erstellt.

## Erstellen eines virtuelles Netzwerks und Subnetzes für das Application Gateway

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### Schritt 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Dieser Befehl weist den Adressbereich 10.0.0.0/24 einer Subnetzvariablen zu, die zum Erstellen eines virtuelles Netzwerks verwendet wird.

### Schritt 2	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Dieser Befehl erstellt ein virtuelles Netzwerk mit dem Namen „appgwvnet“ in der Ressourcengruppe „appw-rg“ für die Region „USA, Westen“ mit dem Präfix 10.0.0.0/16 und dem Subnetz 10.0.0.0/24.

### Schritt 3

	$subnet=$vnet.Subnets[0]

Weist der Variablen "$subnet" das Subnetzobjekt für die nächsten Schritte zu.
	
## Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Dieser Befehl dient zum Erstellen der öffentlichen IP-Ressource „publicIP01“ in der Ressourcengruppe „appw-rg“ für die Region „USA, Westen“.


## Erstellen eines Konfigurationsobjekts für das Application Gateway

### Schritt 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Dieser Befehl erstellt eine IP-Konfiguration für das Application Gateway mit dem Namen „gatewayIP01“. Beim Start des Application Gateways wählt es eine IP-Adresse aus dem konfigurierten Subnetz aus und leitet dann Netzwerkdatenverkehr an die IP-Adressen im Back-End-IP-Pool weiter. Beachten Sie, dass jede Instanz eine IP-Adresse verwendet.
 
### Schritt 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

In diesem Schritt konfigurieren Sie den Back-End-IP-Adresspool „pool01“ mit den IP-Adressen „134.170.185.46“, „134.170.188.221“ und „134.170.185.50“. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Adressendpunkt empfangen werden. Ersetzen Sie die IP-Adressen aus dem oben genannten Beispiel durch die IP-Adressen der Endpunkte Ihrer Webanwendung.

### Schritt 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Dient zum Konfigurieren der Application Gateway-Einstellungen „poolsetting01“ für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool.

### Schritt 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Dieser Befehl konfiguriert den Front-End-IP-Port mit dem Namen „frontendport01“ für den öffentlichen IP-Adressendpunkt.

### Schritt 5 

	$cert = New-AzureApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Dieser Befehl konfiguriert das Zertifikat für SSL-Verbindungen. Das Zertifikat muss im PFX-Format vorliegen, und das Kennwort muss zwischen 4 und 12 Zeichen umfassen.

### Schritt 6

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Hiermit wird die Front-End-IP-Adresskonfiguration namens „fipconfig01“ erstellt und die öffentliche IP-Adresse zugewiesen.

### Schritt 7

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Dieser Befehl dient zum Erstellen des Listeners „listener01“ und zum Zuordnen des Front-End-Ports zur Front-End-IP-Konfiguration und zum Zertifikat.

### Schritt 8 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Dieser Befehl erstellt die Load Balancer-Routingregel „rule01“ und konfiguriert das Load Balancer-Verhalten.

### Schritt 9

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Dieser Befehl konfiguriert die Instanzgröße des Application Gateways.

>[AZURE.NOTE]Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist „Medium“. Sie können zwischen „Standard\_Small“, „Standard\_Medium“ und „Standard\_Large“ wählen.

## Erstellen eines Application Gateways mit dem Cmdlet "New-AzureApplicationGateway"

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Mit diesem Befehl wird ein Application Gateway mit allen Konfigurationselementen aus den vorangegangenen Schritten erstellt. Im Beispiel heißt das Application Gateway „appgwtest“.


## Starten des Application Gateways

Sobald das Gateway konfiguriert wurde, verwenden Sie das `Start-AzureApplicationGateway`-Cmdlet, um das Gateway zu starten. Die Abrechnung für ein Application Gateway beginnt, nachdem das Gateway erfolgreich gestartet wurde.


**Hinweis:** Bis zum Abschluss des `Start-AzureApplicationGateway`-Cmdlets können bis zu 15 – 20 Minuten vergehen.

Im folgenden Beispiel heißt das Application Gateway "appgwtest" und die Ressourcengruppe "app-rg":


### Schritt 1

Rufen Sie das Application Gateway-Objekt ab, und ordnen Sie es der Variablen "$getgw" zu:
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Schritt 2
	 
Verwenden Sie `Start-AzureApplicationGateway`, um das Application Gateway zu starten:

	 Start-AzureApplicationGateway -ApplicationGateway $getgw  

	

## Überprüfen des Application Gateway-Status

Überprüfen Sie mit dem Cmdlet `Get-AzureApplicationGateway` den Status des Gateways. Wenn *Start-AzureApplicationGateway* im vorherigen Schritt erfolgreich ausgeführt wurde, sollte der Status *Running* lauten, und „Vip“ und „DnsName“ sollten gültige Einträge aufweisen.

Dieses Beispiel zeigt ein Application Gateway, das ausgeführt wird und Datenverkehr verarbeiten kann, der für `http://<generated-dns-name>.cloudapp.net` vorgesehen ist.

	Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
	Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## Nächste Schritte


Wenn Sie ein Anwendungsgateway für ILB konfigurieren möchten, lesen Sie [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md).

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO2-->