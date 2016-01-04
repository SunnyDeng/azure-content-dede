<properties 
	pageTitle="Lösungsvorlage „Vehicle Telemetry Analytics“ – Anweisungen zur Einrichtung des Power BI-Dashboards | Microsoft Azure" 
	description="Verwenden Sie die Funktionen von Cortana Analytics zur Echtzeitgewinnung von prädiktiven Einblicken in Fahrzeugzustand und Fahrgewohnheiten." 
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
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Lösungsvorlage „Vehicle Telemetry Analytics“ – Anweisungen zur Einrichtung des Power BI-Dashboards

Dieses **Menü** bietet Links zu den Kapiteln dieses Playbooks.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


Die Lösung „Vehicle Telemetry Analytics“ veranschaulicht, wie Autohäuser, Automobilhersteller und Versicherer die Fähigkeiten von Cortana Analytics nutzen können. Sie können sich prädiktive Einblicke zu Fahrzeugzustand und Fahrgewohnheiten in Echtzeit verschaffen, um in den Bereichen Kundenerfahrung, Forschung und Entwicklung und Marketingkampagnen für Verbesserungen zu sorgen. Dieses Dokument enthält schrittweise Anleitungen, wie Sie Power BI-Berichte und das Dashboard konfigurieren können, nachdem die Lösung in Ihrem Abonnement bereitgestellt wurde.


## Voraussetzungen
1.	Stellen Sie die Lösung „Vehicle Telemetry Analytics“ bereit, indem Sie sie von [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3) herunterladen und installieren.  
2.	[Installieren Sie Microsoft Power BI Desktop.](http://www.microsoft.com/download/details.aspx?id=45331)
3.	Ein [Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/). Wenn Sie kein Abonnement haben, steigen Sie mit dem kostenlosen Azure-Abonnement ein.
4.	Microsoft Power BI-Konto
	

## Cortana Analytics Suite-Komponenten
Als Teil der Lösungsvorlage „Vehicle Telemetry Analytics“ werden die folgenden Cortana Analytics-Dienste in Ihrem Abonnement bereitgestellt.

- **Event Hubs** für das Erfassen von Millionen von Telemetrie-Ereignissen in Azure.
- **Stream Analytics** für das Gewinnen von Einblicken in Echtzeit zum Fahrzeugzustand und Speichern dieser Daten in beständigem Speicher zur umfangreicheren Batchanalyse.
- **Machine Learning** für die Erkennung von Anomalien in Echtzeit und Batchverarbeitung zum Gewinnen prädiktiver Einblicke.
- **HDInsight** wird verwendet, um Daten nach Maß zu transformieren.
- **Data Factory** übernimmt die Orchestrierung, Planung, Ressourcenverwaltung und Überwachung der Batchverarbeitungs-Pipeline.

**Power BI** bietet dieser Lösung ein umfassendes Dashboard für Visualisierungen von Echtzeitdaten und Predictive Analytics.

Die Lösung verwendet zwei Datenquellen: **Dataset mit simulierten Fahrzeugsignalen und Diagnosedataset** und **Fahrzeugkatalog**.

Zu dieser Lösung gehört ein Simulator für Fahrzeugtelematik. Diese Anwendung gibt Diagnosedaten und Signale entsprechend dem Zustand des Fahrzeugs und Fahrmuster zu einem bestimmten Zeitpunkt aus.

Der Fahrzeugkatalog ist ein Referenzdataset mit der Fahrgestellnummer (VIN) zur Modellierung der Zuordnung.


## Vorbereitung des Power BI-Dashboards

### Bereitstellung

Nach der Bereitstellung sollte das folgende Diagramm angezeigt werden, in dem alle diese Komponenten grün gekennzeichnet sind.

- Klicken Sie auf der grünen Leiste auf den nach rechts oben zeigenden Pfeil, um zu den entsprechenden Diensten zu navigieren und zu prüfen, ob diese erfolgreich bereitgestellt wurden.
- Klicken Sie im Feld mit dem „Vehicle Telematics Simulator“ auf den rechts oben zeigenden Pfeil, um das Datensimulatorpaket herunterzuladen. Speichern und extrahieren Sie die Dateien lokal auf Ihrem Computer. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Nun können Sie das Power BI-Dashboard mit umfassenden Visualisierungen konfigurieren, um sich in Echtzeit prädiktive Einblicke zu Fahrzeugzustand und Fahrgewohnheiten zu verschaffen. Es dauert ca. 45-60 Minuten, um alle Berichte zu erstellen und das Dashboard zu konfigurieren.


### Einrichten des Power BI-Echtzeitdashboards

**Generieren simulierter Daten**

1. Wechseln Sie auf Ihrem lokalen Computer zum Ordner, in den Sie das „Vehicle Telematics Simulator“-Paket extrahiert haben. ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.	Führen Sie die Anwendung ***CarEventGenerator.exe*** aus.
3.	Diese Anwendung gibt Diagnosedaten und Signale entsprechend dem Zustand des Fahrzeugs und Fahrmuster zu einem bestimmten Zeitpunkt aus. Diese Daten werden in einer Azure Event Hub-Instanz veröffentlicht, die als Teil der Bereitstellung konfiguriert ist.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
**Starten der Echtzeit-Dashboardanwendung**

Zur Lösung gehört eine Anwendung zum Generieren des Echtzeitdashboards in Power BI. Diese Anwendung überwacht eine Event Hub-Instanz, in der Stream Analytics die Ereignisse fortlaufend veröffentlicht. Die Daten jedes von dieser Anwendung empfangenen Ereignisses werden mithilfe eines Machine Learning Request-Response-Bewertungsendpunkts verarbeitet. Das resultierende Dataset wird zur Visualisierung für die Push-APIs von Power BI veröffentlicht.

So laden Sie die Anwendung herunter

1.	Klicken Sie in der Diagrammansicht auf den Knoten „Power BI“ und dann im Eigenschaftenbereich auf den Link **Echtzeit-Dashboardanwendung herunterladen**.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.	Extrahieren Sie die Anwendung, und speichern Sie sie an einem lokalen Speicherort. ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	Führen Sie die Anwendung **RealtimeDashboardApp.exe** aus.
4.	Geben Sie gültige Power BI-Anmeldeinformationen ein, melden Sie sich an, und klicken Sie auf **Annehmen**.
	
	![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
	![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### Konfigurieren von Power BI-Berichten
Die Erstellung der Echtzeitberichte und des Dashboards dauert ca. 30 bis 45 Minuten. Navigieren Sie zu [http://powerbi.com](http://powerbi.com), und melden Sie sich an.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Ein neues Dataset wird in Power BI generiert. Klicken Sie auf das Dataset **ConnectedCarsRealtime**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Speichern Sie den leeren Bericht durch Drücken von **STRG+S**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Geben Sie den Berichtsnamen *Vehicle Telemetry Analytics Real Time – Reports* ein.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## Echtzeitberichte
Diese Lösung enthält drei Echtzeitberichte:

1.	Vehicles in operation
2.	Vehicles Requiring Maintenance
3.	Vehicles Health Statistics

Sie können alle drei Echtzeitberichte konfigurieren oder nach jeder Phase stoppen und mit dem nächsten Abschnitt der Konfiguration von Batchberichten fortfahren. Wir empfehlen Ihnen, alle drei Berichte zu erstellen, um den Echtzeitpfad der Lösung in vollem Umfang zu visualisieren.

### 1\. Vehicles in operation
  
Doppelklicken Sie auf **Seite 1**, und benennen Sie sie in „Vehicles in operation“ um. ![Connected Cars – Vehicles in operation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

Wählen Sie unter **Felder** das Feld **vin** und als Visualisierungstyp **Karte** aus.

Die Kartenvisualisierung erfolgt wie in der Abbildung gezeigt. ![Connected Cars – Wählen Sie „vin“ aus](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie unter „Felder“ **City** und **vin** aus. Ändern Sie die Visualisierung in **Zuordnung**. Ziehen Sie **vin** in den Bereich „Values“. Ziehen Sie **City** aus „Felder“ in den Bereich **Legende**. ![Connected Cars – Kartenvisualisierung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Wählen Sie unter **Visualisierung** den Abschnitt **Format** aus. Klicken Sie auf **Titel**, und ändern Sie den Text in **Vehicles in operation by city**. ![Connected Cars – Vehicles in operation by city](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

Die endgültige Visualisierung sieht wie in der Abbildung aus. ![Connected Cars – Endgültige Visualisierung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie **City** und **vin** aus, und ändern Sie den Visualisierungstyp in **Gruppiertes Säulendiagramm**. Stellen Sie sicher, dass das Feld **City** im Bereich **Achse** und **vin** im Bereich **Wert** enthalten ist.

Sortieren Sie das Diagramm nach **Count of vin**. ![Connected Cars – Count of vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

Ändern Sie den **Titel** des Diagramms in **Vehicles in operation by city**.

Klicken Sie auf den Abschnitt **Format**, und wählen Sie **Datenfarben** aus. Klicken Sie auf **Ein**, um **Alle anzeigen** zu aktivieren. ![Connected Cars – Alle Datenfarben zeigen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

Ändern Sie die Farbe der einzelnen Städte durch Klicken auf das Farbsymbol. ![Connected Cars – Farben ändern](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie unter „Visualisierungen“ **Gruppiertes Säulendiagramm** aus. Ziehen Sie das Feld **city** in den Bereich **Achse**, **Model** in den Bereich **Legende** und **vin** in den Bereich **Wert**. ![Connected Cars – Gruppiertes Säulendiagramm](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Connected Cars – Rendering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Ordnen Sie alle Visualisierungen wie in der Abbildung gezeigt an. ![Connected Cars – Visualisierungen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Sie haben den Echtzeitbericht „Vehicles in operation“ erfolgreich konfiguriert. Sie können fortfahren, um den nächsten Echtzeitbericht zu erstellen, oder hier aufhören und das Dashboard konfigurieren.

### 2\. Vehicles Requiring Maintenance
  
Klicken Sie auf ![Hinzufügen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png), um einen neuen Bericht hinzuzufügen. Benennen Sie ihn in **Vehicles Requiring Maintenance** um.

![Connected Cars – Vehicles Requiring Maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

Wählen Sie das Feld **vin** aus, und ändern Sie den Visualisierungstyp in **Karte**. ![Connected Cars – Kartenvisualisierung für „vin“](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

Das Dataset enthält das Feld „MaintenanceLabel“. Dieses Feld kann den Wert „0“ oder „1“ haben. Er wurde vom Azure Machine Learning-Modell festgelegt, das als Teil der Lösung bereitgestellt und in den Echtzeitpfad integriert wurde. Der Wert „1“ gibt an, dass ein Fahrzeug gewartet werden muss.

Wir fügen den Filter **Seitenebene** hinzu, um Daten von Fahrzeugen anzuzeigen, die gewartet werden müssen.

1. Ziehen Sie das Feld **MaintenanceLabel** in **Filter auf Seitenebene**. ![Connected Cars – Filter auf Seitenebene](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Klicken Sie auf das Menü **Einfaches Filtern**, das sich unten im Filter auf Seitenebene „MaintenanceLabel“ befindet. ![Connected Cars – Einfache Filterung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  Legen Sie den Filterwert auf **1** fest. ![Connected Cars – Filterwert](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)


Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie unter „Visualisierungen“ **Gruppiertes Säulendiagramm** aus. ![Connected Cars – Kartenvisualisierung für „vin“](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Connected Cars – Gruppiertes Säulendiagramm](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Ziehen Sie das Feld **Model** in den Bereich **Achse** und **Vin** in den Bereich **Wert**. Sortieren Sie die Visualisierung dann nach **Count of vin**. Ändern Sie den **Titel** des Diagramms in **Vehicles requiring maintenance by model**.

Ziehen Sie die Felder vom Typ **vin** in den Bereich **Farbsättigung** im Abschnitt **Felder** ![Felder](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) der Registerkarte **Visualisierung**. ![Connected Cars – Farbsättigung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

Ändern Sie **Datenfarben** in „Visualizations“ im Abschnitt **Format**. Ändern Sie „Minimum color“ in **F2C812**, und ändern Sie „Maximum color“ in **FF6300**. ![Connected Cars – Farbänderung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Connected Cars – Neue Visualisierungsfarben](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie unter „Visualisierungen“ **Gruppiertes Säulendiagramm** aus. Ziehen Sie das Feld **vin** in den Bereich **Wert** und **City** in den Bereich **Achse**. Sortieren Sie das Diagramm nach **Count of vin**. Ändern Sie den **Titel** des Diagramms in **Vehicles requiring maintenance by city**. ![Connected Cars – Vehicles requiring maintenance by city](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie **Mehrzeilige Zuordnung** in „Visualisierungen“ aus. Ziehen Sie **Model** und **vin** in den Bereich **Felder**. ![Connected Cars – Mehrzeilige Zuordnung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

Ordnen Sie alle Visualisierungen neu an. Der endgültige Bericht sieht wie folgt aus: ![Connected Cars – Mehrzeilige Zuordnung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

Sie haben den Echtzeitbericht „Vehicles Requiring Maintenance“ erfolgreich konfiguriert. Sie können fortfahren, um den nächsten Echtzeitbericht zu erstellen, oder hier aufhören und das Dashboard konfigurieren.

### 3\. Vehicles Health Statistics
  
Klicken Sie auf ![Hinzufügen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png), um einen neuen Bericht hinzuzufügen. Benennen Sie ihn in **Vehicles Health Statistics** um.

Wählen Sie **Maßstab** in „Visualisierungen“ aus. Ziehen Sie dann das Feld **Geschwindigkeit** in die Bereiche **Wert, Minimumwert, Maximumwert**. ![Connected Cars – Mehrzeilige Zuordnung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

Ändern Sie die Standardaggregation **Geschwindigkeit** im Bereich **Wert** in **Durchschnitt**.

Ändern Sie die Standardaggregation **Geschwindigkeit** im Bereich **Minimum** in **Minimum**.

Ändern Sie die Standardaggregation **Geschwindigkeit** im Bereich **Maximum** in **Maximum**.

![Connected Cars – Mehrzeilige Zuordnung](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

Benennen Sie den Titel von **Maßstab** in **Average speed** um.
 
![Connected Cars – Maßstab](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Fügen Sie auf ähnliche Weise einen **Maßstab** für **Average engine oil**, **Average fuel** und **Average engine temperate** hinzu.

Ändern Sie die Standardaggregation von Feldern in jedem Maßstab gemäß den für den Maßstab **Durchschnittsgeschwindigkeit** erfolgten Schritten.

![Connected Cars – Maßstäbe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie **Linien- und gruppiertes Säulendiagramm** in „Visualisierungen“ aus. Ziehen Sie das Feld **City** in **Gemeinsame Achse**. Ziehen Sie die Felder **speed**, **tirepressure und engineoil** in den Bereich **Spaltenwerte**. Ändern Sie ihren Aggregationstyp in **Durchschnitt**.

Ziehen Sie das Feld **engineTemperature** in den Bereich **Linienwerte**. Ändern Sie den Aggregationstyp in **Durchschnitt**.

![Connected Cars – Visualisierungsfelder](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Ändern Sie den **Titel** des Diagramms in **Average speed, tire pressure, engine oil and engine temperature**.

![Connected Cars – Visualisierungsfelder](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie **Treemap** in „Visualisierungen“ aus. Ziehen Sie das Feld **Model** in den Bereich **Gruppe** und das Feld **MaintenanceProbability** in den Bereich **Werte**.

Ändern Sie den **Titel** des Diagramms in **Vehicle models requiring maintenance**.

![Connected Cars – Diagrammtitel ändern](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie **Gestapeltes Balkendiagramm (100 %)** in „Visualisierungen“ aus. Ziehen Sie das Feld **city** in den Bereich **Achse** und die Felder **MaintenanceProbability** und **RecallProbability** in den Bereich **Wert**.

![Connected Cars – Neue Visualisierung hinzufügen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Klicken Sie auf **Format**, wählen Sie **Datenfarben** aus, und ändern Sie die Farbe für **MaintenanceProbability** in den Wert **F2C80F**.

Ändern Sie den **Titel** des Diagramms in **Probability of Vehicle Maintenance & Recall by City**.

![Connected Cars – Neue Visualisierung hinzufügen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

Wählen Sie **Flächendiagramm** in „Visualisierungen“ aus. Ziehen Sie das Feld **Model** in den Bereich **Achse** und die Felder **engineOil, tirepressure, speed und MaintenanceProbability** in den Bereich **Werte**. Ändern Sie ihren Aggregationstyp in **Durchschnitt**.

![Connected Cars – Aggregationstyp ändern](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Ändern Sie den Titel des Diagramms in **Average engine oil, tire pressure, speed and maintenance probability by model**.

![Connected Cars – Diagrammtitel ändern](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Klicken Sie auf den leeren Bereich, um eine neue Visualisierung hinzufügen.

1. Wählen Sie in „Visualisierungen“ die Visualisierung **Punktdiagramm** aus.
2. Ziehen Sie das Feld **Model** in den Bereich **Details** und **Legende**. 
3. Ziehen Sie das Feld **fuel** in den Bereich **X-Achse**. Ändern Sie die Aggregation in „Durchschnitt“.
4. Ziehen Sie das Feld **engineTemparature** in den Bereich **Y-Achse**. Ändern Sie die Aggregation in **Durchschnitt**.
5. Ziehen Sie das Feld **vin** in den Bereich **Größe**.

![Connected Cars – Neue Visualisierung hinzufügen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Ändern Sie den **Titel** des Diagramms in **Averages of Fuel, Engine Temperature by Model**.

![Connected Cars – Diagrammtitel ändern](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Der endgültige Bericht sieht wie folgt aus.

![Connected Cars – Endgültiger Bericht](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### Anheften von Visualisierungen der Berichte an das Echtzeitdashboard
  
Erstellen Sie ein leeres Dashboard. Klicken Sie dazu neben „Dashboards“ auf das Pluszeichen. Geben Sie ihm den Namen „Vehicle Telemetry Analytics Dashboard“.

![Connected Cars – Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Heften Sie die Visualisierung aus den obigen Berichten an das Dashboard an.
 
![Connected Cars – Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Das Dashboard sieht wie unten dargestellt aus, wenn alle drei Berichte erstellt werden und die entsprechenden Visualisierungen an das Dashboard angeheftet werden. Ihr Dashboard sieht unter Umständen anders aus, wenn Sie nicht alle Berichte erstellt haben.

![Connected Cars – Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Glückwunsch! Sie haben das Echtzeit-Dashboard erfolgreich erstellt. Wenn Sie „CarEventGenerator.exe“ und „RealtimeDashboardApp.exe“ weiter ausführen, sollten im Dashboard Live-Updates zu sehen sein. Es dauert ungefähr 10 bis 15 Minuten, bis die unten angegebenen Schritte abgeschlossen sind.
 
##  Einrichten des Power BI-Dashboards für die Batchverarbeitung

Hinweis: Es dauert ca. zwei Stunden (ab dem erfolgreichen Abschluss der Bereitstellung), bis die Pipeline für die End-to-End-Batchverarbeitung die Ausführung beendet und generierte Daten für den Zeitraum eines Jahrs verarbeitet hat. Warten Sie diesen Zeitraum ab, ehe Sie mit den nächsten Schritten fortfahren.

**Herunterladen der PowerBI-Designer-Datei** • Eine vorkonfigurierte PowerBI-Designer-Datei ist in der Bereitstellung enthalten. • Klicken Sie in der Diagrammansicht auf den PowerBI-Knoten, und klicken Sie dann im Eigenschaftenbereich auf den Link „PowerBI-Designer-Datei herunterladen“. ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

• Speichern Sie die Datei lokal.

**Konfigurieren von PowerBI-Berichten** • Öffnen Sie die Designer-Datei „VehicleTelemetryAnalytics – Desktop-Report.pbix“ über PowerBI Desktop. Falls noch nicht geschehen, können Sie Power BI Desktop unter [Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331) herunterladen und installieren.

• Klicken Sie auf **Abfragen bearbeiten**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- Doppelklicken Sie auf **Quelle**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Aktualisieren Sie die Serververbindungszeichenfolge mit dem Azure SQL-Server, der im Rahmen der Bereitstellung eingerichtet wurde. Klicken Sie im Diagramm auf den Azure SQL-Knoten, und zeigen Sie den Servernamen im Eigenschaftenbereich an.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- Behalten Sie für **Datenbank** die Einstellung *connectedcar* bei.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Klicken Sie auf **OK**.
- Die Registerkarte **Windows-Anmeldeinformationen** ist standardmäßig ausgewählt. Wechseln Sie zu **Datenbank-Anmeldeinformationen**, indem Sie rechts auf die Registerkarte **Datenbank** klicken.
- Geben Sie den **Benutzernamen** und das **Kennwort** Ihrer Azure SQL-Datenbank ein, die beim Einrichten der Bereitstellung angegeben wurde.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Klicken Sie auf **Verbinden**.
- Wiederholen Sie die vorherigen Schritte für jede der verbleibenden drei Abfragen im rechten Bereich, und aktualisieren Sie die Details der Verbindung mit der Datenquelle.
- Klicken Sie auf **Schließen und laden**. Power BI Desktop-Dateidatasets sind mit SQL Azure-Datenbanktabellen verbunden.
- **Schließen** Sie die Power BI Desktop-Datei.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Klicken Sie auf die Schaltfläche **Speichern**, um die Änderungen zu speichern. 
 
Sie haben jetzt alle Berichte konfiguriert, die dem Batchverarbeitungspfad in der Lösung entsprechen.


## Hochladen auf *powerbi.com*
 
1.	Navigieren Sie zum PowerBI-Web-Portal unter http://powerbi.com, und melden Sie sich an.
2.	Klicken Sie auf „Daten abrufen“.  
3.	Laden Sie die Power BI Desktop-Datei hoch.  
4.	Klicken Sie zum Hochladen auf **Daten abrufen -> Dateien abrufen -> Lokale Datei**.  
5.	Navigieren Sie zu **„VehicleTelemetryAnalytics – Desktop Report.pbix“**.  
6.	Nachdem die Datei hochgeladen wurde, gelangen Sie wieder zu Ihrem Power BI-Arbeitsbereich.  

Ein Dataset, Bericht und leeres Dashboard werden für Sie erstellt.
 

Heften Sie Diagramme an das vorhandene Dashboard **Vehicle Telemetry Analytics Dashboard** in **Power BI** an. Klicken Sie in Power BI auf das zuvor erstellte leere Dashboard. Navigieren Sie zum Abschnitt **Berichte**, und klicken Sie auf den zuvor hochgeladenen Bericht.

![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**Der Bericht hat sechs Seiten:** Seite 1: Vehicle density Seite 2: Real time vehicle health Seite 3: Aggressively Driven Vehicles Seite 4: Recalled vehicles Seite 5: Fuel Efficiently Driven Vehicles Seite 6: Contoso Logo

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**Auf Seite 3** heften Sie Folgendes an: 1. Count of VIN ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

2.	Aggressively driven vehicles by model: Wasserfalldiagramm ![Vehicle Telemetry – Anheften von Diagramm 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Auf Seite 5** heften Sie Folgendes an: 1. Count of vin ![Vehicle Telemetry – Anheften von Diagramm 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2. Fuel efficient vehicles by model: Gruppiertes Säulendiagramm ![Vehicle Telemetry – Anheften von Diagramm 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Auf Seite 4** heften Sie Folgendes an:

1.	Count of vin ![Vehicle Telemetry – Anheften von Diagramm 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Recalled vehicles by city, model : Treemap ![Vehicle Telemetry – Anheften von Diagramm 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**Auf Seite 6** heften Sie Folgendes an:

1.	Contoso Motors-Logo ![Vehicle Telemetry – Anheften von Diagramm 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organisieren des Dashboards**

1.	Navigieren Sie zum Dashboard.
2.	Zeigen Sie auf jedes Diagramm, und benennen Sie es basierend auf der Benennung in der nachstehenden Abbildung des vollständigen Dashboards. Ordnen Sie außerdem die Diagramme so an, dass sie wie im nachstehenden Dashboard aussehen. ![Vehicle Telemetry – Organisieren von Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) ![Vehicle Telemetry PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Das endgültige abgeschlossene Dashboard sollte wie unten dargestellt aussehen, wenn Sie alle Berichte wie in diesem Dokument beschrieben erstellt haben. 

![Vehicle Telemetry – Organisieren von Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Glückwunsch! Sie haben die Berichte und das Dashboard erfolgreich erstellt, um in Bezug auf den Fahrzeugzustand und das Fahrverhalten Informationen in den Bereichen Echtzeit, vorbeugende Wartung und Batchprozesse zu erhalten.

<!---HONumber=AcomDC_1217_2015-->