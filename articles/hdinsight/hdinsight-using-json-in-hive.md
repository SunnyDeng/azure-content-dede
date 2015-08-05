<properties
   pageTitle="Analysieren und Verarbeiten von JSON-Dokumenten mit der Struktur in HDInsight | Microsoft Azure"
   description="Informationen zum Verwenden und Analysieren von JSON-Dokumenten mit der Struktur in HDInsight"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# Verarbeiten und Analysieren von JSON-Dokumenten mit Hive in HDInsight

Erfahren Sie, wie JSON-Dateien mit Hive in HDInsight verarbeitet und analysiert werden. In diesem Tutorial wird das folgende JSON-Dokument verwendet

	{
	    "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
	    "StudentDetails": [
	        {
	            "FirstName": "Peggy",
	            "LastName": "Williams",
	            "YearJoined": 2012
	        }
	    ],
	    "StudentClassCollection": [
	        {
	            "ClassId": "89084343",
	            "ClassParticipation": "Satisfied",
	            "ClassParticipationRank": "High",
	            "Score": 93,
	            "PerformedActivity": false
	        },
	        {
	            "ClassId": "78547522",
	            "ClassParticipation": "NotSatisfied",
	            "ClassParticipationRank": "None",
	            "Score": 74,
	            "PerformedActivity": false
	        },
	        {
	            "ClassId": "78675563",
	            "ClassParticipation": "Satisfied",
	            "ClassParticipationRank": "Low",
	            "Score": 83,
	            "PerformedActivity": true
	        }
	    ]
	}

Die Datei finden Sie unter wasb://processjson@hditutorialdata.blob.core.windows.net/. Weitere Informationen zur Verwendung von Azure-Blobspeicher mit HDInsight finden Sie unter [Verwenden des HDFS-kompatiblen Azure-Blobspeichers mit Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). Wenn Sie möchten, können Sie die Datei in den Standardcontainer des Clusters kopieren.

In diesem Tutorial verwenden Sie die Hive-Konsole. Anweisungen zum Öffnen der Hive-Konsole finden Sie unter [Verwenden von Hive mit Hadoop in HDInsight über den Remotedesktop](hdinsight-hadoop-use-hive-remote-desktop.md).

##Vereinfachen von JSON-Dokumenten

Die im nächsten Abschnitt aufgeführten Methoden erfordern das JSON-Dokument in einer einzelnen Zeile. Sie müssen also das JSON-Dokument zu einer Zeichenfolge vereinfachen. Wenn Ihr JSON-Dokument bereits vereinfacht wurde, können Sie diesen Schritt überspringen und direkt mit dem nächsten Abschnitt fortfahren und die JSON-Daten analysieren.

	DROP TABLE IF EXISTS StudentsRaw;
	CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";
	
	DROP TABLE IF EXISTS StudentsOneLine;
	CREATE EXTERNAL TABLE StudentsOneLine
	(
	  json_body string
	)
	STORED AS TEXTFILE LOCATION '/json/students';
	
	INSERT OVERWRITE TABLE StudentsOneLine
	SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON 
	      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
	      GROUP BY INPUT__FILE__NAME;
	
	SELECT * FROM StudentsOneLine

Die unformatierte JSON-Datei befindet sich unter **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Die Hive-Tabelle *StudentsRaw* verweist auf das unformatierte, nicht vereinfachte JSON-Dokument.

Die Hive-Tabelle *StudentsOneLine* speichert die Daten im HDInsight-Standarddateisystem im Pfad */json/students/*.

Die INSERT-Anweisung füllt die Tabelle "StudentOneLine" mit den vereinfachten JSON-Daten.

Die SELECT-Anweisung gibt nur eine Zeile zurück.

Dies ist die Ausgabe der SELECT-Anweisung:

![Vereinfachen (Flattening) des JSON-Dokuments][image-hdi-hivejson-flatten]

##Analysieren von JSON-Dokumenten in Hive

In der Struktur sind drei verschiedene Mechanismen zum Ausführen von Abfragen bei JSON-Dokumenten verfügbar:

- Verwenden Sie die GET_JSON_OBJECT-UDF (User Defined Function, benutzerdefinierte Funktion).
- Verwenden Sie die JSON_TUPLE-UDF.
- Verwenden Sie ein benutzerdefiniertes Serialisierungs-/Deserialisierungsprogramm (SerDe).
- Schreiben Sie mit Python oder anderen Sprachen eine eigene UDF. In [diesem Artikel][hdinsight-python] finden Sie weitere Informationen zum Ausführen von Python-Code mit Hive. 

### Verwenden der GET_JSON_OBJECT-UDF
In Hive ist eine integrierte UDF namens [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) verfügbar, mit der JSON-Abfragen während der Laufzeit ausgeführt werden können. Von dieser Methode werden zwei Argumente akzeptiert: der Tabellenname/Methodenname mit dem vereinfachten JSON-Dokument sowie das JSON-Feld, das analysiert werden muss. An einem Beispiel soll verdeutlicht werden, wie diese UDF funktioniert.

Abrufen der Vor- und Nachnamen aller Studierenden

	SELECT 
	  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'), 
	  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName') 
	FROM StudentsOneLine;

Dies ist die Ausgabe, wenn diese Abfrage im Konsolenfenster ausgeführt wurde:

![UDF „get_json_object“][image-hdi-hivejson-getjsonobject]

Für die get-json_object-UDF gibt es einige Einschränkungen.

- Da für jedes Feld in der Abfrage eine erneute Analyse die Abfrage erforderlich ist, wird die Leistung beeinträchtigt.
- Von GET_JSON_OBJECT() wird die Zeichenfolgendarstellung eines Arrays zurückgegeben. Zum Umwandeln in ein Hive-Array müssen Sie reguläre Ausdrücke verwenden, um die eckigen Klammern "[" und "]" zu ersetzen, und Sie müssen die Funktion "Call Split" verwenden, um das Array zu erhalten.


Deshalb wird im Hive-Wiki die Verwendung von "json_tuple" empfohlen.

### Verwenden der JSON_TUPLE-UDF

Eine andere in Hive verfügbare UDF ist [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple). Diese Funktion ist leistungsfähiger als [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Bei dieser Methode wird mit einer einzigen Funktion ein Satz von Schlüsseln sowie eine JSON-Zeichenfolge verwendet, um ein Tupel Werte zurückzugeben. Die folgende Abfrage gibt die ID der Studierenden und die Klasse aus dem JSON-Dokument zurück:

    SELECT q1.StudentId, q1.Grade 
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Die Ausgabe des Skripts in der Hive-Konsole:

![UDF „json_tuple“][image-hdi-hivejson-jsontuple]

JSON_TUPLE verwendet die Syntax [Lateral View](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, mit der von "json_tuple" eine virtuelle Tabelle erstellt wird, indem die UDT-Funktion auf jede Zeile der Originaltabelle angewendet wird. Komplexe JSONs werden durch die wiederholte Verwendung von LATERAL VIEW zu unhandlich. Darüber hinaus kann JSON_TUPLE keine geschachtelten JSONs verarbeiten.


###Verwenden eines benutzerdefinierten Serialisierungs-/Deserialisierungsprogramms (SerDe)

SerDe ist die beste Wahl für die Analyse von geschachtelten JSON-Dokumenten. Sie können damit das JSON-Schema definieren und das Schema verwenden, um die Dokumente zu analysieren. In diesem Tutorial verwenden Sie eines der bekannteren SerDe, das von [rcongiu](https://github.com/rcongiu) entwickelt wurde.

**So verwenden Sie das benutzerdefinierte SerDe**

1. Installieren Sie [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Wählen Sie die Windows X64-Version des JDK aus, wenn Sie die Windows-Bereitstellung von HDInsight verwenden möchten.

	>[AZURE.WARNING]JDK 1.8 funktioniert mit diesem SerDe nicht.

	Nachdem die Installation abgeschlossen ist, fügen Sie eine neue Benutzerumgebungsvariable hinzu:

	1. Öffnen Sie **Erweiterte Systemeinstellungen anzeigen** auf dem Windows-Bildschirm.
	2. Klicken Sie auf **Umgebungsvariablen**.  
	3. Fügen Sie eine neue **JAVA_HOME** -Umgebungsvariable hinzu, die auf **C:\Programme\Java\jdk1.7.0_55** bzw. auf den JDK-Installationsort verweist.

	![Einrichten der richtigen Konfigurationswerte für JDK][image-hdi-hivejson-jdk]

2. Installieren Sie [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip).

	Fügen Sie Ihrem Pfad den BIN-Ordner hinzu, indem Sie die Systemsteuerung, „Systemvariablen für dieses Konto bearbeiten“ und dann „Umgebungsvariablen“ aufrufen. Im folgenden Screenshot sehen Sie, wie Sie vorgehen müssen.

	![Einrichten von Maven][image-hdi-hivejson-maven]

3. Klonen Sie das Projekt von der GitHub-Site [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Klicken Sie dazu auf die Schaltfläche „Download Zip“ wie im folgenden Screenshot gezeigt.

	![Klonen des Projekts][image-hdi-hivejson-serde]

4: Öffnen Sie den Ordner, in den Sie das Paket heruntergeladen haben, und geben Sie "mvn package" ein. Dadurch werden die notwendigen JAR-Dateien erstellt, die Sie dann in den Cluster kopieren können.

5: Wechseln Sie zum Zielordner im Stammordner, in den Sie das Paket heruntergeladen haben. Laden Sie die Datei „json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar“ zum Hauptknoten Ihres Clusters hoch. Ich speichere diese Datei normalerweise im Strukturbinärordner „C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin“ oder ähnlich.
 
6: Geben Sie an der Hive-Eingabeaufforderung "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar" ein. Da sich die JAR-Datei in diesem Fall im Ordner „C:\apps\dist\hive-0.13.x\bin“ befindet, kann die JAR-Datei mit dem Namen direkt hinzugefügt werden wie unten dargestellt:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

	![Adding JAR to your project][image-hdi-hivejson-addjar]

Jetzt können Sie das SerDe verwenden, um Abfragen für das JSON-Dokument auszuführen.

Die folgende Anweisung erstellt eine Tabelle mit einem definierten Schema:

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

So listen Sie Vor- und Nachnamen der Studierenden auf

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Dies ist das Ergebnis aus der Strukturkonsole:

![SerDe-Abfrage 1][image-hdi-hivejson-serde_query1]

So berechnen Sie die Summe der Bewertungen im JSON-Dokument

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
Von der obigen Abfrage wird die UDF [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) verwendet, um das Array von Bewertungen erweitern und summieren zu können.

Dies ist die Ausgabe aus der Strukturkonsole:

![SerDe-Abfrage 2][image-hdi-hivejson-serde_query2]

So finden Sie die Fächer, in denen ein bestimmter Studierender über 80 Punkte erhalten hat: SELECT jt.StudentClassCollection.ClassId FROM json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
Von der genannten Abfrage wird ein Strukturarray zurückgegeben (im Gegensatz dazu wird von „get_json_object“ eine Zeichenfolge zurückgegeben).

![SerDe-Abfrage 3][image-hdi-hivejson-serde_query3]

Wenn falsch formatiertes JSON übersprungen werden soll, geben Sie entsprechend der [Wikiseite](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) dieses SerDe den folgenden Code ein:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##Zusammenfassung
Es lässt sich zusammenfassend feststellen, dass der JSON-Operatortyp in der Struktur, den Sie auswählen, von Ihrem Szenario abhängt. Wenn in einem einfachen JSON-Dokument nur ein einziges Feld durchsucht werden soll, können Sie die Struktur-UDF „get_json_object“ verwenden. Wenn mehrere Suchschlüssel vorliegen, können Sie die UDF „json_tuple“ verwenden. Bei geschachtelten Dokumenten müssen Sie das JSON-SerDe verwenden.

Verwandte Artikel

- [Verwenden von Hive und HiveQL mit Hadoop in HDInsight zum Analysieren einer Apache Log4j-Beispieldatei](hdinsight-use-hive.md)
- [Analysieren von Flugverspätungsdaten mit Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analysieren von Twitter-Daten mit Hive in HDInsight](hdinsight-analyze-twitter-data.md)
- [Ausführen eines Hadoop-Auftrags mit DocumentDB und HDInsight](documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
 

<!---HONumber=July15_HO4-->