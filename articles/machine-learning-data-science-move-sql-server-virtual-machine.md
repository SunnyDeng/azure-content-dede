<properties 
	pageTitle="Verschieben von Daten nach SQL Server in Azure | Azure" 
	description="Verschieben von Daten nach SQL Server in Azure" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="fashah,garye"/> 

#Verschieben von Daten nach SQL Server in Azure

In diesem Dokument wird das Verschieben von Daten aus Flatfiles (CSV/TSV) oder von einem lokalen SQL Server auf einen SQL Server in Azure beschrieben


<table>

<tr>
<td><b>QUELLE</b></td>
<td colspan="2" align="center"><b>ZIEL</b></td>
</tr>

<tr>
  <td></td>
  <td><b>SQL Server-VM in Azure</b></td>
</tr>

<tr>
  <td><b>Flatfile</b></td>  
  <td>
    1. <a href="#insert-tables-bcp">Befehlszeilenprogramm zum Massenkopieren (bcp) </a><br>
    2. <a href="#insert-tables-bulkquery">SQL-Abfrage zum Masseneinfügen </a><br>
    3. <a href="#sql-builtin-utilities">Integrierte Dienstprogramme in SQL Server mit Benutzeroberfläche </a>
  </td>
</tr>
<tr>
  <td><b>Lokaler SQL Server</b></td>
  <td>
    1. <a href="#export-flat-file">Exportieren in eine Flatfile </a><br>
    2. <a href="#sql-migration">SQL-Datenbankmigrations-Assistent </a> <br>    
    3. <a href="#sql-backup">Datenbanksicherung und -wiederherstellung </a> <br>
  </td>
</tr>
</table>

Beachten Sie, dass in diesem Dokument davon ausgegangen wird, dass die SQL-Befehle in SQL Server Management Studio oder im Datenbank-Explorer von Visual Studio ausgeführt werden.


## <a name="sqlonazurevm"></a>Verschieben von Daten auf eine SQL Server-VM in Azure

In diesem Abschnitt wird das Verschieben von Daten in eine SQL Server-VM in Azure beschrieben. Wenn Sie die SQL Server-VM noch nicht eingerichtet haben, stellen Sie wie unter [Einrichten eines virtuellen Azure-Computers mit SQL-Server für Data Science](http://azure.microsoft.com/documentation/articles/machine-learning-data-science-setup-sql-server-virtual-machine/ "Set up a Data Science Virtual Machine in Azure") beschrieben einen neuen virtuellen SQL Server-Computer für Data Science bereit. 

In diesem Dokument wird das Verschieben von Daten aus folgenden Datenquellen beschrieben: 
  
1. [Aus Flatfiles](#filesource_to_sqlonazurevm) 
2. [Von einem lokalen SQL Server](#sqlonprem_to_sqlonazurevm)


### <a name="filesource_to_sqlonazurevm"></a>Dateiquelle

Wenn sich Ihre Daten in einer Flatfile (in Zeilen/Spalten angeordnet) befinden, können sie über die folgenden Methoden auf eine SQL Server-VM in Azure verschoben werden:

1. [Befehlszeilenprogramm zum Massenkopieren (bcp)](#insert-tables-bcp) 
2. [SQL-Abfrage zum Masseneinfügen](#insert-tables-bulkquery)
3. [Integrierte Dienstprogramme in SQL Server mit Benutzeroberfläche (Importieren/Exportieren, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Befehlszeilenprogramm zum Massenkopieren (bcp)

bcp ist ein Befehlszeilenprogramm, das mit SQL Server installiert wird und eine der schnellsten Möglichkeiten zum Verschieben von Daten darstellt. Es funktioniert für alle drei SQL Server-Varianten (lokaler SQL Server, SQL Azure und SQL Server-VM in Azure). 

> [AZURE.NOTE] **Wo sollten sich die Daten für die Verwendung mit bcp befinden?**  
> Auch wenn dies nicht erforderlich ist, beschleunigt sich die Übertragung, wenn sich die Ausgangsdaten auf demselben Computer wie der Ziel-SQL Server befinden (Netzwerkgeschwindigkeit im Vergleich zur E/A-Geschwindigkeit des lokalen Datenträgers). Sie können die Flatfiles mit den Daten auf den Computer verschieben, auf dem SQL Server installiert ist. Dazu stehen verschiedene Dateikopiertools wie [AzCopy](storage/storage-use-azcopy.md), [Azure-Speicher-Explorer](https://azurestorageexplorer.codeplex.com/) oder das Kopieren und Einfügen unter Windows über RDP (Remote Desktop Protocol) zur Verfügung.

1. Stellen Sie sicher, dass die Datenbank und die Tabellen in der SQL Server-Zieldatenbank erstellt werden. Es folgt ein Beispiel für die Durchführung unter Verwendung der Befehle `Create Database` und `Create Table`:

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. Generieren Sie die Formatdatei, die das Schema für die Tabelle beschreibt, durch Eingabe des folgenden Befehls an der Befehlszeile des Computers, auf dem bcp installiert ist.

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Fügen Sie die Daten unter Verwendung des bcp-Befehls wie folgt in der Datenbank hinzu. Dies sollte an der Befehlszeile funktionieren, wenn SQL Server auf demselben Computer installiert ist:

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimieren von bcp-Einfügevorgängen** - Im Artikel ['Richtlinien zur Optimierung von Massenimport'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) finden Sie Informationen zum Optimieren von Massenimportvorgängen.

#### <a name="insert-tables-bulkquery-parallel"></a>Parallelisieren von Einfügevorgängen für schnellere Verschiebeoperationen

Wenn die Daten, die Sie verschieben möchten, sehr umfangreich sind, können Sie den Vorgang beschleunigen, indem Sie mehrere bcp-Befehle parallel in einem PowerShell-Skript ausführen.

> [AZURE.NOTE] **Erfassen großer Datenmenden** 
> Partitionieren Sie zur Optimierung der Ladevorgänge bei großen bis sehr großen DataSets Ihre logischen und physischen Datenbanktabellen mithilfe mehrerer Dateigruppen und Partitionstabellen. Weitere Informationen zum Erstellen und Laden von Daten in Partitionstabellen finden Sie unter [Paralleles Laden von SQL-Partitionstabellen](http://azure.microsoft.com/documentation/articles/machine-learning-data-science-parallel-load-sql-partitioned-tables).


Das PowerShell-Beispielskript unten veranschaulicht das parallele Einfügen mithilfe von bcp:
	
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

Mit der [SQL-Abfrage zum Masseneinfügen](https://msdn.microsoft.com/library/ms188365) können Sie Daten aus zeilen-/spaltenbasierten Dateien in die Datenbank importieren (Informationen zu den unterstützten Typen finden Sie [hier](https://msdn.microsoft.com/library/ms188609)). 

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
 	  

### <a name="sql-builtin-utilities"></a>Integrierte Dienstprogramme in SQL Server

Sie können die SQL Server Integration Services (SSIS) zum Importieren von Daten aus einer Flatfile in die SQL Server-VM in Azure verwenden. 
SSIS ist in zwei Studio-Umgebungen verfügbar. Weitere Informationen finden Sie unter [Integration Services (SSIS)- und Studio-Umgebungen](https://technet.microsoft.com/library/ms140028.aspx):

- Einzelheiten zu den SQL Server Data Tools finden Sie unter [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx).  
- Ausführliche Informationen zum Import/Export-Assistenten finden Sie unter [SQL Server-Import/Export-Assistent](https://msdn.microsoft.com/library/ms141209.aspx).

### <a name="sqlonprem_to_sqlonazurevm"></a>Verschieben von Daten von einem lokalen SQL Server

Daten können folgendermaßen von einem lokalen SQL Server verschoben werden:

1. [Exportieren in eine Flatfile](#export-flat-file) 
2. [SQL-Datenbankmigrations-Assistent](#sql-migration)
3. [Datenbanksicherung und -wiederherstellung](#sql-backup)

Diese Verfahren werden im Folgenden beschrieben:

#### <a name="export-flat-file"></a>Exportieren in eine Flatfile

Für das Massenexportieren von Daten von einem lokalen SQL Server können verschiedene Vorgehensweisen verwendet werden, die [hier](https://msdn.microsoft.com/library/ms175937.aspx) beschrieben werden. In diesem Dokument wird als Beispiel bcp (Bulk Copy Program) beschrieben. Nachdem die Daten in eine Flatfile exportiert wurden, können sie mit einem Massenimport auf einen anderen SQL Server importiert werden. 

1. Exportieren Sie die Daten vom lokalen SQL Server folgendermaßen mit dem Dienstprogramm bcp in eine Datei:

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. Erstellen Sie die Datenbank und die Tabelle auf der SQL Server-VM in Azure mithilfe der Befehle `create database` und `create table` für das in Schritt 1 exportierte Tabellenschema.

3. Erstellen Sie eine Formatdatei zur Beschreibung des Tabellenschemas der zu exportierenden/importierenden Daten. Details zur Formatdatei werden [hier](https://msdn.microsoft.com/library/ms191516.aspx) beschriebenen.

	Generieren der Formatdatei bei Ausführung von bcp auf dem SQL Server-Computer: 

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	Generieren der Formatdatei bei Remoteausführung von bcp auf dem SQL Server: 

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. Verwenden Sie eine der im Abschnitt [Dateiquelle](#filesource_to_sqlonazurevm) beschriebenen Methoden, um die Daten in Flatfiles auf einen SQL Server zu verschieben.

#### <a name="sql-migration"></a>SQL-Datenbankmigrations-Assistent

Der [SQL-Datenbankmigrations-Assistent](http://sqlazuremw.codeplex.com/) stellt eine  benutzerfreundliche Möglichkeit zum Verschieben von Daten zwischen zwei SQL Server-Instanzen dar. Er bietet Ihnen die Möglichkeit, das Datenschema zwischen Quell- und Zieltabellen zuzuordnen und die Spaltentypen auszuwählen, sowie verschiedene andere Funktionen. Im Hintergrund wird bcp zum Massenkopieren verwendet. Nachfolgend finden Sie einen Screenshot des Begrüßungsbildschirms für den SQL-Datenbankmigrations-Assistenten.  

![SQL Server Migration Wizard][2]

#### <a name="sql-backup"></a>Datenbanksicherung und -wiederherstellung

SQL Server unterstützt: 

1. [Funktionen zur Datenbanksicherung und -wiederherstellung](https://msdn.microsoft.com/library/ms187048.aspx) (beide Vorgänge in einer lokalen Datei oder per bacpac-Export in ein Blob) und [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx) (mit bacpac). 
2. Die Möglichkeit, SQL Server-VMs in Azure direkt mit einer kopierten Datenbank oder der Kopie einer vorhandenen SQL Azure-Datenbank zu erstellen. Weitere Informationen finden Sie unter [Verwenden des Assistenten zum Kopieren von Datenbanken](https://msdn.microsoft.com/library/ms188664.aspx). 

Einen Screenshot der Optionen für das Sichern/Wiederherstellen von Datenbanken in SQL Server Management Studio finden Sie unten.

![SQL Server Import Tool][1]


[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png


<!--HONumber=49-->