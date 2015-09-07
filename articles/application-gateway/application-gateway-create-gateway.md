<properties
   pageTitle="Erstellen, Starten oder Löschen eines Application Gateways | Microsoft Azure"
	description="Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure Application Gateways"
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
	ms.date="07/29/2015"
	ms.author="joaoma"/>

# Erstellen, Starten oder Löschen eines Application Gateways

In dieser Version können Sie ein Application Gateway mit PowerShell oder REST-API-Aufrufen erstellen. Azure-Portal- und CLI-Unterstützung werden in einer zukünftigen Version bereitgestellt. Dieser Artikel führt Sie durch die Schritte zum Erstellen, Konfigurieren, Starten und Löschen eines Application Gateways.

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-create-gateway.md)
- [Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)



## Voraussetzungen

1. Installieren Sie mit dem Webplattform-Installer die aktuelle Version der Azure PowerShell-Cmdlets. Sie können die neueste Version im Abschnitt **Windows PowerShell** der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Stellen Sie sicher, dass Sie über ein funktionierendes virtuelles Netzwerk mit einem gültigen Subnetz verfügen. Stellen Sie sicher, dass keine virtuellen Computer oder Cloudbereitstellungen das Subnetz verwenden. Das Application Gateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Application Gateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## Was ist zum Erstellen eines Application Gateways erforderlich?


Wenn Sie den Befehl **New-AzureApplicationGateway** zum Erstellen des Application Gateways verwenden, wird zu diesem Zeitpunkt keine Konfiguration festgelegt, und die neu erstellte Ressource muss entweder mit XML oder einem Konfigurationsobjekt konfiguriert werden.


Die Werte sind:

- **Back-End-Serverpool**: Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein.
- **Einstellungen für den Back-End-Serverpool**: Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port**: Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener**: Der Listener verfügt über einen Front-End-Port, ein Protokoll (HTTP oder HTTPS, bei beiden muss die Groß-/Kleinschreibung beachtet werden) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird).
- **Regel**: Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden sollen, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.



## Erstellen eines neuen Application Gateways

Zum Erstellen eines Application Gateways müssen Sie bestimmte Schritte in folgender Reihenfolge ausführen:

1. Erstellen einer Application Gateway-Ressource
2. Erstellen der XML-Konfigurationsdatei oder des Konfigurationsobjekts
3. Übertragen der Konfiguration auf die neu erstellte Application Gateway-Ressource

### Erstellen einer Application Gateway-Ressource

Erstellen Sie das Gateway mithilfe des Cmdlets `New-AzureApplicationGateway`. Ersetzen Sie dabei die Werte durch eigene Werte. Beachten Sie, dass die Abrechnung für das Gateway jetzt noch nicht gestartet wird. Die Abrechnung beginnt in einem späteren Schritt, wenn das Gateway erfolgreich gestartet wurde.

Mit dem folgenden Beispiel wird ein neues Application Gateway über ein virtuelles Netzwerk mit dem Namen "testvnet1" und ein Subnetz mit dem Namen "subnet-1" erstellt.


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* und *GatewaySize* sind optionale Parameter.


**Um zu überprüfen**, ob das Gateway erstellt wurde, können Sie das `Get-AzureApplicationGateway`-Cmdlet verwenden.




	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen "Small", "Medium" und "Large" auswählen.


 *Vip* und *DnsName* werden leer angezeigt, da das Gateway noch nicht gestartet wurde. Die Werte werden erstellt, sobald das Gateway ausgeführt wird.

## Konfigurieren des Application Gateways

Sie können das Application Gateway mit XML oder einem Konfigurationsobjekt konfigurieren.

## Konfigurieren des Application Gateways mit XML

Im folgenden Beispiel verwenden Sie eine XML-Datei, um alle Einstellungen des Application Gateways zu konfigurieren und auf die Application Gateway-Ressource zu übertragen.

### Schritt 1  

Kopieren Sie den folgenden Text in Editor.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

Bearbeiten Sie die Werte zwischen den Klammern für die Konfigurationselemente. Speichern Sie die Datei mit der Erweiterung XML.

>[AZURE.IMPORTANT]Für die Protokollelemente Http oder Https muss die Groß-/Kleinschreibung beachtet werden.

Das folgende Beispiel zeigt, wie Sie mithilfe einer Konfigurationsdatei das Application Gateway für den Lastenausgleich von HTTP-Datenverkehr am öffentlichen Port 80 und zum Senden des Netzwerkdatenverkehrs an den Back-End-Port 80 zwischen zwei IP-Adressen einrichten:

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>





### Schritt 2

Anschließend legen Sie das Application Gateway fest. Verwenden Sie das `Set-AzureApplicationGatewayConfig`-Cmdlet mit einer XML-Konfigurationsdatei.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Konfigurieren des Application Gateways mit einem Konfigurationsobjekt

Das folgende Beispiel zeigt, wie Sie das Application Gateway mithilfe von Konfigurationsobjekten konfigurieren. Alle Konfigurationselemente müssen einzeln konfiguriert und anschließend einem Konfigurationsobjekt für das Application Gateway hinzugefügt werden. Nach dem Erstellen des Konfigurationsobjekts verwenden Sie den Befehl `Set-AzureApplicationGateway`, um die Konfiguration auf die zuvor erstellte Application Gateway-Ressource zu übertragen.

>[AZURE.NOTE]Bevor Sie dem Konfigurationsobjekt einen Wert zuweisen, müssen Sie deklarieren, welcher Objekttyp in PowerShell zum Speichern verwendet wird. Die erste Zeile zum Erstellen der einzelnen Elemente definiert, welches Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(Objektname) verwendet wird.

### Schritt 1

Erstellen Sie die einzelnen Konfigurationselemente.

Erstellen Sie die Front-End-IP wie im folgenden Beispiel gezeigt.

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
	PS C:\> $fip.Name = "fip1"
	PS C:\> $fip.Type = "Private"
	PS C:\> $fip.StaticIPAddress = "10.0.0.5"

Erstellen Sie den Front-End-Port wie im folgenden Beispiel gezeigt.

	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
	PS C:\> $fep.Name = "fep1"
	PS C:\> $fep.Port = 80

Erstellen Sie den Back-End-Serverpool:

 Definieren Sie die IP-Adressen, die dem Back-End-Serverpool hinzugefügt werden, wie im folgenden Beispiel gezeigt:


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 Fügen Sie mit dem $server-Objekt die Werte zum Back-End-Poolobjekt ($pool) hinzu.

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

Erstellen Sie die Einstellung für den Back-End-Serverpool:

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

Erstellen Sie den Listener:

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

Erstellen Sie die Regel:

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### Schritt 2

Weisen Sie die einzelnen Konfigurationselemente einem Konfigurationsobjekt für das Application Gateway ($appgwconfig) zu:

Fügen Sie die Front-End-IP der Konfiguration hinzu:

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Fügen Sie den Front-End-Port der Konfiguration hinzu:

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Fügen Sie den Back-End-Serverpool der Konfiguration hinzu:

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Fügen Sie die Einstellung des Back-End-Pools der Konfiguration hinzu:

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Fügen Sie den Listener der Konfiguration hinzu:

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Fügen Sie die Regel der Konfiguration hinzu:

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Schritt 3

Übertragen Sie mit `Set-AzureApplicationGatewayConfig` das Konfigurationsobjekt auf die Application Gateway-Ressource.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Starten des Gateways

Nachdem das Gateway konfiguriert ist, starten Sie es mit dem Cmdlet `Start-AzureApplicationGateway`. Die Abrechnung für ein Application Gateway beginnt, nachdem das Gateway erfolgreich gestartet wurde.


> [AZURE.NOTE]Die Ausführung des Cmdlets `Start-AzureApplicationGateway` kann 15 bis 20 Minuten dauern.



	PS C:\> Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Überprüfen des Gatewaystatus

Verwenden Sie das Cmdlet `Get-AzureApplicationGateway` zum Überprüfen des Gatewaystatus. Wenn *Start-AzureApplicationGateway* im vorherigen Schritt erfolgreich ausgeführt wurde, sollte der Status *Wird ausgeführt* sein, und "Vip" und "DnsName" sollten gültige Einträge aufweisen.

Dieses Beispiel zeigt ein Application Gateway, das ausgeführt wird und Datenverkehr verarbeiten kann, der für `http://<generated-dns-name>.cloudapp.net` vorgesehen ist.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	Vip           : 138.91.170.26
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Löschen eines Application Gateways

So löschen Sie ein Application Gateway

1. Verwenden Sie das Cmdlet `Stop-AzureApplicationGateway` zum Beenden des Gateways.
2. Verwenden Sie das Cmdlet `Remove-AzureApplicationGateway` zum Entfernen des Gateways.
3. Überprüfen Sie mit dem Cmdlet `Get-AzureApplicationGateway`, ob das Gateway entfernt wurde.

Das folgende Beispiel zeigt das Cmdlet `Stop-AzureApplicationGateway` in der ersten Zeile, gefolgt von der Ausgabe.

	PS C:\> Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Nachdem das Application Gateway beendet wurde, verwenden Sie das Cmdlet `Remove-AzureApplicationGateway`, um den Dienst zu entfernen.


	PS C:\> Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Um sicherzustellen, dass der Dienst wurde entfernt, können Sie das Cmdlet `Get-AzureApplicationGateway` verwenden. Dieser Schritt ist nicht erforderlich.


	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, lesen Sie [Konfigurieren des Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md).

Wenn Sie ein Application Gateway für ILB konfigurieren möchten, lesen Sie [Erstellen eines Application Gateways mit einem internen Load Balancer (ILB)](application-gateway-ilb.md).

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Dokumentation zum Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO9-->