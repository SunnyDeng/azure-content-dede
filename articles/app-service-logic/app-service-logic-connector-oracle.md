<properties
   pageTitle="Verwendung des Oracle-Datenbank-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Oracle-Datenbank-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Erste Schritte mit dem Oracle-Datenbank-Connector und das Hinzufügen zur Logik-App
Stellen Sie eine Verbindung mit einem lokalen Oracle-Datenbankserver her, um Informationen oder Daten zu erstellen oder zu ändern. Connectors können in Logik-Apps als Teil eines "Workflows" verwendet werden, um Daten abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Wenn Sie den Oracle-Connector im Workflow verwenden, können Sie eine Vielzahl von Szenarios umsetzen. Dazu zählen z. B.:

- Machen Sie einen Teil der Daten in der Oracle-Datenbank mithilfe einer Web- oder mobilen Anwendung verfügbar.
- Einfügen von Daten in die Oracle-Datenbanktabelle zum Speichern. Sie können z. B. Mitarbeiterdatensätze eingeben, Aufträge aktualisieren, usw..
- Rufen Sie Daten aus Oracle ab und verwenden Sie sie in einem Geschäftsprozess. Beispielsweise können Sie Kundendatensätze abrufen und sie in Salesforce übertragen.


## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine *Aktion* ist das Ergebnis des Triggers. Z. B. bei Aktualisierung eines Auftrags eine Warnung an den Verkäufer senden. Oder bei Hinzufügen eines neuen Kunden eine E-Mail zur Begrüßung an den neuen Kunden senden.

Der Oracle-Datenbank-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Für jede Tabelle, die in den Paketeinstellungen enthalten ist (mehr dazu weiter unten in diesem Thema), gibt es eine Reihe von JSON-Aktionen und einen Satz von XML-Aktionen. Wenn Sie einen XML-Trigger oder eine -Aktion verwenden, können Sie die [Transform-API-App](app-service-logic-transform-xml-documents.md) zum Konvertieren der Daten in ein XML-Datenformat verwenden.

Der Oracle-Datenbank-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Umfragedaten | <ul><li>In die Tabelle einfügen</li><li>Tabelle aktualisieren</li><li>Aus der Tabelle auswählen</li><li>Aus der Tabelle löschen</li><li>Aufrufen der gespeicherten Prozedur</li>


## Oracle-Datenbankconnector erstellen

Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus und suchen Sie nach "Connector für Oracle-Datenbank".
3. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
4. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Datenquelle | Ja | Datenquellenname (Net-Dienst), der in der Datei "tnsnames.ora" auf dem Computer angegeben wird, auf dem der Oracle-Client installiert ist. Weitere Informationen zu den Datenquellennamen und "tnsnames.ora" finden Sie unter [Konfigurieren des Oracle-Clients](http://msdn.microsoft.com/library/dd787872.aspx).
Benutzername | Ja | Geben Sie einen gültigen Benutzernamen für die Verbindung mit dem Oracle-Server ein.
Kennwort | Ja | Geben Sie das Kennwort für den Benutzernamen ein.
Service Bus-Verbindungszeichenfolge | Ja | Wenn Sie lokale eine Verbindung herstellen, geben Sie die Verbindungszeichenfolge von Service Bus Relay ein.<br/><br/>[Verwendung mit dem Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus-Preise](http://azure.microsoft.com/pricing/details/service-bus/)
Tabellen | Nein | Geben Sie die Tabellen in der Datenbank ein, die vom Connector geändert werden dürfen. Geben Sie z. B. *OrdersTable,EmployeeTable* ein.
Gespeicherte Prozeduren | Nein | Geben Sie die gespeicherten Prozeduren in der Datenbank ein, die vom Connector aufgerufen werden können. Geben Sie z. B. *IsEmployeeEligible,CalculateOrderDiscount* ein.
Funktionen | Nein | Geben Sie die Funktionen in der Datenbank ein, die vom Connector aufgerufen werden können. Geben Sie z. B. *IsEmployeeEligible,CalculateOrderDiscount* ein.
Paket-Entitäten | Nein | Geben Sie die Pakete in der Datenbank ein, die vom Connector aufgerufen werden können. Geben Sie z. B. *PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill* ein.
Anweisung für verfügbare Daten | Nein | Geben Sie die Anweisung ein, um festzustellen, ob Daten für den Abruf verfügbar sind. Geben Sie z. B. *SELECT * from table\_name* ein.
Abruftyp | Nein | Geben Sie den Abruftyp ein. Zulässige Werte sind "Auswählen", "Prozedur", "Funktion" und "Paket".
Abrufanweisung | Nein | Geben Sie die Anweisung zur Abfrage der Oracle-Datenbank ein. Geben Sie z. B. *SELECT * from table\_name* ein.
Anweisung nach Abruf | Nein | Geben Sie die Anweisung ein, die nach der Abfrage ausgeführt werden soll. Geben Sie z. B. *DELETE * from table\_name* ein.

5. Nach Abschluss des Vorgangs sehen die Paketeinstellungen etwa wie folgt aus: <br/> ![][1]


## Verwenden des Connectors als Trigger
Betrachten wir eine einfache Logik-App, die Daten aus einer Oracle-Tabelle abruft, die Daten dann einer anderen Tabelle hinzufügt und anschließend aktualisiert.

### Den Trigger hinzufügen
1. Beim Erstellen oder Bearbeiten einer Logik-App wählen Sie den Oracle-Connector als Trigger aus. Dadurch werden die verfügbaren Trigger aufgelistet: **Poll Data (JSON)** und **Poll Data (XML)**: <br/>![][5]

2. Wählen Sie den Trigger **Poll Data (JSON)** aus, geben Sie die Häufigkeit an, und klicken Sie auf ✓: <br/>![][6]

3. Der Trigger wird jetzt in der Logik-App als konfiguriert angezeigt. Die Ausgaben des Triggers werden angezeigt und können in nachfolgenden Aktionen als Eingabe verwendet werden: <br/> ![][7]

## Verwenden des Connectors als Aktion
Mithilfe unserer einfachen Logik-App, die Daten aus einer Oracle-Tabelle abruft, die Daten dann einer anderen Tabelle hinzufügt und anschließend aktualisiert.

Für die Verwendung des Oracle-Connectors als Aktion geben Sie den Namen der Tabellen und/oder gespeicherte Prozeduren ein, die Sie schon bei der Erstellung des Oracle Connectors eingegeben haben:

1. Wählen Sie denselben Oracle-Connector aus dem Katalog als Aktion aus. Wählen Sie eine der Einfügeaktionen aus, wie *Insert Into TempEmployeeDetails (JSON)*: <br/>![][8]

2. Geben Sie die einzufügenden Eingabewerte des Datensatzes ein, und klicken Sie auf ✓: <br/> ![][9]

3. Wählen Sie aus dem Katalog denselben Oracle-Connector aus, den Sie erstellt haben. Wählen Sie als Aktion die Aktualisierungsaktion für die gleiche Tabelle aus, z. B. *Update TempEmployeeDetails*: <br/> ![][11]

4. Geben Sie die Eingabewerte für die Aktualisierungsaktion ein, und klicken Sie auf ✓: <br/> ![][12]

Sie können die Logik-App testen, indem Sie in der abgefragten Tabelle einen neuen Datensatz hinzufügen.

## Hybridkonfiguration

> [AZURE.NOTE]Dieser Schritt ist nur bei lokaler Verwendung von Oracle hinter Ihrer Firewall erforderlich.

App Service verwendet den Hybrid-Konfigurations-Manager, um sicher eine Verbindung auf Ihr lokales System herzustellen. Wenn Ihr Connector Oracle lokal verwendet, ist der Hybrid Connection Manager erforderlich.

Informationen finden Sie unter [Hybrid Connection Manager konfigurieren](app-service-logic-hybrid-connection-manager.md).

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png

<!---HONumber=AcomDC_1203_2015-->