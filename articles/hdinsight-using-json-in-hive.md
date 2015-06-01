<properties
   pageTitle="Analysieren und Verarbeiten von JSON-Dokumenten mit der Struktur in HDInsight | Microsoft Azure"
   description="Informationen zum Verwenden und Analysieren von JSON-Dokumenten mit der Struktur in HDInsight "
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
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Erfahren Sie, wie Sie JSON-Dokumente in der Struktur verarbeiten und analysieren

JSON gehört zu den meistverwendeten Formaten im Internet. In diesem Lernprogramm erfahren Sie, wie JSON-Dokumente in der Struktur verwendet und dann analysiert werden.

## JSON-Beispiel

Es folgt ein Beispiel. Nehmen wir an, dass wir über das unten gezeigte JSON-Dokument verfügen. Unser Ziel ist es, dieses JSON-Dokument analysieren zu können, um dann Abfragen wie Suchwerte für Schlüssel, Aggregationen usw. ausführen zu können.

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

## Vereinfachen durch Wertezuordnung (Flattening) bei JSON-Dokumenten (dieser Schritt ist nur bei Pretty JSON erforderlich)

Bevor ein Strukturoperator zum Analysieren verwendet wird, muss das JSON-Dokument vorab verarbeitet werden, damit es von der Struktur verwendet werden kann.

Bei allen im nächsten Abschnitt aufgeführten Optionen wird davon ausgegangen, dass das JSON-Dokument eine einzelne Zeile aufweist. Beim JSON-Dokument muss also in eine einzelne Zeichenfolge vereinfacht werden, damit es von den Strukturoperatoren verwendet werden kann.

**Wenn Ihr JSON-Dokument bereits vereinfacht ist und nur eine Zeile aufweist, können Sie diesen Schritt überspringen, direkt mit dem nächsten Abschnitt fortfahren und die JSON-Daten analysieren.**

Angenommen sei, dass das nicht vereinfachte JSON-Dokument mit mehreren Zeilen in Ihrem Azure-Blobspeicher im Standardcontainer im Ordner */json/input/* vorhanden ist. Durch den folgenden Code wird eine Tabelle „jsonexample“ erstellt, in der auf das nicht vereinfachte JSON-Originaldokument verwiesen wird.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

Als Nächstes erstellen Sie eine neue Tabelle namens „one_line_json“, in der auf das vereinfachte JSON-Dokument verwiesen wird. Diese Tabelle wird in Ihrem Azure-Blobspeicher im Standardcontainer im Ordner */json/Temp/* gespeichert.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

Schließlich füllen Sie diese Tabelle mit den vereinfachten JSON-Daten auf.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

Wenn Sie nun die neu erstellte Tabelle „one_line_json“ ansehen, sollte sie das JSON-Dokument in einer einzelnen Zeile enthalten.

    select * from one_line_json;

Dies ist die Ausgabe dieser Abfrage:

![Vereinfachen (Flattening) des JSON-Dokuments][image-hdi-hivejson-flatten]

## Analyseoptionen bei JSON-Dokumenten in der Struktur

Nun liegt das JSON-Dokument in einer Tabelle mit einer einzelnen Spalte vor, und für diese Daten jetzt mit der Struktur Abfragen ausgeführt werden. In der Struktur sind drei verschiedene Mechanismen zum Ausführen von Abfragen bei JSON-Dokumenten verfügbar:

1.	Mithilfe der UDF (User Defined Function, benutzerdefinierte Funktion) „get_json_object“
2.	Mithilfe der UDF „json_tuple“
3.	Mithilfe eines benutzerdefinierten Serialisierungs-/Deserialisierungsprogramms (SerDe)

Im Folgenden soll jede Methode genauer betrachtet werden.

## UDF „get_json_object“
In der Struktur ist eine integrierte UDF namens [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) verfügbar, mit der JSON-Abfragen während der Laufzeit ausgeführt werden können. Von dieser Methode werden zwei Argumente akzeptiert: der Tabellenname/Methodenname mit dem vereinfachten JSON-Dokument sowie das JSON-Feld, das analysiert werden muss. An einem Beispiel soll verdeutlicht werden, wie diese UDF funktioniert.

Abrufen der Vor- und Nachnamen aller Studierenden

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

Dies ist die Ausgabe, wenn diese Abfrage im Konsolenfenster ausgeführt wurde:

![UDF „get_json_object“][image-hdi-hivejson-getjsonobject]

Bei dieser UDF gibt es einige Einschränkungen.


- Zu den wichtigsten Einschränkungen gehört, dass diese UDF weniger leistungsfähig ist, da für jedes Feld in der Abfrage ein erneutes Analysieren der Abfrage erforderlich ist, wodurch die Leistung beeinträchtigt wird. 
- Zweitens wird von get_json_object() die Zeichenfolgendarstellung eines Arrays zurückgegeben. Zum Umwandeln in ein Strukturarray müssen Sie reguläre Ausdrücke verwenden, um die eckigen Klammern „[“ und „]“ zu ersetzen, und Sie müssen die Funktion „Call Split“ verwenden, um das Array zu erhalten.


Aus diesem Grund wird vom Strukturwiki empfohlen, die im Folgenden erläuterte UDF „json_tuple“ zu verwenden.


## UDF „json_tuple“

Die andere in der Struktur verfügbare UDF namens [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) ist leistungsfähiger als [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Bei dieser Methode wird mit einer einzigen Funktion ein Satz von Schlüsseln sowie eine JSON-Zeichenfolge verwendet, um ein Tupel Werte zurückzugeben.  An einem Beispiel soll verdeutlicht werden, wie diese UDF funktioniert.

Nun sollen die Matrikelnummern und Noten aus dem JSON-Dokument abgerufen werden.

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

Wir verwenden die Syntax [LATERAL VIEW](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in der Struktur, mit der von json_tuple eine virtuelle Tabelle erstellt wird, indem jede UDT-Funktion auf jede Zeile der Originaltabelle angewendet wird.

Dies ist die Ausgabe des Skripts in der Strukturkonsole:

![UDF „json_tuple“][image-hdi-hivejson-jsontuple]

Zu den größten Nachteilen dieser UDF gehört, dass komplexe JSONs durch die wiederholte Verwendung von LATERAL VIEW schwierig zu handhaben sind. Geschachtelte JSONs können von dieser UDF nicht verarbeitet werden.

## Benutzerdefiniertes Serialisierungs-/Deserialisierungsprogramm (SerDe)

Zur Analyse geschachtelter JSON-Dokumente ist SerDe die **beste Wahl**, weil das JSON-Schema definiert werden kann und Abfragen dadurch sehr einfach ausgeführt werden können.

Im Folgenden soll dargestellt werden, wie eins der bekannteren SerDe, entwickelt von [rcongiu](https://github.com/rcongiu), verwendet wird.

Schritt 1: Stellen Sie sicher, dass das [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) installiert ist (Hinweis: JDK 1.8 funktioniert mit diesem SerDe nicht).


- Wenn Sie die Windows-Bereitstellung von HDInsight verwenden möchten, wählen Sie die x64-Windows-Version des JDK aus.
- Rufen Sie nach Abschluss der Installation die Systemsteuerung und dann „Umgebungsvariablen hinzufügen“ auf, und fügen Sie eine neue JAVA_HOME-Umgebungsvariable hinzu, von der auf C:\\Programme\\Java\\jdk1.7.0_55 bzw. auf den Speicherort Ihres JDK verwiesen wird. In den folgenden Screenshots sehen Sie, wie die Umgebungsvariable festgelegt wird.

![Einrichten der richtigen Konfigurationswerte für JDK][image-hdi-hivejson-jdk]

Schritt 2: Rufen Sie [diesen](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) Link auf, und laden Sie Maven 3.3.1 herunter. Entpacken Sie das Archiv dort, wo Sie die Binärdateien speichern möchten. Im aktuellen Beispiel werden sie in C:\\Programme\\Maven entpackt. Fügen Sie Ihrem Pfad den BIN-Ordner hinzu, indem Sie die Systemsteuerung, „Systemvariablen für dieses Konto bearbeiten“ und dann „Umgebungsvariablen“ aufrufen. Im folgenden Screenshot sehen Sie, wie Sie vorgehen müssen.

![Einrichten von Maven][image-hdi-hivejson-maven]

Schritt 3: Klonen Sie das Projekt von der GitHub-Site [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Klicken Sie dazu auf die Schaltfläche „Download Zip“ wie im folgenden Screenshot gezeigt.

![Klonen des Projekts][image-hdi-hivejson-serde]

Schritt 4: Öffnen Sie den Ordner, in den Sie das Paket heruntergeladen haben, und geben Sie „mvn package“ ein. Dadurch werden die notwendigen JAR-Dateien erstellt, die Sie dann in den Cluster kopieren können.

Schritt 5: Öffnen Sie als Nächstes den Zielordner im Stammordner, in den Sie das Paket heruntergeladen haben. Laden Sie die Datei „json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar“ zum Hauptknoten Ihres Clusters hoch. Ich speichere diese Datei normalerweise im Strukturbinärordner „C:\\apps\\dist\\hive-0.13.0.2.1.11.0-2316\\bin“ oder ähnlich.
 
Schritt 6: Geben Sie in der Struktureingabeaufforderung „add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar“ ein. Da sich die JAR-Datei in diesem Fall im Ordner „C:\\apps\\dist\\hive-0.13.x\\bin“ befindet, kann die JAR-Datei mit dem Namen direkt hinzugefügt werden wie unten dargestellt:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![Hinzufügen von JAR zum Projekt][image-hdi-hivejson-addjar]

Jetzt kann die SerDe verwendet werden, um Abfragen des JSON-Dokuments auszuführen.

Zunächst wird die Tabelle mit dem Schema des JSON-Dokuments erstellt. Beachten Sie, dass viel komplexere Typen einschließlich Karten, Arrays und Strukturen verarbeitet werden können. Geben Sie in die Strukturkonsole den folgenden Code ein, damit eine Tabelle namens „json_table“ erstellt wird, von der das JSON-Dokument entsprechend dem nachstehenden Schema gelesen werden kann:

    drop table json_table;
    create external table json_table (
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
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

Nun wurde das Schema definiert. Führen wir einige Beispiele aus, um zu sehen, wie wir mithilfe der Struktur das JSON-Dokument abfragen können:

a) Auflisten der Vor- und Nachnamen der Studierenden

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Dies ist das Ergebnis aus der Strukturkonsole:

![SerDe-Abfrage 1][image-hdi-hivejson-serde_query1]

b) Von dieser Abfrage wird die Bewertungssumme des JSON-Dokuments berechnet.

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
Von der obigen Abfrage wird die UDF [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) verwendet, um das Array von Bewertungen erweitern und summieren zu können.

Dies ist die Ausgabe aus der Strukturkonsole:

![SerDe-Abfrage 2][image-hdi-hivejson-serde_query2]

c) Suchen der Fächer, bei denen ein gegebener Studierender über 80 Punkte erhalten hat: Select jt.StudentClassCollection.ClassId from json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
Von der genannten Abfrage wird ein Strukturarray zurückgegeben (im Gegensatz dazu wird von „get_json_object“ eine Zeichenfolge zurückgegeben).

![SerDe-Abfrage 3][image-hdi-hivejson-serde_query3]

Wenn falsch formatiertes JSON übersprungen werden soll, geben Sie entsprechend der [Wikiseite](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) dieses SerDe den folgenden Code ein:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


## Weitere Optionen
Zusätzlich zu den nachfolgend aufgeführten Optionen können Sie mithilfe von Python oder anderen Sprachen auch eigenen Code entsprechend Ihrem Szenario erstellen. Wenn Ihr Python-Skript fertiggestellt ist, lesen Sie [diesen Artikel][hdinsight-python] zum Ausführen von eigenem Python-Code mit der Struktur.

## Zusammenfassung
Es lässt sich zusammenfassend feststellen, dass der JSON-Operatortyp in der Struktur, den Sie auswählen, von Ihrem Szenario abhängt. Wenn in einem einfachen JSON-Dokument nur ein einziges Feld durchsucht werden soll, können Sie die Struktur-UDF „get_json_object“ verwenden. Wenn mehrere Suchschlüssel vorliegen, können Sie die UDF „json_tuple“ verwenden. Bei geschachtelten Dokumenten müssen Sie das JSON-SerDe verwenden.

Wir bei HDInsight arbeiten daran, Ihnen das systemeigene Verwenden von weiteren Formaten in der Struktur zu vereinfachen. Dieses Lernprogramm wird mit weiteren Details aktualisiert, sobald diese verfügbar sind.

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

<!--HONumber=52-->
