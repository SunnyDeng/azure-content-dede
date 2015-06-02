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
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Liste der Connectors und API-Apps in Microsoft Azure App Service
In diesem Thema werden alle verfügbaren Connectors und API-Apps aufgelistet, die von Microsoft erstellt wurden.

Informationen zu Preisen und eine Liste der auf den einzelnen Dienstebenen verfügbaren Komponenten finden Sie unter [Azure App Service-Preise](http://azure.microsoft.com/pricing/details/app-service/).


## Standardconnectors
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten Connectors und API-Apps auf, die mit den Standardconnectors zur Verfügung stehen:

<table border="1">
<tr bgcolor="FAF9F9">
        <th>Name</th>
        <th>Beschreibung</th>
</tr>

<tr>
<td colspan="2"><strong>App und Data Services-Connectors</strong><br/><br/>Unter <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">App und Data Services-Connectors</a> finden Sie ausführlichere Informationen zu diesen Connectors.</td>
</tr>

<tr>
<td>Azure HDInsight</td>
<td>Azure HDInsight stellt Apache Hadoop-Cluster in der Cloud bereit und bietet ein Softwareframework, das zur Verwaltung, Analyse und Berichterstellung für Big Data entwickelt wurde. Dieser Connector erstellt einen Hadoop-Cluster auf Azure und übermittelt verschiedene Hadoop-Aufträge, wie z. B. Hive, Pig, MapReduce und Streaming MapReduce. Mit diesem Connector können Sie einen Cluster einrichten, einen Auftrag übermitteln und warten, bis der Auftrag abgeschlossen ist.</td>
</tr>

<tr>
<td>Azure-Servicebus</td>
<td>Azure Service Bus ist ein generisches, cloudbasiertes Messaging-System, mit dem man praktisch alles verbinden kann. Er ermöglicht den Austausch von Nachrichten in locker gekoppelter Weise für verbesserte Skalierbarkeit und Zuverlässigkeit. Mit diesem Connector können Sie Nachrichten aus Service Bus-Entitäten wie Warteschlangen und Themen senden sowie Nachrichten von Service Bus-Entitäten wie Warteschlangen und Abonnements empfangen.</td>
</tr>

<tr>
<td>Azure Storage-Blob</td>
<td>Das Azure Storage-Blob speichert große Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt zugegriffen werden kann. Mit diesem Connector können Sie Blobspeicher verbinden und Blobs hochladen, abrufen, löschen, im Container auflisten, Momentaufnahmen von Blobs erstellen, Blobs kopieren und einen Trigger zum Abrufen von Blobs verwenden.</td>
</tr>

<tr>
<td>Box</td>
<td>Box ist ein Dienst für die Dateifreigabe. Der Box-Connector verbindet sich mit Box und kann Dateien hochladen, abrufen, löschen und auflisten sowie einen Trigger zum Abrufen von Dateien verwenden.</td>
</tr>

<tr>
<td>Dropbox</td>
<td>Dropbox ist ein Dienst zum Hosten von Dateien. Mit diesem Connector können Sie sich mit Dropbox verbinden und Dateien hochladen, abrufen, löschen und auflisten sowie einen Trigger zum Abrufen von Dateien verwenden.</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>Der Office 365-Connector kann über Ihr Office 365-Konto E-Mails senden und empfangen sowie Ihren Kalender und Ihre Kontakte verwalten. Sie können außerdem E-Mails senden, empfangen und abrufen, Ereignisse in Ihrem Kalender erstellen und löschen sowie Ihre Kontakte erstellen, aktualisieren, abrufen und löschen.</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>Microsoft OneDrive ist ein Dienst zum Hosten von Dateien. Der OneDrive-Connector verbindet sich mit Ihrem persönlichen Microsoft OneDrive und kann Dateien hochladen, abrufen, löschen und auflisten sowie einen Trigger zum Abrufen von Dateien verwenden.</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>Der Microsoft SharePoint-Connector stellt eine Verbindung mit dem lokalen Microsoft SharePoint-Server oder SharePoint Online her und verwaltet Dokumente sowie Listenelemente. Sie können zudem Dokumente und Listenelemente erstellen, aktualisieren, abrufen und löschen. Verschiedene Authentifizierungsmethoden wie Standardanmeldeinformationen, OAuth 2.0, Windows-Authentifizierung und formularbasierte Authentifizierung werden unterstützt.</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>Der Microsoft SQL-Connector stellt eine Verbindung mit der lokalen SQL Server- oder Azure SQL-Datenbank her. Sie können Einträge in einer SQL-Datenbanktabelle erstellen, aktualisieren, abrufen und löschen.</td>
</tr>

<tr>
<td colspan="2"><strong>Connectors für soziale Netzwerke</strong><br/><br/>Unter <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">Connectors für soziale Netzwerke</a> finden Sie ausführlichere Informationen zu diesen Connectors.</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter ist ein soziales Unternehmensnetzwerk. Der Chatter-Connector verbindet sich mit Chatter und kann Nachrichten posten, suchen und einen Trigger zum Abrufen neuer Nachrichten verwenden.</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook ist ein Dienst für soziale Netzwerke. Der Facebook-Connector stellt eine Verbindung mit Facebook her und kann Nachrichten, Videos, Fotos, Angebote usw. veröffentlichen. Sie können auch Nachrichten, Kommentare, Ereignisse, Benutzerfeeds und Benutzerinformationen zu verschiedenen Bewertungen abrufen, einschließlich Bücher, Spiele, Filme und so weiter. Dieser Connector enthält außerdem einen Trigger zum Abrufen neuer Nachrichten, die auf einer Seite gepostet werden.</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer ist ein soziales Unternehmensnetzwerk. Der Yammer-Connector verbindet sich mit Yammer und enthält eine Aktion zum Posten von Nachrichten sowie einen Trigger zum Abrufen neuer Nachrichten.</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio ist ein kommunikationsorientierter SaaS-Dienst. Der Twilio-Connector stellt eine Verbindung mit Twilio her und kann Nachrichten senden, abrufen, auflisten, die Nutzung auflisten, verfügbare gebührenfreie Nummern abrufen, verfügbare Mobilnummern abrufen, verfügbare lokale Nummern abrufen, eingehende Telefonnummern auflisten sowie eingehende Telefonnummern hinzufügen.</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter ist ein Onlinedienst für soziale Netzwerke, mit dem Benutzer kurze Nachrichten von bis zu 140 Zeichen senden und lesen können. Der Twitter-Connector stellt eine Verbindung mit Twitter her und kann die Benutzerchronik abrufen, Tweets durchsuchen, Follower abrufen, Freunde abrufen, Benutzer suchen, die Chronik der eigenen Seite abrufen, die Chronik der Erwähnungen abrufen, Tweets an einen Benutzer posten und direkte Nachrichten senden. Der Twitter-Connector verwendet außerdem Trigger wie das Abrufen von Tweets nach Schlüsselwort, das Abrufen von Tweets nach Benutzerhandle und das Abrufen von Tweets nach Hashtag.</td>
</tr>

<tr>
<td colspan="2"><strong>Protokollconnectors</strong><br/><br/>Unter <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">Protokollconnectors</a> finden Sie ausführlichere Informationen zu diesen Connectors.</td>
</tr>

<tr>
<td>Datei</td>
<td>Der Datei-Connector ermöglicht Kunden das Senden oder Hochladen von Dateien in ein Dateisystem oder Netzwerk sowie das Empfangen oder Herunterladen von Dateien aus einem Dateisystem oder Netzwerk. Mit diesem Connector können Sie eine Verbindung mit dem lokalen Dateiserver herstellen und Dateien hochladen, abrufen, löschen, auflisten und auch Dateien mithilfe eines Triggers abrufen.</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>FTP (File Transfer Protocol) ist ein verbreitetes Netzwerkprotokoll zum Übertragen von Dateien von einem Host zu einem anderen. Der FTP-Connector kann Dateien hochladen, abrufen, löschen, auflisten und Dateien auch mithilfe eines Triggers abrufen.</td>
</tr>

<tr>
<td>HTTP-Listener</td>
<td>Der HTTP-Listener öffnet einen Endpunkt, der als HTTP-Server fungiert und auf eingehende HTTP-Anforderungen lauscht.</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3 (Post Office Protocol) ist das Protokoll, das von einem E-Mail-Client zum Abrufen von E-Mail von einem Mailserver verwendet wird. Der POP3-Connector stellt eine Verbindung mit einem POP3-Server her und umfasst einen Trigger zum Abrufen von E-Mails mit Anlagen.</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP (SSH File Transfer Protocol) ist ein weit verbreitetes Protokoll, das zum sicheren Übertragen von Dateien verwendet wird. Der SFTP-Connector kann Dateien hochladen, abrufen, löschen, auflisten und Dateien mithilfe eines Triggers abrufen.</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP (Simple Mail Transfer Protocol) ist das Protokoll, das zwischen einem E-Mail-Client und einem E-Mail-Server zum Senden von E-Mails verwendet wird. Der SMTP-Connector stellt eine Verbindung mit einen SMTP-Server her und sendet E-Mails mit Anlagen.</td>
</tr>

<tr>
<td colspan="2"><strong>Enterprise-Connectors</strong><br/><br/>Unter <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">Enterprise-Connectors</a> finden Sie ausführlichere Informationen zu diesen Connectors.</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo entwickelt Marketingautomatisierungssoftware, die eingehendes Marketing, soziales Marketing, CRM und andere ähnliche Dienste bietet. Der Marketo-Connector stellt eine Verbindung mit Marketo her und führt verschiedene Aktionen aus wie Leads erstellen/aktualisieren, Leadänderungen abrufen, Kampagne planen, Kampagne anfordern, Leads abrufen, Kampagnen-/Listeninformationen abrufen, Leads zu Liste hinzufügen und Leads aus Liste entfernen.</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>Mit dem QuickBooks-Connector können Sie unterschiedliche Entitäten aus Intuit QuickBooks erstellen, aktualisieren, lesen, löschen und abfragen, beispielsweise Kunden, Artikel, Rechnungen und so weiter.</td>
</tr>

<tr>
<td>Salesforce</td>
<td>Der Salesforce-Connector verwaltet verschiedene Entitäten wie z. B. Kunden, Leads, Verkaufschancen, Fälle usw. in Ihrem Salesforce-Konto. Sie können verschiedene Entitäten auch erstellen, aktualisieren, abfragen, löschen sowie einen Upsert anwenden.</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>Der SugarCRM-Connector stellt eine Verbindung mit SugarCRM Online her und kann verschiedene Modultypen erstellen, aktualisieren, lesen und löschen, beispielsweise Konten, Kontakte, Produkte und so weiter.</td>
</tr>

</table>


## BizTalk-EAI-API-Apps
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten API-Apps auf, die mit den BizTalk-EAI-Diensten zur Verfügung stehen:

Name  | Description
------------- | -------------
BizTalk Flat File Encoder | Ermöglicht die Interoperabilität zwischen Flatfiledaten (wie Excel und CSV) und XML-Daten. Mit dieser API-App können Sie eine Flatfile-Instanz in XML konvertieren und umgekehrt.
BizTalk-Transformationsdienst | Die BizTalk-Transformations-API-App konvertiert Daten von einem Format in ein anderes Format. Sie können auch ein vorhandenes Schema (TRFM-Datei) hochladen und die grafische Darstellung der Karte anzeigen, welche die Links zwischen Quell- und Zielschema darstellt. Zudem können Sie die "Test"-Funktion mit einem Beispieleingabe-XML-Inhalt verwenden. Verschiedene integrierte Funktionen sind ebenfalls verfügbar, einschließlich Zeichenfolgenbearbeitungen, bedingte Zuweisung, arithmetische Ausdrücke, Datum-Uhrzeit-Formatierer, Schleifen und so weiter.
BizTalk XML Validator | Die Validator-API-App überprüft die XML-Daten anhand vordefinierter XML-Schemas. Sie können vorhandene Schemas verwenden oder Schemas auf Grundlage einer Flatfile-Instanz, JSON-Instanz oder vorhandenen Connectors generieren.
BizTalk XPath Extractor | Die Extractor-API-App sucht und extrahiert Daten aus XML-Inhalt basierend auf einem bestimmten XPath.

Weitere Informationen und Details zu diesen API-Apps finden Sie unter [BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md).


## BizTalk-EDI-Connectors und API-Apps
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten Connectors und API-Apps auf, die mit den BizTalk-EDI-Diensten zur Verfügung stehen:

Name  | Description
------------- | -------------
AS2-Connector | Der AS2-Connector kann in der Business-to-Business-Kommunikation Nachrichten über das AS2-Transport-Protokoll empfangen und senden. Daten werden mithilfe von digitalen Zertifikaten und Verschlüsselung sicher und zuverlässig über das Internet transportiert.
BizTalk EDIFACT | Die EDIFACT-API-App empfängt und sendet Nachrichten mithilfe des EDIFACT-Protokolls in der Business-to-Business-Kommunikation.
BizTalk X12 | Die X12-API-App empfängt und sendet Nachrichten mithilfe des X12-Protokolls in der Business-to-Business-Kommunikation.
BizTalk-Handelspartnerverwaltung | Die Handelspartnerverwaltungs-API-App definiert und speichert Business-to-Business-Beziehungen anhand von Partnern, Vereinbarungen und Schemas sowie anhand von in Vereinbarungen verwendeten Zertifikaten. Diese Beziehungen werden durch die AS2-, EDIFACT- und X12-API-Apps durchgesetzt.

Weitere Informationen und Details zu diesen API-Apps finden Sie unter [Business-to-Business-Connectors](app-service-logic-b2b-connectors.md).


## API-App für BizTalk-Regeln
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten API-Apps auf, die in BizTalk-Regeln zur Verfügung stehen:

Name  | Description
------------- | -------------
BizTalk-Regeln | BizTalk-Regeln definieren und steuern die Struktur, den Betrieb und die Strategie einer Organisation. Geschäftsrichtlinien können ohne erneute Kompilierung und Bereitstellung der verbundenen Anwendungen aktualisiert werden.

Weitere Informationen und Details zu dieser API-App finden Sie unter [BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md).


## Premiumconnectors
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten Connectors und API-Apps auf, die in Premiumconnectors zur Verfügung stehen:

Name  | Description
------------- | -------------
DB2-Connector | Der DB2-Connector stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer IBM DB2-Datenbank her. Er kann Befehlen der Informix Structured Query Language Web-API- und OData-API-Vorgänge zuordnen. Zu den Aktionen gehören Tabellenauswahl, Einfügen, Aktualisieren und Löschen sowie benutzerdefinierte Anweisungen.<br/><br/>[App und Data Services-Connectors](app-service-logic-data-connectors.md)
Informix | Der Informix-Connector stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer lokalen IBM Informix-Datenbank her. Er kann Befehlen der Informix Structured Query Language Web-API- und OData-API-Vorgänge zuordnen. Zu den Aktionen gehören Tabellenauswahl, Einfügen, Aktualisieren und Löschen sowie benutzerdefinierte Anweisungen.<br/><br/>[App und Data Services-Connectors](app-service-logic-data-connectors.md)
MQ | Der MQ-Connector stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer IBM WebSphere MQ-Server her.<br/><br/><strong>Hinweis</strong> Kann derzeit nicht mit Logik-Apps verwendet werden.<br/><br/>[App und Data Services-Connectors](app-service-logic-data-connectors.md)
Oracle-Datenbank | Der Oracle-Datenbank-Connector stellt eine Verbindung mit der lokalen Oracle-Datenbank her und kann Einträge in einer Datenbanktabelle erstellen, aktualisieren, abrufen und löschen. <br/><br/>[App und Data Services-Connectors](app-service-logic-data-connectors.md)
SAP | Der SAP-Connector stellt eine Verbindung mit einem lokalen SAP-Server her, ruft RFCs, BAPIs und tRFCs auf und sendet IDOCs.<br/><br/>[Enterprise-Connectors](app-service-logic-enterprise-connectors.md)


## Erstellen Sie eigene Connectors und API-Apps
Informationen hierzu finden Sie unter [Erstellen von Connectors mit REST-APIs](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## Weitere Connectors

[BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md)<br/> [Enterprise-Connectors](app-service-logic-enterprise-connectors.md)<br/> [Business-to-Business-Connectors](app-service-logic-b2b-connectors.md)<br/> [Connectors für soziale Netzwerke](app-service-logic-social-connectors.md)<br/> [Protokollconnectors](app-service-logic-protocol-connectors.md)<br/> [App und Data Services-Connectors](app-service-logic-data-connectors.md)<br/><br/> [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->