<properties 
   pageTitle="Erstellen, Starten oder Löschen eines Anwendungsgateways | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure-Anwendungsgateways"
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
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# Erstellen, Starten oder Löschen eines Anwendungsgateways

In dieser Version können Sie ein Anwendungsgateway mit PowerShell oder REST-API-Aufrufen erstellen. Portal- und CLI-Unterstützung werden in einer zukünftigen Version bereitgestellt. Dieser Artikel führt Sie durch die Schritte zum Erstellen und Konfigurieren, Starten und Löschen eines Anwendungsgateways.

## Voraussetzungen

1. Installieren Sie die neueste Version der Azure PowerShell-Cmdlets mit dem Webplattform-Installer. Sie können die neueste Version aus dem Abschnitt **Windows PowerShell** der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Stellen Sie sicher, dass Sie über ein funktionierendes virtuelles Netzwerk mit einem gültigen Subnetz verfügen.
3. Stellen Sie sicher, dass Sie über Back-End-Server im virtuellen Netzwerk verfügen oder dass ihnen eine öffentliche IP-Adresse/VIP zugewiesen ist.


Um ein neues Anwendungsgateway zu erstellen, führen Sie die folgenden Schritte in der angegebenen Reihenfolge aus.

1. [Erstellen eines neuen Anwendungsgateways](#create-a-new-application-gateway)
2. [Konfigurieren des Gateways](#configure-the-gateway)
3. [Festlegen der Gatewaykonfiguration](#set-the-gateway-configuration)
4. [Starten des Gateways](#start-the-gateway)
4. [Überprüfen des Gatewaystatus](#verify-the-gateway-status)

Wenn Sie ein Anwendungsgateway löschen möchten, wechseln Sie zu [Löschen eines Anwendungsgateways](#delete-an-application-gateway).

## Erstellen eines neuen Anwendungsgateways

**Zum Erstellen des Gateways** verwenden Sie das `New-AzureApplicationGateway`-Cmdlet und ersetzen die Werte durch eigene. Beachten Sie, dass die Abrechnung für das Gateway jetzt noch nicht gestartet wird. Die Abrechnung beginnt in einem späteren Schritt, wenn das Gateway erfolgreich gestartet wurde.

Dieses Beispiel zeigt das Cmdlet in der ersten Zeile, gefolgt von der Ausgabe.
    
	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Um zu überprüfen**, ob das Gateway erstellt wurde, können Sie das `Get-AzureApplicationGateway`-Cmdlet verwenden.

In diesem Beispiel sind *Description*, *InstanceCount* und *GatewaySize* optionale Parameter. Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". "Small" und "Large" sind weitere verfügbare Werte. *Vip* und *DnsName* werden leer angezeigt, da das Gateway noch nicht gestartet wurde. Die Werte werden erstellt, sobald das Gateway ausgeführt wird.




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


## Konfigurieren des Gateways

Eine Anwendungsgatewaykonfiguration besteht aus mehreren Werten. Die Werte können verknüpft werden, um die Konfiguration zu erstellen.

Die Werte sind:

- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem VNet-Subnetz angehören oder eine öffentliche IP-Adresse/VIP sein. 
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Anwendungsgateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll \(Http oder Https, bei beiden muss die Groß-/Kleinschreibung beachtet werden\) und den Namen des SSL-Zertifikats \(falls SSL-Auslagerung konfiguriert wird\). 
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden sollen, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.

Sie können die Konfiguration erzeugen, indem Sie ein Konfigurationsobjekt erstellen oder eine XML-Konfigurationsdatei verwenden. Um die Konfiguration mithilfe einer XML-Konfigurationsdatei zu erstellen, verwenden Sie das folgende Beispiel.

 **XML-Konfigurationsbeispiel**

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

## Festlegen der Gatewaykonfiguration

Dann legen Sie das Anwendungsgateway fest. Sie können das `Set-AzureApplicationGatewayConfig`-Cmdlet mit einem Konfigurationsobjekt oder mit einer XML-Konfigurationsdatei verwenden.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Starten des Gateways

Sobald das Gateway konfiguriert wurde, verwenden Sie das `Start-AzureApplicationGateway`-Cmdlet, um das Gateway zu starten. Die Abrechnung für ein Anwendungsgateway beginnt, nachdem das Gateway erfolgreich gestartet wurde.


**Hinweis:** Bis zum Abschluss des `Start-AzureApplicationGateway`-Cmdlets können bis zu 15 – 20 Minuten vergehen.



	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Überprüfen des Gatewaystatus

Verwenden Sie das `Get-AzureApplicationGateway`-Cmdlet zum Überprüfen des Status des Gateways. Wenn *Start-AzureApplicationGateway* im vorherigen Schritt erfolgreich ausgeführt wurde, sollte der Status *Running* sein, und "Vip" und "DnsName" sollten gültige Einträge aufweisen.

Dieses Beispiel zeigt ein Anwendungsgateway, das ausgeführt wird und Datenverkehr verarbeiten kann, der für `http://<generated-dns-name>.cloudapp.net` vorgesehen ist.

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


## Löschen eines Anwendungsgateways

Um ein Anwendungsgateway zu löschen, müssen Sie die folgenden Schritte in der angegebenen Reihenfolge ausführen:

1. Verwenden Sie das `Stop-AzureApplicationGateway`-Cmdlet zum Beenden des Gateways. 
2. Verwenden Sie das `Remove-AzureApplicationGateway`-Cmdlet zum Entfernen des Gateways.
3. Überprüfen Sie mit dem `Get-AzureApplicationGateway`-Cmdlet, ob das Gateway wurde.

Dieses Beispiel zeigt das `Stop-AzureApplicationGateway`-Cmdlet in der ersten Zeile, gefolgt von der Ausgabe.

	PS C:\> Stop-AzureApplicationGateway AppGwTest 

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Sobald das Anwendungsgateway beendet wurde, verwenden das `Remove-AzureApplicationGateway`-Cmdlet, um den Dienst zu entfernen.


	PS C:\> Remove-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Um sicherzustellen, dass der Dienst wurde entfernt, können Sie das `Get-AzureApplicationGateway`-Cmdlet verwenden. Dieser Schritt ist nicht erforderlich.


	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, lesen Sie [Konfigurieren des Anwendungsgateways für die SSL-Auslagerung](application-gateway-ssl.md).

Wenn Sie ein Anwendungsgateway für ILB konfigurieren möchten, lesen Sie [Erstellen eines Anwendungsgateways mit einem internen Lastenausgleich \(ILB\)](application-gateway-ilb.md).

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=July15_HO5-->