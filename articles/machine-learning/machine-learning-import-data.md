<properties
	pageTitle="Importieren von Daten in Machine Learning Studio | Microsoft Azure"
	description="Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio Erfahren Sie, welche Datentypen und Datenformate unterstützt werden."
	keywords="import data,data format,data types,data sources,training data"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/19/2015"
	ms.author="garye" />


#Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio

Bei der Entwicklung einer Predictive Analytics-Lösung in Azure Machine Learning Studio trainieren Sie Ihr Modell mit Daten, die für Ihren Problembereich repräsentativ sind. In diesem Lernprogramm wird das Importieren von Daten aus verschiedenen Datenquellen zum Trainieren des Modells in Machine Learning Studio veranschaulicht. Außerdem erfahren Sie, welche Datenformate unterstützt werden.

Es gibt eine Reihe von Beispieldatasets in Machine Learning Studio, die Sie für diesen Zweck verwenden können (siehe [Verwenden von Beispieldatasets in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)). Sie können jedoch auch Ihre eigenen Daten für Ihre Experimente in Machine Learning Studio importieren.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

Um Ihre eigenen Daten in Machine Learning Studio zu verwenden, können Sie im Voraus eine Datei von Ihrer lokalen Festplatte hochladen, um ein Datasetmodul in Ihrem Arbeitsbereich zu erstellen. Sie können aber auch auf Daten aus einer von mehreren Onlinedatenquellen zugreifen, während Ihr Experiments mit dem [Reader][reader]-Modul ausgeführt wird:

- Azure-Blobspeicher, Tabelle oder SQL-Datenbank
- Hadoop über HiveQL
- Web-URL über HTTP
- Anbieter für Datenfeeds

Machine Learning Studio ist für die Nutzung von rechteckigen oder tabellarischen Daten vorgesehen, z. B. Textdaten, bei denen es sich um mit Trennzeichen versehene oder strukturierte Daten aus einer Datenbank handelt. Unter Umständen können nicht rechteckige Daten verwendet werden.

Ihre Daten sollten relativ sauber ist. Sie sollten also Probleme wie Zeichenfolgen ohne Anführungszeichen beheben, bevor Sie die Daten in das Experiment hochladen.

Es stehen jedoch in Machine Learning Studio Module zur Verfügung, mit denen Sie einige Änderungen an Daten im Experiment ausführen können. Abhängig vom verwendeten Machine Learning-Algorithmen müssen Sie möglicherweise entscheiden, wie Sie mit Datenstrukturproblemen wie fehlenden Werten und geringen Datenmengen umgehen möchten. Es gibt Module, die dabei helfen können. Suchen Sie im Abschnitt **Data Transformation** der Modulpalette nach Modulen, die diese Funktionen ausführen.

Zu jedem Zeitpunkt des Experiments können Sie die Daten anzeigen oder herunterladen, die von einem Modul erstellt werden, indem Sie mit der rechten Maustaste auf den Ausgabeport klicken. Abhängig vom Modul können verschiedene Downloadoptionen verfügbar sein, oder Sie können die Daten in Ihrem Webbrowser in Machine Learning Studio anzeigen.


## Datenformate

Sie können eine Reihe von Datentypen in das Experiment importieren, je nachdem, welchen Mechanismus Sie zum Importieren von Daten verwenden und woher die Daten stammen:

- Nur-Text (.txt)
- Durch Trennzeichen getrennte Werte (CSV) mit einer Kopfzeile (.csv) oder ohne (.nh.csv)
- Durch Tabulator getrennte Werte (TSV) mit einer Kopfzeile (.tsv) oder ohne (.nh.tsv)
- Hive-Tabelle
- SQL-Datenbanktabelle
- OData-Werte
- SVMLight-Daten (.svmlight) (Formatinformationen finden Sie in der [SVMLight-Definition](http://svmlight.joachims.org/) (in englischer Sprache))
- ARFF-Daten (Attribute Relation File Format) (.arff) (Formatinformationen finden Sie in der [ARFF-Definition](http://weka.wikispaces.com/ARFF) (in englischer Sprache))
- ZIP-Datei (.zip)
- R-Objektdatei oder -Arbeitsbereichsdatei (.RData)

Wenn Sie Daten in einem Format wie z. B. ARFF importieren, die Metadaten enthalten, verwendet Machine Learning Studio diese Metadaten, um die Kopfzeile und den Datentyp der einzelnen Spalten zu definieren. Wenn Sie z. B. Daten im TSV- oder CSV-Format importieren, das diese Metadaten nicht enthält, leitet Machine Learning Studio den Datentyp für jede Spalte ab, indem Stichproben der Daten entnommen werden. Wenn die Daten zudem keine Spaltenüberschriften aufweisen, gibt Machine Learning Studio Standardnamen an. Sie können die Überschriften und Datentypen für Spalten mit dem [Metadaten-Editor][metadata-editor] explizit angeben oder ändern.

Die folgenden Datentypen werden von Machine Learning Studio erkannt:

- String
- Integer
- Double
- Boolean
- DateTime
- TimeSpan

Machine Learning Studio verwendet einen internen Datentyp mit dem Namen *Data Table* zum Übergeben von Daten zwischen Modulen. Mit dem Modul [Convert to Dataset][convert-to-dataset] können Sie Daten explizit in das Data Table-Format konvertieren. Jedes Modul, das andere Formate als Data Table akzeptiert, konvertiert die Daten im Hintergrund vor der Übergabe an das nächste Modul in das Data Table-Format. Bei Bedarf können Sie das Data Table-Format mit anderen Konvertierungsmodulen wieder in die Formate CSV, TSV, ARFF oder SVMLight konvertieren. Suchen Sie im Abschnitt **Data Format Conversions** der Modulpalette nach Modulen, die diese Funktionen ausführen.


## Importieren von Daten aus einer lokalen Datei

Sie können wie folgt Daten von einer lokalen Festplatte importieren:

1. Klicken Sie unten im Machine Learning Studio-Fenster auf **+NEW**.
2. Wählen Sie **DATASET** und **FROM LOCAL FILE** aus.
3. Navigieren Sie im Dialogfeld **Upload a new dataset** zu der Datei, die Sie hochladen möchten.
4. Geben Sie einen Namen ein, identifizieren Sie den Datentyp, und geben Sie optional eine Beschreibung ein. Eine Beschreibung wird empfohlen: Damit können Sie beliebige Eigenschaften zu den Daten zu erfassen, die Sie bedenken möchten, wenn Sie die Daten in der Zukunft verwenden.
5. Mit dem Kontrollkästchen **This is the new version of an existing dataset** können Sie ein vorhandenes Dataset mit neuen Daten aktualisieren. Aktivieren Sie dieses Kontrollkästchen, und geben Sie dann den Namen eines vorhandenen Datasets ein.

Während des Uploads sehen Sie eine Meldung, dass die Datei hochgeladen wird. Die Dauer für das Hochladen hängt von der Größe Ihrer Daten und der Geschwindigkeit der Verbindung mit dem Dienst ab. Wenn Sie wissen, dass das Hochladen der Datei eine lange Zeit dauern kann, können Sie andere Dinge in Machine Learning Studio erledigen, während Sie warten. Durch das Schließen des Browsers werden die Daten jedoch nicht hochgeladen.

Sobald Ihre Daten hochgeladen wurden, werden sie in einem Datasetmodul gespeichert und stehen für alle Experimente im Arbeitsbereich zur Verfügung. Sie finden das Dataset und alle vorab geladenen Beispieldatasets in der Liste **Saved Datasets** in der Modulpalette, wenn Sie ein Experiment bearbeiten.


## Zugreifen auf Onlinedaten mit dem Reader-Modul

Mit dem [Reader][reader]-Modul in Ihrem Experiment können Sie auf Daten von verschiedenen Onlineressourcen zugreifen, während das Experiment ausgeführt wird. Da auf diese Trainingsdaten zugegriffen wird, während das Experiment ausgeführt wird, stehen sie nur in einem Experiment zur Verfügung (im Gegensatz zu Datasetmodulen, die für alle Experimente im Arbeitsbereich verfügbar sind).

Nach dem Hinzufügen des [Reader][reader]-Moduls zum Experiment wählen Sie die **Datenquelle** aus und ermöglichen dann den Zugriff auf Informationen über Modulparameter. Beispiel: Wenn Sie **Web URL via HTTP** auswählen, geben Sie die Quell-URL und das Datenformat an. Wenn Sie auf Trainingsdaten aus dem Azure-Speicher oder von HDInsight (mit einer Hive-Abfrage) zugreifen, geben Sie die entsprechenden Kontoinformationen und den Speicherort der Daten an.

> [AZURE.NOTE]Dieser Artikel bietet allgemeine Informationen über das [Reader][reader]-Modul. Ausführlichere Informationen zu den Datentypen, auf die Sie zugreifen können, zu Formaten, Parametern und Antworten auf häufige Fragen finden Sie im Modulreferenzthema zum [Reader][reader]-Modul.


### Abrufen von Daten aus Azure

Sie können aus drei Azure-Datenquellen importieren:

- **Azure-Blobspeicher** – Wenn Sie das ARFF-Format für die Speicherung verwenden, werden Spalten mit den Kopfzeilenmetadaten zugeordnet. Wenn Sie das TSV- oder CSV-Format verwenden, werden Zuordnungen über Stichproben der Spaltendaten abgeleitet.
- **Azure-Tabellenspeicher** – Das [Reader][reader]-Modul scannt die Daten, um Spaltendatentypen zu identifizieren. Wenn Ihre Daten relativ homogen und vorhersagbar sind, können Sie die Anzahl der Zeilen begrenzen, die gescannt werden.
- **Azure SQL-Datenbank** – Das [Reader][reader]-Modul nutzt die SQL Azure Transact-Client-API zum Importieren von Daten mithilfe einer Abfrage, die Sie bereitstellen.

Für den Blob- und Tabellenspeicher geben Sie eine Shared Access Signature-URI (SAS-URI) oder Azure-Speicherkontoinformationen für den Zugriff auf die Daten an. Für eine Azure SQL-Datenbank geben Sie die Datenbank und Kontoinformationen sowie eine Datenbankabfrage an, die die Daten identifiziert, die Sie importieren möchten.

### Abrufen von Daten aus dem Web

Sie können das [Reader][reader]-Modul verwenden, um Trainingsdaten von einer Web- oder FTP-Site zu lesen. Sie müssen Folgendes angeben:

- Eine vollständige HTTP-URL-Adresse einer Datei
- Das Datenformat der Datei (CSV, TSV, ARFF oder SvmLight)
- Bei CSV- oder TSV-Dateien, ob die erste Zeile in der Datei eine Kopfzeile ist

### Abrufen von Daten aus Hadoop

Sie können das [Reader][reader]-Modul mit der HiveQL-Abfragesprache verwenden, um die Trainingsdaten aus verteilten Speichern zu lesen. Sie müssen die Hive-Datenbankabfrage und Benutzerzugriffsinformationen auf dem HCatalog-Server bereitstellen. Sie müssen auch angeben, ob die Daten in einem Hadoop Distributed File System (HDFS) oder in Azure gespeichert sind. Falls sie in Azure gespeichert sind, müssen Sie zudem Azure-Kontoinformationen angeben.

### Abrufen von Daten von einem Anbieter für Datenfeeds

Indem Sie eine OData-URL angeben, können Sie Daten direkt von einem Anbieter für Datenfeeds lesen. Sie müssen die Quell-URL und den Inhaltstyp der Daten angeben.


## Speichern von Daten des Experiments


Hin und wieder wird ein Zwischenergebnis aus einem Experiment benötigt, um es als Teil eines anderen Experiments zu verwenden. Gehen Sie dazu folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls, die Sie als Dataset speichern möchten.

2. Klicken Sie auf **Save as Dataset**.

3. Wenn Sie dazu aufgefordert werden, geben Sie einen Namen und eine Beschreibung ein, mit denen das Dataset leicht wiederzuerkennen ist.

4. Klicken Sie auf das Häkchen **OK**.

Wenn der Speichervorgang abgeschlossen ist, ist das Dataset für die Verwendung in allen Experimenten in Ihrem Arbeitsbereich verfügbar. Sie finden es in der Liste **Saved Datasets** in der Modulpalette.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=62-->