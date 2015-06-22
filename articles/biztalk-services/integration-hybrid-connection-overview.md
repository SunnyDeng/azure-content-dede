<properties 
	pageTitle="Übersicht über Hybridverbindungen | Azure" 
	description="Erfahren Sie mehr zu Hybridverbindungen, unter anderen zu den Aspekten Sicherheit, TCP-Ports und unterstützte Konfigurationen. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="mandia"/>



# Überblick über Hybridverbindungen
In diesem Thema werden Hybridverbindungen vorgestellt und die unterstützten Konfigurationen sowie die erforderlichen TCP-Ports aufgeführt. Dies gilt insbesondere in folgenden Fällen:

- [Was ist eine Hybridverbindung](#HCOverview)
- [Unterstützte Konfigurationen](#KnownIssues)
- [Sicherheit und Ports](#HCSecurity)

##<a name="HCOverview"></a>Was ist eine Hybridverbindung

Hybridverbindungen bieten eine einfache und praktische Möglichkeit, um Azure-Websites und Azure Mobile Services mit lokalen Ressourcen zu verbinden. Hybridverbindungen sind eine Funktion von Azure BizTalk Services:

![Hybrid Connections][HCImage]

Zu den Vorteilen von Hybridverbindungen zählen:

- Websites und Mobile Services können sicher auf vorhandene lokale Daten und Dienste zugreifen.
- Mehrere Websites oder Mobile Services können eine Hybridverbindung gemeinsam für den Zugriff auf eine lokale Ressource nutzen. 
- Für den Zugriff auf das Netzwerk ist eine minimale Anzahl TCP-Ports erforderlich.
- Anwendungen, die Hybridverbindungen verwenden, greifen nur auf die spezifische lokale Ressource zu, die über die Hybridverbindung veröffentlicht wurde.
- Es sind Verbindungen zu allen lokalen Ressourcen möglich, die einen statischen TCP-Port verwenden, inklusive SQL Server, MySQL, HTTP Web-APIs und die meisten benutzerdefinierten Webdienste.

> [WACOM.NOTE] TCP-basierte Dienste, die dynamische Ports verwenden (wie passiver oder erweiterter passiver FTP-Modus) werden derzeit nicht unterstützt.

- Sie können zusammen mit allen Frameworks verwendet werden, die von Azure-Websites (.NET, PHP, Java, Python, Node.js) und Azure Mobile Services (Node.js, .NET) unterstützt werden.
- Websites und Mobile Services können auf lokale Ressourcen genau so zugreifen, als ob sich die Website oder der Mobile Service im lokalen Netzwerk befände. So kann z. B. die lokal verwendete Verbindungszeichenfolge auch in Azure verwendet werden.


Hybridverbindungen bieten auch Unternehmensadministratoren Kontrolle und Einsicht in die Unternehmensressourcen, auf die durch Hybridverbindungen zugegriffen wird. Dazu zählen folgende Möglichkeiten:

- Anhand von Gruppenrichtlinieneinstellungen können Administratoren Hybridverbindungen im Netzwerk zulassen und auch Ressourcen bestimmen, auf die durch Hybridverbindungen zugegriffen werden kann.
- Ereignis- und Überwachungsprotokolle im Unternehmensnetzwerk geben Einsicht in die Ressourcen, auf die von Hybridverbindungen zugegriffen wird.


##<a name="KnownIssues"></a>Unterstützte Konfigurationen

Hybridverbindungen unterstützen die folgenden Framework- und Anwendungskombinationen:

- .NET-Framework-Zugriff auf SQL Server
- .NET-Framework-Zugriff auf HTP/HTTPS-Dienste mit Web/Client
- PHP-Zugriff auf SQL Server, MySQL
- Java-Zugriff auf SQL Server, MySQL und Oracle
- Java-Zugriff auf HTTP/HTTPS-Dienste

Wenn Hybridverbindungen für den Zugriff auf lokale SQL Server verwendet wird, beachten Sie Folgendes:

- Benannte Instanzen von SQL Express müssen für die Verwendung von statischen Ports konfiguriert sein. Standardmäßig verwenden benannte Instanzen von SQL Express dynamische Ports.
- Standardinstanzen von SQL Express verwenden einen statischen Port; es muss jedoch TCP aktiviert sein. TCP ist standardmäßig nicht aktiviert.
- Wenn Clustering oder Verfügbarkeitsgruppen verwendet werden, wird der Modus "MultiSubnetFailover=true" derzeit nicht unterstützt.
- "ApplicationIntent=ReadOnly" wird derzeit nicht unterstützt.
- Als End-to-End-Autorisierungsmethode, die von der Azure-Anwendung und dem lokalen SQL Server unterstützt wird, kann SQL-Authentifizierung erforderlich sein.


##<a name="HCSecurity"></a>Sicherheit und Ports

Hybridverbindungen verwenden SAS-Autorisierung (Shared Access Signature) zum Sichern der Verbindungen von den Azure-Anwendungen und dem lokalen Hybrid Connection Manager zur Hybridverbindung. Es werden getrennte Verbindungsschlüssel für die Anwendung und den lokalen Hybrid Connection Manager erstellt. Diese Verbindungsschlüssel können unabhängig voneinander ausgetauscht oder widerrufen werden.

Hybridverbindungen stellen eine reibungslose und sichere Verteilung der Schlüssel an die Anwendungen und den lokalen Hybrid Connection Manager bereit. 

Weitere Informationen finden Sie unter  [Erstellen und Verwalten von Hybridverbindungen](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-create-manage). 

**Die Anwendungsautorisierung verläuft getrennt von der Hybridverbindung**. Jede geeignete Autorisierungsmethode kann verwendet werden. Die Autorisierungsmethode hängt davon ab, welche End-to-End-Autorisierungsmethoden in der Azure-Cloud und von den lokalen Komponenten unterstützt werden. Beispiel: Ihre Azure-Anwendung greift auf einen lokalen SQL Server zu. In diesem Szenario kann SQL-Autorisierung die unterstützte End-to-End-Autorisierungsmethode sein.

####TCP-Ports
Hybridverbindungen erfordern nur ausgehende TCP- oder HTTP-Verbindungen von Ihrem privaten Netzwerk. Sie müssen keine Firewallports öffnen oder Ihre Netzwerkkonfiguration  ändern, um eingehende Verbindungen in Ihrem Netzwerk zu ermöglichen.

Die folgenden TCP-Ports werden von Hybridverbindungen verwendet:

<table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Warum</th>
    </tr>
    <tr>
        <td>80</td>
        <td>HTTP-Port, für Zertifikatsvalidierung verwendet.</td>
    </tr>
    <tr>
        <td>443</td>
        <td>HTTPS-Port</td>
    </tr>
	<tr>
        <td>5671</td>
        <td>Für die Verbindung mit Azure verwendet. Wenn TCP-Port 5671 nicht verfügbar ist, wird TCP-Port 443 verwendet.</td>
	</tr>
	<tr>
        <td>9352</td>
        <td>Wird für Push- und Pull-Vorgänge für Daten verwendet. Wenn TCP-Port 9352 nicht verfügbar ist, wird TCP-Port 443 verwendet.</td>
	</tr>
</table>


## Weiter

- [Erstellen und Verwalten von Hybridverbindungen](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-create-manage)
- [Verbinden einer Azure-Website mit einer lokalen Ressource](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Azure Mobile Services und Hybridverbindungen](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)


## Weitere Informationen

- [REST API für die Verwaltung von BizTalk Services unter Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Erstellen eines BizTalk Service mit Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!--HONumber=46--> 
 