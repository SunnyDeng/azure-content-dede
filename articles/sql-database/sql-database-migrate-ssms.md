<properties
   pageTitle="Migrieren zu einer SQL-Datenbank mithilfe von SSMS"
   description="Microsoft Azure SQL-Datenbank, SQL-Datenbank migrieren, Migrieren mithilfe von SSMS"
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

#Migrieren einer kompatiblen Datenbank mit SSMS

![Diagramm der SSMS-Migration](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Wenn ein Datenbankschema mit der Azure SQL-Datenbank kompatibel ist, braucht bei der Migration nur die Datenbank in Azure importiert zu werden. Dies kann entweder in einem einzigen Schritt mit SSMS durch Bereitstellen der Datenbank in Azure SQL-Datenbank geschehen oder als zweistufiger Prozess durch Exportieren einer BACPAC-Datei aus der Datenbank, die anschließend als neue Datenbank in einen Azure SQL Server importiert wird.

Wenn Sie eine BACPAC-Datei exportieren, können Sie sie entweder als lokale Datei oder in direkt in ein Azure-Blob exportieren. Wenn Sie die Datei lokal exportieren, können Sie die exportierte BACPAC-Datei in ein Azure-Blob hochladen. Sobald die BACPAC-Datei in einem Azure-Blob gespeichert wurde, können Sie die BACPAC-Datei mithilfe des Azure-Portals als Datenbank importieren. Durch eine Ausführung des Imports im Azure-Portal wird die Wartezeit beim Importschritt verringert, was wiederum die Leistung und Zuverlässigkeit der Migration mit großen Datenbanken verbessert.

Bei einer direkten Bereitstellung aus SSMS werden immer sowohl das gesamte Schema als auch alle Daten bereitgestellt, während beim Export einer BACPAC-Datei eine Option angeboten wird, eine Teilmenge von Objekten in die BACPAC-Datei zu exportieren. Die exportierte BACPAC-Datei enthält immer das vollständige Datenbankschema und standardmäßig die Daten aus allen Tabellen. Bei beiden Bereitstellungsmethoden, aus SSMS oder per Export mit anschließendem Import aus SSMS (oder aus dem Azure-Portal), kommt die gleiche DAC-Technologie zum Einsatz, und das Ergebnis ist identisch.

Diese Option dient auch als letzter Schritt in Option 2 zum Migrieren der Datenbanken nach der Aktualisierung für die Kompatibilität mit der Azure SQL-Datenbank.

## Herunterladen der aktuellen Version von SQL Server Management Studio

Verwenden Sie die aktuelle Version von Microsoft SQL Server Management Studio für SQL Server, um sicherzustellen, dass Sie die neuesten Updates für die Tools in SSMS und für die Interaktion mit dem Azure-Portal. Um die neueste Version von Microsoft SQL Server Management Studio für SQL Server zu erhalten, [laden Sie sie herunter](https://msdn.microsoft.com/library/mt238290.aspx), und installieren Sie sie auf einem Client-Computer mit Verbindung zu der Datenbank, die Sie für die Migration vorgesehen haben, und zum Internet.

##Verwenden von SSMS zum Bereitstellen der Azure SQL-Datenbank
1.	Stellen Sie über das Azure-Verwaltungsportal einen logischen Server bereit.
2.	Suchen Sie die Quelldatenbank im SSMS-Objekt-Explorer, und führen Sie die Aufgabe **Datenebenenanwendung exportieren...** aus.

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	Konfigurieren Sie im Bereitstellungs-Assistenten die Verbindung mit dem logischen Azure SQL-Datenbank-Zielserver, den Sie in Schritt 1 bereitgestellt haben.
4.	Geben Sie den **Namen** für die Datenbank ein, und legen Sie die **Edition** (Dienstebene) sowie die Leistungsstufe **Service Objective** fest. Weitere Informationen zum Konfigurieren dieser Einstellungen finden Sie unter [Dienstebenen der Azure SQL-Datenbank](sql-database-service-tiers.md).

	![Exporteinstellungen](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Schließen Sie den Assistenten zum Migrieren der Datenbank ab. Je nach Größe und Komplexität der Datenbank kann die Bereitstellung wenige Minuten bis viele Stunden dauern. Sollten Inkompatibilitäten auftreten, erkennen die Schemaüberprüfungsroutinen schnell Fehler, bevor die Bereitstellung in Azure tatsächlich erfolgt. Wenn Fehler darauf hinweisen, dass das Datenbankschema nicht mit der SQL-Datenbank kompatibel ist, verwenden Sie Migrationsoption 2. Informationen dazu finden Sie unter [Lokales Aktualisieren einer Datenbank und Bereitstellen in Azure SQL-Datenbank](sql-database-migrate-visualstudio-ssdt.md).

##Verwenden von SSMS zum Exportieren einer BACPAC-Datei mit anschließendem Importieren in die SQL-Datenbank
Der Bereitstellungsprozess kann in zwei Schritte unterteilt werden: Export und Import. Im ersten Schritt wird eine BACPAC-Datei erstellt, die dann im zweiten Schritt als Eingabe verwendet wird.

1.	Stellen Sie über das Azure-Verwaltungsportal einen logischen Server bereit.
2.	Suchen Sie die Quelldatenbank im SSMS-Objekt-Explorer, und wählen Sie die Aufgabe **Bereitstellen einer Datenbank in Azure SQL-Datenbank...** aus.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. Konfigurieren Sie im Export-Assistenten den Export für das Speichern der BACPAC-Datei entweder an einem lokalen Speicherort auf dem Datenträger oder in einem Azure-Blob. Die exportierte BACPAC-Datei enthält immer das vollständige Datenbankschema und standardmäßig die Daten aus allen Tabellen. Verwenden Sie die Registerkarte "Erweitert", wenn Sie Daten aus einigen oder allen Tabellen ausschließen möchten. Sie können z. B. nur die Daten für Verweistabellen exportieren.

	![Exporteinstellungen](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Sobald die BACPAC-Datei erstellt wurde, stellen Sie eine Verbindung mit dem Server her, den Sie in Schritt 1 erstellt haben. Klicken Sie mit der rechten Maustaste auf den Ordner **Datenbanken**, und klicken Sie auf **Datenebenenanwendung importieren...**.
	
	>[AZURE.NOTE]Hinweis: Sie können die ein einem Azure-Blob gespeicherte BACPAC-Datei auch innerhalb des Azure-Verwaltungsportals importieren.

	![Menüelement für das Importieren von Datenebenenanwendungen](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	Wählen Sie im Import-Assistenten die BACPAC-Datei aus, die Sie gerade exportiert haben, um die neue Datenbank in Azure SQL-Datenbank zu erstellen.

	![Importieren von Einstellungen](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Geben Sie den Namen für die Datenbank ein, und legen Sie die Edition (Dienstebene) sowie das Dienstziel (Leistungsstufe) fest.

7.	Schließen Sie den Assistenten zum Importieren der BACPAC-Datei ab, und erstellen Sie die Datenbank in der Azure SQL-Datenbank.

	![Datenbankeinstellungen](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##Alternativen
Sie können auch das Befehlszeilen-Dienstprogramm "sqlpackage.exe" zum Bereitstellen der Datenbank oder zum Importieren bzw. Exportieren einer BACPAC-Datei verwenden. "sqlpackage.exe" verwendet die gleiche DAC-Technologie wie SSMS, sodass das Ergebnis identisch ist. Weitere Informationen finden Sie [in MSDN unter „SqlPackage.exe“](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=Oct15_HO3-->