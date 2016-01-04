<properties
   pageTitle="Verwenden von Azure Machine Learning mit SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Verwendung von Azure Machine Learning mit Azure SQL Data Warehouse für die Entwicklung von Lösungen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/06/2015"
   ms.author="sahajs"/>

# Verwenden von Azure Machine Learning mit SQL Data Warehouse

Azure Machine Learning ist ein vollständig verwalteter Vorhersageanalysedienst, den Sie zum Erstellen von Vorhersagemodellen anhand Ihrer Daten in SQL Data Warehouse verwenden können, die Sie als sofort nutzbare Webdienste veröffentlichen können. Zum Erlernen der Grundlagen von Vorhersageanalysen und vom maschinellen Lernen lesen Sie [Einführung in Machine Learning in Azure][]. Anschließend erfahren Sie im [Lernprogramm zum Erstellen eines Experiments][] wie Sie ein Modell zum maschinellen Lernen erstellen, trainieren, bewerten und testen.

In diesem Artikel erfahren Sie, wie Sie Folgendes mit [Azure Machine Learning Studio][] ausführen:

- Lesen von Daten aus der Datenbank zum Erstellen, Trainieren und Bewerten eines Vorhersagemodells
- Schreiben von Daten in die Datenbank 


## Lesen von Daten aus SQL Data Warehouse

Wie werden Daten aus der Product-Tabelle in der AdventureWorksDW-Datenbank lesen.

### Schritt 1

Starten Sie ein neues Experiment, indem Sie am unteren Rand des Fensters von Machine Learning Studio auf "+NEU" klicken und anschließend "EXPERIMENT" und dann "Blank Experiment" auswählen. Wählen Sie den Standardnamen am oberen Rand des Bereichs aus, und geben Sie einen aussagekräftigeren Namen ein, z. B. "Fahrradpreisvorhersage".

### Schritt 2

Suchen Sie in der Palette von DataSets und Modulen auf der linken Seite im Experimentbereich nach dem Reader-Modul. Ziehen Sie das Modul in den Experimentbereich. ![][drag_reader]

### Schritt 3

Wählen Sie das Reader-Modul aus, und füllen Sie das Eigenschaftenfenster aus.

1. Wählen Sie „Azure SQL-Datenbank“ als Datenquelle aus.
2. Datenbank-Servername: Geben Sie den Namen des Servers ein. Diese Angaben finden Sie im [klassischen Azure-Portal][].

![][server_name]

3. Datenbankname: Geben Sie den Namen einer Datenbank auf dem Server ein, den Sie soeben angegeben haben. 
4. Server user account name: Geben Sie den Benutzernamen eines Kontos ein, das über Zugriffsberechtigungen für die Datenbank verfügt. 
5. Server user account password: Geben Sie das Kennwort für das angegebene Benutzerkonto ein.
6. Accept any server certificate: Verwenden Sie diese (weniger sichere) Option, um das Überprüfen des Websitezertifikats vor dem Lesen Ihrer Daten zu überspringen.
7. Datenbankabfrage: Geben Sie eine SQL-­Anweisung ein, die die Daten beschreibt, die Sie lesen möchten. In diesem Fall lesen wir mit der folgenden Abfrage Daten aus der Product-Tabelle.


```
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### Schritt 4

1. Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf "Ausführen" klicken.
2. Nach Abschluss des Experiments ist das Reader-Modul mit einem grünen Häkchen markiert, um anzuzeigen, dass es erfolgreich abgeschlossen wurde. Beachten Sie auch den Status Finished running in der oberen rechten Ecke.

![][run]

3. Sie können auf den Ausgabeport im unteren Bereich des Automobil-DataSets klicken und "Visualisieren" auswählen, um die importierten Daten anzuzeigen.


## Erstellen, Trainieren und Bewerten eines Modells

Sie können das Dataset jetzt für Folgendes verwenden:

- Erstellen eines Modells: Verarbeiten von Daten und Definieren von Funktionen
- Trainieren des Modells: Auswählen und Anwenden eines Lernalgorithmus
- Bewerten und Testen des Modells: Vorhersagen eines neuen Fahrradpreises
 

![][model]

Im [Lernprogramm zum Erstellen eines Experiments][] erfahren Sie, wie Sie ein Modell zum maschinellen Lernen erstellen, trainieren, bewerten und testen.

## Schreiben von Daten in Azure SQL Data Warehouse

Wir schreiben das Resultset in die ProductPriceForecast-Tabelle in der AdventureWorksDW-Datenbank.

### Schritt 1

Suchen Sie in der Palette von DataSets und Modulen auf der linken Seite im Experimentbereich nach dem Writer-Modul. Ziehen Sie das Modul in den Experimentbereich.

![][drag_writer]

### Schritt 2

Wählen Sie das Writer-Modul aus, und füllen Sie das Eigenschaftenfenster aus.

1. Wählen Sie "Azure SQL-Datenbank" als Datenziel aus.
2. Datenbank-Servername: Geben Sie den Namen des Servers ein. Diese Angaben finden Sie im [klassischen Azure-Portal][]. 
3. Datenbankname: Geben Sie den Namen einer Datenbank auf dem Server ein, den Sie soeben angegeben haben. 
4. Server user account name: Geben Sie den Benutzernamen eines Kontos ein, das über Schreibberechtigungen für die Datenbank verfügt. 
5. Server user account password: Geben Sie das Kennwort für das angegebene Benutzerkonto ein.
6. Accept any server certificate (insecure): Wählen Sie diese Option aus, wenn Sie das Zertifikat nicht anzeigen möchten.
7. Comma-separated list of columns to be saved: Stellen Sie eine Liste der DataSet- oder Ergebnisspalten bereit, die Sie ausgeben möchten.
8. Name der Datentabelle: Geben Sie den Namen der Datentabelle an.
9. Comma-separated list of datatable columns: Geben Sie die Spaltennamen an, die in der neuen Tabelle verwendet werden. Die Spaltennamen können sich von denen im Quell-DataSet unterscheiden, Sie müssen jedoch die gleiche Anzahl Spalten auflisten, die Sie für die Ausgabetabelle definieren.
10. Number of rows written per SQL Azure operation: Sie können die Anzahl der Zeilen konfigurieren, die in einem einzigen Vorgang in eine SQL-Datenbank geschrieben werden.

![][writer_properties]

### Schritt 3

1. Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf "Ausführen" klicken.
2. Nach Abschluss des Experiments sind alle Module mit einem grünen Häkchen markiert, um anzuzeigen, dass diese erfolgreich abgeschlossen wurden. 

## Nächste Schritte

Einen Überblick über die Integration finden Sie unter [SQL Data Warehouse-Integration (Übersicht)][].

Weitere Hinweise zur Entwicklung finden Sie in der [SQL Data Warehouse-Entwicklungsübersicht][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse-Entwicklungsübersicht]: ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse-Integration (Übersicht)]: ./sql-data-warehouse-overview-integration/
[Lernprogramm zum Erstellen eines Experiments]: https://azure.microsoft.com/de-DE/documentation/articles/machine-learning-create-experiment/
[Einführung in Machine Learning in Azure]: https://azure.microsoft.com/de-DE/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[klassischen Azure-Portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

<!---HONumber=AcomDC_1203_2015-->