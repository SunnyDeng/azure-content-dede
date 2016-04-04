<properties 
	pageTitle="Verschieben von Daten zu SQL Server auf einem virtuellen Azure-Computer | Azure" 
	description="Verschieben von Daten aus Flatfiles oder von einer lokalen SQL Server-Instanz nach SQL Server auf einem virtuellen Azure-Computer" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="fashah;mohabib;bradsev" />

# Verschieben von Daten zu SQL Server auf einem virtuellen Azure-Computer

## Einführung

**In diesem Dokument** beschreiben wir die Optionen zum Verschieben von Daten aus Flatfiles (CSV- oder TSV-Format) oder von einem lokalen SQL Server in SQL Server auf einem virtuellen Azure-Computer. Diese Aufgaben zum Verschieben von Daten in die Cloud gehören zum Cortana-Analyseprozess, der von Azure bereitgestellt wird.

Ein Thema, in dem die Optionen für das Verschieben von Daten in eine Azure SQL-Datenbank für Machine Learning beschrieben werden, finden Sie unter [Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning](machine-learning-data-science-move-sql-azure.md).

Das nachstehende **Menü** bietet Links zu Themen, in denen beschrieben wird, wie Daten in anderen Zielumgebungen erfasst werden, in denen die Daten während des Cortana Analytics-Prozesses (CAP) gespeichert und verarbeitet werden.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


In der folgenden Tabelle sind die Optionen zum Verschieben von Daten zu SQL Server auf einem virtuellen Azure-Computer zusammengefasst.

<b>QUELLE</b> |<b>ZIEL: SQL Server auf virtuellem Azure-Computer</b> |
------------------ |-------------------- |
<b>Flatfile</b> |1\. <a href="#insert-tables-bcp">Befehlszeilenprogramm zum Massenkopieren (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">SQL-Abfrage zum Masseneinfügen </a><br>3. <a href="#sql-builtin-utilities">Integrierte grafische Hilfsprogramme in SQL Server</a>
<b>Lokaler SQL Server</b> | 1\. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer</a><br> 2. <a href="#export-flat-file">Exportieren in eine Flatfile </a><br> 3. <a href="#sql-migration">SQL-Datenbankmigrations-Assistent </a><br> 4. <a href="#sql-backup">Datenbanksicherung und -wiederherstellung </a><br>

Beachten Sie, dass in diesem Dokument davon ausgegangen wird, dass die SQL-Befehle in SQL Server Management Studio oder im Datenbank-Explorer von Visual Studio ausgeführt werden.

> [AZURE.TIP] Alternativ dazu können Sie [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) verwenden, um eine Pipeline zu erstellen und zu planen, die Daten in eine SQL Server-VM auf Azure verschiebt. Weitere Informationen hierzu finden Sie unter [Kopieren von Daten mit Azure Data Factory (Kopieraktivität)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Voraussetzungen
In diesem Tutorial wird Folgendes vorausgesetzt:

* Ein **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein **Azure-Speicherkonto**. Sie benötigen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../storage/storage-create-storage-account.md#create-a-storage-account). Nachdem Sie das Speicherkonto erstellt haben, müssen Sie den Kontoschlüssel für den Zugriff auf den Speicher abrufen. Siehe [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Bereitgestellter **SQL Server auf einem virtuellen Azure-Computer**. Anleitungen finden Sie unter [Einrichten eines virtuellen Azure SQL Server-Computers als IPython Notebook-Server für die erweiterte Analyse](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Lokal installierte und konfigurierte **Azure PowerShell**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a> Verschieben von Daten aus einer Flatfilequelle zu SQL Server auf einem virtuellen Azure-Computer

Wenn sich Ihre Daten in einer Flatfile (in Zeilen/Spalten angeordnet) befinden, können sie über die folgenden Methoden auf eine SQL Server-VM in Azure verschoben werden:

1. [Befehlszeilenprogramm zum Massenkopieren (BCP)](#insert-tables-bcp) 
2. [SQL-Abfrage zum Masseneinfügen](#insert-tables-bulkquery)
3. [Integrierte grafische Hilfsprogramme in SQL Server (Importieren/Exportieren, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Befehlszeilenprogramm zum Massenkopieren (BCP)

BPC ist ein Befehlszeilenprogramm, das mit SQL Server installiert wird und eine der schnellsten Möglichkeiten zum Verschieben von Daten darstellt. Es funktioniert für alle drei SQL Server-Varianten (lokaler SQL Server, SQL Azure und SQL Server-VM in Azure).

> [AZURE.NOTE] **Wo sollten sich die Daten für die Verwendung mit BCP befinden?** Auch wenn dies nicht erforderlich ist, beschleunigt sich die Übertragung, wenn sich die Ausgangsdaten auf demselben Computer wie der Ziel-SQL Server befinden (Netzwerkgeschwindigkeit im Vergleich zur E/A-Geschwindigkeit des lokalen Datenträgers). Sie können die Flatfiles mit den Daten auf den Computer verschieben, auf dem SQL Server installiert ist. Dazu stehen verschiedene Dateikopiertools wie [AzCopy](../storage/storage-use-azcopy.md), [Azure Storage-Explorer](http://storageexplorer.com/) oder das Kopieren und Einfügen unter Windows über RDP (Remotedesktopprotokoll) zur Verfügung.

1. Stellen Sie sicher, dass die Datenbank und die Tabellen in der SQL Server-Zieldatenbank erstellt werden. Es folgt ein Beispiel für die Durchführung unter Verwendung der Befehle `Create Database` und `Create Table`:

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. Generieren Sie die Formatdatei, die das Schema für die Tabelle beschreibt, durch Eingabe des folgenden Befehls an der Befehlszeile des Computers, auf dem BPC installiert ist.

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. Fügen Sie die Daten unter Verwendung des BPC-Befehls wie folgt in der Datenbank hinzu. Dies sollte an der Befehlszeile funktionieren, wenn SQL Server auf demselben Computer installiert ist:

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimieren von BCP-Einfügevorgängen**: Im Artikel [Richtlinien zur Optimierung von Massenimport](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) finden Sie Informationen zum Optimieren von Massenimportvorgängen.

### <a name="insert-tables-bulkquery-parallel"></a>Parallelisieren von Einfügevorgängen für schnellere Datenverschiebungen

Wenn die Daten, die Sie verschieben möchten, sehr umfangreich sind, können Sie den Vorgang beschleunigen, indem Sie mehrere BPC-Befehle parallel in einem PowerShell-Skript ausführen.

> [AZURE.NOTE] **Erfassen großer Datenmengen** Partitionieren Sie zur Optimierung der Ladevorgänge bei großen bis sehr großen DataSets Ihre logischen und physischen Datenbanktabellen mithilfe mehrerer Dateigruppen und Partitionstabellen. Weitere Informationen zum Erstellen und Laden von Daten in Partitionstabellen finden Sie unter [Paralleles Laden von SQL-Partitionstabellen](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


Das PowerShell-Beispielskript unten veranschaulicht das parallele Einfügen mithilfe von BPC:
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>SQL-Abfrage zum Masseneinfügen

Mit der [SQL-Abfrage zum Masseneinfügen](https://msdn.microsoft.com/library/ms188365) können Sie Daten aus zeilen-/spaltenbasierten Dateien in die Datenbank importieren (Informationen zu den unterstützten Typen finden Sie unter [Vorbereiten von Daten für den Massenexport oder -import (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Im Folgenden sehen Sie einige Beispielbefehle für Masseneinfügungen:

1. Analysieren Sie vor dem Importieren Ihre Daten, und legen Sie benutzerdefinierte Optionen fest, um sicherzustellen, dass die SQL Server-Datenbank dasselbe Format für alle Sonderfelder wie z. B. Datumsangaben annimmt. Hier ist ein Beispiel für das Festlegen des Datumsformats Jahr-Monat-Tag (wenn die Daten das Datum im Jahr-Monat-Tag-Format enthalten):

		SET DATEFORMAT ymd;	
	
2. Importieren von Daten mit Anweisungen zum Massenimport:

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities"></a>Integrierte Hilfsprogramme in SQL Server

Sie können die SQL Server Integration Services (SSIS) zum Importieren von Daten aus einer Flatfile in die SQL Server-VM in Azure verwenden. SSIS ist in zwei Studio-Umgebungen verfügbar. Weitere Informationen finden Sie unter [Integration Services (SSIS) und Studio-Umgebungen](https://technet.microsoft.com/library/ms140028.aspx):

- Einzelheiten zu den SQL Server Data Tools finden Sie unter [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx).  
- Ausführliche Informationen zum Import/Export-Assistenten finden Sie unter [SQL Server-Import/Export-Assistent](https://msdn.microsoft.com/library/ms141209.aspx).

## <a name="sqlonprem_to_sqlonazurevm"></a> Verschieben von Daten von einer lokalen SQL Server-Instanz nach SQL Server auf einem virtuellen Azure-Computer

Sie können auch die folgenden Migrationsstrategien verwenden:

1. [Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportieren in eine Flatfile](#export-flat-file) 
3. [SQL-Datenbankmigrations-Assistent](#sql-migration)
4. [Datenbanksicherung und -wiederherstellung](#sql-backup)

Diese Verfahren werden im Folgenden beschrieben:

### Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer

Der **Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer** ist eine einfache und empfohlene Möglichkeit zum Verschieben von Daten von einer lokalen SQL Server-Instanz nach SQL Server auf einem virtuellen Azure-Computer. Ausführliche Schritte sowie eine Erläuterung weitere Alternativen finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](../virtual-machines/virtual-machines-windows-classic-migrate-sql.md).

### <a name="export-flat-file"></a>Exportieren in eine Flatfile

Für das Massenexportieren von Daten von einer lokalen SQL Server-Instanz stehen verschiedene Vorgehensweisen zur Verfügung, die unter [Massenimport und -export von Daten (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) beschrieben werden. In diesem Dokument wird als Beispiel BPC (Bulk Copy Program) beschrieben. Nachdem die Daten in eine Flatfile exportiert wurden, können sie mit einem Massenimport auf einen anderen SQL Server importiert werden.

1. Exportieren Sie die Daten vom lokalen SQL Server folgendermaßen mit dem Dienstprogramm BPC in eine Datei:

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. Erstellen Sie die Datenbank und die Tabelle auf der SQL Server-VM in Azure mithilfe der Befehle `create database` und `create table` für das in Schritt 1 exportierte Tabellenschema.

3. Erstellen Sie eine Formatdatei zur Beschreibung des Tabellenschemas der zu exportierenden/importierenden Daten. Einzelheiten zur Formatdatei finden Sie unter [Erstellen einer Formatdatei (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

	Generieren der Formatdatei bei Ausführung von bcp auf dem SQL Server-Computer:

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	Generieren der Formatdatei bei Remoteausführung von BPC auf dem SQL Server:

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. Verwenden Sie eine der im Abschnitt [Dateiquelle](#filesource_to_sqlonazurevm) beschriebenen Methoden, um die Daten in Flatfiles auf einen SQL Server zu verschieben.

### <a name="sql-migration"></a>SQL-Datenbankmigrations-Assistent

Der [SQL-Datenbankmigrations-Assistent](http://sqlazuremw.codeplex.com/) stellt eine benutzerfreundliche Möglichkeit zum Verschieben von Daten zwischen zwei SQL Server-Instanzen dar. Er bietet Ihnen die Möglichkeit, das Datenschema zwischen Quell- und Zieltabellen zuzuordnen und die Spaltentypen auszuwählen, sowie verschiedene andere Funktionen. Im Hintergrund wird BPC zum Massenkopieren verwendet. Nachfolgend finden Sie einen Screenshot des Begrüßungsbildschirms für den SQL-Datenbankmigrations-Assistenten.

![SQL Server-Migrations-Assistent][2]

### <a name="sql-backup"></a>Datenbanksicherung und -wiederherstellung

SQL Server unterstützt:

1. [Funktionen zur Datenbanksicherung und -wiederherstellung](https://msdn.microsoft.com/library/ms187048.aspx) (sowohl in einer lokalen Datei als auch per bacpac-Export in ein Blob) und [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx) (mit bacpac). 
2. Die Möglichkeit, SQL Server-VMs in Azure direkt mit einer kopierten Datenbank oder der Kopie einer vorhandenen SQL Azure-Datenbank zu erstellen. Weitere Informationen finden Sie unter [Verwenden des Assistenten zum Kopieren von Datenbanken](https://msdn.microsoft.com/library/ms188664.aspx). 

Einen Screenshot der Optionen für das Sichern/Wiederherstellen von Datenbanken in SQL Server Management Studio finden Sie unten.

![SQL Server-Importtool][1]

## Ressourcen

[Migrieren einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](../virtual-machines/virtual-machines-windows-classic-migrate-sql.md)

[Übersicht zu SQL Server auf virtuellen Azure-Computern](../virtual-machines/virtual-machines-windows-classic-sql-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

<!---HONumber=AcomDC_0323_2016-->