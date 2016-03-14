<properties 
   pageTitle="Kopieren von Daten zwischen Data Lake-Speicher und Azure SQL-Datenbank mithilfe von Sqoop | Microsoft Azure"
   description="Verwenden von Sqoop zum Kopieren von Daten zwischen Azure SQL-Datenbank und Data Lake-Speicher" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/29/2016"
   ms.author="nitinme"/>

# Kopieren von Daten zwischen Data Lake-Speicher und Azure SQL-Datenbank mithilfe von Sqoop

Erfahren Sie mehr über das Verwenden von Apache Sqoop zum Importieren und Exportieren von Daten zwischen Azure SQL-Datenbank und Data Lake-Speicher.
 

## Was ist Sqoop?

Big Data-Anwendungen sind eine gute Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z. B. Protokolle und Dateien. Allerdings müssen gelegentlich strukturierte Daten verarbeitet werden, die in relationalen Datenbanken gespeichert sind.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) ist ein Tool zum Übertragen von Daten zwischen relationalen Datenbanken und einem Big Data-Repository wie Data Lake-Speicher. Mit diesem Tool können Sie Daten aus einem relationalen Datenbank-Managementsystem (RDBMS) wie z. B. Azure SQL-Datenbank in Data Lake-Speicher importieren. Sie können die Daten dann mithilfe von Big Data-Workloads transformieren und analysieren und anschließend wieder in ein RDBMS zurückexportieren. In diesem Tutorial verwenden Sie eine Azure SQL-Datenbank als relationale Datenbank für den Import/Export.
 

## Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktiviertes Azure-Abonnement** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup). 
- **Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake-Speicherkonto. Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md). In diesem Artikel wird davon ausgegangen, dass Sie einen HDInsight Linux-Cluster mit Data Lage-Speicherzugriff verwenden.
- **Azure SQL-Datenbank**. Anleitungen zum Erstellen einer Datenbank finden Sie unter [Erstellen einer Azure SQL-Datenbank](../sql-database/sql-database-get-started.md).

## Erstellen von Beispieltabellen in der Azure SQL-Datenbank

1. Erstellen Sie zuerst zwei Beispieltabellen in der Azure SQL-Datenbank. Stellen Sie mit [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) oder Visual Studio eine Verbindung mit der Azure SQL-Datenbank her, und führen Sie die folgenden Abfragen aus:

	**Erstellen von Table1**

		CREATE TABLE [dbo].[Table1]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

	**Erstellen von Table2**

		CREATE TABLE [dbo].[Table2]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

2. Fügen Sie in **Table1** einige Beispieldaten hinzu. Lassen Sie **Table2** leer. Wir importieren Daten aus **Table1** in Data Lake-Speicher. Anschließend exportieren wir Daten aus Data Lake-Speicher in **Table2**. Führen Sie den folgenden Codeausschnitt aus:

		 
		INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## Verwenden von Sqoop in einem HDInsight-Cluster mit Zugriff auf Data Lake-Speicher

In einem HDInsight-Cluster sind die Sqoop-Pakete bereits verfügbar. Wenn Sie den HDInsight-Cluster so konfiguriert haben, dass Data Lake-Speicher als zusätzlicher Speicher verwendet wird, können Sie Sqoop (ohne Konfigurationsänderungen) zum Importieren/Exportieren von Daten zwischen einer relationalen Datenbank (in diesem Beispiel die Azure SQL-Datenbank) und einem Data Lake-Speicherkonto verwenden.

1. In diesem Tutorial wird davon ausgegangen, dass Sie einen Linux-Cluster erstellt haben. Daher sollten Sie SSH für das Herstellen einer Verbindung mit dem Cluster verwenden. Informationen hierzu finden Sie unter [Verbinden mit einem Linux-basierten HDInsight-Cluster](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Überprüfen Sie, ob Sie vom Cluster aus auf das Data Lake-Speicherkonto zugreifen können. Geben Sie an der SSH-Eingabeaufforderung Folgendes ein:

		
		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

	Dadurch sollte eine Liste der Dateien bzw. Ordner im Data Lake-Speicherkonto bereitgestellt werden.

### Importieren von Daten aus Azure SQL-Datenbank in Data Lake-Speicher

3. Navigieren Sie zu dem Verzeichnis, in dem Sqoop-Pakete verfügbar sind. In der Regel befinden sie sich unter `/usr/hdp/<version>/sqoop/bin`. 

4. Importieren Sie die Daten aus **Table1** in das Data Lake-Speicherkonto. Verwenden Sie die folgende Syntax:

		
		sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

	Beachten Sie, dass der Platzhalter **sql-database-server-name** für den Namen des Servers steht, auf dem die Azure SQL-Datenbank ausgeführt wird. Der Platzhalter **sql-database-name** steht für den eigentlichen Datenbanknamen.

	Beispiel:

		
		sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Stellen Sie sicher, dass die Daten in das Data Lake-Speicherkonto übertragen wurden. Führen Sie den folgenden Befehl aus:

		
		hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

	Die folgende Ausgabe wird angezeigt.

		
		-rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
		-rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
		-rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
		-rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
		-rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

	Jede „**part-m-***“-Datei entspricht einer Zeile in der Quelltabelle **Table1**. Sie können den Inhalt der „part-m-*“-Dateien anzeigen und überprüfen.


### Exportieren von Daten aus Data Lake-Speicher in Azure SQL-Datenbank

6. Exportieren Sie die Daten aus dem Data Lake-Speicherkonto in die leere Tabelle **Table2** in der Azure SQL-Datenbank. Verwenden Sie die folgende Syntax.

		
		sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

	Beispiel:

		
		sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Stellen Sie sicher, dass die Daten in die SQL-Datenbanktabelle hochgeladen wurden. Stellen Sie mit [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) oder Visual Studio eine Verbindung mit der Azure SQL-Datenbank her, und führen Sie die folgende Abfrage aus:

		
		SELECT * FROM TABLE2

	Dies führt zu folgender Ausgabe.

	 	ID  FName   LName
		------------------
		1	Neal	Kell
		2	Lila	Fulton
		3	Erna	Myers
		4	Annette	Simpson

## Weitere Informationen

- [Kopieren von Daten aus Azure Storage-Blobs in den Data Lake-Speicher](data-lake-store-copy-data-azure-storage-blob.md)
- [Sichern von Daten in Data Lake-Speicher](data-lake-store-secure-data.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0302_2016-->