<properties 
	pageTitle="Liste der Connectors und API-Apps | Azure" 
	description="Informieren Sie sich über die Connectors und API-Apps in Azure App Service; Microservices-Architektur" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="mandia"/>


# Liste der Connectors und API-Apps in Microsoft Azure App Service
In diesem Thema werden alle verfügbaren Connectors und API-Apps aufgelistet, die von Microsoft erstellt wurden.

Informationen zu Preisen und eine Liste der auf den einzelnen Dienstebenen verfügbaren Komponenten finden Sie unter [Azure App Service-Preise](http://azure.microsoft.com/pricing/details/app-service/).


## Standardconnectors
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten Connectors und API-Apps auf, die mit den Standardconnectors zur Verfügung stehen:

Name | Beschreibung
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Verwenden Sie diesen Connector, um einen Hadoop-Cluster auf Azure zu erstellen und verschiedene Hadoop-Aufträge zu übermitteln, wie z. B. Hive, Pig, MapReduce und Streaming MapReduce. Mit diesem Connector können Sie außerdem einen Cluster einrichten, einen Auftrag übermitteln und warten, bis der Auftrag abgeschlossen ist.
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | Mit diesem Connector können Sie Nachrichten aus Service Bus-Entitäten wie Warteschlangen und Themen senden sowie Nachrichten von Service Bus-Entitäten wie Warteschlangen und Abonnements empfangen.
[Azure Storage-Blob](app-service-logic-connector-azurestorageblob.md) | Stellt eine Verbindung zum Blobspeicher her, lädt Blobs hoch, ruft sie ab, löscht sie, listet sie im Container auf, erstellt Momentaufnahmen von Blobs, kopiert Blobs und verwendet einen Trigger zum Abrufen von Blobs.
Azure WebJobs | Stellt eine Verbindung zu WebJobs her.
[Box](app-service-logic-connector-box.md) | Stellt eine Verbindung mit Box her, kann Dateien hochladen, abrufen, löschen und auflisten sowie einen Trigger zum Abrufen von Dateien verwenden.
[Chatter](app-service-logic-connector-chatter.md) | Stellt eine Verbindung mit Chatter her und kann Nachrichten posten, suchen und einen Trigger zum Abrufen neuer Nachrichten verwenden.
[Dropbox](app-service-logic-connector-dropbox.md) | Stellt eine Verbindung mit Dropbox her und kann Dateien hochladen, abrufen, löschen, auflisten und mithilfe eines Triggers abrufen.
[Facebook](app-service-logic-connector-facebook.md) | Stellt eine Verbindung mit Facebook her und kann Nachrichten, Videos, Fotos, Angebote usw. veröffentlichen. Sie können auch Nachrichten, Kommentare, Ereignisse, Benutzerfeeds und Benutzerinformationen zu verschiedenen Bewertungen abrufen, z. B. Bücher, Spiele, Filme usw. Dieser Connector enthält außerdem einen Trigger zum Abrufen neuer Nachrichten, die auf einer Seite gepostet werden.
[Datei](app-service-logic-connector-file.md) | Ermöglicht Kunden das Senden oder Hochladen von Dateien in ein Dateisystem oder Netzwerk sowie das Empfangen oder Herunterladen von Dateien aus einem Dateisystem oder Netzwerk. Mit diesem Connector können Sie eine Verbindung mit dem lokalen Dateiserver herstellen und Dateien hochladen, abrufen, löschen, auflisten und auch Dateien mithilfe eines Triggers abrufen.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Stellt eine Verbindung mit einem FTP-/FTPS-Server her und kann Dateien hochladen, abrufen, löschen, auflisten und Dateien auch mithilfe eines Triggers abrufen.
[HTTP](app-service-logic-connector-http.md) | Der HTTP-Listener öffnet einen Endpunkt, der als HTTP-Server fungiert und auf eingehende HTTP-Anforderungen lauscht. Sie können BEREITSTELLEN, ABRUFEN, LÖSCHEN und ABLEGEN. Die HTTP-Aktion erfordert keine API-App und wird nativ innerhalb der Logik-Apps unterstützt. Sie können das HTTP- oder HTTPS-Protokoll verwenden.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Der Office 365-Connector kann über Ihr Office 365-Konto E-Mails senden und empfangen sowie Ihren Kalender und Ihre Kontakte verwalten. Sie können außerdem E-Mails senden, empfangen und abrufen, Ereignisse in Ihrem Kalender erstellen und löschen sowie Ihre Kontakte erstellen, aktualisieren, abrufen und löschen.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive ist ein Dienst zum Hosten von Dateien. Der OneDrive-Connector verbindet sich mit Ihrem persönlichen Microsoft OneDrive und kann Dateien hochladen, abrufen, löschen und auflisten sowie einen Trigger zum Abrufen von Dateien verwenden.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Stellt eine Verbindung mit dem lokalen Microsoft SharePoint-Server oder SharePoint Online her und verwaltet Dokumente sowie Listenelemente. Sie können zudem Dokumente und Listenelemente erstellen, aktualisieren, abrufen und löschen. Verschiedene Authentifizierungsmethoden wie Standardanmeldeinformationen, OAuth 2.0, Windows-Authentifizierung und formularbasierte Authentifizierung werden unterstützt.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Stellt eine Verbindung mit dem lokalen SQL Server oder einer Azure SQL-Datenbank her. Sie können Einträge in einer SQL-Datenbanktabelle erstellen, aktualisieren, abrufen und löschen.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Stellt eine Verbindung mit Yammer her. Enthält die Aktion "Nachrict bereitstellen" und einen Trigger, um neue Nachrichten abzurufen.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Stellt eine Verbindung mit einem POP3-Server her und umfasst einen Trigger zum Abrufen von E-Mails mit Anlagen.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Mit diesem Connector können Sie unterschiedliche Entitäten aus Intuit QuickBooks erstellen, aktualisieren, lesen, löschen und abfragen, beispielsweise Kunden, Artikel, Rechnungen und so weiter.
[SFTP](app-service-logic-connector-sftp.md) (SSH File Transfer Protocol)| Stellt eine Verbindung mit SFTP her und kann Dateien hochladen, abrufen, löschen, auflisten und mithilfe eines Triggers abrufen.
[SMTP](app-service-logic-connector-smtp.md) (Simple Mail Transfer Protocol) | Stellt eine Verbindung mit einem SMTP-Server her und kann E-Mails mit Anlagen senden.
[Puffer](app-service-logic-connector-slack.md) | Herstellen einer Verbindung mit Slack und Veröffentlichen von Nachrichten auf Slack-Kanälen.
[Salesforce](app-service-logic-connector-salesforce.md) | Der Salesforce-Connector verwaltet verschiedene Entitäten, z. B. Kunden, Leads, Verkaufschancen, Anfragen usw., in Ihrem Salesforce-Konto. Sie können verschiedene Entitäten auch erstellen, aktualisieren, abfragen, löschen sowie einen Upsert anwenden.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Stellt eine Verbindung mit SugarCRM Online her und kann verschiedene Modultypen erstellen, aktualisieren, lesen und löschen, beispielsweise Konten, Kontakte, Produkte und so weiter.
[Twilio](app-service-logic-connector-twilio.md) | Stellt eine Verbindung mit Twilio her und kann Nachrichten senden, abrufen, auflisten, die Nutzung auflisten, verfügbare gebührenfreie Nummern abrufen, verfügbare Mobilnummern abrufen, verfügbare lokale Nummern abrufen, eingehende Telefonnummern auflisten sowie eingehende Telefonnummern hinzufügen.
[Twitter](app-service-logic-connector-twitter.md) | Stellt eine Verbindung mit Twitter her und kann die Benutzerchronik abrufen, Tweets durchsuchen, Follower abrufen, Freunde abrufen, Benutzer suchen, die Chronik der eigenen Seite abrufen, die Chronik der Erwähnungen abrufen, Tweets an einen Benutzer posten und direkte Nachrichten senden. Der Twitter-Connector verwendet außerdem Trigger wie das Abrufen von Tweets nach Schlüsselwort, das Abrufen von Tweets nach Benutzerhandle und das Abrufen von Tweets nach Hashtag.
[Warten](app-service-logic-connector-wait.md) | Verwenden Sie diesen Connector, um die Ausführung der App zu verzögern. Die App kann für einen bestimmten Zeitraum oder bis zu einem Ereignis an einem bestimmten Zeitpunkt verzögert werden.


## Premiumconnectors
Die folgende Tabelle enthält alle verfügbaren von Microsoft erstellten Connectors und API-Apps, die in Premiumconnectors zur Verfügung stehen:

Name | Beschreibung
------------- | -------------
[AS2-Connector](app-service-logic-connector-as2.md) | Der AS2-Connector kann in der Business-to-Business-Kommunikation Nachrichten über das AS2-Transport-Protokoll empfangen und senden. Daten werden mithilfe von digitalen Zertifikaten und Verschlüsselung sicher und zuverlässig über das Internet transportiert.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Die EDIFACT-API-App empfängt und sendet Nachrichten mithilfe des EDIFACT-Protokolls in der Business-to-Business-Kommunikation.
[BizTalk X12](app-service-logic-connector-x12.md) | Die X12-API-App empfängt und sendet Nachrichten mithilfe des X12-Protokolls in der Business-to-Business-Kommunikation.
[BizTalk-Handelspartnerverwaltung](app-service-logic-connector-tpm.md) | Die Handelspartnerverwaltungs-API-App definiert und speichert Business-to-Business-Beziehungen anhand von Partnern, Vereinbarungen und Schemas sowie anhand von in Vereinbarungen verwendeten Zertifikaten. Diese Beziehungen werden mit den AS2-, EDIFACT- und X12-API-Apps durchgesetzt.
[BizTalk JSON Encoder](app-service-logic-connector-jsonencoder.md) | Ein Encoder und Decoder helfen Ihrer App bei der Interoperation zwischen JSON- und XML-Daten. Sie können damit eine angegebene JSON-Instanz in XML konvertieren und umgekehrt.
[BizTalk-Regeln](app-service-logic-use-biztalk-rules.md) | BizTalk-Regeln definieren und steuern die Struktur, den Betrieb und die Strategie einer Organisation. Geschäftsrichtlinien können ohne erneute Kompilierung und Bereitstellung der verbundenen Anwendungen aktualisiert werden.
DB2-Connector | Stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer IBM DB2-Datenbank her. Er kann Befehlen der Informix Structured Query Language Web-API- und OData-API-Vorgänge zuordnen. <br/><br/>Keine Trigger. Aktionen umfassen die Auswahl von Tabellen, das Einfügen, Aktualisieren und Löschen sowie benutzerdefinierte Anweisungen.<br/><br/>Dieser Connector enthält auch den Microsoft-Client für DRDA zum Herstellen einer Verbindung mit einem Informix-Server über ein TCP/IP-Netzwerk.
Informix | Stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer IBM Informix-Datenbank her. Er kann Befehlen der Informix Structured Query Language Web-API- und OData-API-Vorgänge zuordnen.<br/><br/>Keine Trigger. Aktionen umfassen die Auswahl von Tabellen, das Einfügen, Aktualisieren und Löschen sowie benutzerdefinierte Anweisungen.<br/><br/>Lokal oder mit VPN oder Azure kann ExpressRoute verwendet werden. Dieser Connector enthält auch einen Microsoft-Client für DRDA zum Herstellen einer Verbindung mit einem Informix-Server über ein TCP/IP-Netzwerk.
MQ | Stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einem IBM WebSphere MQ-Server, Version 8, her. Bei lokaler Verwendung können VPN- oder Azure ExpressRoute-Verbindungen verwendet werden. Der Connector enthält außerdem den Microsoft-Client für MQ.<br/><br/>Keine Trigger. Keine Aktionen.<br/><br/><strong>Hinweis</strong> Kann derzeit nicht mit Logik-Apps verwendet werden.
[Oracle-Datenbank](app-service-logic-connector-oracle.md) | Der Oracle-Datenbank-Connector stellt eine Verbindung mit der lokalen Oracle-Datenbank her und kann Einträge in einer Datenbanktabelle erstellen, aktualisieren, abrufen und löschen.
[SAP](app-service-logic-connector-sap.md) | Der SAP-Connector stellt eine Verbindung mit einem lokalen SAP-Server her, ruft RFCs, BAPIs und tRFCs auf und sendet IDOCs.
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | Ermöglicht die Interoperabilität zwischen Flatfiledaten (wie Excel und CSV) und XML-Daten. Mit dieser API-App können Sie eine Flatfile-Instanz in XML konvertieren und umgekehrt.
[BizTalk-Transformationsdienst](app-service-logic-transform-xml-documents.md) | Die BizTalk-Transformations-API-App konvertiert Daten von einem Format in ein anderes Format. Sie können auch ein vorhandenes Schema (TRFM-Datei) hochladen und die grafische Darstellung der Karte anzeigen, welche die Links zwischen Quell- und Zielschema darstellt. Zudem können Sie die "Test"-Funktion mit einem Beispieleingabe-XML-Inhalt verwenden. Verschiedene integrierte Funktionen sind ebenfalls verfügbar, einschließlich Zeichenfolgenbearbeitungen, bedingte Zuweisung, arithmetische Ausdrücke, Datum-Uhrzeit-Formatierer, Schleifen und so weiter.
[BizTalk XML Validator](app-service-logic-xml-validator.md) | Die Validator-API-App überprüft die XML-Daten anhand vordefinierter XML-Schemas. Sie können vorhandene Schemas verwenden oder Schemas auf Grundlage einer Flatfile-Instanz, JSON-Instanz oder vorhandenen Connectors generieren.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Die Extractor-API-App sucht und extrahiert Daten aus XML-Inhalt basierend auf einem bestimmten XPath.

## Connectors als Trigger
Mehrere Connectors bieten Trigger für Logik-Apps. Diese Trigger haben zwei Typen:

1. Abfragetrigger: Diese Trigger fragen den Dienst in angegebenen Abständen auf neue Daten ab. Sobald neue Daten verfügbar sind, wird eine neue Instanz der Logik-App mit den Daten als Eingabe ausgeführt. Damit die gleichen Daten nicht mehrere Male verarbeitet werden, kann der Trigger Daten bereinigen, die gelesen und an die Logik-App übergeben wurden. Beispiele für solche Connectors sind "Datei", "SQL" und "Azure Storage".
2. Push-Trigger: Diese Trigger lauschen auf den Eingang von Daten an einem Endpunkt oder das Eintreten eines Ereignisses. Dann lösen sie eine neue Instanz einer Logik-App aus. Beispiele für solche Connectors sind "HTTP-Listener" und "Twitter".

## Connectors als Aktionen
Connectors können innerhalb Ihrer Logik-App auch als Aktionen verwendet werden. Aktionen sind hilfreich für die Suche nach Daten in der Logik-App, die dann bei der Ausführung verwendet werden können. Beispielsweise kann es sein, dass Sie Daten aus einer SQL-­Datenbank für zusätzliche Informationen zu einem Kunden bei der Verarbeitung einer Bestellung nachschauen müssen. Oder Sie müssen möglicherweise Daten in ein Ziel schreiben, darin aktualisieren oder löschen. Sie können dazu die Aktionen verwenden, die von den Connectors bereitgestellt werden. Aktionen werden Vorgängen in API-Apps zugeordnet (gemäß ihren Swagger-Metadaten).

## Erstellen Sie eigene Connectors und API-Apps
[Referenz zu Connectors und API-Apps](http://aka.ms/appservicesconnectorreference)<br/> [Trigger für Azure App Service-API-Apps](../app-service-api/app-service-api-dotnet-triggers.md)<br/> [Referenz zu Logik-Apps](https://msdn.microsoft.com/library/azure/dn948510.aspx)


## Weitere Informationen zu Connectors und API-Apps

[Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)<br/>[Verwenden des Hybrid Connection Managers in Azure App Service](app-service-logic-hybrid-connection-manager.md)<br/>[Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md)

<!---HONumber=July15_HO3-->