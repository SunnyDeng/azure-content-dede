<properties 
   pageTitle="SQL-Connector" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft SQL-Connector #

Connectors können in Logik-Apps als Teil eines Datenflusses verwendet werden, um Daten im Rahmen eines Datenflusses abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Es gibt Szenarios, in denen Sie möglicherweise mit einer SQL-Datenbank auf Azure-SQL oder SQL Server (das hinter einer Firewall lokal installiert ist) arbeiten müssen. Mithilfe des SQL-Connectors im Datenfluss können Sie eine Vielzahl von Szenarios erzielen. Einige Beispiele:  

1.	Machen Sie einen Teil der Daten in der SQL-Datenbank über ein Front-End für Web- oder mobile Benutzer verfügbar.
2.	Einfügen von Daten zum Speichern in der SQL-Datenbanktabelle (Beispiel: Mitarbeiterdatensätze, Rechnungen usw.)
3.	Extrahieren von Daten aus SQL zur Verwendung in einem Geschäftsprozess

Für diese Szenarien muss Folgendes ausgeführt werden: 

1. Erstellen einer Instanz der SQL-Connector-API-App
2. Herstellen von Hybridkonnektivität für die API-App zur Kommunikation mit dem lokalen SQL-System Dieser Schritt ist optional und nur für lokale SQL Server-Installationen und nicht für SQL Azure erforderlich.
3. Verwenden der erstellten API-App in einer Logik-App zum Erreichen des gewünschten Geschäftsprozesses

	###Grundlegende Trigger und Aktionen
		
    - Daten abfragen (Trigger) 
    - In Tabelle einfügen
    - Tabelle aktualisieren
    - In Tabelle auswählen
    - Aus Tabelle löschen
    - Gespeicherte Prozedur aufrufen

## Erstellen einer Instanz der SQL-Connector-API-App ##

Zur Verwendung des SQL-Connectors müssen Sie zunächst eine Instanz der SQL-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2. Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SQL-Connector".
3. Geben Sie die generischen Details wie Name, App Service-Plan usw. auf dem ersten Blatt an.
4. Geben Sie die in der folgenden Tabelle aufgeführten Paketeinstellungen an.	

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>Name</th><th>Erforderlich</th><th>Standardwert</th><th>Beschreibung</th></tr>
 <tr><td>Servername</td><td>Ja</td><td>&nbsp;</td><td>Geben Sie den SQL Server-Namen an. Beispiel: "SQLserver", "SQLserver/Sqlexpress" oder "SQLserver.mydomain.com".</td></tr>
 <tr><td>Port</td><td>Nein</td><td> 1433</td><td>Optional. Die Nummer des Ports, an dem die Verbindung hergestellt wird. Wenn Sie keinen Wert angeben, stellt der Connector die Verbindung über den Standardport her.</td></tr>
 <tr><td>Benutzername</td><td>Ja</td><td>&nbsp;</td><td>Geben Sie einen gültigen Benutzernamen für die Verbindung mit SQL Server an.</td></tr>
 <tr><td>Kennwort</td><td>Ja</td><td>&nbsp;</td><td>Geben Sie ein gültiges Kennwort für die Verbindung mit SQL Server an.</td></tr>
 <tr><td>Datenbankname</td><td>Ja</td><td>&nbsp;</td><td>Geben Sie einen gültigen Datenbanknamen für SQL Server an. Beispiel: "Aufträge" oder "dbo/Aufträge" oder "myaccount/Mitarbeiter".</td></tr>
 <tr><td>Lokal</td><td>Ja</td><td>FALSE</td><td>Geben Sie an, ob SQL Server lokal hinter einer Firewall ausgeführt wird. Ist das der Fall, müssen Sie einen Listener-Agent auf einem Server installieren, der auf Ihren SQL-Server zugreifen kann. Sie können zur API-App-Seite "Zusammenfassung" wechseln und zum Installieren des Agents auf 'Hybridverbindung' klicken.</td></tr>
 <tr><td>Service Bus-Verbindungszeichenfolge</td><td>Nein</td><td>&nbsp;</td><td>Optional. Geben Sie diesen Parameter an, wenn SQL Server lokal ausgeführt wird. Dies muss eine gültige Verbindungszeichenfolge für den Service Bus-Namespace sein. Stellen Sie sicher, dass Sie die 'Standardedition' des Azure Service Bus verwenden und nicht 'Basic'.</td></tr>
 <tr><td>Name des Partnerservers</td><td>Nein</td><td>&nbsp;</td><td>Optional. Geben Sie den Partnerserver zur Verbindung bei einem Ausfall des primären Servers an.</td></tr>
 <tr><td>Tabellen</td><td>Nein</td><td>&nbsp;</td><td>Optional. Geben Sie die Tabellen in der Datenbank an, die vom Connector geändert werden dürfen. Beispiel: OrdersTable,EmployeeTable</td></tr>
 <tr><td>Gespeicherte Prozeduren</td><td>Nein</td><td>&nbsp;</td><td>Optional. Geben Sie die gespeicherten Prozeduren in der Datenbank an, die vom Connector aufgerufen werden können. Beispiel: IsEmployeeEligible,CalculateOrderDiscount</td></tr>
 <tr><td>Abfrage zu verfügbaren Daten</td><td>Nein</td><td>&nbsp;</td><td>Optional. Geben Sie die SQL-Anweisung an, mit der bestimmt wird, ob Daten zum Abrufen einer SQL Server-Datenbanktabelle verfügbar sind. Beispiel: SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>Abfrage zu Umfragedaten</td><td>Nein</td><td>&nbsp;</td><td>Optional. Geben Sie die SQL-Anweisung zum Abrufen der SQL Server-Datenbanktabelle an. Sie können eine beliebige Anzahl von SQL-Anweisungen durch ein Semikolon getrennt angeben. Beispiel: SELECT * from table_name; DELETE from table_name. HINWEIS: Sie müssen die Abrufanweisung so angeben, dass sie nicht in einer Endlosschleife endet. So sollte beispielsweise auf eine Auswahl ein Löschvorgang folgen, und eine auf einem Flag basierende Auswahl sollte von einer Aktualisierung des Flags gefolgt sein.</td></tr>
</table>


 ![][1]  

## Hybridkonfiguration (optional) ##

Hinweis: Dieser Schritt ist nur bei lokaler Verwendung von SQL Server hinter einer Firewall erforderlich.

Navigieren Sie zu der soeben erstellten API-App über "Durchsuchen" -> "API-Apps" -> <Name der soeben erstellten App-API>. Sie stellen das folgende Verhalten fest. Das Setup ist unvollständig, da die Hybridverbindung noch nicht hergestellt wurde.

![][2] 

Gehen Sie zum Einrichten der Hybridkonnektivität folgendermaßen vor:

1. Kopieren Sie die primäre Verbindungszeichenfolge.
2. Klicken Sie auf den Link 'Herunterladen und konfigurieren'
3. Führen Sie den gestarteten Installationsvorgang durch, und geben Sie nach Aufforderung die primäre Verbindungszeichenfolge an.
4. Sobald der Installationsvorgang abgeschlossen ist, wird ein Dialogfeld wie das Folgende angezeigt.

![][3] 

Wenn Sie jetzt erneut zur erstellten API-App wechseln, stellen Sie fest, dass der Hybridverbindungsstatus "Verbunden" lautet. 

![][4] 

Hinweis: Für den Fall, dass Sie zur sekundären Verbindungszeichenfolge wechseln möchten, führen Sie die Hybrideinrichtung einfach erneut durch, und geben Sie anstelle der primären Verbindungszeichenfolge die sekundäre Verbindungszeichenfolge an.  

## Verwendung in einer Logik-App ##

Der SQL-Connector kann als Trigger oder Aktion in einer Logik-App verwendet werden. Der Trigger und alle Aktionen unterstützen sowohl JSON- als auch XML-Datenformate. Für jede Tabelle, die als Teil der Paketeinstellungen bereitgestellt wird, steht eine Reihe von JSON-Aktionen und XML-Aktionen zur Verfügung. Wenn Sie XML-Trigger oder -Aktionen verwenden, können Sie die Transform-API-App zum Konvertieren der Daten in ein XML-Datenformat verwenden. 

Betrachten wir eine einfache Logik-App, die Daten aus einer SQL-Tabelle abruft, dann einer anderen Tabelle hinzufügt und anschließend aktualisiert.



-  Beim Erstellen/Bearbeiten einer Logik-App wählen Sie die SQL-Connector-API-App als Trigger aus. Dadurch werden die verfügbaren Trigger aufgelistet - "Poll Data (JSON)" und "Poll Data (XML)".

 ![][5] 


- Wählen Sie diesen Trigger aus: "Poll Data (JSON)". Geben Sie die Häufigkeit an, und klicken Sie auf ✓.

![][6] 



- Der Trigger wird jetzt in der Logik-App als konfiguriert angezeigt. Die Ausgaben des Triggers werden angezeigt und können in nachfolgenden Aktionen als Eingabe verwendet werden. 

![][7] 


- Wählen Sie den gleichen SQL-Connector aus dem Katalog als Aktion aus. Wählen Sie eine der Einfügeaktionen aus - "Insert Into TempEmployeeDetails (JSON)".

![][8] 



- Geben Sie die einzufügenden Datensatzeingaben an, und klicken Sie auf ✓. 

![][9] 



- Wählen Sie den gleichen SQL-Connector aus dem Katalog als Aktion aus. Wählen Sie die Aktualisierungsaktion aus der gleichen Tabelle aus (z. B. "Update EmployeeDetails")

![][11] 



- Nehmen Sie die Eingaben für die Aktualisierungsaktion vor, und klicken Sie auf ✓. 

![][12] 

Sie können die Logik-App testen, indem Sie in der abgefragten Tabelle einen neuen Datensatz hinzufügen.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg



<!--HONumber=52-->