<properties
   pageTitle="Erstellen, Starten oder Löschen eines Application Gateways | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure Application Gateways."
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
   ms.date="02/02/2016"
   ms.author="joaoma"/>

# Erstellen, Starten oder Löschen eines Application Gateways

Für das Azure Application Gateway wird ein Load Balancer auf Schicht 7 (Anwendungsschicht) verwendet. Das Application Gateway ermöglicht ein Failover sowie schnelles Routing von HTTP-Anforderungen zwischen verschiedenen Servern in der Cloud und der lokalen Umgebung. Application Gateways weisen folgende Anwendungsbereitstellungsfunktionen auf: HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität und Secure Sockets Layer-Auslagerung (SSL).

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template](application-gateway-create-gateway-arm-template.md)


<BR>

In diesem Artikel werden Sie durch die Schritte zum Erstellen, Konfigurieren, Starten und Löschen eines Application Gateways geführt.


## Voraussetzungen

1. Installieren Sie mit dem Webplattform-Installer die aktuelle Version der Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt **Windows PowerShell** der [Downloadseite](https://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Stellen Sie sicher, dass Sie über ein funktionierendes virtuelles Netzwerk mit einem gültigen Subnetz verfügen. Stellen Sie sicher, dass keine virtuellen Maschinen oder Cloudbereitstellungen das Subnetz verwenden. Das Application Gateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Application Gateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## Was ist zum Erstellen eines Application Gateways erforderlich?


Wenn Sie den Befehl **New-AzureApplicationGateway** zum Erstellen des Application Gateways verwenden, wird zu diesem Zeitpunkt keine Konfiguration festgelegt, und die neu erstellte Ressource muss entweder per XML oder mit einem Konfigurationsobjekt konfiguriert werden.


Die Werte sind:

- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein.
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, jeweils mit Beachtung der Groß-/Kleinschreibung) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird).
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden soll, wenn er einen bestimmten Listener erreicht.


## Erstellen eines neuen Anwendungsgateways

So erstellen Sie ein Application Gateway

1. Erstellen Sie eine neue Application Gateway-Ressource.
2. Erstellen Sie eine XML-Konfigurationsdatei oder ein Konfigurationsobjekt.
3. Ordnen Sie die Konfiguration der neu erstellten Application Gateway-Ressource zu.

>[AZURE.NOTE] Falls Sie einen benutzerdefinierten Test für Ihr Application Gateway konfigurieren müssen, helfen Ihnen die entsprechenden Informationen unter [Erstellen eines Application Gateways mit benutzerdefinierten Tests mithilfe von PowerShell](application-gateway-create-probe-classic-ps.md) weiter. Weitere Informationen finden Sie unter [Benutzerdefinierte Tests und Systemüberwachung](application-gateway-probe-overview.md).


### Erstellen einer Application Gateway-Ressource

Verwenden Sie zum Erstellen des Gateways das **New-AzureApplicationGateway**-Cmdlet, und ersetzen Sie die Werte durch Ihre eigenen Werte. Beachten Sie, dass die Abrechnung für das Gateway jetzt noch nicht gestartet wird. Die Abrechnung beginnt in einem späteren Schritt, wenn das Gateway erfolgreich gestartet wurde.

Mit dem folgenden Beispiel wird ein neues Application Gateway über ein virtuelles Netzwerk mit dem Namen „testvnet1“ und ein Subnetz mit dem Namen „subnet-1“ erstellt.


	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* und *GatewaySize* sind optionale Parameter.


Sie können das **Get-AzureApplicationGateway**-Cmdlet verwenden, um zu überprüfen, ob das Gateway erstellt wurde.




	Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]  Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". Sie können zwischen "Small", "Medium" und "Large" auswählen.


 *VirtualIPs* und *DnsName* werden leer angezeigt, da das Gateway noch nicht gestartet wurde. Die Werte werden erstellt, sobald das Gateway ausgeführt wird.

## Konfigurieren des Application Gateways

Sie können das Application Gateway per XML oder mit einem Konfigurationsobjekt konfigurieren.

## Konfigurieren des Application Gateways per XML

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

>[AZURE.IMPORTANT] Für die Protokollelemente Http oder Https muss die Groß-/Kleinschreibung beachtet werden.

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

Legen Sie anschließend das Application Gateway fest. Verwenden Sie das **Set-AzureApplicationGatewayConfig**-Cmdlet mit einer XML-Konfigurationsdatei.


	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Konfigurieren des Application Gateways mit einem Konfigurationsobjekt

Das folgende Beispiel zeigt, wie Sie das Application Gateway mithilfe von Konfigurationsobjekten konfigurieren. Alle Konfigurationselemente müssen einzeln konfiguriert und anschließend einem Konfigurationsobjekt für das Application Gateway hinzugefügt werden. Nach dem Erstellen des Konfigurationsobjekts verwenden Sie den Befehl **Set-AzureApplicationGateway**, um die Konfiguration der zuvor erstellten Application Gateway-Ressource zuzuordnen.

>[AZURE.NOTE] Bevor Sie dem Konfigurationsobjekt einen Wert zuweisen, müssen Sie deklarieren, welcher Objekttyp in PowerShell zum Speichern verwendet wird. Die erste Zeile zum Erstellen der einzelnen Elemente definiert, welches Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(Objektname) verwendet wird.

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

Erstellen Sie den Back-End-Serverpool.

 Definieren Sie die IP-Adressen, die dem Back-End-Serverpool hinzugefügt werden, wie im folgenden Beispiel gezeigt.


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 Verwenden Sie das $server-Objekt, um die Werte dem Back-End-Poolobjekt ($pool) hinzuzufügen.

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

Erstellen Sie die Einstellung für den Back-End-Serverpool.

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

Erstellen Sie den Listener.

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

Erstellen Sie die Regel.

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### Schritt 2

Weisen Sie die einzelnen Konfigurationselemente einem Konfigurationsobjekt für das Application Gateway ($appgwconfig) zu.

Fügen Sie die Front-End-IP der Konfiguration hinzu.

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Fügen Sie den Front-End-Port der Konfiguration hinzu.

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Fügen Sie den Back-End-Serverpool der Konfiguration hinzu.

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Fügen Sie die Einstellung des Back-End-Pools der Konfiguration hinzu.

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Fügen Sie den Listener der Konfiguration hinzu.

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Fügen Sie die Regel der Konfiguration hinzu.

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Schritt 3

Ordnen Sie das Konfigurationsobjekt der Application Gateway-Ressource zu, indem Sie **Set AzureApplicationGatewayConfig** verwenden.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Starten des Gateways

Verwenden Sie nach dem Konfigurieren des Gateways das **Start-AzureApplicationGateway**-Cmdlet, um das Gateway zu starten. Die Abrechnung für ein Application Gateway beginnt, nachdem das Gateway erfolgreich gestartet wurde.


> [AZURE.NOTE] Es kann 15 bis 20 Minuten dauern, bis der Vorgang für das **Start-AzureApplicationGateway**-Cmdlet abgeschlossen ist.



	Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Überprüfen des Gatewaystatus

Verwenden Sie das **Get-AzureApplicationGateway**-Cmdlet, um den Status des Gateways zu überprüfen. Wenn **Start-AzureApplicationGateway** im vorherigen Schritt erfolgreich ausgeführt wurde, sollte der *Status* „Running“ lauten, und *Vip* und *DnsName* sollten gültige Einträge aufweisen.

Dieses Beispiel zeigt ein Application Gateway, das ausgeführt wird und Datenverkehr verarbeiten kann, der für `http://<generated-dns-name>.cloudapp.net` vorgesehen ist.

	Get-AzureApplicationGateway AppGwTest

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

1. Verwenden Sie das **Stop-AzureApplicationGateway**-Cmdlet, um das Gateway zu beenden.
2. Verwenden Sie das **Remove-AzureApplicationGateway**-Cmdlet, um das Gateway zu entfernen.
3. Stellen Sie sicher, dass das Gateway entfernt wurde, indem Sie das **Get-AzureApplicationGateway**-Cmdlet verwenden.

Im folgenden Beispiel ist das **Stop-AzureApplicationGateway**-Cmdlet in der ersten Zeile dargestellt, gefolgt von der Ausgabe.

	Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Verwenden Sie das **Remove-AzureApplicationGateway**-Cmdlet, um den Dienst zu entfernen, sobald das Application Gateway beendet wurde.


	Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Um sicherzustellen, dass der Dienst entfernt wurde, können Sie das **Get-AzureApplicationGateway**-Cmdlet verwenden. Dieser Schritt ist nicht erforderlich.


	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, ist es ratsam, den Abschnitt [Konfigurieren eines Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md) zu lesen.

Wenn Sie ein Application Gateway für die Verwendung mit einem internen Load Balancer konfigurieren möchten, ist es ratsam, den Abschnitt [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md) zu lesen.

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0224_2016-->