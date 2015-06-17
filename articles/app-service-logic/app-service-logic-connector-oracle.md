<properties 
   pageTitle="Mithilfe des Oracle-Connectors in Microsoft Azure-App-Dienst" 
   description="Verwenden des Oracle-Connectors" 
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
   ms.date="06/14/2015"
   ms.author="sutalasi"/>


# Oracle-Datenbankconnector

Verbinden Sie mit einem lokalen Oracle-Datenbank-Server erstellen und ändern die Informationen oder Daten. Connectors können in Logik Apps abrufen, Prozess oder Verschieben von Daten als Teil einer "Flow" verwendet werden. Wenn Sie in den Fluss der Oracle-Connector verwenden, können Sie eine Vielzahl von Szenarien erzielen. Dazu zählen z. B.:

- Machen Sie einen Teil der Daten in Oracle-Datenbank anhand einer mobilen Anwendung oder eine Webanwendung.
- Fügen Sie Daten in der Oracle-Datenbanktabelle für die Speicherung. Sie können z. B. Mitarbeiterdatensätze eingeben, Aktualisieren von Aufträgen und usw..
- Rufen Sie Daten aus Oracle und in einem Geschäftsprozess. Beispielsweise können Sie Kundendatensätze erhalten und die Kundendatensätze in SalesForce aufgenommen. 


## Trigger und Aktionen
*Trigger* Ereignisse, die auftreten, z. B. wenn ein Auftrag aktualisiert wird oder wenn ein neuer Kunde hinzugefügt wird, sind. Ein *Aktion* ist das Ergebnis des Triggers. Wenn ein Auftrag aktualisiert wird, senden Sie eine Warnung an den Verkäufer. Oder, wenn ein neuer Kunde hinzugefügt wird, senden Sie eine e-Mail zur Begrüßung an den neuen Kunden.

Der Oracle-Datenbank-Connector kann als ein Trigger oder eine Aktion in einer App Logik und unterstützt Daten in JSON und XML-Format verwendet werden. Für jede Tabelle, die im Paket enthaltenen Einstellungen (mehr dazu weiter unten in diesem Thema), es gibt eine Reihe von JSON-Aktionen und einen Satz von XML-Aktionen. Wenn Sie ein XML-Trigger oder eine Aktion verwenden, können Sie die [Transformieren API-Anwendung](app-service-logic-transform-xml-documents.md) zum Konvertieren von Daten in ein anderes Format von XML-Daten.

Der Oracle-Datenbank-Connector hat folgende Trigger und Aktionen zur Verfügung:

Trigger | Aktion
--- | ---
Abrufen von Daten | <ul><li>Legen Sie in der Tabelle</li><li>Tabelle aktualisieren</li><li>Wählen Sie aus der Tabelle</li><li>Löschen aus Tabelle</li><li>Aufrufen der gespeicherten Prozedur</li>


## Erstellen Sie einen Oracle-Datenbank-Connector

Ein Connector kann innerhalb einer Anwendung Logik erstellt werden oder direkt aus dem Azure Marketplace erstellt werden.

1. Wählen Sie in der Azure Startmenü **Marketplace**.
2. Wählen Sie **API-Apps** und suchen Sie nach "Connector für Oracle-Datenbank".
3. Geben Sie den Namen, die App planen, Service und andere Eigenschaften.
4. Geben Sie die folgenden Einstellungen:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Datenquelle | Ja | (Net-Dienst) Datenquellenname, die in der Datei tnsnames.ora auf dem Computer angegeben wird, in der Oracle-Client installiert ist. Informationen zu den Datenquellennamen und tnsnames.ora, finden Sie unter [Konfigurieren des Oracle-Clients](http://msdn.microsoft.com/library/dd787872.aspx).
Benutzername | Ja | Geben Sie einen Benutzernamen ein, mit dem Oracle-Server herzustellen.
Kennwort | Ja | Geben Sie das Benutzernamen, das Kennwort ein.
Service Bus-Verbindungszeichenfolge | Ja | Wenn Sie die lokale Verbindung herstellen, geben Sie die Verbindungszeichenfolge der Service Bus-Relay.<br/><br/>[Mit Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus Preise](http://azure.microsoft.com/pricing/details/service-bus/)
Tabellen | Nein | Geben Sie die Tabellen in der Datenbank, die vom Connector geändert werden dürfen. Geben Sie z. B. *OrdersTable,-Tabelle*.
Gespeicherte Prozeduren | Nein | Geben Sie die gespeicherten Prozeduren in der Datenbank, die vom Connector aufgerufen werden kann. Geben Sie z. B. *IsEmployeeEligible, CalculateOrderDiscount*.
Funktionen | Nein | Geben Sie die Funktionen in der Datenbank, die vom Connector aufgerufen werden kann. Geben Sie z. B. *IsEmployeeEligible, CalculateOrderDiscount*.
Paket-Entitäten | Nein | Geben Sie die Pakete in der Datenbank, die vom Connector aufgerufen werden kann. Geben Sie z. B. *PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill*.
Anweisung für verfügbare Daten | Nein | Geben Sie die Anweisung aus, um zu bestimmen, ob alle Daten für den Abruf verfügbar sind. Geben Sie z. B. *Wählen Sie * aus Table_name*.
Abruftyp | Nein | Geben Sie die Abfragen. Die zulässigen Werte sind "Select", "Procedure", "Function" und "Package".
Abrufanweisung | Nein | Geben Sie die Anweisung aus, um die Oracle-Datenbank abrufen. Geben Sie z. B. *Wählen Sie * aus Table_name*.
Anweisung nach Abruf | Nein | Geben Sie die Anweisung nach die Abfrage ausgeführt werden soll. Geben Sie z. B. *Löschen * aus Table_name*.

5. Nach Abschluss des Vorgangs die Paketeinstellungen etwa wie folgt aussehen: <br/> ![][1]


## Verwenden Sie den Connector als Trigger
Betrachten wir nun eine einfache Logik-app, die Daten aus einer Oracle-Tabelle abfragt, die Daten in einer anderen Tabelle hinzugefügt und aktualisiert die Daten.

### Fügen Sie den Trigger hinzu.
1. Beim Erstellen oder Bearbeiten einer app Logik, wählen Sie als Auslöser des Oracle-Connectors, die Sie erstellt haben. Dies führt die verfügbaren Trigger: **Poll-Daten (JSON)** und **Poll-Daten (XML)**: <br/> ![][5] 

2. Wählen Sie die **Abruf Daten (JSON)** auslösen, geben Sie die Häufigkeit und klicken Sie auf die ✓: <br/> ![][6]

3. Der Trigger wird nun angezeigt, wie in der Logik app konfiguriert. Die Ausgaben vorhanden sein, der der Trigger wird angezeigt und können als Eingaben in nachfolgenden Aktionen verwendet werden: <br/> ![][7]

## Verwenden Sie den Connector als Aktion
Unsere einfache Logik Anwendung, die Daten aus einer Oracle-Tabelle abruft, fügt die Daten in einer anderen Tabelle und aktualisiert die Daten.

Geben Sie den Namen der Tabellen und/oder gespeicherte Prozeduren, die Sie eingegeben haben, wenn Sie den Oracle-Connector erstellt haben, für die Verwendung der Oracle-Connector als Aktion:

1. Wählen Sie denselben Oracle-Connector aus dem Katalog als Aktion aus. Wählen Sie eine der Insert-Aktionen, wie z. B. *(Insert in TempEmployeeDetails, JSON)*: <br/> ![][8] 

2. Geben Sie die Eingabewerte des Datensatzes eingefügt werden, und klicken auf die ✓: <br/> ![][9]

3. Wählen Sie aus dem Katalog derselben Oracle-Connector, den Sie erstellt haben. Wählen Sie als Aktion, wie z. B. die Update-Aktion für die gleiche Tabelle *Update TempEmployeeDetails*: <br/> ![][11]

4. Geben Sie die Eingabewerte für die Updateaktion, und klicken Sie auf die ✓: <br/> ![][12]

Sie können die Logik-App testen, indem Sie in der abgefragten Tabelle einen neuen Datensatz hinzufügen.

## Hybridkonfiguration

> [AZURE.NOTE]Dieser Schritt ist erforderlich, nur bei Verwendung von lokalen Oracle hinter der Firewall.

App-Dienst verwendet den Hybrid-Konfigurations-Manager, um sicher auf Ihrem lokalen System zu verbinden. Wenn Sie die Verbindung mithilfe einer lokalen Oracle sind, ist der Hybrid-Verbindungs-Manager erforderlich.

Finden Sie unter [mithilfe der Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md).

## Möchten Sie den Connector
Der Connector erstellt wird, können Sie es in einen Business-Datenfluss mit einer Anwendung Logik hinzufügen. Finden Sie unter [Was Logik Apps sind?](app-service-logic-what-are-logic-apps.md).

Sie können auch die Leistung Statistiken und Steuerung der Sicherheit mit dem Connector überprüfen. Finden Sie unter [Verwalten und überwachen-API-apps und Connector](../app-service-api/app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.jpg
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.jpg



 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->