<properties
   pageTitle="Verwenden von Connectors"
   description="Verwenden von Connectors"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/07/2015"
   ms.author="prkumar"/>

#Connectors#

> [AZURE.NOTE]Dieses Thema wird eingestellt. Unter [Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md) finden Sie alle verfügbaren integrierten Connectors und API-Apps.


Connectors sind API-Apps, mit denen Sie eine Verbindung mit Daten und Diensten herstellen können, die in der Cloud oder lokal ausgeführt werden. Connectors erleichtern das Abrufen der Daten mit einer Vielzahl von integrierten Triggern und Aktionen, die beispielsweise in Logik-Apps problemlos zugänglich sind.

Azure App Service bietet eine Reihe von Connectors, die direkt einsatzbereit sind:

##Standardconnectors##
* [Azure Service Bus-Connector]
* [Azure Storage-Blobconnector]
* Azure Webjobs-Connector
* [Box-Connector]
* [Chatter-Connector]
* [Dropbox-Connector]
* [Facebook-Connector]
* [Datei-Connector]
* [FTP-Connector]
* [HDInsight-Connector]
* [HTTP-Connector]
* [Office 365-Connector]
* [OneDrive-Connector]
* [POP3-Connector]
* [QuickBooks-Connector]
* [Salesforce-Connector]
* [SFTP-Connector]
* [Sharepoint-Connector]
* [Pufferconnector]
* [SMTP-Connector]
* [SQL-Connector]
* [SugarCRM-Connector]
* [Twilio-Connector]
* [Twitter-Connector]
* [Warten-Connector]
* [Yammer-Connector]


##Premiumconnectors und API-Apps##
* [AS2-Connector]
* [BizTalk EDIFACT]
* [BizTalk Flat File Encoder]
* [BizTalk JSON Encoder]
* [BizTalk-Transformationsdienst]
* [BizTalk-Regeln]
* [BizTalk X12]
* [BizTalk XPath Extractor]
* [BizTalk XML Validator]
* DB2-Connector
* Informix-Connector
* [Oracle-Connector]
* MQ-Connector
* [SAP-Connector]

Weitere Informationen finden Sie in der Connector-API-Referenz [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).

##Connectors und Logik-Apps##
Logik-Apps bestehen aus Triggern und Aktionen. Einige Connectors können als Trigger zum Instanziieren eines Workflows verwendet werden, der basierend auf einem Ereignis oder auf der Verfügbarkeit von Daten ausgelöst wird. Connectors werden auch als Aktionen zum Lesen und Schreiben von Daten oder zum Durchführen anderer Aktionen verwendet, die vom Connector unterstützt werden.

###Connectors als Trigger###
Mehrere Connectors bieten Trigger für Logik-Apps. Diese Trigger haben zwei Typen:

1. Abfragetrigger: Diese Trigger fragen den betreffenden Dienst in angegebenen Abständen auf neue Daten ab. Sobald neue Daten verfügbar sind, wird eine neue Instanz der Logik-App mit den Daten als Eingabe ausgeführt. Der Trigger kann zusätzliche Aufgaben ausführen, wie z. B. die Daten bereinigen, die gelesen und an die Logik-App übergeben wurden, um zu verhindern, dass die gleichen Daten mehrere Male verarbeitet werden. Beispiele für solche Connectors sind "Datei", "SQL" und "Azure Storage".
2. Push-Trigger: Diese Trigger lauschen auf den Eingang von Daten an einem Endpunkt oder das Eintreten eines Ereignisses und lösen eine neue Instanz einer Logik-App aus. Beispiele für solche Connectors sind "HTTP-Listener" und "Twitter".

###Connectors als Aktionen###
Connectors können in Ihrer Logik-App auch als Aktionen verwendet werden. Aktionen sind nützlich für die Suche nach Daten in der Logik-App, die bei der Ausführung verwendet werden. Sie müssen möglicherweise in einer SQL-Datenbank zusätzliche Informationen zu einem Kunden nachschlagen, wenn Sie eine Bestellung verarbeiten. Alternativ müssen Sie möglicherweise Daten in ein Ziel schreiben und dort aktualisieren oder löschen, wofür Sie die von den Connectors bereitgestellten Aktionen verwenden können. Aktionen werden Vorgängen in API-Apps zugeordnet (gemäß ihren Swagger-Metadaten).


<!-- Links -->

[Box-Connector]: app-service-logic-connector-box.md
[Facebook-Connector]: app-service-logic-connector-facebook.md
[Salesforce-Connector]: app-service-logic-connector-salesforce.md
[Twitter-Connector]: app-service-logic-connector-twitter.md
[SAP-Connector]: app-service-logic-connector-sap.md
[FTP-Connector]: app-service-logic-connector-ftp.md
[HTTP-Connector]: app-service-logic-connector-http.md
[Azure Storage-Blobconnector]: app-service-logic-connector-azurestorageblob.md
[Office 365-Connector]: app-service-logic-connector-office365.md
[Sharepoint-Connector]: app-service-logic-connector-sharepoint.md
[SugarCRM-Connector]: app-service-logic-connector-sugarcrm.md
[QuickBooks-Connector]: app-service-logic-connector-quickbooks.md
[Yammer-Connector]: app-service-logic-connector-yammer.md
[Twilio-Connector]: app-service-logic-connector-twilio.md
[SMTP-Connector]: app-service-logic-connector-smtp.md
[SFTP-Connector]: app-service-logic-connector-sftp.md
[POP3-Connector]: app-service-logic-connector-pop3.md
[Dropbox-Connector]: app-service-logic-connector-dropbox.md
[Chatter-Connector]: app-service-logic-connector-chatter.md
[HDInsight-Connector]: app-service-logic-connector-hdinsight.md
[Azure Service Bus-Connector]: app-service-logic-connector-azureservicebus.md
[Oracle-Connector]: app-service-logic-connector-oracle.md
[SQL-Connector]: app-service-logic-connector-sql.md
[OneDrive-Connector]: app-service-logic-connector-onedrive.md
[Datei-Connector]: app-service-logic-connector-file.md
[Pufferconnector]: app-service-logic-connector-slack.md
[BizTalk Flat File Encoder]: app-service-logic-flatfile-encoder.md
[BizTalk XPath Extractor]: app-service-logic-xpath-extract.md
[BizTalk XML Validator]: app-service-logic-xml-validator.md
[BizTalk-Regeln]: app-service-logic-use-biztalk-rules.md
[AS2-Connector]: app-service-logic-connector-as2.md
[BizTalk EDIFACT]: app-service-logic-connector-edifact.md
[BizTalk X12]: app-service-logic-connector-x12.md
[JSON Encoder]: app-service-logic-json-encoder.md
[BizTalk-Transformationsdienst]: app-service-logic-transform-xml-documents.md
[Warten-Connector]: app-service-logic-wait.md

<!---HONumber=August15_HO6-->