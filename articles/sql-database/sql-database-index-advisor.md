<properties 
   pageTitle="Azure SQL-Datenbank-Indexratgeber" 
   description="Der SQL-Datenbank-Indexratgeber empfiehlt neue Indizes für vorhandene SQL-Datenbanken, die die aktuelle Abfrageleistung verbessern können." 
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
   ms.date="01/23/2016"
   ms.author="sstein"/>

# SQL-Datenbank-Indexratgeber

Der Azure SQL-Datenbank-Indexratgeber stellt Indexempfehlungen für Ihre vorhandenen SQL-Datenbanken bereit, die die aktuelle Abfrageleistung verbessern können. Der SQL-Datenbankdienst bewertet die Indexleistung durch eine Analyse des Nutzungsverlaufs Ihrer SQL-Datenbank. Es werden hierbei die Indizes empfohlen, die sich am besten für die Ausführung der typischen Workload Ihrer Datenbank eignen.

Der Indexratgeber unterstützt Sie folgendermaßen dabei, die Leistung Ihrer Datenbank zu optimieren:

- Empfehlungen dazu, welche Indizes erstellt werden sollten (Empfehlungen stehen nur für nicht gruppierte Indizes zur Verfügung)
- Empfehlungen dazu, welche Indizes gelöscht werden sollten (Empfehlungen zum Löschen von Indizes befinden sich in der Vorschau und gelten aktuellen nur für doppelte Indizes)
- Möglichkeit zum automatischen Anwenden von Indexempfehlungen ohne Benutzerinteraktion (Für automatisierte Empfehlungen muss der [Abfragespeicher](https://msdn.microsoft.com/library/dn817826.aspx) aktiviert sein und ausgeführt werden)
- Automatisches Rollback von Empfehlungen, die sich negativ auf die Leistung auswirken 


Dieser Artikel beschreibt den Indexratgeber für V12-Server. Es stehen Indexempfehlungen für V11-Server zur Verfügung, aber Sie müssen das bereitgestellte Transact-SQL-Skript (T-SQL) ausführen, um die Empfehlung zu implementieren. Der Ratgeber setzt keine Indexvorgänge auf V11-Servern zurück, deshalb müssen Sie die Auswirkungen auf die Leistung überwachen und Empfehlungen ggf. zurücksetzen.


### Berechtigungen

Zum Anzeigen und Erstellen von Indexempfehlungen benötigen Sie die richtigen Berechtigungen für [rollenbasierte Zugriffssteuerung](../active-directory/role-based-access-control-configure.md) in Azure.

- Die Berechtigungen **Reader** und **SQL DB Contributor** sind zum Anzeigen von Empfehlungen erforderlich.
- Die Berechtigungen **Owner** und **SQL DB Contributor** sind erforderlich, um Aktionen ausführen zu können, Indizes zu erstellen oder zu löschen und die Indexerstellung abzubrechen.


## Anzeigen von Indexempfehlungen

Auf der Seite „Indexempfehlungen“ können Sie die vorgeschlagenen Indizes basierend auf ihrer potenziellen Auswirkung zur Verbesserung der Leistung anzeigen. Außerdem können Sie hier den Status der letzten Indexvorgänge sehen. Wählen Sie eine Empfehlung oder einen Status aus, um Detailinformationen anzuzeigen.

So zeigen Sie Indexempfehlungen an

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **DURCHSUCHEN** > **SQL-Datenbanken**, und wählen Sie Ihre Datenbank aus.
5. Klicken Sie auf **Alle Einstellungen** > **Indexratgeber** um die verfügbaren **Indexempfehlungen** für die ausgewählte Datenbank zu öffnen und anzuzeigen.

> [AZURE.NOTE] Um Indexempfehlungen zu erhalten, muss eine Datenbank ungefähr eine Woche lang genutzt werden, und innerhalb dieser Woche müssen Aktivitäten stattfinden. Außerdem müssen auch konsistente Aktivitäten vorhanden sein. Der Indexratgeber kann leichter für konsistente Abfragemuster optimiert werden als für zufällige, unregelmäßige Aktivitätsspitzen. Wenn auf der Seite **Indexempfehlungen** keine Empfehlungen verfügbar sind, sollte der Grund hier in einer Meldung erläutert werden.

![Empfohlene Indizes](./media/sql-database-index-advisor/recommendations.png)

Empfehlungen werden nach möglichen Auswirkungen auf die Leistung in die folgenden vier Kategorien unterteilt:

| Auswirkung | Beschreibung |
| :--- | :--- |
| Hoch | Empfehlungen für hohe Auswirkungen sollten den größten Einfluss auf die Leistung haben. |
| Wesentlich | Empfehlungen für erhebliche Auswirkungen sollten die Leistung deutlich verbessern. |
| Moderat | Empfehlungen für mittlere Auswirkungen sollten die Leistung verbessern, jedoch nicht wesentlich. |
| Niedrig | Empfehlungen für geringe Auswirkungen sollten eine bessere Leistung als ohne Index bieten, die Verbesserungen sind möglicherweise jedoch nicht signifikant. 
Verwenden Sie die Kennzeichnung „Auswirkung“, um die besten Kandidaten zum Erstellen neuer Indizes zu bestimmen.


### Entfernen von Indexempfehlungen aus der Liste

Wenn die Liste der empfohlenen Indizes Einträge enthält, die Sie aus der Liste entfernen möchten, können Sie die Empfehlung verwerfen:

1. Wählen Sie in der Liste **Empfohlene Indizes** eine Empfehlung aus.
2. Klicken Sie auf dem Blatt **Indexdetails** auf **Index verwerfen**.


Falls gewünscht, können Sie verworfene Indizes wieder zur Liste **Empfohlene Indizes** hinzufügen:

1. Klicken Sie auf dem Blatt **Indexempfehlungen** auf **Verworfene Indexempfehlungen anzeigen**.
1. Wählen Sie einen verworfenen Index aus der Liste, um dessen Details anzuzeigen.
1. Klicken Sie optional auf **Verwerfen rückgängig machen**, um den Index wieder der Hauptliste der **Indexempfehlungen** hinzuzufügen.



## Anwenden von Indexempfehlungen

Der Indexratgeber gibt Ihnen vollständige Kontrolle darüber, wie Indexempfehlungen umgesetzt werden. Dazu stehen Ihnen die folgenden 3 Optionen zur Verfügung:

- Aktivieren Sie einzelne Empfehlungen nacheinander.
- Konfigurieren Sie den Indexratgeber zum automatischen Anwenden von Indexempfehlungen.
- Führen Sie das empfohlene T-SQL-Skript manuell für Ihre Datenbank aus, um eine Empfehlung zu implementieren.

Wählen Sie eine beliebige Empfehlung aus, um die zugehörigen Detailinformationen anzuzeigen. Klicken Sie dann auf **Skript anzeigen**, um genaue Informationen dazu anzuzeigen, wie die Empfehlung erstellt wird.

Die Datenbank bleibt online, während der Ratgeber die Empfehlung anwendet – durch den Indexratgeber wird niemals eine Datenbank offline geschaltet.

### Anwenden einzelner Empfehlungen

Sie können Empfehlungen nacheinander anzeigen und akzeptieren.

1. Klicken Sie auf dem Blatt **Indexempfehlungen** auf eine Empfehlung.
2. Klicken Sie auf dem Blatt **Indexdetails** auf **Übernehmen**.

    ![Anwenden einer Empfehlung](./media/sql-database-index-advisor/apply.png)


### Aktivieren der automatischen Indexverwaltung

Sie können den Indexratgeber so konfigurieren, dass Empfehlungen automatisch implementiert werden. Sobald Empfehlungen zur Verfügung stehen, werden sie automatisch angewendet. Wie bei allen vom Dienst verwalteten Indexvorgängen wird eine Empfehlung zurückgesetzt, wenn sie sich negativ auf die Leistung auswirkt.

1. Klicken Sie auf dem Blatt **Indexempfehlungen** auf **Ratgebereinstellungen**:

    ![Ratgebereinstellungen](./media/sql-database-index-advisor/settings.png)

2. Konfigurieren Sie den Ratgeber zum automatischen **Erstellen** oder **Löschen** von Indizes:

    ![Empfohlene Indizes](./media/sql-database-index-advisor/automation.png)




### Manuelles Ausführen des empfohlenen T-SQL-Skripts

Wählen Sie eine beliebige Empfehlung aus, und klicken Sie auf **Skript anzeigen**. Führen Sie dieses Skript für Ihre Datenbank aus, um die Empfehlung manuell anzuwenden.

*Indizes, die manuell erstellt wurden, werden nicht durch den Dienst überwacht und auf ihre tatsächlichen Auswirkungen auf die Leistung überprüft*. Es empfiehlt sich daher, diese Indizes nach der Erstellung zu überwachen. So können Sie sicherstellen, dass sie Leistungssteigerungen bieten, und sie gegebenenfalls anpassen oder löschen. Ausführliche Informationen zum Erstellen von Indizes finden Sie unter [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Abbrechen der Indexerstellung

Indizes, die den Status **Ausstehend** haben, können abgebrochen werden. Indizes, die gerade erstellt werden (Status **Wird ausgeführt**), können nicht abgebrochen werden.

1. Wählen Sie einen beliebigen Index mit dem Status **Ausstehend** im Bereich **Indexvorgänge** aus, um das Blatt **Indexdetails** zu öffnen.
2. Klicken Sie auf **Abbrechen**, um den Vorgang der Indexerstellung abzubrechen.



## Überwachen von Indexvorgängen

Eine Empfehlung wird möglicherweise nicht umgehend angewendet. Im Portal finden Sie ausführliche Informationen zum Status der Indexvorgänge. Beim Verwalten von Indizes können die folgenden möglichen Indexzustände vorliegen:

| Status | Beschreibung |
| :--- | :--- |
| Ausstehend | Der Befehl „Index erstellen“ wurde empfangen, und die Erstellung des Index wurde geplant. |
| Wird ausgeführt | Der Befehl „Index erstellen“ wird ausgeführt, und der Index wird gegenwärtig erstellt. |
| Erfolgreich | Der Index wurde erfolgreich erstellt. |
| Fehler | Der Index wurde nicht erstellt. Dies kann ein vorübergehendes Problem sein, oder es handelt sich möglicherweise um eine Schemaänderung an der Tabelle, und das Skript ist nicht mehr gültig. |
| Wird zurückgesetzt | Die Indexerstellung wurde abgebrochen oder wurde als nicht leistungsfähig erachtet und automatisch zurückgesetzt. |

Klicken Sie auf eine in Bearbeitung befindliche Empfehlung in der Liste, um die zugehörigen Detailinformationen anzuzeigen:

![Empfohlene Indizes](./media/sql-database-index-advisor/operations.png)



### Zurücksetzen eines Index

Wenn Sie mit dem Ratgeber einen Index erstellt haben (Sie haben das T-SQL-Skript nicht manuell ausgeführt), wird der Index automatisch zurückgesetzt, wenn er eine negative Auswirkung auf die Leistung hat. Wenn Sie aus irgendeinem Grund einen Indexratgebervorgang zurücksetzen möchten, können Sie Folgendes tun.


1. Wählen Sie einen erfolgreich erstellten Index in der Liste **Indexvorgänge** aus.
2. Klicken Sie auf dem Blatt **Indexdetails** auf **zurücksetzen**, oder klicken Sie für ein DROP INDEX-Skript auf **Skript anzeigen**.

![Empfohlene Indizes](./media/sql-database-index-advisor/details.png)


## Überwachen der Auswirkung von Indexempfehlungen auf die Leistung

Nachdem Empfehlungen erfolgreich implementiert wurden, können Sie auf dem Blatt „Indexdetails“ auf **Details abfragen** klicken, um die [Statistik zur Abfrageleistung](sql-database-query-performance.md) zu öffnen und die Auswirkung Ihrer häufigsten Abfragen auf die Leistung anzuzeigen.

![Überwachen der Auswirkung auf die Leistung](./media/sql-database-index-advisor/query-insights.png)


## Zusammenfassung

Der Indexratgeber stellt Indexempfehlungen bereit und bietet Funktionen zum automatisierten Verwalten von Indizes für SQL-Datenbank. Durch das Bereitstellen von T-SQL-Skripts sowie mithilfe von Optionen zur individuellen und vollständig automatisierten Indexverwaltung bietet der Indexratgeber wertvolle Unterstützung bei der Optimierung Ihrer Datenbankindizes und damit Ihrer Abfrageleistung.



## Nächste Schritte

Überwachen Sie Ihre Indexempfehlungen, und wenden Sie sie weiterhin an, um die Leistung zu optimieren. Datenbankworkloads sind dynamisch und ändern sich ständig. Der Indexratgeber setzt die Überwachung fort und empfiehlt Indizes, die die Leistung Ihrer Datenbank potenziell erhöhen können.

<!---HONumber=AcomDC_0224_2016-->