<properties
	pageTitle="Importieren Sie Ihre Trainingsdaten in Machine Learning Studio | Azure"
	description="Gewusst wie: Importieren Ihrer Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio"
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
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="garye" />


#Importieren Sie Ihre Trainingsdaten in Azure Machine Learning Studio

Bei der Entwicklung einer Lösung für Vorhersageanalysen in Azure Machine Learning Studio trainieren Sie Ihr Modell mit Daten, die für Ihren Problembereich repräsentativ sind.
Ihnen stehen eine Anzahl von Beispieldatasets in ML Studio zur Verfügung, die Sie zu diesem Zweck verwenden können 
(siehe [Verwenden von Beispieldatasets in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)). Sie können jedoch auch Ihre eigenen Daten in ML Studio für die Verwendung in Ihren Experimenten importieren.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

Um Ihre eigenen Daten in ML Studio zu verwenden, können Sie eine Datei im Voraus von Ihrer lokalen Festplatte hochladen und ein Datasetmodul in Ihrem Arbeitsbereich erstellen. 
Sie können auch auf Daten aus einer von mehreren Onlinequellen zugreifen, während das Experiment mit dem [Reader][reader]-Modul ausgeführt wird:

- Azure BLOB-Speicher, Tabelle oder SQL-Datenbank
- Hadoop mit HiveQL
- Eine Web-URL mit HTTP
- Ein Anbieter für einen Datenfeed

ML-Studio dient zur Arbeit mit rechteckigen oder tabellarischen Daten, z. B. Textdaten, die getrennte oder strukturierte Daten aus einer Datenbank umfassen. Unter Umständen können jedoch nicht rechteckige Daten verwendet werden.

Es empfiehlt sich, wenn Ihre Daten im Prinzip bereinigt sind.
Das heißt, Sie sollten Probleme wie z. B. Zeichenfolgen ohne Anführungszeichen vor dem Hochladen der Daten in das Experiment beheben.
Es stehen Ihnen jedoch Module in ML Studio zur Verfügung, mit denen Sie einige Veränderungen an Daten im Experiment vornehmen können.
Je nachdem, welche Algorithmen für Machine Learning verwendet werden, müssen Sie möglicherweise entscheiden, wie strukturelle Probleme bei Daten, wie fehlende Werte und geringe Datenmengen, behandelt werden. Es gibt Module, die dabei helfen können. 
Suchen Sie im Abschnitt **Datentransformation** der Modulpalette nach Modulen, die diese Funktionen bieten. 

 Sie können zu jedem Zeitpunkt des Experiments Daten anzeigen oder herunterladen, die durch ein Modul erstellt werden. Klicken Sie einfach mit der rechten Maustaste in den Ausgangsport.
Je nach Modul können verschiedene Downloadoptionen verfügbar sein, oder möglicherweise können Sie die Daten in Ihrem Webbrowser in ML Studio anzeigen.


## Datenformate

Sie können eine Reihe von Datentypen in das Experiment importieren, je nachdem welchen Mechanismus Sie verwenden, um die Daten zu importieren und woher sie stammen:

- Nur Text (.txt)
- Kommagetrennte Werte (CSV) mit einem Header (.csv) oder ohne (.nh.csv)
- Registerkarte mit durch Tabstopp getrennten Werten (TSV) mit einem Header (.tsv) oder ohne (.nh.tsv)
- Hive-Tabelle
- SQL-Datenbanktabelle
- OData-Werte
- SVMLight-Daten (.svmlight) (Informationen zu Formaten finden Sie in der [SVMLight-Definition](http://svmlight.joachims.org/).)
- Daten imAttribute Relation File Format (ARFF), (.arff), (Informationen zu Formaten finden Sie in der [ARFF-Definition](http://weka.wikispaces.com/ARFF).)
- ZIP-Datei (.zip)
- R-Objekt oder Workspace-Datei (.RData)

Wenn Sie Daten in einem Format wie z. B. ARFF importieren, das Metadaten enthält, verwendet ML Studio diese Metadaten, um die Überschrift und den Datentyp der einzelnen Spalten zu definieren.
Wenn Sie Daten in einem Format wie z. B. TSV oder CSV importieren, das diese Metadaten nicht enthält, leitet ML Studio den Datentyp für jede Spalte ab, indem es Stichproben der Daten entnimmt. Wenn die Daten auch keine Spaltenüberschriften aufweisen, verwendet ML Studio Standardnamen.
Sie können Überschriften und Datentypen für Spalten mit dem [Metadaten-Editor][metadata-editor] explizit angeben oder ändern.
 
Die folgenden Datentypen werden von ML Studio erkannt:

- Zeichenfolge
- Ganze Zahl
- Doppelt
- Boolesch
- DateTime
- TimeSpan

ML-Studio verwendet einen internen Datentyp mit dem Namen  *Data Table* zum Übergeben von Daten zwischen Modulen. Sie können Ihre Daten explizit in ein Datentabellenformat konvertieren, indem Sie das Modul [ In Dataset konvetieren][convert-to-dataset] verwenden.
Jedes Modul, das anderen Formate als das Datentabellenformat akzeptiert, konvertiert die Daten vor der Übergabe an das nächste Modul im Hintergrund in eine Datentabelle.
Bei Bedarf können Sie das Datentabellenformat mit anderen Konvertierungsmodulen wieder in CSV, TSV, ARFF oder SVMLight konvertieren.
Suchen Sie im Abschnitt **Datenformatkonvertierung** der Modulpalette nach Modulen, die diese Funktionen bieten.


## Eine lokale Datendatei importieren

Sie können Daten aus einer lokalen Festplatte wie folgt hochladen:

1. Klicken Sie am unteren Rand des ML Studio-Fensters auf **+NEU**.
2. Wählen Sie **DATASET** und **AUS LOKALER DATEI**.
3. Navigieren Sie im Dialogfeld **Neuen Datensatz hochladen** zu der Datei, die Sie hochladen möchten.
4. Geben Sie einen Namen ein, legen Sie den Datentyp fest, und geben Sie optional eine Beschreibung ein. Eine Beschreibung wird empfohlen. So können Sie beliebige Eigenschaften zu den Daten erfassen, die Sie benötigen, wenn Sie die Daten in der Zukunft verwenden.
5. Mit dem Kontrollkästchen **Dies ist die neue Version eines vorhandenen Datasets** können Sie ein vorhandenes Dataset mit neuen Daten aktualisieren. Markieren Sie Kontrollkästchen einfach, und geben Sie den Namen eines vorhandenen Datasets ein.

Während des Uploads wird eine Meldung angezeigt, dass die Datei hochgeladen wird. Die Hochladedauer ist abhängig von der Größe Ihrer Daten und der Geschwindigkeit der Verbindung mit dem Dienst. 
Wenn Sie bereits wissen, dass das Hochladen der Datei sehr lange dauert, können Sie ML Studio anderweitig verwenden, während Sie warten. Wenn Sie den Browser schließen, schlägt jedoch das Hochladen der Daten fehl. 

Sobald Ihre Daten hochgeladen werden, werden sie in einem Datasetmodul gespeichert und stehen für alle Experimente im Arbeitsbereich zur Verfügung.
Sie finden das Dataset und alle vorinstallierten Beispieldatasets in de Liste **Gespeicherte Datasets** in der Modulpalette, wenn Sie ein Experiment bearbeiten.


## Onlinedatenzugriff mit dem Reader-Modul

Wenn Sie das [Reader][reader]-Modul in Ihrem Experiment verwenden, können Sie über verschiedene Onlineressourcen auf Daten zugreifen, während das Experiment ausgeführt wird.
Da auf diese Daten zugegriffen wird, während das Experiment ausgeführt wird, sind sie nur in einem Experiment verfügbar (im Gegensatz zu Datasetmodulen, die für alle Experimente im Arbeitsbereich zur Verfügung stehen).

Zuerst fügen Sie das [Reader][reader]-Modul zu Ihrem Experiment hinzu. Dann wählen Sie die **Datenquelle**, und daraufhin stellen Sie die Zugriffsinformationen über Modulparameter bereit. 
Wenn Sie z. B. **Web-URL über HTTP**auswählen, geben Sie Quell-URL und Datenformat an.
Wenn Sie über den Azure-Speicher oder HDInsight (mit einer Hive-Abfrage) auf Ihre Daten zugreifen, geben Sie die entsprechenden Kontoinformationen und den Speicherort der Daten an.

> [AZURE.NOTE] Dieser Artikel bietet allgemeine Informationen über das [Reader][reader]-Modul. Ausführlichere Informationen zu den Datentypen, auf die Sie zugreifen können, Formate, Parameter und Antworten auf häufige Fragen finden Sie im Modulreferenzthema für das [Reader][reader]-Modul.


### Abrufen von Daten aus Azure

Sie können Daten aus drei Azure-Quellen importieren:

- **Azure BLOB-Speicher** - Wenn Sie das ARFF-Format für die Speicherung verwenden, werden Spalten mit den Header-Metadaten zugeordnet. Wenn Sie das TSV- oder CSV-Format verwenden, werden Zuordnungen mit Stichproben von Spaltendaten abgeleitet. 
- **Azure Table Storage** - Das [Reader][reader]-Modul scannt die Daten, um Spaltendatentypen zu identifizieren. Wenn Ihre Daten relativ homogen und vorhersagbar sind, können Sie die Anzahl der Zeilen begrenzen, die gescannt werden.
- **Azure SQL-Datenbank** - Das [Reader][reader]-Modul nutzt die SQL Azure Transact-Client-API zum Importieren von Daten mithilfe einer Abfrage, die Sie bereitstellen.

Geben Sie für die BLOB- und Tabellenspeicherung eine Shared Access Signature-URI (SAS-URI) oder die Azure-Speicher-Kontoinformationen für den Zugriff auf die Daten an. Für eine Azure SQL-Datenbank geben Sie die Datenbank und Ihre Kontoinformationen sowie eine Datenbankabfrage ein, die die Daten identifiziert, die Sie importieren möchten.

### Abrufen von Daten aus dem Web

Sie können das [Reader][reader]-Modul zum Lesen von Daten aus einer Web- oder FTP-Site verwenden. Sie müssen Folgendes angeben:

- Eine vollständige HTTP-URL-Adresse einer Datei
- Das Datenformat der Datei (CSV, TSV, ARFF oder SvmLight)
- Geben Sie für CSV- oder TSV-Dateien an, ob die erste Zeile in der Datei eine Kopfzeile ist.

### Abrufen von Daten aus Hadoop

Sie können das [Reader][reader]-Modul mit der HiveQL-Abfragesprache zum Lesen von Daten aus verteilten Speichern verwenden.
Sie müssen die Hive-Datenbankabfrage und Benutzerzugriffsinformationen auf dem HCatalog-Server bereitstellen.
Sie müssen auch angeben, ob die Daten in einem verteilten Hadoop-Dateisystem (HDFS) oder in Azure gespeichert sind. Wenn sie in Azure gespeichert sind, müssen Sie außerdem die entsprechenden Azure-Kontoinformationen angeben.  

### Abrufen von Daten von einem Anbieter für einen Datenfeed

Indem Sie einen OData-URL angeben, kann direkt aus einem Datenfeedanbieters gelesen werden. Sie müssen die Quell-URL und den Inhaltstyp der Daten angeben.  


## Speichern von Daten aus Ihrem Experiment


Hin und wieder benötigen Sie ein Zwischenergebnis aus einem Experiment, um es als Teil eines anderen Experiments zu verwenden. Gehen Sie dazu folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste in die Ausgabe des Moduls, die Sie als Dataset speichern möchten.

2. Klicken Sie auf **Als Dataset speichern**. 

3. Wenn Sie aufgefordert werden, geben Sie einen Namen und eine Beschreibung ein, mit denen das Dataset leicht wiederzuerkennen ist.

4. Klicken Sie auf das Häkchen **OK**.

Wenn der Speichervorgang abgeschlossen ist, ist das Dataset für die Verwendung innerhalb jedes Experiments im Arbeitsbereich verfügbar. Sie finden es in der Liste **Gespeicherte Datasets** in der Modulpalette.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!--HONumber=54-->