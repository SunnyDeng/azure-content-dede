<properties 
   pageTitle="Azure SQL-Datenbank Index Advisor" 
   description="Der SQL Database Index Advisor empfiehlt neue Indizes für vorhandene SQL-Datenbanken, die die aktuelle Abfrageleistung verbessern können." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/22/2015"
   ms.author="sstein"/>

# SQL Database Index Advisor

Der SQL Database Index Advisor empfiehlt neue Indizes für vorhandene SQL-Datenbanken, die die aktuelle Abfrageleistung verbessern können.

Der SQL-Datenbankdienst bewertet die Indexleistung durch eine Analyse des Ressourcennutzungsverlaufs für eine SQL-Datenbank, und es werden die Indizes empfohlen, die sich am besten zur Ausführung typischer Datenbankworkloads eignen.

Der Index Advisor erleichtert die Indexverwaltung durch Empfehlungen zu den zu erstellenden Indizes. Bei V12-Servern kann der Index Advisor auch Indizes mit wenigen Mausklicks im [Azure-Vorschauportal](https://portal.azure.com/) erstellen und überprüfen. Nachdem der Index erstellt wurde, analysiert der SQL-Datenbankdienst die Leistung der Datenbankworkload und stellt ausführliche Informationen zu den Auswirkungen des neuen Index bereit. Ergibt die Analyse, dass ein empfohlener Index negative Auswirkungen auf die Leistung hat, wird der Index automatisch wiederhergestellt.

Mit dem Index Advisor können Sie Zeit bei der Optimierung der Datenbankleistung einsparen.


> [AZURE.NOTE]Der Index Advisor befindet sich derzeit in der Vorschau und steht nur im [Azure-Vorschauportal](https://portal.azure.com/) zur Verfügung.


## Überlegungen zur Vorschau

Der Index Advisor befindet sich derzeit in der Vorschau und weist die folgenden Einschränkungen auf:

- Indexempfehlungen können nur für V12-Server automatisch erstellt und überprüft werden. (Empfehlungen und Indexerstellungsskripts werden für V12-Server bereitgestellt.)
- Empfehlungen und Verwaltung sind nur für nicht gruppierte Indizes verfügbar.

## Voraussetzungen

Zum Anzeigen und Erstellen von Indexempfehlungen benötigen Sie die richtigen Berechtigungen für [rollenbasierte Zugriffssteuerung](role-based-access-control-configure.md) in Azure.

- Die Berechtigungen **Reader** und **SQL DB Contributor** sind zum Anzeigen von Empfehlungen erforderlich.
- Die Berechtigungen **Owner** und **SQL DB Contributor** sind erforderlich, um Aktionen ausführen zu können, Indizes zu erstellen oder zu löschen und die Indexerstellung abzubrechen.


## Verwenden des Index Advisors

Der Index Advisor ist einfach zu verwenden. Gehen Sie zur Vereinfachung der Indexverwaltung für die Datenbank nach den folgenden Richtlinien vor:

- Zunächst überprüfen Sie die Liste der Indexempfehlungen und entscheiden, welche Indizes erstellt oder ignoriert werden sollen. Die Liste der Empfehlungen ist nach den geschätzten Auswirkungen auf die Leistung sortiert und bezeichnet (siehe unten). 
- Erstellen oder ignorieren Sie die empfohlenen Indizes. Erstellen Sie den Index automatisch, indem Sie im Portal auf **Create Index** klicken, oder erstellen Sie den Index manuell, indem Sie das Skript für die Indexerstellung ausführen.
- Bei manuell erstellten Indizes sollten Sie den Erstellungsvorgang überwachen und die Auswirkungen auf die Leistung messen. Bei automatisch erstellten Indizes wird die Überwachung und die Analyse der Auswirkungen auf die Leistung automatisch vom Azure SQL-Datenbankdienst ausgeführt. 



## Überprüfen der empfohlenen Indizes

Der Index Advisor stellt eine Liste von Indexempfehlungen im Fenster „Datenbank“ im [Azure-Vorschauportal](https://portal.azure.com/) zur Verfügung. Die obersten ausgewählten Empfehlungen werden für alle Tabellen in der ausgewählten Datenbank angezeigt, für die das Erstellen eines neuen Index Leistungssteigerungen bringen kann.

### So überprüfen Sie die derzeit verfügbaren Indexempfehlungen

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2. Klicken Sie im linken Menü auf **DURCHSUCHEN**.
3. Klicken Sie im Blatt **Durchsuchen** auf **SQL-Datenbanken**.
4. Klicken Sie im Blatt **SQL-Datenbanken** auf die Datenbank, für die Sie die empfohlenen Indizes anzeigen möchten.
5. Klicken Sie auf **Index Advisor**, um die verfügbaren **Indexempfehlungen** für die ausgewählte Datenbank zu öffnen und anzuzeigen.

> [AZURE.NOTE]Um Indexempfehlungen zu erhalten, muss eine Datenbank ungefähr eine Woche lang genutzt werden, und innerhalb dieser Woche müssen Aktivitäten stattfinden. Außerdem müssen auch konsistente Aktivitäten vorhanden sein. Der Index Advisor kann leichter für konsistente Abfragemuster optimiert werden als für zufällige, unregelmäßige Aktivitätsspitzen.

![Empfohlene Indizes][3]

Empfehlungen werden nach möglichen Auswirkungen auf die Leistung in die folgenden vier Kategorien unterteilt:

| Impact | Beschreibung |
| :--- | :--- |
| Hoch | Empfehlungen für hohe Auswirkungen sollten den größten Einfluss auf die Leistung haben. |
| Substantial | Empfehlungen für erhebliche Auswirkungen sollten die Leistung deutlich verbessern. |
| Moderate | Empfehlungen für mittlere Auswirkungen sollten die Leistung verbessern, jedoch nicht wesentlich. |
| Low | Empfehlungen für geringe Auswirkungen sollten eine bessere Leistung als ohne Index bieten, die Verbesserungen sind möglicherweise jedoch nicht signifikant. 
Verwenden Sie das Tag "Impact", um die besten Kandidaten zum Erstellen neuer Indizes zu bestimmen.

### Verwalten der Liste der empfohlenen Indizes

Wenn die Empfehlungsliste Indizes enthält, bei denen Sie der Meinung sind, dass diese keinen Nutzen haben, können Sie die Indexempfehlungen im Index Advisor verwerfen. (Sie können die verworfenen Indizes bei Bedarf später wieder in die Liste **Recommended indexes** aufnehmen.)

#### Verwerfen einer Indexempfehlung

1. Wählen Sie den Index in der Liste **Recommended indexes** aus.
1. Klicken Sie auf dem Blatt **Index details** auf **Discard index**.

#### Anzeigen von verworfen Indizes und erneutes Hinzufügen zur Hauptliste

1. Klicken Sie auf dem Blatt **Index recommendations** auf **View discarded index recommendations**.
1. Wählen Sie einen verworfenen Index aus der Liste, um dessen Details anzuzeigen.
1. Klicken Sie optional auf **Undo Discard**, um den Index wieder der Hauptliste der **Index recommendations** hinzuzufügen.



## Erstellen neuer Indizes

Der Index Advisor bietet Ihnen die vollständige Kontrolle über die Erstellung von Indizes. Jede Empfehlung enthält ein T-SQL-Indexerstellungsskript, und Sie können die genauen Angaben zur Indexerstellung überprüfen, bevor eine Aktion für eine Datenbank durchgeführt wird.

Indexempfehlungen Sie für alle Azure SQL-Datenbankserver verfügbar, aber nur V12-Server bieten automatisierte Indexerstellung. Andere Server profitieren auch vom Index Advisor, Sie müssen Indizes jedoch manuell erstellen, wie im Folgenden beschrieben.

Wählen Sie sowohl für automatische als auch manuelle Erstellung einfach einen empfohlenen Index auf dem Blatt **Index recommendations** aus, und gehen Sie folgendermaßen vor:

### Automatische Indexerstellung (nur V12-Server)

Wenn sich die Datenbank auf einem V12-Server befindet, können Sie problemlos einen empfohlenen Index erstellen, indem Sie den gewünschten Index im Portal auswählen und dann auf **Create Index** klicken.

Die Datenbank bleibt während der Indexerstellung online. Bei der Verwendung des Index Advisors zum Erstellen eines Index wird die Datenbank nicht offline geschaltet.

Darüber hinaus erfordern mit **Create Index** erstellte Indizes keine weitere Leistungsüberwachung. Wenn ein empfohlener Index negative Auswirkungen auf die Leistung hat, wird der Index automatisch wiederhergestellt. Nach der Verwendung von "Create Index" sind die Metriken zu den Auswirkungen des neuen Index im Portal verfügbar.


### Manuelle Indexerstellung (alle Server)

Wählen Sie einen beliebigen empfohlenen Index im Portal aus, und klicken Sie dann auf **View Script**. Führen Sie dieses Skript für die Datenbank aus, um den empfohlenen Index zu erstellen. Indizes, die manuell erstellt wurden, werden nicht überwacht und nicht auf ihre tatsächlichen Auswirkungen auf die Leistung überprüft. Es empfiehlt sich daher, diese Indizes nach der Erstellung zu überwachen, um sicherzustellen, dass sie Leistungssteigerungen bieten, und um sie gegebenenfalls anzupassen oder zu löschen. Ausführliche Informationen zum Erstellen von Indizes finden Sie unter [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Abbrechen der Indexerstellung

Indizes, die den Status **Pending** haben, können abgebrochen werden. Indizes, die gerade erstellt werden (Status **Executing**), können nicht abgebrochen werden.

1. Wählen Sie einen beliebigen Index mit dem Status **Pending** im Bereich **Index operations** aus, um das Blatt **Index details** zu öffnen.
1. Klicken Sie auf **Abbrechen**, um den Vorgang der Indexerstellung abzubrechen.

## Überwachen der Indexvorgänge nach der Erstellung der Indizes

Die Erstellung eines Index wird nicht sofort ausgeführt. Im Portal finden Sie ausführliche Informationen zum Status der Indexvorgänge. Beim Verwalten von Indizes können die folgenden möglichen Indexzustände vorliegen:

| Status | Beschreibung |
| :--- | :--- |
| Ausstehend | Der Befehl "Create index" wurde empfangen, und die Erstellung des Index wurde geplant. |
| Executing | Der Befehl "Create Index" wird ausgeführt, und der Index wird gegenwärtig erstellt. |
| Erfolgreich | Der Index wurde erfolgreich erstellt. |
| Fehler | Der Index wurde nicht erstellt. Dies kann ein vorübergehendes Problem sein, oder es handelt sich möglicherweise um eine Schemaänderung an der Tabelle, und das Skript ist nicht mehr gültig. |
| Wiederherstellen | Die Indexerstellung wurde abgebrochen oder wurde als nicht leistungsfähig erachtet und automatisch zurückgesetzt. |



![Empfohlene Indizes][4]



## Entfernen eines Index
Sie können Indizes entfernen, die mit dem Index Advisor erstellt wurden.


1. Wählen Sie einen erfolgreich erstellten Index in der Liste **Index operations** aus.
1. Klicken Sie auf dem Blatt **Index details** auf **Remove index**, oder klicken Sie für ein "DROP INDEX"-Skript auf **View Script**.



## Zusammenfassung

Indexempfehlungen bieten eine automatisierte Lösung für die Indexerstellung und Analyse der einzelnen SQL-Datenbanken, um die am besten geeigneten Indizes zu ermitteln. Klicken Sie auf einem Blatt "Datenbank" auf die Kachel **Index Advisor**, um Indexempfehlungen anzuzeigen.



## Nächste Schritte

Überwachen Sie Ihre Indexempfehlungen, und wenden Sie sie weiterhin an, um die Leistung zu optimieren. Datenbankworkloads sind dynamisch und ändern sich ständig. Der Index Advisor setzt die Überwachung fort und empfiehlt Indizes, die die Leistung Ihrer Datenbank potenziell erhöhen können.


<!--Image references-->
[1]: ./media/sql-database-index-advisor/index-recommendations.png
[2]: ./media/sql-database-index-advisor/index-details.png
[3]: ./media/sql-database-index-advisor/recommended-indexes.png
[4]: ./media/sql-database-index-advisor/index-operations.png

<!---HONumber=Oct15_HO3-->