<properties
   pageTitle="Verwendung des SQL-Connectors in Microsoft Azure App Service"
   description="Verwenden des SQL-Connectors"
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
   ms.date="08/09/2015"
   ms.author="sameerch"/>


# Microsoft SQL-Connector

Stellen Sie eine Verbindung mit einem lokalen SQL Server oder einer Azure SQL-Datenbank her, um Informationen oder Daten zu erstellen oder zu ändern. Connectors können in Logik-Apps als Teil eines "Workflows" verwendet werden, um Daten abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Wenn Sie den SQL-Connector im Workflow verwenden, können Sie eine Vielzahl von Szenarios umsetzen. Dazu zählen z. B.:

- Machen Sie einen Teil der Daten in der SQL-Datenbank mithilfe einer Web- oder mobilen Anwendung verfügbar.
- Einfügen von Daten in eine SQL-Datenbanktabelle zum Speichern. Sie können z. B. Mitarbeiterdatensätze eingeben, Aufträge aktualisieren, usw..
- Rufen Sie Daten aus SQL ab und verwenden Sie sie in einem Geschäftsprozess. Beispielsweise können Sie Kundendatensätze abrufen und sie in Salesforce übertragen.

## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine *Aktion* ist das Ergebnis des Triggers. Z. B. bei Aktualisierung eines Auftrags eine Warnung an den Verkäufer senden. Oder bei Hinzufügen eines neuen Kunden eine E-Mail zur Begrüßung an den neuen Kunden senden.

Der SQL-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Für jede Tabelle, die in den Paketeinstellungen enthalten ist \(mehr dazu weiter unten in diesem Thema\), gibt es eine Reihe von JSON-Aktionen und einen Satz von XML-Aktionen.

Der SQL-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Umfragedaten | <ul><li>In die Tabelle einfügen</li><li>Tabelle aktualisieren</li><li>In Tabelle auswählen</li><li>Aus der Tabelle löschen</li><li>Gespeicherte Prozedur aufrufen</li></ul>

## Erstellen des SQL-Connectors

Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "SQL-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
4. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Servername | Ja | Geben Sie den SQL Server-Namen ein. Geben Sie z. B. *SQLserver/sqlexpress* oder *SQLserver.mydomain.com* ein.
Port | Nein | Der Standardwert ist 1433.
Benutzername | Ja | Geben Sie einen Benutzernamen ein, mit dem Sie sich bei SQL Server anmelden können. Wenn Sie eine Verbindung mit einem lokalen Computer mit SQL Server herstellen, geben Sie Anmeldeinformationen für die SQL Server-Authentifizierung ein.
Kennwort | Ja | Geben Sie das Kennwort für den Benutzernamen ein.
Datenbankname | Ja | Geben Sie die Datenbank ein, mit der Sie eine Verbindung herstellen. Sie können z. B. *Customers* oder *dbo/orders* eingeben.
Lokal | Ja | Die Standardeinstellung ist "False". Geben Sie "False" ein, wenn Sie eine Verbindung mit einer Azure SQL-Datenbank herstellen. Geben Sie "True" ein, wenn Sie eine Verbindung zu einem lokalen SQL Server herstellen.
Service Bus-Verbindungszeichenfolge | Nein | Wenn Sie lokale eine Verbindung herstellen, geben Sie die Verbindungszeichenfolge von Service Bus Relay ein.<br/><br/>[Verwendung mit dem Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus-Preise](http://azure.microsoft.com/pricing/details/service-bus/)
Name des Partnerservers | Nein | Wenn der primäre Server nicht verfügbar ist, können Sie einen Partnerserver als Alternative oder einen Backup-Server eingeben.
Tabellen | Nein | Listen Sie die Datenbanktabellen auf, die vom Connector aktualisiert werden können. Geben Sie z. B. *OrdersTable* oder *EmployeeTable* ein. Wenn keine Tabellen eingegeben werden, können alle Tabellen verwendet werden. Gültige Tabellen und/oder gespeicherte Prozeduren sind erforderlich, um diesen Connector als Aktion zu verwenden.
Gespeicherte Prozeduren | Nein | Geben Sie eine vorhandene gespeicherte Prozedur ein, die vom Connector aufgerufen werden kann. Geben Sie z. B. *sp\_IsEmployeeEligible* oder *sp\_CalculateOrderDiscount* ein. Gültige Tabellen und/oder gespeicherte Prozeduren sind erforderlich, um diesen Connector als Aktion zu verwenden.
Abfrage zu verfügbaren Daten | Für Trigger-Support | Die SQL-Anweisung, mit der bestimmt wird, ob Daten zum Abrufen einer SQL Server-Datenbanktabelle verfügbar sind. Dies sollte einen numerischen Wert für die Anzahl der Zeilen der verfügbaren Daten zurückgeben. Beispiel: SELECT COUNT(*) from Table_name
 Abrufen der Datenabfrage | Unterstützung für Trigger | Die SQL-Anweisung zum Abrufen der SQL Server-Datenbanktabelle. Sie können eine beliebige Anzahl von SQL-Anweisungen durch ein Semikolon getrennt eingeben. Diese Anweisung wird im Hinblick auf Transaktionen ausgeführt und nur übernommen, wenn die Daten in Ihrer Logik-App sicher gespeichert sind. Beispiel: SELECT * FROM Table_name; DELETE FROM Table\_name. <br/><br/>** Hinweis**<br/>Sie müssen eine Umfrageanweisung bereitstellen, die eine Endlosschleife durch Löschen, Verschieben oder Aktualisieren der ausgewählten Daten vermeidet, um sicherzustellen, dass die gleichen Daten nicht erneut abgefragt werden.

5. Nach Abschluss des Vorgangs sehen die Paketeinstellungen etwa wie folgt aus:  
	![][1]  

6. Klicken Sie auf **Erstellen**. 


## Verwenden des Connectors als Trigger
Betrachten wir eine einfache Logik-App, die Daten aus einer SQL-Tabelle abruft, die Daten dann einer anderen Tabelle hinzufügt und anschließend aktualisiert.

Um den SQL-Connector als Trigger zu verwenden, geben Sie die Werte für die **Verfügbare Datenabfrage** und den **Abruf der Datenabfrage** ein. Die **Verfügbare Datenabfrage** wird für den eingegebenen Zeitplan ausgeführt, und bestimmt, ob Daten verfügbar sind. Da diese Abfrage nur einen skalaren Wert zurückgibt, können sie verbessert und für die regelmäßige Ausführung optimiert werden.

Der **Abruf der Datenabfrage** wird nur ausgeführt, wenn die Datenabfrage nach verfügbaren Daten angibt, dass Daten verfügbar sind. Diese Anweisung wird innerhalb einer Transaktion ausgeführt, und wird nur übernommen, wenn die extrahierten Daten in Ihrem Workflow dauerhaft gespeichert werden. Es ist wichtig, das unendliche Extrahieren der gleichen Daten zu vermeiden. Das transaktionale Merkmal dieser Ausführung kann verwendet werden, um Daten zu löschen oder zu aktualisieren, damit sie bei der nächsten Datenabfrage nicht wieder gesammelt werden.

> [AZURE.NOTE]Das Schema, das von dieser Anweisung zurückgegebenen wird, identifiziert die verfügbaren Eigenschaften im Connector. Alle Spalten müssen benannt werden.

#### Beispiel für eine Abfrage verfügbarer Daten

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### Beispiel für eine Abfrage zu Umfragedaten

	SELECT *, GetData() as 'PollTime' FROM [Order]
		WHERE OrderStatus = 'ProcessedForCollection'
		ORDER BY Id DESC;
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
		WHERE Id =
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### Den Trigger hinzufügen
1. Beim Erstellen oder Bearbeiten einer Logik-App wählen Sie den SQL-Connector als Trigger aus. Dadurch werden die verfügbaren Trigger aufgelistet: **Poll Data (JSON)** und **Poll Data (XML)**:  
	![][5]

2. Wählen Sie den Trigger **Poll Data \(JSON\)** aus, geben Sie die Häufigkeit an, und klicken Sie auf ✓:  
	![][6]

3. Der Trigger wird jetzt in der Logik-App als konfiguriert angezeigt. Die Ausgaben des Triggers werden angezeigt und können in nachfolgenden Aktionen als Eingabe verwendet werden:  
	![][7]

## Verwenden des Connectors als Aktion
Mithilfe unseres einfachen Logik-App-Szenarios, das Daten aus einer SQL-Tabelle abruft, die Daten dann einer anderen Tabelle hinzufügt und anschließend aktualisiert.

Für die Verwendung des SQL-Connectors als Aktion geben Sie den Namen der Tabellen und/oder gespeicherte Prozeduren ein, die Sie schon bei der Erstellung des SQL-Connectors eingegeben haben:

1. Fügen Sie nach dem Trigger (oder wählen Sie "Diese Logik manuell ausführen") den aus dem Katalog erstellten SQL-Connector hinzu. Wählen Sie eine der Einfügeaktionen aus, wie *Insert Into TempEmployeeDetails (JSON)*:  
	![][8]

2. Geben Sie die einzufügenden Eingabewerte des Datensatzes ein, und klicken Sie auf ✓:  
	![][9]

3. Wählen Sie aus dem Katalog denselben SQL-Connector aus, den Sie erstellt haben. Wählen Sie als Aktion die Aktualisierungsaktion für die gleiche Tabelle aus, z. B. *Update EmployeeDetails*:  
	![][11]

4. Geben Sie die Eingabewerte für die Aktualisierungsaktion ein, und klicken Sie auf ✓:  
	![][12]

Sie können die Logik-App testen, indem Sie in der abgefragten Tabelle einen neuen Datensatz hinzufügen.

## Was möglich ist und was nicht

SQL-Abfrage | Unterstützt | Nicht unterstützt
--- | --- | ---
WHERE-Klausel | <ul><li>Operatoren: AND, OR, =, \<\>, \<, \<=, \>, \>= und LIKE</li><li>Mehrere Unterbedingungen können mithilfe von ‘\(‘ und ‘\)’ kombiniert werden.</li><li>Zeichenfolgenliterale, Datetime \(in einfache Anführungszeichen eingeschlossen\), Zahlen \(dürfen nur numerische Zeichen enthalten\)</li><li>Muss unbedingt ein Binärausdruckformat haben wie \(\(Operand Operator Operand\) UND/ODER \(Operand Operator Operand\)\)**</li></ul> \| <ul><li>Operatoren: Between, IN</li><li>Alle integrierten Funktionen wie ADD\(\), MAX\(\) NOW\(\), POWER\(\) usw.</li><li>Mathematische Operatoren wie *, -, + usw.</li><li>Zeichenfolgenverkettungen unter Verwendung von +.</li><li>Alle Joins</li><li>IS NULL und IS NOT NULL</li><li>Zahlen mit nicht numerischen Zeichen, wie z. B. Hexadezimalzahlen</li></ul>Felder \(in der SELECT-Abfrage\) \|<ul><li>Gültige Spaltennamen \(durch Kommas getrennt\). Präfixe sind für Tabellennamen unzulässig \(der Connector wird stets nur auf eine Tabelle angewendet\).</li><li>Namen können mit den Escapezeichen ' \[' und '\]' versehen werden.</li></ul>\|<ul><li>Schlüsselwörter wie TOP, DISTINCT usw.</li><li>Aliase wie Straße + Ort + PLZ AS Adresse</li><li>Alle integrierten Funktionen, z. B. ADD\(\), MAX\(\) NOW\(\), POWER\(\) usw.</li><li>Mathematische Operatoren, wie z. B. *, -, + usw.</li><li>Zeichenfolgenverkettungen unter Verwendung von +</li></ul>

#### Tipps

- Es wird empfohlen, für erweiterte Abfragen eine gespeicherte Prozedur zu erstellen und mit der API zum Ausführen gespeicherter Prozeduren auszuführen.
- Wenn Sie innere Abfragen verwenden, verwenden Sie sie innerhalb gespeicherter Prozeduren.
- Zum Verknüpfen mehrerer Bedingungen können Sie die Operatoren AND und OR verwenden.

## Hybridkonfiguration \(optional\)

> [AZURE.NOTE]Dieser Schritt ist nur bei lokaler Verwendung von SQL Server hinter Ihrer Firewall erforderlich.

App Service verwendet den Hybrid-Konfigurations-Manager, um sicher eine Verbindung auf Ihr lokales System herzustellen. Wenn Ihr Connector SQL Server lokal verwendet, ist der Hybrid Connection Manager erforderlich.

Informationen finden Sie unter [Hybrid Connection Manager konfigurieren](app-service-logic-hybrid-connection-manager.md).


## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png

<!----HONumber=August15_HO7-->

<!---HONumber=August15_HO7-->