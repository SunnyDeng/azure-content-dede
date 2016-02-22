<properties 
	pageTitle="Erstellen von Features für Daten in SQL Server mithilfe von SQL und Python | Microsoft Azure" 
	description="Verarbeiten von Daten aus SQL Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="bradsev;fashah;garye" />


# Erstellen von Features für Daten in SQL Server mithilfe von SQL und Python

## Einführung

Dieses Dokument veranschaulicht das Generieren von Features für Daten in einer SQL Server-VM in Azure, aus denen Algorithmen effizient lernen können. Dies kann mithilfe von SQL oder über die Verwendung einer Programmiersprache wie Python erreicht werden. Hier werden beide Methoden eingesetzt.

> [AZURE.NOTE] Für ein praktisches Beispiel können Sie das [NYC Taxi-DataSet](http://www.andresmh.com/nyctaxitrips/) verwenden. Eine vollständige Beschreibung finden Sie im IPython Notebook-Beispiel unter [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb).

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Dieses **Menü** stellt eine Verknüpfung mit Themen dar, in denen beschrieben wird, wie Features für Daten in verschiedenen Umgebungen erstellt werden. Diese Aufgabe ist ein Teil des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage)
* Ihre Daten sind in SQL Server gespeichert. Wenn dies nicht der Fall ist, finden Sie unter [Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning](machine-learning-data-science-move-sql-azure.md) Anweisungen zum Verschieben von Daten.


##<a name="sql-featuregen"></a>Generieren von Features mit SQL

In diesem Abschnitt werden Methoden zum Generieren von Funktionen mithilfe von SQL beschrieben.

1. [Anzahlbasierte Funktionsgenerierung](#sql-countfeature)
2. [Gruppenbasierte Funktionsgenerierung](#sql-binningfeature)
3. [Einführen von Funktionen aus einer einzelnen Spalte](#sql-featurerollout)


> [AZURE.NOTE] Wenn Sie zusätzliche Funktionen generieren, können Sie diese als Spalten in der vorhandenen Tabelle hinzufügen oder eine neue Tabelle mit den zusätzlichen Funktionen und einem Primärschlüssel erstellen, die dann mit der ursprünglichen Tabelle zusammengeführt werden kann.

###<a name="sql-countfeature"></a>Anzahlbasierte Funktionsgenerierung

In diesem Abschnitt werden zwei Methoden zur Generierung von Anzahlfunktionen demonstriert. Die erste Methode verwendet eine bedingte Summe und die zweite die "where"-Klausel. Diese können dann mit der ursprünglichen Tabelle (über Primärschlüsselspalten) zusammengeführt werden, um die Anzahlfunktionen zusammen mit den ursprünglichen Daten verwenden zu können.

	select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

	select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
	where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Gruppenbasierte Funktionsgenerierung

Das folgende Beispiel zeigt, wie Sie klassifizierte Funktionen erstellen, indem Sie eine numerische Spalte, die stattdessen als Funktion verwendet wird, klassifizieren (mit 5 Klassifizierungen):

	`SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Einführen von Funktionen aus einer einzelnen Spalte

In diesem Abschnitt wird gezeigt, wie Sie eine einzelne Spalte in eine Tabelle einführen, um zusätzliche Funktionen zu generieren. Im Beispiel wird davon ausgegangen, dass die Tabelle, aus der Sie Funktionen generieren, die Spalten "latitude" und "longitude" enthält.

Es folgt eine kurze Einführung in Positionsdaten mit Längen- und Breitengrad (aus Stackoverflow: `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Dies ist für das Verständnis hilfreich, bevor Sie die Positionsfelder verwenden:

- Das Vorzeichen gibt an, ob sich die Position im Norden, Süden, Osten oder Westen auf dem Globus befindet.
- Ein Hunderterwert ungleich null gibt an, dass der Längengrad und nicht der Breitengrad verwendet wird!
- Die Zehnerstelle gibt eine Position auf ca. 1.000 km Genauigkeit an. Damit verfügen Sie über nützliche Informationen zum Kontinent oder Ozean.
- Die Einheitenstelle (ein Dezimalzeichen) gibt die Position auf etwa 111 km (60 nautische Meilen, etwa 69 Meilen) genau an. Damit wissen Sie ungefähr, welches Land gemeint ist.
- Die erste Dezimalstelle gibt einen Wert von etwa 11,1 km Genauigkeit an: Sie können damit die Position von einer großen Stadt von der einer benachbarten großen Stadt unterscheiden.
- Die zweite Dezimalstelle bietet eine Genauigkeit von ca. 1,1 km: Damit können Sie Dörfer voneinander trennen.
- Die dritte Dezimalstelle ist auf etwa 110 m genau: Sie können so große landwirtschaftliche Felder oder Industriegebiete identifizieren.
- Die vierte Dezimalstelle ist auf etwa 11 m genau: Sie können so einzelne Grundstücke unterscheiden. Dies ist vergleichbar mit der typischen Genauigkeit eines nicht korrigierten GPS-Geräts ohne Störungen.
- Die fünfte Dezimalstelle ist auf etwa 1,1 m genau: Damit können Sie einzelne Strukturen voneinander unterscheiden. Eine Genauigkeit auf dieser Stufe lässt sich mit kommerziellen GPS-Geräten nur mit einer differenziellen Korrektur erreichen.
- Die sechste Dezimalstelle bietet eine Genauigkeit von etwa 0,11 m: Damit können Sie Strukturen detailgetreu erkennen, um Landschaften zu planen oder Straßen zu bauen. Dies sollte mehr als ausreichend für die Nachverfolgung der Bewegungen von Gletschern und Flüssen sein. Erreicht wird diese Genauigkeit nur durch umfangreiche GPS-Maßnahmen, z. B. differenziell korrigiertes GPS.

Die Positionsinformationen können wie folgt in Funktionen umgewandelt werden, wobei die Informationen zu Region, Standort und Stadt getrennt werden. Beachten Sie, dass Sie auch einen REST-Endpunkt wie die Bing Maps-API unter `https://msdn.microsoft.com/library/ff701710.aspx` aufrufen können, um Informationen über Region/Bezirk abzurufen.

	select 
		<location_columnname>
		,round(<location_columnname>,0) as l1		
		,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end 	
		,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end 	
		,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end 	
		,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end 	
		,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end 	
		,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end 	
	from <tablename>

Die oben beschriebenen positionsbasierten Funktionen können dann wie oben beschrieben zum Generieren weiterer Anzahlfunktionen verwendet werden.


> [AZURE.TIP] Sie können die Datensätze programmgesteuert mithilfe Ihrer bevorzugten Sprache einfügen. Möglicherweise müssen Sie die Daten in Blöcken einfügen, um die Effizienz des Schreibvorgangs zu verbessern. [Ein pyodbc-Beispiel dazu finden Sie hier](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
 

> [AZURE.TIP] Eine weitere Möglichkeit zum Einfügen von Daten in die Datenbank bietet das [Hilfsprogramm BCP](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning

Die neu generierte Funktion kann als Spalte einer vorhandenen Tabelle hinzugefügt oder in einer neuen Tabelle gespeichert und für Machine Learning mit der ursprünglichen Tabelle zusammengeführt werden. Sie können wie unten dargestellt mit dem [Reader](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)-Modul in Azure ML Funktionen generieren oder, falls sie bereits vorhanden sind, darauf zugreifen:

![azureml-Reader](./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png)

##<a name="python"></a>Mit einer Programmiersprache wie Python

Die Verwendung von Python zum Generieren von Features mit Daten in SQL Server ähnelt der Datenverarbeitung in Azure-Blobs mit Python, die unter [Verarbeiten von Azure-Blob-Daten in Ihrer Data Science-Umgebung](machine-learning-data-science-process-data-blob.md) beschrieben ist. Die Daten müssen aus der Datenbank in ein Pandas-DataFrame geladen werden, um dann weiter verarbeitet werden zu können. In diesem Abschnitt werden das Herstellen einer Verbindung mit der Datenbank und das Laden der Daten in den DataFrame beschrieben.

Das folgende Format für die Verbindungszeichenfolge kann verwendet werden, um aus Python mit "pyodbc" eine Verbindung mit einer SQL Server-Datenbank herzustellen (ersetzen Sie "servername", "dbname", "username" und "password" durch Ihre Daten):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Die [Pandas-Bibliothek](http://pandas.pydata.org/) in Python bietet eine Vielzahl an Datenstrukturen und Datenanalysetools für die Datenbearbeitung zur Python-Programmierung. Der folgende Code liest die aus einer SQL Server-Datenbank zurückgegebenen Ergebnisse in ein Pandas-DataFrame ein:

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Sie können nun mit dem Pandas-DataFrame arbeiten, wie unter [Erstellen von Features für Azure-Blob-Speicherdaten mithilfe von Panda](machine-learning-data-science-create-features-blob.md) beschrieben.

 

<!---HONumber=AcomDC_0211_2016-->