<properties 
   pageTitle="Erstellen und Konfigurieren eines Anwendungsgateways mit internem Lastenausgleich (ILB) in einem virtuellen Netzwerk | Microsoft Azure"
   description="Diese Seite enthält Anweisungen zum Konfigurieren eines Azure-Anwendungsgateways mit einem Endpunkt für internen Lastenausgleich"
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
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# Erstellen eines Anwendungsgateways mit einem internen Lastenausgleich \(ILB\)


Ein Anwendungsgateway kann mit einer VIP mit Internetzugriff oder mit einem internen Endpunkt konfiguriert werden, der nicht über das Internet erreichbar ist. Dies wird auch als Endpunkt für internen Lastenausgleich \(Internal Load Balancer, ILB\) bezeichnet. Das Konfigurieren des Gateways mit einem ILB ist für interne Branchenanwendungen nützlich, die nicht für das Internet verfügbar gemacht werden. Es ist auch hilfreich für die Dienste/Ebenen in einer Anwendung mit mehreren Ebenen, die sich innerhalb einer Sicherheitsgrenze befindet und nicht für das Internet verfügbar gemacht wird, aber dennoch eine Round-Robin-Lastverteilung, Sitzungsbindungen oder SSL-Beendigung erfordert. Dieser Artikel führt Sie durch die Schritte zum Konfigurieren eines Anwendungsgateways mit einem ILB.

## Voraussetzungen

1. Installieren Sie die neueste Version der Azure PowerShell-Cmdlets mit dem Webplattform-Installer. Sie können die neueste Version aus dem Abschnitt **Windows PowerShell** der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren.
2. Stellen Sie sicher, dass Sie über ein funktionierendes virtuelles Netzwerk mit einem gültigen Subnetz verfügen.
3. Stellen Sie sicher, dass Sie über Back-End-Server im virtuellen Netzwerk verfügen oder dass ihnen eine öffentliche IP-Adresse/VIP zugewiesen ist.


Um ein neues Anwendungsgateway zu erstellen, führen Sie die folgenden Schritte in der angegebenen Reihenfolge aus.

1. [Erstellen eines neuen Anwendungsgateways](#create-a-new-application-gateway)
2. [Konfigurieren des Gateways](#configure-the-gateway)
3. [Festlegen der Gatewaykonfiguration](#set-the-gateway-configuration)
4. [Starten des Gateways](#start-the-gateway)
4. [Überprüfen des Gateways](#verify-the-gateway-status)



## Erstellen Sie ein neues Anwendungsgateway:

**Zum Erstellen des Gateways** verwenden Sie das `New-AzureApplicationGateway`-Cmdlet und ersetzen die Werte durch eigene. Beachten Sie, dass die Abrechnung für das Gateway jetzt noch nicht gestartet wird. Die Abrechnung beginnt in einem späteren Schritt, wenn das Gateway erfolgreich gestartet wurde.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Um zu überprüfen**, ob das Gateway erstellt wurde, können Sie das `Get-AzureApplicationGateway`-Cmdlet verwenden.

In diesem Beispiel sind *Description*, *InstanceCount* und *GatewaySize* optionale Parameter. Der Standardwert für *InstanceCount* ist 2, der Maximalwert ist 10. Der Standardwert für *GatewaySize* ist "Medium". "Small" und "Large" sind weitere verfügbare Werte. *Vip* und *DnsName* werden leer angezeigt, da das Gateway noch nicht gestartet wurde. Die Werte werden erstellt, sobald das Gateway ausgeführt wird.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## Konfigurieren des Gateways

Eine Anwendungsgatewaykonfiguration besteht aus mehreren Werten. Die Werte können verknüpft werden, um die Konfiguration zu erstellen.
 
Die Werte sind:

- **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem VNet-Subnetz angehören oder eine öffentliche IP-Adresse/VIP sein. 
- **Einstellungen für den Back-End-Serverpool:** Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port:** Dieser Port ist der öffentliche Port, der im Anwendungsgateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll \(Http oder Https, bei beiden muss die Groß-/Kleinschreibung beachtet werden\) und den Namen des SSL-Zertifikats \(falls SSL-Auslagerung konfiguriert wird\). 
- **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden sollen, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.

Sie können die Konfiguration erzeugen, indem Sie ein Konfigurationsobjekt erstellen oder eine XML-Konfigurationsdatei verwenden. Um die Konfiguration mithilfe einer XML-Konfigurationsdatei zu erstellen, verwenden Sie das folgende Beispiel.



Beachten Sie Folgendes:


- Das *FrontendIPConfigurations*-Element beschreibt die ILB-Details, die zum Konfigurieren eines Anwendungsgateways mit einem ILB relevant sind. 

- Der Wert für *Type* der Front-End-IP sollte auf "Private" festgelegt werden.

- *StaticIPAddress* sollte auf die gewünschte interne IP-Adresse festgelegt werden, an der das Gateway Datenverkehr empfangen wird. Beachten Sie, dass das *StaticIPAddress*-Element optional ist. Wenn es nicht festgelegt ist, wird eine verfügbare interne IP-Adresse aus dem bereitgestellten Subnetz ausgewählt.

- Der Wert des *Name*-Elements, das für *FrontendIPConfiguration* angegeben wurde, sollte im *FrontendIP*-Element von HTTPListener verwendet werden, um auf FrontendIPConfiguration zu verweisen.

 **XML-Konfigurationsbeispiel**

 

		<?xml version="1.0" encoding="utf-8"?>
		<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
			<FrontendIPConfigurations>
				<FrontendIPConfiguration>
					<Name>fip1</Name> 
					<Type>Private</Type> 
					<StaticIPAddress>10.0.0.10</StaticIPAddress> 
				</FrontendIPConfiguration>
			</FrontendIPConfigurations>
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
					<FrontendIP>fip1</FrontendIP>
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

Verwenden Sie das `Get-AzureApplicationGateway`-Cmdlet zum Überprüfen des Status des Gateways. Wenn *Start-AzureApplicationGateway* im vorherigen Schritt erfolgreich ausgeführt wurde, sollte der Status *Running* sein, und "Vip" und "DnsName" sollten gültige Einträge aufweisen. Dieses Beispiel zeigt das Cmdlet in der ersten Zeile, gefolgt von der Ausgabe. In diesem Beispiel wird das Gateway ausgeführt und kann Datenverkehr verarbeiten.

**Hinweis:** Das Anwendungsgateway ist so konfiguriert, dass in diesem Beispiel Datenverkehr am konfigurierten ILB-Endpunkt 10.0.0.10 akzeptiert wird.

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
	VirtualIPs    : {10.0.0.10}
	DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## Nächste Schritte


Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

- [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=July15_HO5-->