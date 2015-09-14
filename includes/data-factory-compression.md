### Unterstützung für die Komprimierung  
Die Verarbeitung großer Datenmengen kann zu E/A- und Netzwerkengpässen führen. Aus diesem Grund kann die Komprimierung von Daten in Speichern nicht nur die Datenübertragung im Netzwerk beschleunigen und Speicherplatz sparen, sondern auch erhebliche Leistungssteigerungen bei der Verarbeitung von Big Data bewirken. Zu diesem Zeitpunkt wird die Komprimierung für dateibasierte Datenspeicher wie etwa Azure-Blobs oder das lokale Dateisystem unterstützt.

Um die Komprimierung für ein Dataset anzugeben, verwenden Sie im JSON-Dataset die Eigenschaft für die **Komprimierung** wie im folgenden Beispiel:

	{  
		"name": "AzureBlobDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureBlob",  
	    	"linkedServiceName": "StorageLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Beachten Sie, dass der Abschnitt für die **Komprimierung** zwei Eigenschaften enthält:
  
- **Typ:** Der Komprimierungscodec, z. B. **GZIP**, **Deflate** oder **BZIP2**.  
- **Ebene:** Das Komprimierungsverhältnis, z. B. **Optimal** oder **Schnellstes**. 
	- **Schnellstes:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist. 
	- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. 
	
	Weitere Informationen finden Sie im Thema [Komprimierungsstufe](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Angenommen, das obige Beispieldataset wird als Ausgabe eines Kopiervorgangs verwendet. Der Kopiervorgang komprimiert die Ausgabedaten mit dem GZIP-Codec bei optimalem Verhältnis und schreibt die komprimierten Daten anschließend in die Datei „pagecounts.csv.gz“ im Azure-Blobspeicher.

Wenn Sie die Komprimierungseigenschaft in einem JSON-Eingabedataset angeben, kann die Pipeline komprimierte Daten aus der Quelle lesen. Bei Angabe der Eigenschaft in einem JSON-Ausgabedataset kann der Kopiervorgang komprimierte Daten am Ziel schreiben. Es folgen einige Beispielszenarios:

- Lesen Sie GZIP-komprimierte Daten aus einem Azure-Blob, dekomprimieren Sie sie, und schreiben Sie die resultierenden Daten in eine Azure SQL-Datenbank. In diesem Fall definieren Sie die Azure-Blob-Eingabedatasets mit der JSON-Komprimierungseigenschaft. 
- Lesen Sie Daten aus einer Nur-Text-Datei aus einem lokalen Dateisystem, komprimieren Sie sie mithilfe des GZip-Formats, und schreiben Sie die komprimierten Daten in einen Azure-Blob. In diesem Fall definieren Sie ein Azure-Blob-Ausgabedataset mit der JSON-Komprimierungseigenschaft.  
- Lesen Sie GZIP-komprimierte Daten aus einem Azure-Blob, dekomprimieren Sie sie, komprimieren Sie sie mit BZIP2, und schreiben Sie die resultierenden Daten in einen Azure-Blob. In diesem Fall definieren Sie das Azure-Blob-Eingabedataset mit dem Komprimierungstyp GZIP und das Ausgabedataset mit dem Komprimierungstyp BZIP2.   

<!---HONumber=September15_HO1-->