<properties 
   pageTitle="Migration mithilfe von Visual Studio und SSDT" 
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Datenbank importieren, Datenbank exportieren, Migrations-Assistent" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/17/2015"
   ms.author="pehteh"/>

#Lokales Aktualisieren einer Datenbank und Bereitstellen in Azure SQL-Datenbank

![alt text](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Verwenden Sie diese Option, wenn bei der Migration einer Datenbank zu Azure SQL-Datenbank V12 Schemaänderungen erforderlich sind, die mit dem SQL Azure Migrations-Assistenten (SAMW) nicht durchgeführt werden können, da die Datenbank SQL Server-Funktionen verwendet, die in Azure SQL-Datenbank nicht unterstützt werden. Bei dieser Option wird zunächst Visual Studio verwendet, um ein Datenbankprojekt aus der Quelldatenbank zu erstellen. Die Zielplattform für das Projekt wird dann auf Azure SQL-Datenbank V12 festgelegt, und das Projekt wird erstellt, um mögliche Kompatibilitätsprobleme zu ermitteln. SAMW kann viele (aber nicht alle) Kompatibilitätsprobleme beheben und wird somit als erster Schritt zur Verarbeitung aller Skripts in den Projekten verwendet. Die Verwendung von SAMW ist optional, wird jedoch dringend empfohlen. Beim Erstellen des Projekts nach der Verarbeitung der Skriptdateien mit SAMW werden verbleibende Probleme erkannt, die dann manuell mithilfe der Transact-SQL-Bearbeitungstools in Visual Studio gelöst werden müssen. Nachdem das Projekt erfolgreich erstellt wurde, erfolgt eine erneute Veröffentlichung des Schemas in einer Kopie (empfohlen) der Quelldatenbank, um das Schema und die Daten lokal zu aktualisieren. Die aktualisierte Datenbank wird daraufhin entweder direkt oder durch Exportieren und Importieren einer BACPAC-Datei mit den in Option 1 beschriebenen Techniken in Azure bereitgestellt.
 
Da diese Option die lokale Aktualisierung des Datenbankschemas vor der Bereitstellung in Azure umfasst, wird dringend empfohlen, eine Kopie der Datenbank zu verwenden. Das Visual Studio-Schemavergleichstool kann verwendet werden, um den vollständigen Satz von Änderungen zu überprüfen, die vor dem Veröffentlichen des Projekts auf die Datenbank angewendet werden.

Die Verwendung des SQL Azure-Migrations-Assistenten (SAMW) ist optional, wird jedoch empfohlen. SAMW erkennt Kompatibilitätsprobleme innerhalb des Hauptteils der Funktionen sowie in gespeicherten Prozeduren und Triggern, die auf andere Weise bis zur Bereitstellung nicht erkannt werden.

Wenn nur ein Schema bereitgestellt werden muss, kann das aktualisierte Schema direkt aus Visual Studio in Azure SQL-Datenbank veröffentlicht werden.

## Schritte bei der Migration

1.	Öffnen Sie den **Objekt-Explorer von SQL Server** in Visual Studio. Verwenden Sie **SQL Server hinzufügen**, um eine Verbindung mit der SQL Server-Instanz herzustellen, die die zu migrierende Datenbank enthält. Suchen Sie die Datenbank im Explorer, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Neues Projekt erstellen...** aus. 

![alt text](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Legen Sie die Importeinstellungen auf **Import application-scoped objects only** fest. Deaktivieren Sie die Optionen zum Importieren der Anmeldungsverweise, Berechtigungen und Datenbankeinstellungen.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Klicken Sie auf **Starten**, um die Datenbank zu importieren und das Projekt zu erstellen, das eine T-SQL-Skriptdatei für jedes Objekt in der Datenbank enthält. Die Skriptdateien werden in Ordnern innerhalb des Projekts geschachtelt.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Datenbankprojekt, und wählen Sie "Eigenschaften" aus. Dadurch wird die Seite **Projekteinstellungen** geöffnet, auf der Sie die Zielplattform auf "Microsoft Azure SQL Database V12" festlegen.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Optional: Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Erstellen** aus, um das Projekt zu erstellen. (Dies ist zu diesem Zeitpunkt nicht unbedingt erforderlich, Sie erhalten dadurch jedoch einen Überblick über die Anzahl der Anwendungskompatibilitätsprobleme im Projekt und die Effektivität der folgenden Schritte.)

![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	Optional: Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Snapshot Project** aus. Durch die Aufzeichnung einer Momentaufnahme zu Beginn und möglicherweise auch in späteren Phasen bei der Transformation können Sie das Schemavergleichstool in jeder Phase für Vergleiche nutzen.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png) – Durch die Erstellung von Momentaufnahmen des Projekts wird eine DACPAC-Datei mit Zeitstempel erstellt, die das vollständige Schema des Projekts enthält. Sie können den Dateinamen ändern, um anzuzeigen, in welcher Phase des Prozesses diese Momentaufnahme erstellt wurde. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

7.	Verarbeiten Sie die importierten Skriptdateien mit dem SQL Azure-Migrations-Assistenten (SAMW). Verwenden Sie die Ordneroption, und wählen Sie den Stammordner des Projekts aus. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)

8.	Der Assistent verarbeitet jede Skriptdatei in diesem Ordner und allen Unterordnern. Auf der nächsten Seite wird eine Zusammenfassung der Ergebnisse angezeigt. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)
9.	Klicken Sie auf "Weiter", um eine zusammenfassende Liste der Dateien anzuzeigen, die geändert wurden. 

![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

> [AZURE.NOTE]Temporäre Kopien der ursprünglichen Dateien vor der Verarbeitung und der betroffenen Dateien nach der Verarbeitung werden an den oben auf der Seite angegebenen Speicherorten erstellt.

10.	Klicken Sie im Bestätigungsdialogfeld auf **Überschreiben** und **OK**, um die ursprünglichen Dateien mit den geänderten Dateien zu überschreiben. Dabei werden nur die Dateien, die tatsächlich geändert wurden, überschrieben.
11.	Optional. Verwenden Sie einen Schemavergleich, um das Projekt mit einer früheren Momentaufnahme oder der ursprünglichen Datenbank zu vergleichen, sodass Sie die vom Assistenten vorgenommenen Änderungen nachvollziehen können. Sie sollten zu diesem Zeitpunkt eine weitere Momentaufnahme erstellen. 

![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

12.	Erstellen Sie das Projekt erneut (siehe vorherigen Schritt), um die verbleibenden Fehler zu ermitteln.

13.	Arbeiten Sie sich systematisch durch die Fehler, um jedes Problem zu beheben. Bewerten Sie die Auswirkungen der einzelnen Änderungen auf Anwendungen, die die Datenbank verwenden.

14.	Wenn alle Fehler in der Datenbank behoben wurden, klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus, um die Datenbank als Kopie der Quelldatenbank zu erstellen und zu veröffentlichen. (Es wird dringend empfohlen, zumindest zu Beginn eine Kopie zu verwenden.) Das Ziel dieses Schritts ist es, die Quell-Datenbankschemas zu aktualisieren und die sich daraus ergebenden Änderungen an Daten in der Datenbank vorzunehmen.
- Vor der Veröffentlichung sollten Sie in Abhängigkeit von der Quell-SQL Server-Version die Zielplattform für das Projekt zum Aktivieren der Bereitstellung zurücksetzen. Wenn Sie eine ältere SQL Server-Datenbank migrieren, dürfen Sie keine Features in das Projekt einführen,die nicht vom Quell-SQL Server unterstützt werden, es sei denn, Sie migrieren zuerst die Datenbank in eine neuere Version von SQL Server. 
- Sie müssen den Veröffentlichungsschritt zum Aktivieren der entsprechenden Veröffentlichungsoptionen konfigurieren. Wenn Sie beispielsweise nicht unterstützte Objekte im Projekt gelöscht oder auskommentiert haben, müssen diese aus der Datenbank gelöscht werden, sodass Sie die Veröffentlichung zum Löschen von Daten in der Zieldatenbank zulassen müssen. 
- Bei geplanten wiederholten Veröffentlichungen 

15.	Stellen Sie die kopierte Datenbank für die Azure SQL-Datenbank mit den in Option 1 beschriebenen Techniken bereit.

## Überprüfen der Migration

Nach Abschluss der Migration empfiehlt es sich, das Schema in der migrierten Datenbank mit dem Schema in der Quelldatenbank zu vergleichen, um sich mit den Änderungen vertraut zu machen und sicherzustellen, dass diese Ihren Erwartungen entsprechen und keine Probleme beim Migrieren von Anwendungen in die neue Datenbank verursachen. Sie können das Schemavergleichstool aus den SQL Server-Tools in Visual Studio zum Durchführen des Vergleichs verwenden. Sie können die Datenbank in Azure SQL-Datenbank mit der ursprünglichen SQL Server-Datenbank oder einer Momentaufnahme vergleichen, die beim ersten Importieren der Datenbank in das Projekt erstellt wurde.

1.	Stellen Sie eine Verbindung mit dem Server in der Azure SQL-Datenbank her, die die migrierte Datenbank enthält, und suchen Sie die Datenbank. 

2.	Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Schemavergleich...** aus. Dadurch wird auf der linken Seite ein neuer Schemavergleich mit der Azure-Datenbank als Quelle geöffnet. Verwenden Sie die Dropdownliste "Ziel auswählen" auf der rechten Seite, um die Zieldatenbank oder die Momentaufnahmedatei für den Vergleich auszuwählen.

3.	Klicken Sie, wenn Quelle und Ziel ausgewählt sind, auf "Vergleichen", um den Vergleich auszulösen. Das Laden des Schemas aus einer komplexen Datenbank in der Azure SQL-Datenbank kann eine beträchtliche Zeit dauern. Das Laden des Schemas und die Ausführung anderer Metadatenaufgaben auf einer Azure SQL-Datenbank geht mit einer höheren Preisstufe schneller.

4.	Überprüfen Sie die Unterschiede, wenn der Vergleich abgeschlossen ist. Sie sollten grundsätzlich keine Änderungen an einem Schema anwenden, sofern Sie keine Beanstandungen haben.

Im Schemavergleich unten wird die Adventure Works 2014-Datenbank in Azure SQL-Datenbank V12 auf der linken Seite als transformiert und vom Migrations-Assistenten von SQL Azure migriert dargestellt und mit der Quelldatenbank in SQL Server auf der rechten Seite verglichen.

![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

<!---HONumber=July15_HO4-->