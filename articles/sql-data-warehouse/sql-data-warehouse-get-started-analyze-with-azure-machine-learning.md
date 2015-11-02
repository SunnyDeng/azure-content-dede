<properties
   pageTitle="Analysieren von Daten mit Azure Machine Learning | Microsoft Azure"
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
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# Analysieren von Daten mit Azure Machine Learning
In diesem Tutorial erfahren Sie, wie Sie ein prädiktives Machine Learning-Modell mit Azure Machine Learning und Ihren Azure SQL Data Warehouse-Daten erstellen. Wir ermitteln in diesem Tutorial auf der Grundlage der AdventureWorksDW-Datenbank, bei welchen Kunden des Adventure Works-Fahrradgeschäfts es sich um potenzielle Fahrradkäufer handelt, um eine zielgerichtete Marketingkampagne zu erstellen.


## Voraussetzungen
Für dieses Tutorial ist Folgendes erforderlich:

- SQL Data Warehouse mit einer AdventureWorksDW-Beispieldatenbank

Unter [Erstellen eines SQL Data Warehouse][] wird erläutert, wie Sie eine Datenbank mit Beispieldaten bereitstellen. Wenn Sie bereits über eine SQL Data Warehouse-Datenbank verfügen, aber noch keine Beispieldaten besitzen, können Sie [Beispieldaten manuell laden][].


## Schritt 1: Bereitstellen von Daten 
Wir lesen die Daten aus der Ansicht „dbo.vTargetMail“ in der AdventureWorksDW-Datenbank.

1. Melden Sie sich bei [Azure Machine Learning Studio][] an, und klicken Sie auf „Meine Experimente“.
2. Klicken Sie auf **+ NEU**, und wählen Sie **Leeres Experiment**.
3. Geben Sie einen Namen für das Experiment ein: Zielgerichtetes Marketing.
4. Ziehen Sie das **Reader**-Modul aus dem Modulbereich in den Zeichenbereich.
5. Geben Sie im Eigenschaftenbereich ausführliche Informationen zu Ihrer SQL Data Warehouse-Datenbank ein. 
6. Geben Sie die Datenbankabfrage zum Lesen der für Sie interessanten Daten an.

```
SELECT [CustomerKey]
      ,[GeographyKey]
      ,[CustomerAlternateKey]
      ,[MaritalStatus]
      ,[Gender]
      ,cast ([YearlyIncome] as int) as SalaryYear
      ,[TotalChildren]
      ,[NumberChildrenAtHome]
      ,[EnglishEducation]
      ,[EnglishOccupation]
      ,[HouseOwnerFlag]
      ,[NumberCarsOwned]
      ,[CommuteDistance]
      ,[Region]
      ,[Age]
      ,[BikeBuyer]
  FROM [dbo].[vTargetMail]
```
7. Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf **Ausführen** klicken.
8. Klicken Sie auf den Ausgabeport im unteren Bereich des Reader-Moduls, und wählen Sie **Visualisieren**, um die importierten Daten anzuzeigen.



## Schritt 2: Bereinigen von Daten
Wir löschen einige Spalten, die für das Modell nicht relevant sind.

1. Ziehen Sie das Modul **Project Columns** in den Zeichenbereich.
2. Klicken Sie im Eigenschaftenbereich auf **Spaltenauswahl starten**, um die zu löschenden Spalten anzugeben.
3. Schließen Sie zwei Spalten aus: CustomerAlternateKey und GeographyKey.



## Schritt 3: Erstellen des Modells
Wir teilen die Daten im Verhältnis 80:20: 80 Prozent zum Trainieren eines Machine Learning-Modells und 20 Prozent zum Testen des Modells. Für dieses binäre Klassifizierungsproblem nutzen wir die Zwei-Klassen-Algorithmen.

1. Ziehen Sie das Modul **Split** in den Zeichenbereich.
2. Geben Sie im Eigenschaftenbereich für den Anteil der Zeilen im ersten Ausgabedatensatz „0,8“ ein.
3. Ziehen Sie das Modul **Two-Class Boosted Decision Tree** in den Zeichenbereich.
4. Ziehen Sie das Modul **Train Model** in den Zeichenbereich der Leinwand, und geben Sie folgendes an: Erste Eingabe: ML-Algorithmus. Zweite Eingabe: Daten zum Trainieren des Algorithmus.
5. Klicken Sie im Eigenschaftenbereich auf **Spaltenauswahl starten**, um anzugeben, welche Spalte das Modell vorhersagen soll: BikeBuyer.


## Schritt 4: Bewertungsmodell
Wir testen nun die Leistung des Modells mithilfe von Testdaten. Wir vergleichen zwei Algorithmen, um zu ermitteln, welcher Algorithmus die bessere Leistung erzielt.

1. Ziehen Sie das Modul **Score Model** in den Zeichenbereich. Erste Eingabe: Trainiertes Modell. Zweite Eingabe: Testdaten
2. Ziehen Sie das Modul **Two-Class Bayes Point Machine** in den Experimentbereich. Wir sehen uns an, wie dieser Algorithmus im Vergleich zum Modul „Two-Class Boosted Decision Tree“ abschneidet.
3. Kopieren Sie die Module „Train Model“ und „Score Model“, und fügen Sie sie im Zeichenbereich ein.
4. Ziehen Sie das Modul **Evaluate Model** in den Zeichenbereich, um die beiden Algorithmen zu vergleichen.
5. Sie können nun das Experiment **ausführen**.
6. Klicken Sie unten im Modul „Evaluate Model“ auf den Ausgabeport und anschließend auf „Visualisieren“.


Folgende Metriken stehen zur Verfügung: ROC-Kurve, Precision-Recall-Diagramm und Lorenz-Kurve. Anhand dieser Metriken erkennen wir, dass mit dem ersten Modell eine bessere Leistung als mit dem zweiten Modell erzielt wird. Wenn Sie die Vorhersage des ersten Modells anzeigen möchten, klicken Sie im Bewertungsmodell auf den Ausgabeport und anschließend auf „Visualisieren“.


Ihrem Testdatensatz werden zwei weitere Spalten hinzugefügt.

- Bewertete Wahrscheinlichkeiten: Die Wahrscheinlichkeit, dass es sich bei einem Kunden um einen Fahrradkäufer handelt.
- Bewertete Beschriftungen: die vom Modell vorgenommene Klassifizierung – Fahrradkäufer (1) oder kein Fahrradkäufer (0). Der Wahrscheinlichkeitsschwellenwert für die Beschriftung ist auf 50 Prozent festgelegt, kann aber angepasst werden.

Durch einen Vergleich der Spalte „BikeBuyer“ (tatsächliche Werte) mit „Bewertete Beschriftungen“ (Vorhersage) können Sie die Leistung des Modells ermitteln. Als Nächstes können Sie mit diesem Modell Vorhersagen für neue Kunden treffen und das Modell als Webdienst veröffentlichen oder Ergebnisse zurück in SQL Data Warehouse schreiben.

Weitere Informationen finden Sie unter [Einführung in Machine Learning in Azure][].



<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Einführung in Machine Learning in Azure]: ../machine-learning/machine-learning-what-is-machine-learning.md
[Beispieldaten manuell laden]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Erstellen eines SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO4-->