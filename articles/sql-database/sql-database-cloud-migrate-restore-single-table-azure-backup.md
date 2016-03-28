<properties
	pageTitle="Wiederherstellen einer einzelnen Tabelle aus einer Azure SQL-Datenbanksicherung | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine einzelne Tabelle aus einer Azure SQL-Datenbanksicherung wiederherstellen."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="daleche"/>


# Wiederherstellen einer einzelnen Tabelle aus einer Azure SQL-Datenbanksicherung

Es kann Situationen geben, in denen Sie versehentlich einige Daten in einer SQL-Datenbank geändert haben und die einzelne betroffene Tabelle wiederherstellen möchten. In diesem Artikel wird beschrieben, wie Sie eine einzelne Tabelle in einer Datenbank aus einer der Sicherungen wiederherstellen, die basierend auf der ausgewählten Leistungsstufe automatisch von Azure SQL-Datenbank erstellt werden.

## Vorbereitungsschritte: Benennen Sie die Tabelle um, und stellen Sie eine Kopie der Datenbank wieder her.
1. Identifizieren Sie die Tabelle in Ihrer Azure SQL-Datenbank, die Sie durch die wiederhergestellte Kopie ersetzen möchten. Verwenden Sie Microsoft SQL Management Studio zum Umbenennen der Tabelle. Benennen Sie die Tabelle beispielsweise in &lt;Tabellenname&gt;\_alt um.

	**Hinweis**: Um eine Blockierung zu verhindern, müssen Sie sicherstellen, dass keine Aktivitäten für die Tabelle ausgeführt werden, die umbenannt werden soll. Wenn Probleme auftreten, stellen Sie sicher, dass dieser Schritt während eines Wartungsfensters ausgeführt wird.

2. Stellen Sie eine Sicherung Ihrer Datenbank zu einem Zeitpunkt wieder her, den Sie wiederherstellen möchten. Informationen zur Vorgehensweise finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank nach einem Benutzerfehler](../sql-database/sql-database-user-error-recovery.md).

	**Hinweise**:
	- Der Name der wiederhergestellten Datenbank erhält das Format „Datenbankname + Zeitstempel“, beispielsweise **Adventureworks2012\_2016-01-01T22-12Z**. Durch diesen Schritt wird der vorhandene Datenbankname auf dem Server nicht überschrieben. Dies ist eine Sicherheitsmaßnahme, durch die der Benutzer die wiederhergestellte Datenbank überprüfen kann, bevor die aktuelle Datenbank gelöscht und die wiederhergestellte Datenbank zum Einsatz in der Produktion umbenannt wird.
	- Alle Leistungsstufen von Basic bis Premium werden automatisch vom Dienst gesichert. Hierbei variieren die Aufbewahrungszeiten für die Sicherung basierend auf dem Tarif:

| Datenbankwiederherstellung | Basic-Tarif | Standard-Tarife | Premium-Tarife |
| :-- | :-- | :-- | :-- |
| Zeitpunktwiederherstellung | Jeder Wiederherstellungspunkt innerhalb von 7 Tagen|Jeder Wiederherstellungspunkt innerhalb von 14 Tagen| Jeder Wiederherstellungspunkt innerhalb von 35 Tagen|

## Kopieren der Tabelle aus der wiederhergestellten Datenbank mithilfe des Tools für die SQL-Datenbankmigration
1. Laden Sie den [SQL-Datenbankmigrations-Assistenten](https://sqlazuremw.codeplex.com) herunter, und installieren Sie ihn.

2. Öffnen Sie den SQL-Datenbankmigrations-Assistenten, wählen Sie auf der Seite **Prozess auswählen** die Einstellung **Datenbank in Analyse/Migration**, und klicken Sie dann auf **Weiter**. ![SQL-Datenbankmigrations-Assistent: Prozess auswählen](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Geben Sie im Dialogfeld **Verbindung mit dem Server herstellen** die folgenden Einstellungen an:
 - **Servername**: Ihre SQL Azure-Instanz
 - **Authentifizierung**: **SQL Server-Authentifizierung** Geben Sie Ihre Anmeldeinformationen ein.
 - **Datenbank**: **Masterdatenbank (alle Datenbanken auflisten)**.
 - **Hinweis**: Der Assistent speichert standardmäßig Ihre Anmeldeinformationen. Wenn Sie dies nicht möchten, aktivieren Sie **Anmeldeinformationen vergessen**. ![SQL-Datenbankmigrations-Assistent: Quelle auswählen, Schritt 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Wählen Sie im Dialogfeld **Quelle auswählen** den Namen der wiederhergestellten Datenbank aus dem Abschnitt **Vorbereitungsschritte** als Quelle aus, und klicken Sie auf **Weiter**.

	![SQL-Datenbankmigrations-Assistent: Quelle auswählen, Schritt 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Wählen Sie im Dialogfeld **Objekte auswählen** die Option **Bestimmte Datenbankobjekte auswählen**, und wählen Sie dann die Tabelle (oder Tabellen) aus, die Sie zum Zielserver migrieren möchten. ![SQL-Datenbankmigrations-Assistent: Objekte auswählen](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Klicken Sie auf der Seite **Skript-Assistent - Zusammenfassung** auf **Ja**, wenn Sie gefragt werden, ob Sie zum Erstellen eines SQL-Skripts bereit sind. Sie können das TSQL-Skript aus zur späteren Verwendung speichern. ![SQL-Datenbankmigrations-Assistent: Skript-Assistent – Zusammenfassung](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Klicken Sie auf der Seite **Zusammenfassung der Ergebnisse** auf **Weiter**. ![SQL-Datenbankmigrations-Assistent: Ergebniszusammenfassung](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Klicken Sie auf der Seite **Zielserververbindung einrichten** auf **Verbindung mit Server herstellen**, und geben Sie dann wie folgt die Details ein:
	- **Servername**: Zielserverinstanz
	- **Authentifizierung**: **SQL Server-Authentifizierung** Geben Sie Ihre Anmeldeinformationen ein.
	- **Datenbank**: **Masterdatenbank (alle Datenbanken auflisten)**. Mit dieser Option werden alle Datenbanken auf dem Zielserver aufgelistet.

	![SQL-Datenbankmigrations-Assistent: Zielserververbindung einrichten](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Klicken Sie auf **Verbinden**, wählen Sie die Zieldatenbank aus, in die Sie die Tabelle verschieben möchten, und klicken Sie dann auf **Weiter**. Dadurch wird die Ausführung des zuvor generierten Skripts abgeschlossen, und es sollte die neu verschobene Tabelle angezeigt werden, die Sie in die Zieldatenbank kopiert haben.

## Überprüfungsschritt
1. Testen Sie die neu kopierte Tabelle, und führen Sie Abfragen aus, um sicherzustellen, dass die Daten intakt sind. Wenn Sie sich von der Richtigkeit der Daten überzeugt haben, können Sie die umbenannte Tabelle aus dem Abschnitt **Vorbereitungsschritte** löschen. (Beispielsweise &lt;Tabellenname&gt;\_alt.)

<!---HONumber=AcomDC_0316_2016-->