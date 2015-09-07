<properties 
   pageTitle="Migration mithilfe von Visual Studio und SSDT"
	description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Datenbank importieren, Datenbank exportieren, Migrations-Assistent"
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
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#Lokales Aktualisieren einer Datenbank und Bereitstellen in Azure SQL-Datenbank

![alt text](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Verwenden Sie diese Option, wenn bei der Migration einer lokalen Datenbank zu Azure SQL-Datenbank V12 Schemaänderungen erforderlich sind, da die Datenbank SQL Server-Funktionen verwendet, die in Azure SQL-Datenbank nicht unterstützt werden, oder um zu testen, ob nicht unterstützte Funktionen in einer lokalen Datenbank vorhanden sind.

Verwenden Sie die [aktuellen SQL Server Data Tools für Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) mit Visual Studio 2013 Update 4 oder höher.

Mit dieser Option:

 - SQL Server Data Tools („SSDT“) für Visual Studio werden zunächst verwendet, um ein Datenbankprojekt aus der Quelldatenbank zu erstellen. 
 - Die Zielplattform für das Projekt wird dann auf Azure SQL-Datenbank V12 festgelegt, und das Projekt wird erstellt, um mögliche Kompatibilitätsprobleme zu ermitteln. 
 - Nachdem das Projekt erfolgreich erstellt wurde, erfolgt eine erneute Veröffentlichung des Schemas in einer Kopie der Quelldatenbank (Quelldatenbank nicht überschreiben).
 - Die Datenvergleichsfunktion in SSDT wird dann zum Vergleichen der Quelldatenbank mit der neu erstellten, mit Azure SQL kompatiblen Datenbank und zum anschließenden Aktualisieren der neuen Datenbank mit Daten aus der Quelldatenbank verwendet. 
 - Die aktualisierte Datenbank wird daraufhin mithilfe von SSMS entweder direkt oder durch Exportieren und Importieren einer BACPAC-Datei in Azure bereitgestellt.
 
>[AZURE.NOTE]Hinweis: Wenn nur ein Schema bereitgestellt werden muss, kann das aktualisierte Schema direkt aus Visual Studio in Azure SQL-Datenbank veröffentlicht werden.

## Schritte bei der Migration

1.	Öffnen Sie den **Objekt-Explorer von SQL Server** in Visual Studio. Verwenden Sie **SQL Server hinzufügen**, um eine Verbindung mit der SQL Server-Instanz herzustellen, die die zu migrierende Datenbank enthält. Suchen Sie die Datenbank im Explorer, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Neues Projekt erstellen...** aus. 

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Legen Sie die Importeinstellungen auf **Import application-scoped objects only** fest. Deaktivieren Sie die Optionen zum Importieren der folgenden Anmeldungsverweise, Berechtigungen und Datenbankeinstellungen.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Klicken Sie auf **Starten**, um die Datenbank zu importieren und das Projekt zu erstellen, das eine T-SQL-Skriptdatei für jedes Objekt in der Datenbank enthält. Die Skriptdateien werden in Ordnern innerhalb des Projekts geschachtelt.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Datenbankprojekt, und wählen Sie "Eigenschaften" aus. Dadurch wird die Seite **Projekteinstellungen** geöffnet, auf der Sie die Zielplattform auf "Microsoft Azure SQL Database V12" festlegen.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Erstellen**, um das Projekt zu erstellen.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	Die **Fehlerliste** zeigt die einzelnen Inkompatibilitäten an. In diesem Fall ist der Benutzername „NT AUTHORITY\\NETWORK SERVICE“ nicht kompatibel. Da er nicht kompatibel ist, können Sie ihn kommentieren oder entfernen (und die Auswirkungen des Entfernens dieser Anmeldung und Rolle aus der Datenbanklösung behandeln).

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	Doppelklicken Sie auf das erste Skript, um es in einem Abfragefenster zu öffnen, auszukommentieren und dann auszuführen. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	Wiederholen Sie diesen Vorgang für jedes Skript mit Inkompatibilitäten mit, bis kein Fehler mehr vorhanden ist. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	Wenn alle Fehler in der Datenbank behoben wurden, klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus, um die Datenbank als Kopie der Quelldatenbank zu erstellen und zu veröffentlichen. (Es wird dringend empfohlen, zumindest zu Beginn eine Kopie zu verwenden.) 
 - Vor der Veröffentlichung sollten Sie in Abhängigkeit von der Quell-SQL Server-Version (älter als SQL Server 2014) die Zielplattform für das Projekt zum Aktivieren der Bereitstellung zurücksetzen. 
 - Wenn Sie eine ältere SQL Server-Datenbank migrieren, dürfen Sie keine Features in das Projekt einführen,die nicht vom Quell-SQL Server unterstützt werden, es sei denn, Sie migrieren zuerst die Datenbank in eine neuere Version von SQL Server. 

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	Klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf Ihre Quelldatenbank und klicken dann auf **Datenvergleich**, um das Projekt mit der ursprünglichen Datenbank zu vergleichen, um zu verstehen, welche Änderungen vom Assistenten vorgenommen wurden. Wählen Sie Ihre Azure SQL-Version V12 der Datenbank aus, und klicken Sie dann auf **Fertig stellen**.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	Überprüfen Sie die erkannten Unterschiede, und klicken Sie dann auf **Ziel aktualisieren**, um Daten aus der Quelldatenbank in die Azure SQL V12-Datenbank zu migrieren.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	Stellen Sie das mit Azure SQL V12 kompatible Datenbankschema und die Daten mithilfe von SSMS in der Azure SQL-Datenbank bereit. Siehe [Migrieren einer kompatiblen Datenbank mit SSMS](sql-database-migrate-ssms.md)

<!---HONumber=August15_HO9-->