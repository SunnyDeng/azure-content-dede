<properties 
   pageTitle="Erstellen eines benutzerdefinierten Tests für ein Application Gateway im klassischen Bereitstellungsmodell mithilfe von PowerShell | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie mithilfe von PowerShell einen benutzerdefinierten Test für ein Application Gateway im klassischen Bereitstellungsmodell erstellen."
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Erstellen eines benutzerdefinierten Tests für ein Application Gateway (klassisch) mithilfe von PowerShell


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


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


Mithilfe des Cmdlets `Get-AzureApplicationGateway` können Sie **überprüfen**, ob das Gateway erstellt wurde.


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


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


Bearbeiten Sie die Werte zwischen den Klammern für die Konfigurationselemente. Speichern Sie die Datei mit der Erweiterung XML.

Das folgende Beispiel zeigt, wie Sie mithilfe einer Konfigurationsdatei das Application Gateway für den Lastenausgleich von HTTP-Datenverkehr am öffentlichen Port 80 und für das Senden des Netzwerkdatenverkehrs an den Back-End-Port 80 zwischen zwei IP-Adressen einrichten. Dabei wird ein benutzerdefinierter Test (engl. „Probe“) verwendet.

>[AZURE.IMPORTANT]Für die Protokollelemente Http oder Https muss die Groß-/Kleinschreibung beachtet werden.


Es wird ein neues Konfigurationselement <Probe> hinzugefügt, um benutzerdefinierte Tests zu konfigurieren.

Die Konfigurationsparameter sind:

- **Name**: Der Verweisname für den benutzerdefinierten Test.
- **Protocol**: Das verwendete Protokoll (mögliche Werte sind „Http“ oder „Https“).
- **Host** und **Path**: Vollständiger URL-Pfad, der vom Application Gateway aufgerufen wird, um die Integrität der Instanz zu ermitteln. Beispiel: Bei einer Website http://contoso.com/ können Sie den benutzerdefinierten Test für „http://contoso.com/path/custompath.htm“ konfigurieren, damit die HTTP-Antwort bei den Prüfungen des Tests erfolgreich ist. 
- **Interval**: Konfiguriert die Intervalle für die Prüfungen des Tests (in Sekunden). 
- **Timeout**: Definiert das Zeitlimit des Tests für eine HTTP-Antwortprüfung.
- **UnhealthyThreshold**: Die Anzahl der Fehler bei HTTP-Antworten, ab der die Back-End-Instanz als *fehlerhaft* gekennzeichnet wird.

Auf den Namen des Tests wird in der <BackendHttpSettings> Konfiguration verwiesen, um festzulegen, welcher Back-End-Pool die Einstellungen für den benutzerdefinierten Test verwenden soll.

## Hinzufügen der Konfiguration eines benutzerdefinierten Tests zu einem vorhandenen Application Gateway

Zum Ändern der aktuellen Konfiguration eines Application Gateways sind drei Schritte nötig: das Abrufen der aktuellen XML-Konfigurationsdatei, das Einfügen des benutzerdefinierten Tests in die XML-Datei und das Konfigurieren des Application Gateways mit den neuen XML-Einstellungen

### Schritt 1

Rufen Sie mit „get-AzureApplicationGatewayConfig“ die XML-Datei ab. Dadurch wird die Konfigurations-XML-Datei exportiert, sodass ihr die Einstellungen für den Test hinzugefügt werden können.
	
	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### Schritt 2 

Öffnen Sie die XML-Datei in einem Texteditor. Fügen Sie nach `<frontendport>` den Abschnitt `<probe>` ein.
	
	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

Im Abschnitt „BackendHttpSettings“ der XML-Datei fügen Sie den Namen des Tests so hinzu wie im folgenden Beispiel gezeigt:
    
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Speichern Sie die XML-Datei.


### Schritt 3 

Aktualisieren Sie die Konfiguration des Application Gateways mit der neuen XML-Datei, und verwenden Sie dazu `Set-AzureApplicationGatewayConfig`. Dadurch wird das Application Gateway mit der neuen Konfiguration aktualisiert.

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, lesen Sie [Konfigurieren eines Application Gateways für die SSL-Auslagerung](application-gateway-ssl.md).

Wenn Sie ein Application Gateway für ILB konfigurieren möchten, lesen Sie [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0107_2016-->