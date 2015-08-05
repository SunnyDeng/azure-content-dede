<properties
   pageTitle="Migrieren zu einer SQL-Datenbank mithilfe von SSMS"
   description="Microsoft Azure SQL-Datenbank, SQL-Datenbank migrieren, Migrieren mithilfe von SSMS"
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

#Migrieren einer kompatiblen Datenbank mit SSMS 

![Diagramm der SSMS-Migration](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Wenn ein Datenbankschema bereits mit der Azure SQL-Datenbank kompatibel ist, geht die Migration einfach vonstatten. Wenn keine Schemaänderungen erforderlich sind, braucht bei der Migration nur die Datenbank in Azure importiert zu werden. Dies kann in einem einzigen Schritt mit SSMS durch "Bereitstellen" der Datenbank in Azure SQL-Datenbank geschehen oder als zweistufiger Prozess durch Exportieren einer BACPAC-Datei aus der aktuellen Datenbank mit anschließendem Importieren in Azure, um eine neue Azure SQL-Datenbank zu erstellen.

Darüber hinaus können Sie die exportierte BACPAC-Datei in den Azure-Speicher hochladen und als Datenbank über das Portal importieren. Durch den Import in Azure wird die Wartezeit beim Importschritt verringert, was wiederum die Leistung und Zuverlässigkeit der Migration bei großen Datenbanken verbessert.

Bei einer direkten Bereitstellung aus SSMS werden immer sowohl Schema als auch Daten bereitgestellt, während beim Export und Import immer das Schema bereitgestellt und dann eine Option angeboten wird, Daten aus allen Tabellen oder einem Teil der Tabellen bereitzustellen. Bei beiden Bereitstellungsmethoden, aus SSMS oder per Export mit anschließendem Import aus SSMS (oder später aus dem Portal), kommt die gleiche DAC-Technologie zum Einsatz, und das Ergebnis ist identisch.

Diese Option dient auch als letzter Schritt in Option 3 zum Migrieren der Datenbanken nach der Aktualisierung für die Kompatibilität mit der Azure SQL-Datenbank.

##Verwenden von SSMS zum Bereitstellen der Azure SQL-Datenbank
1.	Stellen Sie über das Azure-Verwaltungsportal einen logischen Server bereit.
2. Suchen Sie die Quelldatenbank im SSMS-Objekt-Explorer, und führen Sie die Aufgabe **Bereitstellen einer Datenbank in Azure SQL-Datenbank** aus.

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	Konfigurieren Sie im Bereitstellungs-Assistenten die Verbindung mit dem Azure SQL-Datenbank-Zielserver, den Sie bereitgestellt haben.
4.	Geben Sie den **Namen** für die Datenbank ein, und legen Sie die **Edition** (Dienstebene) sowie die Leistungsstufe **Service Objective** fest. Weitere Informationen zum Konfigurieren dieser Einstellungen finden Sie unter [Dienstebenen der Azure SQL-Datenbank](sql-database-service-tiers.md). 

	![Exporteinstellungen](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Schließen Sie den Assistenten zum Migrieren der Datenbank ab. Je nach Größe und Komplexität der Datenbank kann die Bereitstellung wenige Minuten bis viele Stunden dauern. Wenn Fehler darauf hinweisen, dass das Datenbankschema nicht mit der SQL-Datenbank kompatibel ist, verwenden Sie eine andere Migrationsoption.

##Verwenden von SSMS zum Exportieren einer BACPAC-Datei mit anschließendem Importieren in die SQL-Datenbank
Der Bereitstellungsprozess kann in zwei Schritte unterteilt werden: Export und Import. Im ersten Schritt wird eine BACPAC-Datei erstellt, die dann im zweiten Schritt als Eingabe verwendet wird.

1.	Stellen Sie über das Azure-Verwaltungsportal einen logischen Server bereit.
2.	Suchen Sie die Quelldatenbank im SSMS-Objekt-Explorer, und wählen Sie die Aufgabe **Bereitstellen einer Datenbank in Azure SQL-Datenbank** aus.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. Konfigurieren Sie im Export-Assistenten die entsprechenden Einstellungen, um die BACPAC-Datei lokal zu speichern. Die exportierte BACPAC-Datei enthält immer das vollständige Datenbankschema und standardmäßig die Daten aus allen Tabellen. Verwenden Sie die Registerkarte "Erweitert", wenn Sie Daten aus einigen oder allen Tabellen ausschließen möchten. Sie können z. B. nur die Daten für Verweistabellen exportieren.
	>[AZURE.NOTE]Hinweis: Sobald das Azure-Verwaltungsportal den Import in Azure unterstützt, können Sie auswählen, dass die exportierte BACPAC-Datei in Azure-Speicher gespeichert und der Import in der Cloud ausgeführt wird.

	![Exporteinstellungen](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Sobald die BACPAC-Datei erstellt wurde, stellen Sie eine Verbindung mit dem Server her, den Sie in Schritt 1 erstellt haben. Klicken Sie mit der rechten Maustaste auf den Ordner **Datenbanken**, und klicken Sie auf **Datenebenenanwendung importieren**.

	![Menüelement für das Importieren von Datenebenenanwendungen](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	Wählen Sie im Import-Assistenten die BACPAC-Datei aus, die Sie gerade exportiert haben, um die neue Datenbank in Azure SQL-Datenbank zu erstellen.

	![Importieren von Einstellungen](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Geben Sie den Namen für die Datenbank ein, und legen Sie die Edition (Dienstebene) sowie das Dienstziel (Leistungsstufe) fest.
	 
7.	Schließen Sie den Assistenten zum Importieren der BACPAC-Datei ab, und erstellen Sie die Datenbank in der Azure SQL-Datenbank.

	![Datenbankeinstellungen](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##Alternativen
Sie können auch das Befehlszeilen-Dienstprogramm "sqlpackage.exe" zum Bereitstellen der Datenbank oder zum Importieren bzw. Exportieren einer BACPAC-Datei verwenden. "sqlpackage.exe" verwendet die gleiche DAC-Technologie wie SSMS, sodass das Ergebnis identisch ist. Weitere Informationen finden Sie im MSDN unter [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=July15_HO4-->