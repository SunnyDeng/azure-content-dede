<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning-Beispiel: Vorhersagen von Flugverspätungen | Azure" description="Azure Machine Learning-Beispielexperiment für die Entwicklung eines Modells, das prognostiziert, ob ein geplanter Passagierflug mehr als 15 Minuten Verspätung haben wird" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning-Beispiel: Vorhersagen von Flugverspätungen

>[AZURE.NOTE]
>Das [Beispielexperiment] und die [Beispieldatasets] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldatasets]: #sample-datasets


## Problembeschreibung  ##

Verwendung historischer Leistungs- und Wetterdaten, um vorherzusagen, ob bestimmte Passagierflüge bei der Ankunft mehr als 15 Minuten Verspätung haben. 

## Daten ##

**Pünktlichkeitsübersicht von Passagierflügen aus der TranStats Datensammlung vom U.S. Department of Transportation: **  [http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)  

Dieses Dataset enthält Daten von April bis Oktober 2013. Vor dem Upload in Azure ML Studio:  

- Das Dataset wurde nach den 70 verkehrsreichsten Flughäfen auf dem US-Festland gefiltert.  
- Die folgenden Spalten wurden ausgewählt: 'Year', 'Month', 'DayofMonth', 'DayOfWeek','Carrier', 'OriginAirportID','DestAirportID','CRSDepTime','DepDelay','DepDel15', 'CRSArrTime','ArrDelay','ArrDel15','Cancelled'  
- Stornierte Flüge wurden als "mehr als 15 Minuten verspätet" markiert.  
- Umgeleitete Flüge wurden herausgefiltert.  

**Stündliche landbasierte Wetterbeobachtungen von NOAA:** http://cdo.ncdc.noaa.gov/qclcd_ascii/  

Die Wetterdaten enthalten Beobachtungen aus den Wetterstationen der einzelnen Flughäfen von April bis Oktober 2013. Vor dem Upload in ML Studio: 

- Die IDs der Wetterstationen wurden den entsprechenden Flughafen-IDs zugeordnet. 
- Wetterstationen, die nicht zu den 70 verkehrsreichsten Flughäfen gehören, wurden herausgefiltert  
- Die Datumsspalte wurde in separate Jahres-, Monats- und Tagesspalten aufgeteilt  
- Die folgenden Spalten wurden ausgewählt: 'AirportID', 'Year','Month','Day', 'Time', 'TimeZone', 'SkyCondition', 'Visibility','WeatherType','DryBulbFarenheit', 'DryBulbCelsius',  'WetBulbFarenheit',  'WetBulbCelsius',  'DewPointFarenheit','DewPointCelsius',  'RelativeHumidity',  'WindSpeed',  'WindDirection',  'ValueForWindCharacter','StationPressure',  'PressureTendency',  'PressureChange',  'SeaLevelPressure',  'RecordType',  'HourlyPrecip', 'Altimeter'  

## Modell ##

**Vorverarbeiten der Flugdaten**  

Zunächst die Spalten, die unsere Ergebnisse verfälschen könnten: DepDelay, DepDel15, ArrDelay, Cancelled werden aus dem Dataset ausgeschlossen.  

Die Spalten 'DayOfWeek','OriginAirportID' und 'DestAirportID' enthalten kategorische Attribute. Da es sich jedoch um Ganzzahlen handelt, werden sie zunächst als fortlaufend numerisch analysiert und müssen im Metadaten-Editor in kategorische Werte konvertiert werden.  

Wir wollen die Flugdaten mit den stündlichen Wetteraufzeichnungen verknüpfen, indem wir die geplante Abflugzeit als einen der Verknüpfungsschlüssel verwenden. Dazu wird die Spalte CSRDepTime auf die vorherige ganze Stunde abgerundet.  

**Vorverarbeiten der Wetterdaten**  

Zunächst werden Spalten ausgeschlossen, in denen ein großer Teil der Werte fehlt. Dazu gehören  

- Alle Spalten mit Zeichenfolgenwerten  
- ValueForWindCharacter,WetBulbFarenheit,WetBulbCelsius,PressureTendency,PressureChange,SeaLevelPressure,StationPressure  

Anschließend wird die Bereinigung fehlender Werte auf die restlichen Spalten angewendet.  

Die Zeit der Wetterbeobachtung wird auf die nachfolgende ganze Stunde gerundet, um diese Daten mit der geplanten Abflugzeit verknüpfen zu können. Beachten Sie, dass geplante Abflugzeit und Zeit der Wetterbeobachtung in gegensätzliche Richtungen gerundet werden. Auf diese Weise wird sichergestellt, dass das Modell nur Wetterbeobachtungen verwendet, die relativ zur Flugzeit in der Vergangenheit geschehen sind.  

**Verknüpfen der Datasets**

Flug- und Wetterdaten werden jeweils am Abflugs- und Ankunftsort des Flugs miteinander verknüpft.  

Beachten Sie, dass die Wetterdaten in lokaler Zeit gemeldet werden, Abflugs- und Ankunftsort jedoch in unterschiedlichen Zeitzonen liegen können. Daher werden die Zeitzonen berücksichtigt, indem die jeweiligen Werte aus der entsprechenden Spalte von der geplanten Abflugzeit und der Zeit der Wetterbeobachtung subtrahiert werden.  

**Vorbereiten von Trainings- und Prüfungsproben**  

Zur Erstellung der Trainings- und Prüfungsproben werden die Daten aufgeteilt in Einträge von April bis September für das Training und Oktober-Einträge für die Prüfung. Die Jahres- und Monatsspalten werden anschließend aus dem Dataset entfernt. 

Außerdem werden Trainings- und Prüfungsdaten durch eine sogenannte Equal-Height-Klassierung quantisiert.  

**Trainieren und Prüfen des Modells** 

Das zweiklassige Boosted Decision Tree-Modell wird anhand der Trainingsprobe trainiert. Dieses Modell ist für eine optimale Fläche unter der Kurve optimiert und verwendet einen 10-fachen zufälligen Parameterdurchlauf. Zum Vergleich: Das zweiklassige logistische Regressionsmodell ist auf dieselbe Weise optimiert.  

Die trainierten Modelle werden anschließend gegen die Prüfungsdaten bewertet, und das "Modell bewerten"-Modul wird verwendet, um die Qualität der Modelle miteinander zu vergleichen. 

**Nachbearbeitung** 

Die Flughafen-IDs werden mit aussagekräftigen Flughafennamen und Standorten verknüpft, um die Ergebnisse leichter analysieren zu können. 

## Ergebnisse ##

Das Boosted Decision Tree-Modell hat eine Fläche unter der Kurve von 0,697 für die Prüfungsdaten und ist damit knapp besser als das logistische Regressionsmodell mit einer Fläche unter der Kurve von 0,675. 



## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperiment - Vorhersagen von Flugverspätungen - Entwicklung**

## Beispieldatasets

Die folgenden Beispieldatasets, die von diesem Experiment verwendet werden, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###Flughafencodes-Dataset
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../includes/machine-learning-sample-dataset-airport-codes.md)]

###Flugverspätungsdaten
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../includes/machine-learning-sample-dataset-flight-delays-data.md)]

###Wetter-Dataset
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../includes/machine-learning-sample-dataset-weather-dataset.md)]

