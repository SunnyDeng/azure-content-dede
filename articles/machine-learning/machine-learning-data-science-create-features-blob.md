<properties 
	pageTitle="Erstellen von Features für Azure-Blob-Speicherdaten mithilfe von Panda | Microsoft Azure" 
	description="Erstellen Sie Features für in einem Azure-Blob-Container gespeicherte Daten mithilfe des Panda-Python-Pakets." 
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
	ms.date="02/05/2016" 
	ms.author="fashah;garye;bradsev" />

#Erstellen von Features für Azure-Blob-Speicherdaten mithilfe von Panda


##Einführung

In diesem Dokument wird erläutert, wie Sie Features für in einem Azure-Blob-Container gespeicherte Daten mithilfe des [Pandas](http://pandas.pydata.org/)-Python-Pakets erstellen. Nach der Beschreibung des Ladens von Daten in einen Panda-Datenrahmen wird erläutert, wie kategorische Features mit Indikatorwerten und Klassifizierungsfeatures mithilfe von Python-Skripts generiert werden.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]
Dieses **Menü** stellt eine Verknüpfung mit Themen dar, in denen beschrieben wird, wie Features für Daten in verschiedenen Umgebungen erstellt werden. Diese Aufgabe ist ein Teil des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Ein Azure-Blob-Speicherkonto erstellt haben, in dem Sie Daten speichern. Anweisungen zum Einrichten eines Kontos finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage)


## Laden der Daten in ein Pandas-DataFrame
Um ein DataSet zu untersuchen und zu bearbeiten, muss es aus der Blobquelle in eine lokale Datei heruntergeladen werden, die anschließend in ein Pandas-DataFrame geladen werden kann. Nachfolgend sehen Sie für dieses Verfahren erforderlichen Schritte:

1. Laden Sie die Daten mithilfe des Blobdiensts und folgenden Python-Beispielcodes aus dem Azure-Blob herunter. Ersetzen Sie die Variablen im Code durch die für Ihre Umgebung geltenden Werte: 

	    from azure.storage import BlobService
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
	
##<a name="blob-featuregen"></a>Generieren von Funktionen
	
In den nächsten beiden Abschnitten wird veranschaulicht, wie kategorische Features mit Indikatorwerten und Klassifizierungsfeatures mithilfe von Python-Skripts generiert werden.

###<a name="blob-countfeature"></a>Indikatorwertbasiertes Generieren von Funktionen

Kategorische Funktionen können wie folgt erstellt werden:

1. Untersuchen Sie die Verteilung der Kategoriespalte:
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. Generieren Sie Indikatorwerte für alle Spaltenwerte:

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Führen Sie die Indikatorspalte mit dem ursprünglichen DataFrame zusammen:
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Entfernen Sie die ursprüngliche Variable:

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
###<a name="blob-binningfeature"></a>Gruppenbasierte Funktionsgenerierung

Zum Generieren von klassifizierten Funktionen gehen Sie wie folgt vor:

1. Fügen Sie eine Folge von Spalten zum Klassifizieren einer numerischen Spalte hinzu:
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. Konvertieren Sie die Klassifizierung in eine Folge von booleschen Variablen:

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. Führen Sie zum Schluss die Platzhaltervariablen mit dem ursprünglichen DataFrame zusammen:

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

##<a name="sql-featuregen"></a>Zurückschreiben von Daten in das Azure-Blob und Verwenden in Azure Machine Learning

Nachdem Sie die Daten untersucht und die erforderlichen Funktionen erstellt haben, können Sie die Daten (als Stichproben oder über Funktionen) mithilfe der folgenden Schritte in ein Azure-Blob hochladen und in Azure Machine Learning verwenden. Beachten Sie, dass in Azure Machine Learning Studio ebenfalls weitere Funktionen erstellt werden können. 1. Schreiben Sie den DataFrame in eine lokale Datei:

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Laden Sie die Daten in ein Azure-Blob hoch:

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Die Daten können nun wie im folgenden Screenshot gezeigt mit dem [Reader](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)-Modul von Azure Machine Learning aus dem Blob gelesen werden:
 
![Reader-Blob](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

<!---HONumber=AcomDC_0211_2016-->