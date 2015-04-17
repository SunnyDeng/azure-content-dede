<properties 
   pageTitle="Verwenden von Connectors" 
   description="Verwenden von Connectors" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


#Verwenden von Connectors#

Connectors sind API-Apps, mit denen Sie eine Verbindung mit Diensten herstellen können, die in der Cloud oder lokal ausgeführt werden, um Daten abzurufen oder abzulegen bzw. Aktionen ausführen. Connectors können in Azure App Services in Web-, mobilen und Logik-Apps verwendet werden.

Azure App Services bietet standardmäßig mehrere Connectors, mit deren Hilfe eine Verbindung mit Diensten von Interesse herstellen können, während der Schwerpunkt Ihrer Bemühungen auf der tatsächlichen Geschäftslogik Ihrer Anwendung liegt. Darüber hinaus kann die Plattform durch Schreiben eigener API-Apps zum Herstellen von Verbindungen mit älteren Anwendungen leicht erweitert werden.

Einige der wichtigsten Features von BizTalk-Connectors sind u. a.:

1. OAuth-Sicherheit für die Verbindung mit beliebten SaaS-Diensten (Facebook, Twitter, Office 365, SalesForce).
2. Verbindungen mit Ihren lokalen Ressourcen über ein Service Bus-Relay.
3. Konnektivität für gängige Protokolle zum Empfangen und Senden von Daten.

##Connectors und Logik-Apps##
Logik-Apps bestehen aus Triggern und Aktionen. Einige Connectors dienen als Trigger, die ausgelöst werden, wenn neue Daten verfügbar sind, und zum Auslösen einer Logik-App mit diesen Daten als Eingabe. Connectors können auch als Aktion in der Mitte einer Logik-App verwendet werden, um Daten nachzuschlagen, zu schreiben oder andere Aktionen auszuführen, die vom Connector unterstützt werden.

###Connectors als Trigger###
Mehrere Connectors bieten Trigger für Logik-Apps. Diese Trigger haben zwei Typen:

1. Abfragetrigger: Dieser Trigger fragen den betreffenden Dienst in angegebenen Abständen auf neue Daten ab. Sobald neue Daten verfügbar ist, wird eine neue Instanz der Logik-App mit den Daten als Eingabe ausgeführt. Der Trigger kann zusätzliche Aufgaben ausführen, wie z. B. die Daten bereinigen, die gelesen und an die Logik-App übergeben wurden, um zu verhindern, dass die gleichen Daten mehrere Male verarbeitet werden. Beispiele für solche Connectors sind "Datei", "SQL" und "Azure Storage".
2. Pushtrigger: Diese Trigger lauschen auf den Eingang von Daten an einem Endpunkt oder das Eintreten eines Ereignisses und lösen eine neue Instanz einer Logik-App aus. Beispiele für solche Connectors sind "HTTP-Listener" und "Twitter".

###Connectors als Aktionen###
Connectors können in Ihrer Logik-App auch als Aktionen verwendet werden. Dies kann nützlich für die Suche nach Daten in der Logik-App sein, die bei der Ausführung verwendet werden. Sie müssen möglicherweise in einer SQL-Datenbank zusätzliche Informationen zu einem Kunden nachschlagen, wenn Sie eine Bestellung des Kunden in einer Logik-App verarbeiten. Alternativ müssen Sie möglicherweise Daten in ein Ziel schreiben und dort aktualisieren oder löschen, wofür Sie die von den Connectors bereitgestellten Aktionen verwenden können.

##Verwenden von Connectors##
Die folgenden Artikel bieten Beispiele, wie einige standardmäßigen Connectors verwendet werden können.

* [Verwenden des HTTP-Connectors]
* [Verwenden des FTP-Connectors]
* [Verwenden des Box-Connectors]
* [Verwenden des Azure Storage-BLOB-Connectors]
* [Verwenden des Facebook-Connectors]
* [Verwenden des Twitter-Connectors]
* [Verwenden des Salesforce-Connectors]
* [Verwenden des Office 365-Connectors]
* [Verwenden des SharePoint-Connectors]
* [Verwenden des SAP-Connectors]

Weitere Informationen finden Sie in der Connector-API-Referenz [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).


<!-- Links -->

[Verwenden des Box-Connectors]: app-service-logic-connector-box.md
[Verwenden des Facebook-Connectors]: app-service-logic-connector-facebook.md
[Verwenden des Salesforce-Connectors]: app-service-logic-connector-salesforce.md
[Verwenden des Twitter-Connectors]: app-service-logic-connector-twitter.md
[Verwenden des SAP-Connectors]: app-service-logic-connector-sap.md
[Verwenden des FTP-Connectors]: app-service-logic-connector-ftp.md
[Verwenden des HTTP-Connectors]: app-service-logic-connector-http.md
[Verwenden des Azure Storage-BLOB-Connectors]: app-service-logic-connector-azurestorageblob.md
[Verwenden des Office 365-Connectors]: app-service-logic-connector-office365.md
[Verwenden des SharePoint-Connectors]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->