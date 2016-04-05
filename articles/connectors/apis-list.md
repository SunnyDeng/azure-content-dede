<properties
	pageTitle="Liste der von Microsoft verwalteten APIs | Microsoft Azure App Service"
	description="Vollständige Liste der von Microsoft verwalteten APIs, über die Sie Logik-Apps in Azure App Service erstellen können"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/28/2016"
	ms.author="deonhe"/>

# Liste der verwalteten APIs

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [Liste der Connectors](../app-service-logic/app-service-logic-connectors-list.md).

Informationen zu Preisen und eine Liste der auf den einzelnen Dienstebenen verfügbaren Komponenten finden Sie unter [Azure App Service-Preise](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Wenn Sie Azure-Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Logik-App testen](https://tryappservice.azure.com/?appservice=logic). Sie können sofort eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Wählen Sie ein Symbol aus, um zu erfahren, wie Sie über diese APIs schnell Apps zum Aufrufen der Dienste erstellen können. Diese APIs können zum Erstellen von Logik-Apps und/oder PowerApps verwendet werden.

|APIs||||
|-----------|-----------|-----------|-----------|
|[![API-Symbol][blobicon]<br/>**Azure Blob**][azureblobdoc]|[![API-Symbol][bingsearchicon]<br/>**Bing-Suche**][bingsearchdoc]|[![API-Symbol][boxicon]<br/>**Box**][boxDoc]|[![API-Symbol][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![API-Symbol][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![API-Symbol][facebookicon]<br/>**Facebook**][facebookdoc]|[![API-Symbol][ftpicon]<br/>**FTP**][ftpdoc]|[![API-Symbol][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![API-Symbol][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API-Symbol][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![API-Symbol][office365icon]<br/>**Office 365**<br/>**Benutzer**][office365usersdoc]|[![API-Symbol][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|
|[![API-Symbol][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API-Symbol][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![API-Symbol][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![API-Symbol][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![API-Symbol][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![API-Symbol][slackicon]<br/>**Puffer**<br/>][slackdoc]|[![API-Symbol][smtpicon]<br/>**SMTP**][smtpdoc]|[![API-Symbol][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![API-Symbol][twilioicon]<br/>**Twilio**][twiliodoc]|[![API-Symbol][twittericon]<br/>**Twitter**][twitterdoc]|[![API-Symbol][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Wenn Sie Logik-Apps mit dem Schema „2014-12-01-preview“ erstellt haben, werden Sie bemerken, dass die Enterprise Integration-APIs, z.B. für BizTalk, oben nicht aufgeführt sind. Wir wissen, dass dies wichtig ist, und wir arbeiten hart daran, diese schnellstmöglich für Sie bereitzustellen. Wir können derzeit zwar noch kein genaues Verfügbarkeitsdatum angeben, aber diese Aufgabe gehört bei uns zu den Aufgaben mit der höchsten Priorität. In der Zwischenzeit können Sie auf Ihre [v1-APIs und BizTalk-APIs über Logik-Apps zugreifen](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Vielen Dank für Ihr Verständnis. Halten Sie sich hierüber auf dem Laufenden.


### APIs können Trigger sein
Mehrere APIs umfassen Trigger, die Ihre App benachrichtigen können, wenn bestimmte Ereignisse eintreten. Die FTP-API umfasst beispielsweise den OnUpdatedFile-Trigger. Sie können eine Logik-App oder eine PowerApp erstellen, die auf diesen Trigger lauscht und immer dann eine Aktion ausführt, wenn der Trigger ausgelöst wird.

Es gibt zwei Arten von Triggern:

* Abfragetrigger: Diese Trigger fragen den Dienst in angegebenen Abständen auf neue Daten ab. Wenn neue Daten verfügbar sind, wird eine neue Instanz der App mit den Daten als Eingabe ausgeführt. Damit die gleichen Daten nicht mehrere Male verarbeitet werden, kann der Trigger Daten bereinigen, die gelesen und an die App übergeben wurden.
* Push-Trigger: Diese Trigger lauschen auf den Eingang von Daten an einem Endpunkt oder das Eintreten eines Ereignisses. Dann lösen sie eine neue Instanz der App aus. Ein Beispiel hierfür ist die Twitter-API.


### APIs können Aktionen sein
APIs können innerhalb Ihrer Apps auch als Aktionen verwendet werden. Aktionen sind hilfreich für die Suche nach Daten, die dann bei der Ausführung der App verwendet werden können. Beispielsweise kann es sein, dass Sie bei der Verarbeitung einer Bestellung Daten in einer SQL-Datenbank suchen müssen. Oder Sie müssen möglicherweise Daten in eine Zieltabelle schreiben oder in dieser aktualisieren oder löschen. Dazu können Sie die Aktionen verwenden, die von den APIs bereitgestellt werden. Aktionen sind den in den Swagger-Metadaten definierten Vorgängen zugeordnet.


[Neuigkeiten](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md) [Erste Schritte mit PowerApps](../power-apps/powerapps-get-started-azure-portal.md) [Migrate existing Logic apps to the latest schema version](connectors-schema-migration.md) (Migrieren vorhandener Logik-Apps zur neuesten Schemaversion)

<!--API Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Verbindung mit Azure-Blob zum Verwalten der Dateien in Ihrem Blobcontainer."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Für die Suche in Bing im Web und nach Bildern, Nachrichten, und Videos."
[boxDoc]: ./connectors-create-api-box.md "Stellt eine Verbindung mit Box her und kann zum Hochladen, Abrufen, Löschen, Auflisten und weitere Dateiaufgaben verwendet werden."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Verbindung mit Dynamics CRM Online und weitere Verwendung Ihrer CRM Online-Daten."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Stellt eine Verbindung mit Dropbox her und kann zum Abrufen, Löschen, Auflisten und weitere Dateiaufgaben verwendet werden."
[exceldoc]: ./connectors-create-api-excel.md "Verbindung mit Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Verbindung mit Facebook zum Veröffentlichen einer Zeitachse, zum Abrufen eines Seitenfeeds und vielem mehr."
[ftpdoc]: ./connectors-create-api-ftp.md "Stellt eine Verbindung mit einem FTP-/FTPS-Server her und ermöglicht u. a. die Ausführung verschiedener FTP-Tasks, einschließlich Hochladen, Abrufen und Löschen von Dateien."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Verbindung mit GoogleDrive und Interaktion mit Ihren Daten."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Der Office 365-Connector kann über Ihr Office 365-Konto E-Mails senden und empfangen sowie Ihren Kalender und Ihre Kontakte verwalten."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Stellt eine Verbindung mit Ihrem persönlichen Microsoft OneDrive-Ordner her und ermöglicht u. a. das Hochladen, Löschen und Auflisten von Dateien."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Stellt eine Verbindung mit Ihrem Salesforce-Konto her und dient zum Verwalten von Konten, Leads, Verkaufschancen und mehr."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Kann Nachrichten aus Service Bus-Warteschlangen und -Themen senden sowie Nachrichten aus Service Bus-Warteschlangen und -Abonnements empfangen."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Stellt eine Verbindung mit SharePoint Online her, um Dokumente und Listenelemente zu verwalten."
[slackdoc]: ./connectors-create-api-slack.md "Herstellen einer Verbindung mit Slack und Veröffentlichen von Nachrichten auf Slack-Kanälen."
[sftpdoc]: ./connectors-create-api-sftp.md "Stellt eine Verbindung mit SFTP her und kann zum Hochladen, Abrufen und Löschen von Dateien verwendet werden."
[smtpdoc]: ./connectors-create-api-smtp.md "Stellt eine Verbindung mit einem SMTP-Server her und kann E-Mails mit Anlagen senden."
[sqldoc]: ./connectors-create-api-sqlazure.md "Stellt eine Verbindung mit einer SQL Azure-Datenbank her. Sie können Einträge in einer SQL-Datenbanktabelle erstellen, aktualisieren, abrufen und löschen."
[twiliodoc]: ./connectors-create-api-twilio.md "Stellt eine Verbindung mit Twilio her und kann Nachrichten und verfügbare Nummern abrufen, eingehende Telefonnummern verwalten usw."
[twitterdoc]: ./connectors-create-api-twitter.md "Stellt eine Verbindung mit Twitter her und dient u. a. zum Abrufen von Zeitachsen und Posten von Tweets."
[yammerdoc]: ./connectors-create-api-yammer.md "Stellt eine Verbindung mit Yammer her, um Nachrichten zu posten und neue Nachrichten abzurufen."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0330_2016-->