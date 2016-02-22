<properties
	pageTitle="Importieren von Daten in Machine Learning Studio | Microsoft Azure"
	description="Erfahren Sie, wie Sie Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio importieren. Erfahren Sie, welche Datentypen und Datenformate unterstützt werden."
	keywords="Importieren von Daten,Datenformat,Datentypen,Datenquellen,Trainingsdaten"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="garye;bradsev" />


# Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio

## Einführung

Um Ihre eigenen Daten in Machine Learning Studio zum Entwickeln und Trainieren einer Predictive Analytics-Lösung zu verwenden, können Sie folgende Aktionen ausführen:

- Hochladen von Daten aus einer **lokalen Datei** von der Festplatte im Voraus, um damit ein DataSet-Modul in Ihrem Arbeitsbereich zu erstellen  
- Zugreifen auf Daten aus einer von mehreren **Onlinedatenquellen**, während Ihr Experiment mit dem [Reader][reader]-Modul ausgeführt wird 
- Verwenden von Daten aus einem anderen Azure Machine Learning-Experiment, die als **DataSet** gespeichert sind 

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

Diese Optionen werden in den Themen im Menü oben beschrieben. In diesen Themen wird das Importieren von Daten aus verschiedenen Datenquellen für die Verwendung in Machine Learning Studio veranschaulicht.

> [AZURE.NOTE] Es gibt eine Reihe von Beispiel-DataSets in Machine Learning Studio, die Sie für diesen Zweck verwenden können. Weitere Informationen finden Sie unter [Verwenden von Beispiel-DataSets in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)).

In diesem Einführungsthema wird auch gezeigt, wie Sie Daten für die Verwendung in Machine Learning Studio vorbereiten, und es wird beschrieben, welche Datenformate und Datentypen unterstützt werden.

> [AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Vorbereiten von Daten für die Verwendung in Azure Machine Learning Studio
Machine Learning Studio ist für die Nutzung von rechteckigen oder tabellarischen Daten vorgesehen, z. B. Textdaten, bei denen es sich um mit Trennzeichen versehene oder strukturierte Daten aus einer Datenbank handelt. Unter Umständen können nicht rechteckige Daten verwendet werden.

Es sollten möglichst relativ saubere Daten verwendet werden. Sie sollten also Probleme wie Zeichenfolgen ohne Anführungszeichen beheben, bevor Sie die Daten in das Experiment hochladen.

In Machine Learning Studio stehen jedoch Module zur Verfügung, mit denen Sie einige Änderungen an Daten im Experiment vornehmen können. Abhängig vom verwendeten Machine Learning-Algorithmen müssen Sie möglicherweise entscheiden, wie Sie mit Datenstrukturproblemen wie fehlenden Werten und geringen Datenmengen umgehen möchten. Es gibt Module, die dabei helfen können. Suchen Sie im Abschnitt **Data Transformation** der Modulpalette nach Modulen, die diese Funktionen ausführen.

Zu jedem Zeitpunkt des Experiments können Sie die Daten anzeigen oder herunterladen, die von einem Modul erstellt werden, indem Sie mit der rechten Maustaste auf den Ausgabeport klicken. Abhängig vom Modul können verschiedene Downloadoptionen verfügbar sein, oder Sie können die Daten in Ihrem Webbrowser in Machine Learning Studio anzeigen.

## Unterstützte Datenformate und Datentypen

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

Wenn Sie Daten in einem Format wie z. B. ARFF importieren, die Metadaten enthalten, verwendet Machine Learning Studio diese Metadaten, um die Kopfzeile und den Datentyp der einzelnen Spalten zu definieren.

Wenn Sie z. B. Daten im TSV- oder CSV-Format importieren, das diese Metadaten nicht enthält, leitet Machine Learning Studio den Datentyp für jede Spalte ab, indem Stichproben der Daten entnommen werden. Wenn die Daten zudem keine Spaltenüberschriften aufweisen, gibt Machine Learning Studio Standardnamen an.

Sie können die Überschriften und Datentypen für Spalten mit dem [Metadaten-Editor][metadata-editor] explizit angeben oder ändern.

Die folgenden **Datentypen** werden von Machine Learning Studio erkannt:

- String
- Integer
- Double
- Boolean
- DateTime
- TimeSpan

Machine Learning Studio verwendet einen internen Datentyp mit dem Namen ***Data Table*** zum Übergeben von Daten zwischen Modulen. Mit dem Modul [Convert to Dataset][convert-to-dataset] können Sie Daten explizit in das Data Table-Format konvertieren.

Jedes Modul, das andere Formate als Data Table akzeptiert, konvertiert die Daten im Hintergrund vor der Übergabe an das nächste Modul in das Data Table-Format.

Bei Bedarf können Sie das Data Table-Format mit anderen Konvertierungsmodulen wieder in die Formate CSV, TSV, ARFF oder SVMLight konvertieren. Suchen Sie im Abschnitt **Data Format Conversions** der Modulpalette nach Modulen, die diese Funktionen ausführen.



<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0211_2016-->