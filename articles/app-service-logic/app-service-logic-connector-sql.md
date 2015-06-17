<properties 
   pageTitle="Mithilfe des SQL-Connectors in Microsoft Azure-App-Dienst" 
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
   ms.date="06/14/2015"
   ms.author="sutalasi"/>


# Microsoft SQL-Connector

Verbinden Sie mit einer lokalen SQL Server oder einer Azure SQL-Datenbank zum Erstellen und ändern Sie die Informationen oder Daten. Connectors können in Logik Apps abrufen, Prozess oder Verschieben von Daten als Teil einer "Flow" verwendet werden. Bei Verwendung von SQL-Connector in den Fluss erzielen Sie eine Vielzahl von Szenarien. Dazu zählen z. B.:

- Machen Sie einen Teil der Daten in der SQL-Datenbank mit einer mobilen Anwendung oder eine Webanwendung. 
- Einfügen von Daten in eine SQL-Datenbanktabelle für die Speicherung. Sie können z. B. Mitarbeiterdatensätze eingeben, Aktualisieren von Aufträgen und usw..
- Abrufen von Daten aus SQL, und verwenden Sie es in einem Geschäftsprozess. Beispielsweise können Sie Kundendatensätze erhalten und die Kundendatensätze in SalesForce aufgenommen. 

## Trigger und Aktionen
*Trigger* Ereignisse, die auftreten, z. B. wenn ein Auftrag aktualisiert wird oder wenn ein neuer Kunde hinzugefügt wird, sind. Ein *Aktion* ist das Ergebnis des Triggers. Wenn ein Auftrag aktualisiert wird, senden Sie eine Warnung an den Verkäufer. Oder, wenn ein neuer Kunde hinzugefügt wird, senden Sie eine e-Mail zur Begrüßung an den neuen Kunden.

SQL-Connector kann als ein Trigger oder eine Aktion in einer App Logik und unterstützt Daten in JSON und XML-Format verwendet werden. Für jede Tabelle, die im Paket enthaltenen Einstellungen (mehr dazu weiter unten in diesem Thema), es gibt eine Reihe von JSON-Aktionen und einen Satz von XML-Aktionen.

SQL-Connector hat folgende Trigger und Aktionen zur Verfügung:

Trigger | Aktion
--- | ---
Abrufen von Daten | <ul><li>Legen Sie in der Tabelle</li><li>Tabelle aktualisieren</li><li>Wählen Sie aus der Tabelle</li><li>Löschen aus Tabelle</li><li>Aufrufen der gespeicherten Prozedur</li>

## Erstellen von SQL-Connector

Ein Connector kann innerhalb einer Anwendung Logik erstellt werden oder direkt aus dem Azure Marketplace erstellt werden.

1. Wählen Sie in der Azure Startmenü **Marketplace**.
2. Wählen Sie **API-Apps** und suchen Sie nach "SQL-Connector".
3. Geben Sie den Namen, die App planen, Service und andere Eigenschaften.
4. Geben Sie die folgenden Einstellungen:

	Name | Erforderlich | Beschreibung
--- | --- | ---
Servername | Ja | Geben Sie den SQL Server-Namen. Geben Sie z. B. *SQLserver/Sqlexpress* oder *SQLserver.mydomain.com*.
Port | Nein | Der Standardwert ist 1433.
Benutzername | Ja | Geben Sie einen Benutzernamen ein, der sich anmelden, kann in den SQL-Server. Geben Sie "Domäne\\Benutzername", wenn die Verbindung mit einer lokalen SQL Server. 
Kennwort | Ja | Geben Sie das Benutzernamen, das Kennwort ein.
Datenbankname | Ja | Geben Sie die Datenbank, die Sie verbinden. Sie können z. B. eingeben *Kunden* oder *Dbo/Bestellungen*.
Lokal | Ja | Der Standardwert lautet False. Geben Sie False, wenn eine Verbindung mit einer Azure SQL-Datenbank herstellen. Geben Sie True, wenn eine Verbindung zu einer lokalen SQL Server. 
Service Bus-Verbindungszeichenfolge | Nein | Wenn Sie die lokale Verbindung herstellen, geben Sie die Verbindungszeichenfolge der Service Bus-Relay.<br/><br/>[Mit Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus Preise](http://azure.microsoft.com/pricing/details/service-bus/)
Name des Partnerservers | Nein | Wenn der primäre Server nicht verfügbar ist, können Sie einem Partnerserver als Alternative oder backup-Server eingeben. 
Tabellen | Nein | Listen Sie die Datenbanktabellen, die vom Connector aktualisiert werden können. Geben Sie z. B. *OrdersTable* oder *-Tabelle*. Wenn keine Tabellen eingegeben werden, können alle Tabellen verwendet werden. Gültige Tabellen und/oder gespeicherte Prozeduren sind auf diesen Connector als Aktion erforderlich. 
Gespeicherte Prozeduren | Nein | Geben Sie eine vorhandene gespeicherte Prozedur, die vom Connector aufgerufen werden kann. Geben Sie z. B. *Sp_IsEmployeeEligible* oder *Sp_CalculateOrderDiscount*. Gültige Tabellen und/oder gespeicherte Prozeduren sind auf diesen Connector als Aktion erforderlich. 
Abfrage zu verfügbaren Daten | Für die Unterstützung für trigger | SQL-Anweisung, um festzustellen, ob alle Daten für das Abrufen einer SQL Server-Datenbanktabelle verfügbar ist. Dies sollte einen numerischen Wert für die Anzahl der Zeilen der verfügbaren Daten zurückgeben. Beispiel: SELECT COUNT(*) from Table_name. 
Abfrage zu Umfragedaten | Für die Unterstützung für trigger | Die SQL-Anweisung zum Abrufen der SQL Server-Datenbanktabelle. Sie können eine beliebige Anzahl von SQL-Anweisungen, die durch Semikolons getrennt eingeben. Diese Anweisung ist im Hinblick auf Transaktionen ausgeführt und nur übernommen werden, wenn die Daten in Ihrer Anwendung Logik sicher gespeichert werden. Beispiel: SELECT * aus Table_name; DELETE FROM Table_name. <br/><br/>** Hinweis **<br/>müssen Sie eine Umfrage-Anweisung, die eine Endlosschleife durch Löschen vermeidet bereitstellen, verschieben oder aktualisieren die ausgewählte Daten, um sicherzustellen, dass diese Daten ist nicht erneut abgefragt. 

5. Nach Abschluss des Vorgangs die Paketeinstellungen etwa wie folgt aussehen: <br/> ![][1]

## Verwenden Sie den Connector als Trigger
Betrachten wir nun eine einfache Logik-app, die Daten aus einer SQL-Tabelle abfragt, die Daten in einer anderen Tabelle hinzugefügt und aktualisiert die Daten.

Um den SQL-Connector als Trigger verwenden möchten, geben Sie die **verfügbaren Datenabfrage** und **Abruf Datenabfrage** Werte. **Verfügbaren Datenabfrage** für den eingegebenen Zeitplan ausgeführt wird, und bestimmt, ob alle Daten verfügbar sind. Da diese Abfrage nur einen skalaren Wert zurückgibt, können Sie optimiert und für die regelmäßige Ausführung optimiert werden.

**Abruf Datenabfrage** wird nur ausgeführt, wenn die Abfrage zur Verfügung gibt an, dass die Daten verfügbar sind. Diese Anweisung innerhalb einer Transaktion ausgeführt wird, und wird nur übernommen, wenn die extrahierten Daten in Ihrem Workflow dauerhaft gespeichert werden. Es ist wichtig, um zu vermeiden, unendlich wieder die gleichen Daten zu extrahieren. Die grundlegenden Merkmale dieser Ausführung kann verwendet werden, löschen oder aktualisieren Sie die Daten, um sicherzustellen, dass es ist nicht das nächste Mal gesammelt, die Daten abgefragt werden.

> [AZURE.NOTE]Das Schema, das von dieser Anweisung zurückgegebenen identifiziert die verfügbaren Eigenschaften in den Connector. Alle Spalten müssen benannt werden.

#### Beispiel für Daten verfügbaren Abfrage

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### Daten-Abfragebeispiel abrufen

	SELECT *, GetData() as 'PollTime' FROM [Order] 
		WHERE OrderStatus = 'ProcessedForCollection' 
		ORDER BY Id DESC; 
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk' 
		WHERE Id = 
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### Fügen Sie den Trigger hinzu.
1. Beim Erstellen oder Bearbeiten einer app Logik, wählen Sie als Auslöser SQL-Connectors, die Sie erstellt haben. Dies führt die verfügbaren Trigger: **Poll-Daten (JSON)** und **Poll-Daten (XML)**: <br/> ![][5] 

2. Wählen Sie die **Abruf Daten (JSON)** auslösen, geben Sie die Häufigkeit und klicken Sie auf die ✓: <br/> ![][6]

3. Der Trigger wird nun angezeigt, wie in der Logik app konfiguriert. Die Ausgaben des Triggers vorhanden sein werden angezeigt und können als Eingaben in nachfolgenden Aktionen verwendet werden: <br/> ![][7]

## Verwenden Sie den Connector als Aktion
Unsere einfache Logik Anwendung, die Daten aus einer SQL-Tabelle abruft, fügt die Daten in einer anderen Tabelle und aktualisiert die Daten.

Geben Sie den Namen der Tabellen und/oder gespeicherte Prozeduren, die Sie eingegeben haben, wenn Sie den SQL-Connector erstellt haben, für die Verwendung der SQL-Connector als Aktion:

1. Nachdem die Trigger (oder wählen Sie "Diese Logik manuell ausführen"), SQL-Connectors, die Sie erstellt, aus dem Katalog haben hinzufügen. Wählen Sie eine der Insert-Aktionen, wie z. B. *(Insert in TempEmployeeDetails, JSON)*: <br/> ![][8] 

2. Geben Sie die Eingabewerte des Datensatzes eingefügt werden, und klicken auf die ✓: <br/> ![][9]

3. Wählen Sie aus dem Katalog erstellten SQL-Connector. Wählen Sie als Aktion, wie z. B. die Update-Aktion für die gleiche Tabelle *Update EmployeeDetails*: <br/> ![][11]

4. Geben Sie die Eingabewerte für die Updateaktion, und klicken Sie auf die ✓: <br/> ![][12]

Sie können die Logik-App testen, indem Sie in der abgefragten Tabelle einen neuen Datensatz hinzufügen.

## Hybridkonfiguration (optional)

> [AZURE.NOTE]Dieser Schritt ist erforderlich, nur bei Verwendung von lokalen SQL-Server hinter der Firewall.

App-Dienst verwendet den Hybrid-Konfigurations-Manager, um sicher auf Ihrem lokalen System zu verbinden. Wenn Sie die Verbindung mithilfe einer lokalen SQL Server sind, ist der Hybrid-Verbindungs-Manager erforderlich.

Finden Sie unter [mithilfe der Hybrid Connection Manager](app-service-logic-hybrid-connection-manager.md).


## Möchten Sie den Connector
Der Connector erstellt wird, können Sie es in einen Business-Datenfluss mit einer Anwendung Logik hinzufügen. Finden Sie unter [Was Logik Apps sind?](app-service-logic-what-are-logic-apps.md).

Sie können auch die Leistung Statistiken und Steuerung der Sicherheit mit dem Connector überprüfen. Finden Sie unter [Verwalten und überwachen-API-apps und Connector](../app-service-api/app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->