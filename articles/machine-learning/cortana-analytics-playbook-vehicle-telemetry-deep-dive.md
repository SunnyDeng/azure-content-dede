<properties 
	pageTitle="Lösungs-Playbook zur Fahrzeugtelemetrieanalyse: Ausführliche Betrachtung der Lösung | Microsoft Azure" 
	description="Verwenden Sie die Funktionen von Cortana Analytics zur Echtzeitgewinnung von prädiktiven Einblicken in den Fahrzeugzustand und das Fahrverhalten." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2015" 
	ms.author="bradsev" />


# Lösungs-Playbook zur Fahrzeugtelemetrieanalyse: Ausführliche Betrachtung der Lösung

Dieses **Menü** enthält Links zu den Abschnitten des Playbooks:

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

In diesem Abschnitt geht es um die Details der einzelnen Phasen, die in der Lösungsarchitektur dargestellt sind, und Sie erhalten Anleitungen und Hinweise zur Anpassung.

## Datenquellen

Für die Lösung werden zwei unterschiedliche Datenquellen verwendet:

- **Dataset mit simulierten Fahrzeugsignalen und Diagnosedaten** 
- **Fahrzeugkatalog**

Zu dieser Lösung gehört ein Simulator für Fahrzeugtelematik (Vehicle Telematics Simulator). Diese Anwendung gibt Diagnosedaten und Signale entsprechend dem Zustand des Fahrzeugs und Fahrmuster zu einem bestimmten Zeitpunkt aus. Klicken Sie auf [Vehicle Telematics Simulator](http://go.microsoft.com/fwlink/?LinkId=717075), um die **Visual Studio-Projektmappe „Vehicle Telematics Simulator“** herunterzuladen, wenn Sie Anpassungen nach Ihren Anforderungen vornehmen möchten. Der Fahrzeugkatalog enthält ein Referenzdataset mit einer Zuordnung von Fahrzeug-Identifizierungsnummer und Modell.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Abbildung 2 – Vehicle Telematics Simulator*

Dies ist ein Dataset im JSON-Format, das das unten angegebene Schema enthält.

Column | Beschreibung | Werte   
 ------- | ----------- | ---------  
VIN (Fahrzeug-Identifizierungsnummer) | Zufällig generierte Fahrzeug-Identifizierungsnummer | Wird aus einer Masterliste mit 10.000 zufällig generierten Fahrzeug-Identifizierungsnummern ausgewählt
Outside temperature (Außentemperatur) | Die Außentemperatur an dem Ort, an dem sich das Fahrzeug befindet | Zufällig generierte Zahl von 0 bis 100
Engine temperature (Motortemperatur) | Motortemperatur des Fahrzeugs | Zufällig generierte Zahl von 0 bis 500
Speed (Geschwindigkeit) | Geschwindigkeit, mit der sich das Fahrzeug bewegt | Zufällig generierte Zahl von 0 bis 100
Fuel (Kraftstoff) | Kraftstoffmenge des Fahrzeugs | Zufällig generierte Zahl von 0 bis 100 (Kraftstoffstand in Prozent)
EngineOil (Motoröl) | Motorölstand des Fahrzeugs | Zufällig generierte Zahl von 0 bis 100 (Motorölstand in Prozent)
Tirepressure (Reifendruck) | Reifendruck des Fahrzeugs | Zufällig generierte Zahl von 0 bis 50 (Reifendruck in Prozent)
Odometer (Kilometerzähler) | Kilometerstand des Fahrzeugs | Zufällig generierte Zahl von 0 bis 200.000
Accelerator\_pedal\_position (Position des Gaspedals) | Gaspedalposition des Fahrzeugs | Zufällig generierte Zahl von 0 bis 100 (Gaspedalposition in Prozent)
Parking\_brake\_status (Status der Handbremse) | Gibt an, ob das Fahrzeug geparkt und die Handbremse gezogen ist | „true“ oder „false“
Headlamp\_status (Scheinwerferstatus) | Gibt an, ob die Scheinwerfer eingeschaltet sind | „true“ oder „false“
Brake\_pedal\_status (Status des Bremspedals) | Gibt an, ob das Bremspedal betätigt wird | „true“ oder „false“
Transmission\_gear\_position (Position des Ganghebels) | Gangposition des Fahrzeugs | Positionen: Erster, Zweiter, Dritter, Vierter, Fünfter, Sechster, Siebter, Achter
Ignition\_status (Status der Zündung) | Gibt an, ob das Fahrzeug fährt oder ob die Zündung ausgeschaltet ist | „true“ oder „false“
Windshield\_wiper\_status (Scheibenwischerstatus) | Gibt an, ob die Scheibenwischer eingeschaltet sind | „true“ oder „false“
ABS (ABS) | Gibt an, ob ABS aktiviert ist | „true“ oder „false“
Timestamp (Zeitstempel) | Zeitstempel für die Erstellung des Datenpunkts | Datum
City (Ort) | Standort des Fahrzeugs | Diese Projektmappe enthält vier Orte : Bellevue, Redmond, Sammamish, Seattle


Das Referenzdataset für das Fahrzeugmodell enthält die Zuordnung von Fahrzeug-Identifizierungsnummer und Modell.

VIN (Fahrzeug-Identifizierungsnummer) | Modell |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Limousine |
8J0U8XCPRGW4Z3NQE | Hybrid |
WORG68Z2PLTNZDBI7 | Familienlimousine |
JTHMYHQTEPP4WBMRN | Limousine |
W9FTHG27LZN1YWO0Y | Hybrid |
MHTP9N792PHK08WJM | Familienlimousine |
EI4QXI2AXVQQING4I | Limousine |
5KKR2VB4WHQH97PF8 | Hybrid |
W9NSZ423XZHAONYXB | Familienlimousine |
26WJSGHX4MA5ROHNL | Cabrio |
GHLUB6ONKMOSI7E77 | Kombi |
9C2RHVRVLMEJDBXLP | Kleinwagen |
BRNHVMZOUJ6EOCP32 | Kleines SUV |
VCYVW0WUZNBTM594J | Sportwagen |
HNVCE6YFZSA5M82NY | Mittelgroßes SUV |
4R30FOR7NUOBL05GJ | Kombi |
WYNIIY42VKV6OQS1J | Großes SUV |
8Y5QKG27QET1RBK7I | Großes SUV |
DF6OX2WSRA6511BVG | Coupé |
Z2EOZWZBXAEW3E60T | Limousine |
M4TV6IEALD5QDS3IR | Hybrid |
VHRA1Y2TGTA84F00H | Familienlimousine |
R0JAUHT1L1R3BIKI0 | Limousine |
9230C202Z60XX84AU | Hybrid |
T8DNDN5UDCWL7M72H | Familienlimousine |
4WPYRUZII5YV7YA42 | Limousine |
D1ZVY26UV2BFGHZNO | Hybrid |
XUF99EW9OIQOMV7Q7 | Familienlimousine
8OMCL3LGI7XNCC21U | Cabrio |
……. | |


### So generieren Sie simulierte Daten
1.	Klicken Sie oben rechts vom Knoten „Vehicle Telematics Simulator“ auf den Pfeil, um das Datensimulatorpaket herunterzuladen. Speichern und extrahieren Sie die Dateien lokal auf Ihrem Computer. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *Abbildung 3 – Lösungsblaupause für die Fahrzeugtelemetrieanalyse*

2.	Wechseln Sie auf Ihrem lokalen Computer zum Ordner, in den Sie das „Vehicle Telematics Simulator“-Paket extrahiert haben. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png) *Abbildung 4 – Ordner mit „Vehicle Telematics Simulator“*

3.	Führen Sie die Anwendung **CarEventGenerator.exe** aus.

### Referenzen

[Visual Studio-Projektmappe „Vehicle Telematics Simulator“](http://go.microsoft.com/fwlink/?LinkId=717075)

[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## Erfassung
Es wird eine Kombination aus Azure Event Hubs, Stream Analytics und Data Factory genutzt, um die Fahrzeugsignale sowie die Diagnoseereignisse und Echtzeit- und Batchanalysen zu erfassen. All diese Komponenten werden im Rahmen der Lösungsbereitstellung erstellt und konfiguriert.

### Echtzeitanalyse
Die vom Vehicle Telematics Simulator generierten Ereignisse werden mit dem Event Hub SDK auf dem Event Hub veröffentlicht. Der Stream Analytics-Auftrag erfasst diese Ereignisse vom Event Hub und verarbeitet die Daten in Echtzeit, um die Integrität des Fahrzeugs zu analysieren.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*Abbildung 5 – Event Hub-Dashboard*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*Abbildung 6 – Verarbeitungsdaten für Stream Analytics-Auftrag*

Der Stream Analytics-Auftrag erfasst die Daten vom Event Hub und führt eine Verknüpfung mit den Referenzdaten durch, um die Fahrzeug-Identifizierungsnummer dem entsprechenden Modell zuzuordnen. Außerdem werden die Daten in der Azure Blob Storage-Einheit dauerhaft gespeichert, um Batchanalysen durchführen zu können. Die unten angegebene Stream Analytics-Abfrage wird verwendet, um die Daten dauerhaft in der Azure Blob Storage-Einheit zu speichern.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*Abbildung 7 – Stream Analytics-Auftrag – Abfrage zur Datenerfassung*

### Batchanalyse
Wir generieren auch ein zusätzliches Volume mit simulierten Fahrzeugsignalen und einem Diagnosedataset, um eine umfassendere Batchanalyse zu ermöglichen. Dies ist erforderlich, um für die Batchverarbeitung ein gutes, repräsentatives Datenvolume sicherzustellen. Zu diesem Zweck verwenden wir eine Pipeline mit dem Namen „PrepareSampleDataPipeline“ im Azure Data Factory-Workflow, um simulierte Fahrzeugsignale und ein Diagnosedataset für den Zeitraum eines Jahres zu generieren. Klicken Sie auf [Data Factory custom activity](http://go.microsoft.com/fwlink/?LinkId=717077), um die Visual Studio-Projektmappe „Data Factory custom DotNet activity“ herunterzuladen, wenn Sie Anpassungen nach Ihren Anforderungen vornehmen möchten.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*Abbildung 8 – Vorbereiten von Beispieldaten für den Workflow der Batchverarbeitung*

Die Pipeline umfasst eine benutzerdefinierte ADF-.Net-Aktivität, wie hier zu sehen ist:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*Abbildung 9 – PrepareSampleDataPipeline*

Nachdem die Pipeline erfolgreich ausgeführt wurde und das Dataset „RawCarEventsTable“ mit „Ready“ gekennzeichnet ist, werden simulierte Fahrzeugsignale und Diagnosedaten für den Zeitraum eines Jahrs generiert. Sie sehen, dass in Ihrem Speicherkonto unter dem Container „connectedcar“ der folgende Ordner und die folgende Datei erstellt werden:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*Abbildung 10 – PrepareSampleDataPipeline Output*

### Referenzen

[Azure Event Hub-SDK für Datenstromerfassung](event-hubs-csharp-ephcs-getstarted.md)

[Azure Data Factory-Funktionen für die Datenverschiebung](data-factory-data-movement-activities.md) [Azure Data Factory DotNet-Aktivität](data-factory-use-custom-activities.md)

[Visual Studio-Projektmappe „Azure Data Factory-DotNet-Aktivität“ zum Vorbereiten von Beispieldaten](http://go.microsoft.com/fwlink/?LinkId=717077)


## Vorbereiten
>[AZURE.ALERT] Dieser Schritt der Projektmappe gilt nur für die Batchverarbeitung.

Die unformatierten, halbstrukturierten Fahrzeugsignale und das Diagnosedataset werden im Datenvorbereitungsschritt in das Format JAHR/MONAT partitioniert, um effiziente Abfragen und eine skalierbare langfristige Speicherung zu ermöglichen (also ein Failover von einem Blob-Konto zum nächsten, wenn das erste voll ist). Die Ausgabedaten (mit der Bezeichnung *PartitionedCarEventsTable*) werden im „Data Lake“ des Kunden über einen längeren Zeitraum als grundlegende „Rohdaten“ aufbewahrt. Die Eingabedaten für die Pipeline werden normalerweise verworfen, da die Ausgabedaten die Qualität der Eingabedaten voll widerspiegeln. Sie werden lediglich für die nachfolgende Nutzung besser gespeichert (partitioniert).

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Abbildung 11 – Workflow „Partition Car Events“*

Die unformatierten Daten werden mit einer Hive HDInsight-Aktivität in „PartitionCarEventsPipeline“ partitioniert. Die in Schritt 1 generierten Beispieldaten für den Zeitraum eines Jahrs werden nach JAHR/MONAT partitioniert, um Fahrzeugsignale und Diagnosedatenpartitionen für jeden Monat eines Jahrs zu generieren (insgesamt zwölf Partitionen).

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Abbildung 12 – PartitionCarEventsPipeline*

Das unten angegebene Hive-Skript mit dem Namen „partitioncarevents.hql“ wird für die Partitionierung verwendet und befindet sich im Ordner „\\demo\\src\\connectedcar\\scripts“ der heruntergeladenen ZIP-Datei.

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Abbildung 13 – PartitionConnectedCarEvents-Hive-Skript*

Nachdem die Pipeline erfolgreich ausgeführt wurde, werden im Speicherkonto unter dem Container „connectedcar“ die unten angegebenen Partitionen generiert.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Abbildung 14 – Partitionierte Ausgabe*

Die Daten sind jetzt optimiert, besser verwaltbar und bereit für die weitere Verarbeitung, damit Sie umfassende Erkenntnisse zu den Batches gewinnen können.

## Datenanalyse

In diesem Abschnitt erfahren Sie, wie wir die Kombination aus Azure Stream Analytics, Azure Machine Learning, Azure Data Factory und Azure HDInsight für die umfassende erweiterte Analyse des Fahrzeugzustands und des Fahrverhaltens verwendet haben. Es gibt hier drei Unterabschnitte:

1.	**Machine Learning**: Dieser Unterabschnitt enthält Informationen zum Experiment zur Erkennung von Anomalien. Wir haben dieses Experiment in dieser Projektmappe verwendet, um vorherzusagen, dass Fahrzeuge zur Wartung müssen oder aufgrund von Sicherheitsproblemen zurückgerufen werden müssen.
2.	**Echtzeitanalyse**: Dieser Unterabschnitt enthält Informationen zu den Echtzeitanalysen mit der Stream Analytics-Abfragesprache und zum Operationalisieren des Machine Learning-Experiments in Echtzeit mit einer benutzerdefinierten Anwendung.
3.	**Batchanalyse**: Dieser Unterabschnitt enthält Informationen zur Transformation und Verarbeitung der Batchdaten mit Azure HDInsight und Azure Machine Learning per Operationalisierung mit Azure Data Factory.

### Machine Learning

Das Ziel besteht hierbei darin, die Fahrzeuge vorherzusagen, die gewartet oder aufgrund bestimmter Integritätsstatistiken zurückgerufen werden müssen. Wir gehen von folgenden Annahmen aus:

- Für Fahrzeuge ist eine **Wartung** erforderlich, wenn eine der folgenden drei Bedingungen erfüllt ist:
	- Niedriger Reifendruck
	- Niedriger Motorölstand
	- Hohe Motortemperatur

- Für Fahrzeuge besteht unter Umständen ein **Sicherheitsproblem** und die Notwendigkeit eines **Rückrufs**, wenn eine der folgenden Bedingungen erfüllt ist:
	- Hohe Motortemperatur bei niedriger Außentemperatur
	- Niedrige Motortemperatur bei hoher Außentemperatur

Basierend auf den obigen Anforderungen haben wir zwei separate Modelle zum Erkennen von Anomalien erstellt: eins für die Erkennung der Fahrzeugwartung und eins für die Erkennung des Fahrzeugrückrufs. Für beide Modelle wird zur Erkennung von Anomalien der integrierte Principal Component Analysis (PCA)-Algorithmus verwendet.

**Modell für die Erkennung der Wartung** Beim Modell für die Erkennung der Wartung wird eine Anomalie gemeldet, wenn für einen von drei Indikatoren – Reifendruck, Motoröl oder Motortemperatur – die jeweilige Bedingung erfüllt ist. Daher müssen wir beim Erstellen des Modells nur diese drei Variablen berücksichtigen. Für unser Experiment in Azure Machine Learning verwenden wir zuerst das Modul **Project Columns**, um diese drei Variablen zu extrahieren. Als Nächstes verwenden wir das PCA-basierte Modul für die Erkennung von Anomalien, um das Modell für die Erkennung von Anomalien zu erstellen.

Die Hauptkomponentenanalyse (Principal Component Analysis, PCA) ist ein bewährtes Machine Learning-Verfahren, das auf die Featureauswahl, Klassifizierung und Erkennung von Anomalien angewendet werden kann. Bei der PCA wird ein Gruppe von Fällen, die unter Umständen korrelierte Variablen enthalten, in eine Gruppe von Werten konvertiert, die als Hauptkomponenten (Principal Components) bezeichnet werden. Der Hauptgedanke der PCA-basierten Modellierung ist das Projizieren von Daten auf eine niedrigere Dimension, damit Merkmale und Anomalien leichter identifiziert werden können.
 
Bei der Erkennung von Anomalien berechnet die Anomalieerkennung für jede neue Eingabe zuerst die Projektion auf die Eigenvektoren und dann den normalisierten Rekonstruktionsfehler. Dieser normalisierte Fehler ist das Anomalieergebnis. Je höher der Fehler, desto anomaler ist die Instanz.

Bei der Wartungserkennung kann jeder Datensatz als Punkt in einem dreidimensionalen Raum angesehen werden, der durch die Koordinaten für Reifendruck, Motoröl und Motortemperatur definiert wird. Zum Erfassen dieser Anomalien können wir die Originaldaten per PCA aus dem dreidimensionalen Raum in einen zweidimensionalen Raum projizieren. Daher legen wir den Parameter für die Anzahl der Komponenten, die für die PCA verwendet werden sollen, auf „2“ fest. Dieser Parameter spielt eine wichtige Rolle beim Anwenden der PCA-basierten Erkennung von Anomalien. Nach dem Projizieren der Daten per PCA können wir diese Anomalien leichter identifizieren.

**Modell für die Erkennung von Anomalien** Für das Modell zur Erkennung von Anomalien in Bezug auf Rückrufe verwenden wir die Module „Project Columns“ und „PCA-basierte Erkennung“ auf ähnliche Weise. Zuerst extrahieren wir dabei mit dem Modul **Project Columns** drei Variablen: Motortemperatur, Außentemperatur und Geschwindigkeit. Wir beziehen die Geschwindigkeitsvariable ein, da die Motortemperatur normalerweise mit der Geschwindigkeit korreliert. Als Nächstes verwenden wir das Modul für die PCA-basierte Erkennung von Anomalien, um die Daten aus dem dreidimensionalen Raum in einen zweidimensionalen Raum zu projizieren. Die Kriterien für den Rückruf werden erfüllt, und für das Fahrzeug ist ein Rückruf erforderlich, wenn die Motortemperatur und die Außentemperatur eine hohe negative Korrelation aufweisen. Indem wir den Algorithmus für die PCA-basierte Erkennung von Anomalien verwenden, können wir die Anomalien nach Durchführung der PCA erfassen.

Beachten Sie Folgendes: Beim Trainieren beider Modelle müssen wir normale Daten, bei denen keine Wartung bzw. kein Rückruf erforderlich ist, als Eingabedaten verwenden, um das Modell für die PCA-basierte Erkennung von Anomalien zu trainieren. Beim Bewertungsexperiment nutzen wir das trainierte Modell zur Erkennung von Anomalien, um erkennen zu können, ob für das Fahrzeug eine Wartung oder ein Rückruf erforderlich ist.


### Echtzeitanalyse

Die folgende Stream Analytics-SQL-Abfrage wird verwendet, um den Mittelwert aller wichtigen Fahrzeugparameter zu bilden, z. B. Geschwindigkeit, Kraftstoffstand, Motortemperatur, Kilometerzähleranzeige, Reihendruck, Motorölstand usw. So können Anomalien erkannt und Warnungen ausgegeben werden. Außerdem kann der Gesamtzustand von Fahrzeugen ermittelt werden, die in einer bestimmten Region bewegt werden, und dieser Wert kann dann mit demografischen Daten korreliert werden.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Abbildung 15 – Stream Analytics-Abfrage für Echtzeitverarbeitung

Alle Mittelwerte werden mit einem rollierenden Fenster (TumblingWindow) von drei Sekunden berechnet. Wir verwenden hier ein rollierendes Fenster, da wir nicht überlappende und zusammenhängende Zeitintervalle benötigen.

Weitere Informationen zu allen „Windowing“-Funktionen in Azure Stream Analytics erhalten Sie auf der Seite [Windowing (Azure Streamanalyse)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Echtzeitvorhersage**

Die Projektmappe enthält eine Anwendung, mit der das Machine Learning-Modell in Echtzeit operationalisiert werden kann. Diese Anwendung hat den Namen „RealTimeDashboardApp“ und wird im Rahmen der Lösungsbereitstellung erstellt und konfiguriert. Die Anwendung führt Folgendes aus:

1.	Sie lauscht auf eine Event Hub-Instanz, auf der Stream Analytics die Ereignisse in einem kontinuierlichen Muster veröffentlicht. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Abbildung 16 – Stream Analytics-Abfrage zum Veröffentlichen der Daten auf einer Event Hub-Ausgabeinstanz* 

2.	Für jedes Ereignis, das diese Anwendung empfängt, wird Folgendes durchgeführt:

	- Die Daten werden über einen Endpunkt mit Machine Learning-Request-Response-Bewertung (RRS) verarbeitet. Der RRS-Endpunkt wird automatisch als Teil der Bereitstellung veröffentlicht.
	- Die RRS-Ausgabe wird mit den Push-APIs also in einem PowerBI-Dataset veröffentlicht.

Dieses Muster gilt auch für Szenarien, bei denen Sie eine Branchenanwendung in den Ablauf der Echtzeitanalyse integrieren möchten, z. B. für Warnungen, Benachrichtigungen, Messaging usw.

Klicken Sie auf [RealtimeDashboardApp download](http://go.microsoft.com/fwlink/?LinkId=717078), um die Visual Studio-Projektmappe „RealtimeDashboardApp“ zur Verwendung für Anpassungen herunterzuladen.

****So führen Sie die Echtzeit-Dashboardanwendung aus **

1.	Klicken Sie in der Diagrammansicht auf den Knoten „Power BI“ und dann im Eigenschaftenbereich auf den Link „Echtzeit-Dashboardanwendung herunterladen“. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *Abbildung 17 – Setupanweisungen zum PowerBI-Dashboard*
2.	Lokales Extrahieren und Speichern ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Abbildung 18 – Ordner „RealtimeDashboardApp“*
3.	Führen Sie die Anwendung „RealtimeDashboardApp.exe“ aus.
4.	Geben Sie gültige Power BI-Anmeldeinformationen ein, melden Sie sich an, und klicken Sie auf „Annehmen“. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Abbildung 19 – RealtimeDashboardApp: Anmelden bei PowerBI*

>[AZURE.NOTE] Hinweis: Um die Daten für das PowerBI-Dataset zu löschen, führen Sie RealtimeDashboardApp mit dem Parameter „flushdata“ aus:

	RealtimeDashboardApp.exe -flushdata

### Batchanalyse

Hierbei soll veranschaulicht werden, wie Contoso Motors die Azure-Berechnungsfunktionen in Bezug auf Big Data nutzt, um umfassende Erkenntnisse zum Fahrverhalten, Nutzungsverhalten und Fahrzeugzustand zu gewinnen. Dies dient folgenden Zielen:

- Verbessern der Kundenerfahrung und Kostensenkung, indem Erkenntnisse zu Fahrgewohnheiten und kraftstoffsparendem Fahrverhalten gewonnen werden
- Proaktives Gewinnen von Informationen zu Kunden und ihrem Fahrverhalten, um bessere Geschäftsentscheidungen treffen und die besten Produkte und Dienstleistungen anbieten zu können

Bei dieser Lösung liegt der Schwerpunkt auf folgenden Metriken:

1.	**Aggressives Fahrverhalten** Identifiziert den Trend in Bezug auf Modelle, Standorte, Fahrbedingungen und Jahreszeiten, um Erkenntnisse zu aggressivem Fahrverhalten zu gewinnen, damit Contoso Motors diese Daten für Marketingkampagnen, neue personalisierte Funktionen und eine nutzungsabhängige Versicherung einsetzen kann.
2.	**Kraftstoffsparendes Fahrverhalten** Identifiziert den Trend in Bezug auf Modelle, Standorte, Fahrbedingungen und Jahreszeiten, um Erkenntnisse zu kraftstoffsparendem Fahrverhalten zu gewinnen. Diese Daten kann Contoso Motors für Marketingkampagnen, neue Funktionen und das proaktive Bereitstellen von Daten für Fahrer nutzen, um das kostensparende und umweltfreundliche Fahrverhalten zu fördern. 
3.	**Rückrufmodelle** Identifiziert Modelle, für die ein Rückruf erforderlich ist, indem das Machine Learning-Experiment zur Erkennung von Anomalien operationalisiert wird.

Wir sehen uns nun die Details dieser Metriken an.


**Aggressives Fahrmuster**

Die partitionierten Fahrzeugsignale und Diagnosedaten werden in der Pipeline mit dem Namen „AggresiveDrivingPatternPipeline“ per Hive verarbeitet, um die Modelle, den Standort, die Fahrzeug- und Fahrbedingungen usw. zu bestimmen, die auf ein aggressives Fahrmuster hindeuten.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *Abbildung 20 – Aggressives Fahrmuster – Workflow*

Das Hive-Script mit dem Namen „aggresivedriving.hql“, das zum Analysieren des aggressiven Fahrmusters verwendet wird, befindet sich im Ordner „\\demo\\src\\connectedcar\\scripts“ der heruntergeladenen ZIP-Datei.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Abbildung 21 – Aggressives Fahrmuster – Hive-Abfrage*

Hierbei wird die Kombination aus Ganghebelposition, Gaspedalstatus und Geschwindigkeit des Fahrzeugs verwendet, um ein rücksichtsloses bzw. aggressives Fahrverhalten anhand des Bremsmusters bei hoher Geschwindigkeit zu erkennen.

Nachdem die Pipeline erfolgreich ausgeführt wurde, werden im Speicherkonto unter dem Container „connectedcar“ die unten angegebenen Partitionen generiert.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*Abbildung 22 – Ausgabe von „AggressiveDrivingPatternPipeline“*


**Kraftstoffsparendes Fahrmuster**

Die partitionierten Fahrzeugsignale und Diagnosedaten werden in der Pipeline mit dem Namen „FuelEfficientDrivingPatternPipeline“ per Hive verarbeitet, um die Modelle, den Standort, die Fahrzeug- und Fahrbedingungen usw. zu bestimmen, die auf ein kraftstoffsparendes Fahrmuster hindeuten.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*Abbildung 23 – Kraftstoffsparendes Fahrmuster – Workflow*

Das Hive-Script mit dem Namen „fuelefficientdriving.hql“, das zum Analysieren des aggressiven Fahrmusters verwendet wird, befindet sich im Ordner „\\demo\\src\\connectedcar\\scripts“ der heruntergeladenen ZIP-Datei.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Abbildung 24 – Kraftstoffsparendes Fahrmuster – Hive-Abfrage*

Hierbei wird die Kombination aus Ganghebelposition, Bremspedalstatus, Geschwindigkeit und Gaspedalposition des Fahrzeugs verwendet, um kraftstoffsparendes Fahrverhalten anhand des Musters aus Beschleunigung, Bremsmanövern und Geschwindigkeit zu erkennen.

Nachdem die Pipeline erfolgreich ausgeführt wurde, werden im Speicherkonto unter dem Container „connectedcar“ die unten angegebenen Partitionen generiert.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*Abbildung 25 – Ausgabe von „FuelEfficientDrivingPatternPipeline“*


**Vorhersage von Rückrufen**

Das Machine Learning-Experiment wird im Rahmen der Lösungsbereitstellung als Webdienst bereitgestellt und veröffentlicht. Der Endpunkt für die Batchbewertung wird in diesem Workflow genutzt, als Dienst mit Data Factory-Verknüpfung registriert und mit der Data Factory-Aktivität für die Batchbewertung operationalisiert.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*Abbildung 26 – Machine Learning-Endpunkt, der als verknüpfter Dienst in Data Factory registriert ist*

Der registrierte verknüpfte Dienst wird in „DetectAnomalyPipeline“ verwendet, um die Daten mit dem Modell zur Erkennung von Anomalien zu bewerten.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*Abbildung 27 – Azure Machine Learning-Aktivität für die Batchbewertung in Data Factory*

In dieser Pipeline werden einige Schritte für die Datenvorbereitung ausgeführt, damit die Operationalisierung mit dem Webdienst für die Batchbewertung erfolgen kann.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*Abbildung 28 – DetectAnomalyPipeline zum Vorhersagen von Fahrzeugen, für die ein Rückruf erforderlich ist*

Nach Abschluss der Bewertung wird eine HDInsight-Aktivität verwendet, um die Daten zu verarbeiten und zu aggregieren, die vom Modell mit einer Wahrscheinlichkeitspunktzahl von 0,60 oder höher als Anomalien kategorisiert werden.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'

*Abbildung 29 – Hive-Abfrage für Rückrufaggregation*

Nachdem die Pipeline erfolgreich ausgeführt wurde, werden im Speicherkonto unter dem Container „connectedcar“ die unten angegebenen Partitionen generiert.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*Abbildung 30 – Ausgabe von „DetectAnomalyPipeline“*


## Veröffentlichen

### Echtzeitanalyse

Eine der Abfragen im Stream Analytics-Auftrag veröffentlicht die Ereignisse auf einer Event Hub-Ausgabeinstanz.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Abbildung 31 – Veröffentlichung auf einer Event Hub-Ausgabeinstanz durch einen Stream Analytics-Auftrag*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Abbildung 32 – Stream Analytics-Abfrage zum Veröffentlichen auf der Event Hub-Ausgabeinstanz*

Dieser Datenstrom mit Ereignissen wird von der RealTimeDashboardApp genutzt, die in der Projektmappe enthalten ist. Diese Anwendung nutzt den Machine Learning-Request-Response-Webdienst für die Echtzeitbewertung und veröffentlicht die sich ergebenden Daten zur Nutzung in einem PowerBI-Dataset.

### Batchanalyse

Die Ergebnisse der Batch- und Echtzeitverarbeitung werden zur Nutzung in den Tabellen von Azure SQL-Datenbank veröffentlicht. Azure SQL Server, die Datenbank und die Tabellen werden im Rahmen des Setupskripts automatisch erstellt.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Abbildung 33 – Kopieren von Ergebnissen der Batchverarbeitung in den Data Mart-Workflow*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Abbildung 34 – Veröffentlichung in Data Mart durch Stream Analytics-Auftrag*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Abbildung 35 – Data Mart-Einstellung in Stream Analytics-Auftrag*


## Nutzen

Power BI bietet für diese Lösung ein umfassendes Dashboard für Visualisierungen von Echtzeitdaten und Predictive Analytics.

Klicken Sie hier, um eine ausführliche Anleitung zum Einrichten der PowerBI-Berichte und des Dashboards zu erhalten. Das fertige Dashboard sieht wie folgt aus:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Abbildung 36 – PowerBI-Dashboard*

## Zusammenfassung

In diesem Dokument wird die Projektmappe für die Fahrzeugtelemetrieanalyse (Vehicle Telemetry Analytics) ausführlich beschrieben. Es wird ein Lambda-Architekturmuster für die Echtzeit- und Batchanalyse mit Vorhersagen und Aktionen veranschaulicht. Dieses Muster gilt für eine Vielzahl von Anwendungsfällen, für die „Hot Path“-Analysen (Echtzeit) und „Cold Path“-Analysen (Batch) erforderlich sind.

<!---HONumber=AcomDC_0128_2016-->