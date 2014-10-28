<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Hybrid Connections Overview | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia"></tags>

# Überblick über Hybridverbindungen

In diesem Thema werden Hybridverbindungen vorgestellt und die unterstützten Konfigurationen sowie die erforderlichen TCP-Ports aufgeführt. Dies gilt insbesondere in folgenden Fällen:

-   [Was ist eine Hybridverbindung][Was ist eine Hybridverbindung]
-   [Unterstützte Konfigurationen][Unterstützte Konfigurationen]
-   [Sicherheit][Sicherheit]

## <a name="HCOverview"></a>Was ist eine Hybridverbindung

Hybridverbindungen bieten eine einfache und praktische Möglichkeit, um Azure-Websites und Azure Mobile Services mit lokalen Ressourcen zu verbinden. Hybridverbindungen sind eine Funktion von Azure BizTalk Services:

![Hybridverbindungen][Hybridverbindungen]

Zu den Vorteilen von Hybridverbindungen zählen:

-   Websites und Mobile Services können sicher auf vorhandene lokale Daten und Dienste zugreifen.
-   Mehrere Websites oder Mobile Services können eine Hybridverbindung gemeinsam für den Zugriff auf eine lokale Ressource nutzen.
-   Für den Zugriff auf das Netzwerk ist eine minimale Anzahl TCP-Ports erforderlich.
-   Anwendungen, die Hybridverbindungen verwenden, greifen nur auf die spezifische lokale Ressource zu, die über die Hybridverbindung veröffentlicht wurde.
-   Es sind Verbindungen zu allen lokalen Ressourcen möglich, die einen statischen TCP-Port verwenden, inklusive SQL Server, MySQL, HTTP Web-APIs und die meisten benutzerdefinierten Webdienste.

> [WACOM.NOTE] TCP-basierte Dienste, die dynamische Ports verwenden (wie passiver oder erweiterter passiver FTP-Modus) werden derzeit nicht unterstützt.

-   Sie können zusammen mit allen Frameworks verwendet werden, die von Azure-Websites (.NET, PHP, Java, Python, Node.js) und Azure Mobile Services (Node.js, .NET) unterstützt werden.
-   Websites und Mobile Services können auf lokale Ressourcen genau so zugreifen, als ob sich die Website oder der Mobile Service im lokalen Netzwerk befände. So kann z. B. die lokal verwendete Verbindungszeichenfolge auch in Azure verwendet werden.

Hybridverbindungen bieten auch Unternehmensadministratoren Kontrolle und Einsicht in die Unternehmensressourcen, auf die durch Hybridverbindungen zugegriffen wird. Dazu zählen folgende Möglichkeiten:

-   Anhand von Gruppenrichtlinieneinstellungen können Administratoren Hybridverbindungen im Netzwerk zulassen und auch Ressourcen bestimmen, auf die durch Hybridverbindungen zugegriffen werden kann.
-   Ereignis- und Überwachungsprotokolle im Unternehmensnetzwerk geben Einsicht in die Ressourcen, auf die von Hybridverbindungen zugegriffen wird.

## <a name="KnownIssues"></a>Unterstützte Konfigurationen

Hybridverbindungen unterstützen die folgenden Framework- und Anwendungskombinationen:

-   .NET-Framework-Zugriff auf SQL Server
-   .NET-Framework-Zugriff auf HTP/HTTPS-Dienste mit Web/Client
-   PHP-Zugriff auf SQL Server, MySQL
-   Java-Zugriff auf SQL Server, MySQL und Oracle
-   Java-Zugriff auf HTTP/HTTPS-Dienste

Wenn Hybridverbindungen für den Zugriff auf lokale SQL Server verwendet wird, beachten Sie Folgendes:

-   Benannte Instanzen von SQL Express müssen für die Verwendung von statischen Ports konfiguriert sein. Standardmäßig verwenden benannte Instanzen von SQL Express dynamische Ports.
-   Standardinstanzen von SQL Express verwenden einen statischen Port; es muss jedoch TCP aktiviert sein. TCP ist standardmäßig nicht aktiviert.
-   Wenn Clustering oder Verfügbarkeitsgruppen verwendet werden, wird der Modus „MultiSubnetFailover=true“ derzeit nicht unterstützt.
-   „ApplicationIntent=ReadOnly“ wird derzeit nicht unterstützt.
-   Als End-to-End-Autorisierungsmethode, die von der Azure-Anwendung und dem lokalen SQL Server unterstützt wird, kann SQL-Authentifizierung erforderlich sein.

## <a name="HCSecurity"></a>Sicherheit

Hybridverbindungen verwenden SAS-Autorisierung (Shared Access Signature) zum Sichern der Verbindungen von den Azure-Anwendungen und dem lokalen Hybrid Connection Manager zur Hybridverbindung. Es werden getrennte Verbindungsschlüssel für die Anwendung und den lokalen Hybrid Connection Manager erstellt. Diese Verbindungsschlüssel können unabhängig voneinander ausgetauscht oder widerrufen werden.

Hybridverbindungen stellen eine reibungslose und sichere Verteilung der Schlüssel an die Anwendungen und den lokalen Hybrid Connection Manager bereit.

Weitere Informationen finden Sie unter [Erstellen und Verwalten von Hybridverbindungen][Erstellen und Verwalten von Hybridverbindungen].

**Die Anwendungsautorisierung verläuft getrennt von der Hybridverbindung**. Jede geeignete Autorisierungsmethode kann verwendet werden. Die Autorisierungsmethode hängt davon ab, welche End-to-End-Autorisierungsmethoden in der Azure-Cloud und von den lokalen Komponenten unterstützt werden. Beispiel: Ihre Azure-Anwendung greift auf einen lokalen SQL Server zu. In diesem Szenario kann SQL-Autorisierung die unterstützte End-to-End-Autorisierungsmethode sein.

#### TCP-Ports

| **Port** | Warum                                                                                                                      |
|----------|----------------------------------------------------------------------------------------------------------------------------|
| 80       | HTTP-Port, für Zertifikatsvalidierung verwendet.                                                                           |
| 443      | HTTPS-Port                                                                                                                 |
| 5671     | Für die Verbindung mit Azure verwendet. Wenn TCP-Port 5671 nicht verfügbar ist, wird TCP-Port 443 verwendet.               |
| 9352     | Wird für Push- und Pull-Vorgänge für Daten verwendet. Wenn TCP-Port 9352 nicht verfügbar ist, wird TCP-Port 443 verwendet. |

## Weiter

-   [Erstellen und Verwalten von Hybridverbindungen][Erstellen und Verwalten von Hybridverbindungen]
-   [Verbinden einer Azure-Website mit einer lokalen Ressource][Verbinden einer Azure-Website mit einer lokalen Ressource]
-   [Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus][Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus]
-   [Azure Mobile Services und Hybridverbindungen][Azure Mobile Services und Hybridverbindungen]

## Weitere Informationen

-   [REST API für die Verwaltung von BizTalk Services unter Microsoft Azure][REST API für die Verwaltung von BizTalk Services unter Microsoft Azure]
-   [BizTalk Services: Editionsübersicht][BizTalk Services: Editionsübersicht]
-   [Erstellen eines BizTalk Service mit Azure-Verwaltungsportal][Erstellen eines BizTalk Service mit Azure-Verwaltungsportal]
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"][BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]

  [Was ist eine Hybridverbindung]: #HCOverview
  [Unterstützte Konfigurationen]: #KnownIssues
  [Sicherheit]: #HCSecurity
  [Hybridverbindungen]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
  [Erstellen und Verwalten von Hybridverbindungen]: http://azure.microsoft.com/de-de/documentation/articles/integration-hybrid-connection-create-manage
  [Verbinden einer Azure-Website mit einer lokalen Ressource]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Azure Mobile Services und Hybridverbindungen]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [REST API für die Verwaltung von BizTalk Services unter Microsoft Azure]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [BizTalk Services: Editionsübersicht]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Erstellen eines BizTalk Service mit Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
