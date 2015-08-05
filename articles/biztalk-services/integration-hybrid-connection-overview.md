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
	ms.date="06/14/2015" 
	ms.author="mandia"/>


# Überblick über Hybridverbindungen
In diesem Thema werden Hybridverbindungen vorgestellt und die unterstützten Konfigurationen sowie die erforderlichen TCP-Ports aufgeführt.


## Was ist eine Hybridverbindung

Hybridverbindungen sind eine Funktion von Azure BizTalk Services. Hybridverbindungen bieten eine einfache und praktische Möglichkeit, Azure-Web-Apps (früher Websites) und mobile Azure-Apps (früher Mobile Services) mit lokalen Ressourcen hinter der Firewall zu verbinden:

![Hybridverbindungen][HCImage]

Zu den Vorteilen von Hybridverbindungen zählen:

- Web-Apps und Mobile Apps können sicher auf vorhandene lokale Daten und Dienste zugreifen.
- Mehrere Web-Apps oder mobile Apps können eine Hybridverbindung gemeinsam für den Zugriff auf eine lokale Ressource nutzen. 
- Für den Zugriff auf das Netzwerk ist eine minimale Anzahl TCP-Ports erforderlich.
- Anwendungen, die Hybridverbindungen verwenden, greifen nur auf die spezifische lokale Ressource zu, die über die Hybridverbindung veröffentlicht wurde.
- Es sind Verbindungen zu allen lokalen Ressourcen möglich, die einen statischen TCP-Port verwenden, inklusive SQL Server, MySQL, HTTP Web-APIs und die meisten benutzerdefinierten Webdienste.

	> [AZURE.NOTE]TCP-basierte Dienste, die dynamische Ports verwenden (wie passiver oder erweiterter passiver FTP-Modus) werden derzeit nicht unterstützt.

- Sie können zusammen mit allen Frameworks verwendet werden, die von Azure-Web-Apps (.NET, PHP, Java, Python, Node.js) und mobilen Azure-Apps (Node.js, .NET) unterstützt werden.
- Web-Apps und Mobile Apps können auf lokale Ressourcen genauso zugreifen, als ob sich die Web-App oder die mobile Apps im lokalen Netzwerk befände. So kann z. B. die lokal verwendete Verbindungszeichenfolge auch in Azure verwendet werden.


Hybridverbindungen bieten auch Unternehmensadministratoren Kontrolle und Einsicht in die Unternehmensressourcen, auf die durch Hybridverbindungen zugegriffen wird. Dazu zählen folgende Möglichkeiten:

- Anhand von Gruppenrichtlinieneinstellungen können Administratoren Hybridverbindungen im Netzwerk zulassen und auch Ressourcen bestimmen, auf die durch Hybridverbindungen zugegriffen werden kann.
- Ereignis- und Überwachungsprotokolle im Unternehmensnetzwerk geben Einsicht in die Ressourcen, auf die von Hybridverbindungen zugegriffen wird.


## Unterstützte Konfigurationen

Hybridverbindungen unterstützen die folgenden Framework- und Anwendungskombinationen:

- .NET-Framework-Zugriff auf SQL Server
- .NET-Framework-Zugriff auf HTP/HTTPS-Dienste mit Web/Client
- PHP-Zugriff auf SQL Server, MySQL
- Java-Zugriff auf SQL Server, MySQL und Oracle
- Java-Zugriff auf HTTP/HTTPS-Dienste

Wenn Hybridverbindungen für den Zugriff auf lokale SQL Server verwendet wird, beachten Sie Folgendes:

- Benannte Instanzen von SQL Express müssen für die Verwendung von statischen Ports konfiguriert sein. Standardmäßig verwenden benannte Instanzen von SQL Express dynamische Ports.
- Standardinstanzen von SQL Express verwenden einen statischen Port; es muss jedoch TCP aktiviert sein. TCP ist standardmäßig nicht aktiviert.
- Wenn Clustering oder Verfügbarkeitsgruppen verwendet werden, wird der Modus `MultiSubnetFailover=true` derzeit nicht unterstützt.
- `ApplicationIntent=ReadOnly` wird derzeit nicht unterstützt.
- Als End-to-End-Autorisierungsmethode, die von der Azure-Anwendung und dem lokalen SQL Server unterstützt wird, kann SQL-Authentifizierung erforderlich sein.


## Sicherheit und Ports

Hybridverbindungen verwenden SAS-Autorisierung (Shared Access Signature) zum Sichern der Verbindungen von den Azure-Anwendungen und dem lokalen Hybrid Connection Manager zur Hybridverbindung. Es werden getrennte Verbindungsschlüssel für die Anwendung und den lokalen Hybrid Connection Manager erstellt. Diese Verbindungsschlüssel können unabhängig voneinander ausgetauscht oder widerrufen werden.

Hybridverbindungen stellen eine reibungslose und sichere Verteilung der Schlüssel an die Anwendungen und den lokalen Hybrid Connection Manager bereit.

Weitere Informationen finden Sie unter [Erstellen und Verwalten von Hybridverbindungen](integration-hybrid-connection-create-manage.md).

**Die Anwendungsautorisierung verläuft getrennt von der Hybridverbindung**. Jede geeignete Autorisierungsmethode kann verwendet werden. Die Autorisierungsmethode hängt davon ab, welche End-to-End-Autorisierungsmethoden in der Azure-Cloud und von den lokalen Komponenten unterstützt werden. Beispiel: Ihre Azure-Anwendung greift auf einen lokalen SQL Server zu. In diesem Szenario kann SQL-Autorisierung die unterstützte End-to-End-Autorisierungsmethode sein.

#### TCP-Ports
Hybridverbindungen erfordern nur ausgehende TCP- oder HTTP-Verbindungen von Ihrem privaten Netzwerk. Sie müssen keine Firewallports öffnen oder Ihre Netzwerkkonfiguration ändern, um eingehende Verbindungen in Ihrem Netzwerk zu ermöglichen.

Die folgenden TCP-Ports werden von Hybridverbindungen verwendet:

Port | Grund
--- | ---
9350 - 9354 | Diese Ports werden für die Datenübertragung verwendet. Der Service Bus Relay-Manager prüft Port 9350, um festzustellen, ob die TCP-Konnektivität verfügbar ist. Wenn sie verfügbar ist, wird davon ausgegangen, dass Port 9352 ebenfalls verfügbar ist. Der Datenverkehr geht über Port 9352. <br/><br/>Ausgehende Verbindungen auf diese Ports zulassen.
5671 | Wenn Port 9352 für den Datenverkehr verwendet wird, wird Port 5671 als Steuerungskanal verwendet. <br/><br/>Ausgehende Verbindungen auf diesen Port zulassen. 
80, 443 | Wenn die Ports 9352 und 5671 nicht brauchbar sind, *dann* sind die Ports 80 und 443 die Alternative für die Datenübertragung und den Steuerungskanal.<br/><br/>Ausgehende Verbindungen auf diese Ports zulassen.<br/><br/>** Hinweis ** Sie sollten diese alternativen Ports nicht anstelle der anderen TCP-Ports verwenden. HTTP/WebSocket wird als Protokoll anstelle des systemeigenen TCP für Datenkanäle verwendet. Dies könnte zu einer niedrigeren Leistung führen.



## Weiter

[Erstellen und Verwalten von Hybridverbindungen](integration-hybrid-connection-create-manage.md)<br/> [Verbinden einer Azure-Website mit einer lokalen Ressource](../web-sites-hybrid-connection-get-started.md)<br/> [Verbinden mit einem lokalen SQL Server über Azure-Web-Apps](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Azure Mobile Services und Hybridverbindungen](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)


## Weitere Informationen

[REST-API zum Verwalten von BizTalk Services auf Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx) [BizTalk Services: Editionsübersicht](biztalk-editions-feature-chart.md) <br/>[Erstellen eines BizTalk Service mit dem Azure-Verwaltungsportal](biztalk-provision-services.md)<br/> [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!---HONumber=July15_HO4-->