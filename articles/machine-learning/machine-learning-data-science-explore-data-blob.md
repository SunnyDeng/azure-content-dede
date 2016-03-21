<properties 
	pageTitle="Untersuchen von Daten im Azure-Blob-Speicher mit Pandas | Microsoft Azure" 
	description="Untersuchen von Daten, die in einem Azure-Blob-Container gespeichert sind, mithilfe von Pandas" 
	services="machine-learning,storage" 
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
	ms.date="03/04/2016" 
	ms.author="fashah;garye;bradsev" />

#Untersuchen von Daten im Azure-Blob-Speicher mit Pandas

## Einführung

In diesem Dokument wird erläutert, wie Sie in einem Azure-Blob-Container gespeicherte Daten mithilfe des [Pandas](http://pandas.pydata.org/)-Python-Pakets untersuchen.

Das nachstehende **Menü** enthält Links zu Themen, in denen die Verwendung von Tools zum Untersuchen von Daten aus verschiedenen Speicherumgebungen beschrieben wird. Diese Aufgabe ist ein Teil des Cortana-Analyseprozesses (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage)
* Die Daten wurden in einem Azure-Blob-Speicherkonto gespeichert.

## Laden der Daten in ein Pandas-DataFrame
Um ein DataSet zu untersuchen und zu bearbeiten, muss es aus der Blobquelle in eine lokale Datei heruntergeladen werden, die anschließend in ein Pandas-DataFrame geladen werden kann. Nachfolgend sehen Sie für dieses Verfahren erforderlichen Schritte:

1. Laden Sie die Daten mithilfe des Blob-Diensts und folgenden Python-Beispielcodes aus dem Azure-Blob herunter. Ersetzen Sie die Variablen im Code durch die für Ihre Umgebung geltenden Werte: 

	    from azure.storage.blob import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Lesen Sie die Daten aus der heruntergeladenen Datei in ein Pandas-DataFrame ein.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

Sie können nun die Daten durchsuchen und Funktionen mit diesem DataSet generieren.

##<a name="blob-dataexploration"></a>Beispiele für das Untersuchen von Daten mithilfe von Pandas

Hier sind einige Beispiele für Möglichkeiten zum Durchsuchen von Daten mithilfe von Pandas:

1. Untersuchen der **Anzahl von Zeilen und Spalten** 

		print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Untersuchen** der ersten oder letzten **Zeilen** im DataSet:

		dataframe_blobdata.head(10)
		
		dataframe_blobdata.tail(10)

3. Überprüfen des **Datentyps** der einzelnen importierten Spalten:
 	
		for col in dataframe_blobdata.columns:
		    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Überprüfen der **Basisstatistiken** für die Spalten im DataSet:
 
		dataframe_blobdata.describe()
	
5. Überprüfen der Anzahl der Einträge für jeden Spaltenwert:

		dataframe_blobdata['<column_name>'].value_counts()

6. **Zählen der fehlenden Werte** im Vergleich zur tatsächlichen Anzahl der Einträge in den einzelnen Spalten:

		miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
		print miss_num
	 
7.	Löschen **fehlender Werte** in einer bestimmten Spalte aus den Daten:

		dataframe_blobdata_noNA = dataframe_blobdata.dropna()
		dataframe_blobdata_noNA.shape

	Alternatives Ersetzen der fehlenden Werte mit der "mode"-Funktion:
	
		dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})		

8. Erstellen eines **Histogramms** mithilfe der Variablenanzahl von Gruppen zur Darstellung der Verteilung einer Variablen:
	
		dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
		
		np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
	
9. Überprüfen der **Korrelationen** zwischen Variablen mithilfe eines Punktdiagramms oder einer integrierten „correlation“-Funktion:

		#relationship between column_a and column_b using scatter plot
		plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
		
		#correlation between column_a and column_b
		dataframe_blobdata[['<column_a>', '<column_b>']].corr()

<!---HONumber=AcomDC_0309_2016-->