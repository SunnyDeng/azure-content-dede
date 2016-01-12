<properties
   pageTitle="Ermitteln der SQL-Datenbankkompatibilität mithilfe von SSMS"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, SQL-Datenbankkompatibilität, Assistent „Datenebenenanwendung exportieren“"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Ermitteln der SQL-Datenbankkompatibilität mithilfe von SSMS

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
 
In diesem Artikel erfahren Sie, wie Sie ermitteln können, ob eine SQL Server-Datenbank für die Migration zur SQL-Datenbank geeignet ist, indem Sie den Assistenten „Datenebenenanwendung exportieren“ in SQL Server Management Studio verwenden.

## Verwenden von SQL Server Management Studio

1. Stellen Sie sicher, dass Sie über die neueste Version von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

 	 >[AZURE.IMPORTANT]Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates von Microsoft Azure und SQL-Datenbank synchron sind. [Aktualisieren Sie SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Öffnen Sie Management Studio, und stellen Sie eine Verbindung mit Ihrer Quelldatenbank im Objekt-Explorer her.
3. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Quelldatenbank, zeigen Sie auf **Aufgaben**, und klicken Sie auf **Datenebenenanwendung exportieren…**.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Klicken Sie im Export-Assistenten auf **Weiter**, und konfigurieren Sie dann auf der Registerkarte **Einstellungen** den Export für das Speichern der BACPAC-Datei entweder an einem lokalen Speicherort auf dem Datenträger oder in einem Azure-Blob. Eine BACPAC-Datei wird nur gespeichert, wenn keine Probleme mit der Datenbankkompatibilität vorliegen. Wenn Kompatibilitätsprobleme auftreten, werden sie auf der Konsole angezeigt.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Klicken Sie auf die **Registerkarte „Erweitert“**, und deaktivieren Sie das Kontrollkästchen **Alles markieren**, um das Exportieren von Daten zu überspringen. An diesem Punkt möchten wir nur die Kompatibilität testen.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Klicken Sie auf **Weiter** und anschließend auf **Fertig stellen**. Probleme mit der Datenbankkompatibilität werden, falls vorhanden, angezeigt, nachdem der Assistent das Schema überprüft hat.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Wenn keine Fehler angezeigt werden, ist Ihre Datenbank kompatibel, und Sie können die Migration durchführen. Falls Fehler auftreten, müssen Sie sie beheben. Klicken Sie zum Anzeigen der Fehler für **Schema wird verifiziert** auf **Fehler**. ![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Wenn die BACPAC-Datei erfolgreich generiert wurde, ist Ihre Datenbank mit der SQL-Datenbank kompatibel, und Sie können die Migration durchführen.

## Nächster Schritt: Beheben von Kompatibilitätsproblemen, falls vorhanden

[Beheben von Problemen mit der Datenbankkompatibilität](sql-database-cloud-migrate-fix-compatibility-issues.md), falls vorhanden.

<!---HONumber=AcomDC_1223_2015-->