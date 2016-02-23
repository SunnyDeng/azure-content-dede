<properties
 pageTitle="Exemplarische Vorgehensweise zur vorbeugenden Wartung | Microsoft Azure"
 description="Exemplarische Vorgehensweise zur vorkonfigurierten Azure IoT-Lösung für vorbeugende Wartung | Microsoft Azure"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/02/2015"
 ms.author="araguila"/>

# Exemplarische Vorgehensweise zur vorkonfigurierten Lösung für vorbeugende Wartung

## Einführung

Die vorkonfigurierte IoT Suite-Lösung für vorbeugende Wartung ist eine End-to-End-Lösung für ein Geschäftsszenario, bei dem der Zeitpunkt vorhergesagt wird, zu dem voraussichtlich ein Fehler auftritt. Sie können diese vorkonfigurierte Lösung proaktiv für Aktivitäten nutzen, z. B. zum Optimieren der Wartung. Bei dieser Lösung werden wichtige Azure IoT Suite-Dienste kombiniert, z. B. ein [Azure Machine Learning][lnk_machine_learning]-Arbeitsbereich mit Experimenten zum Vorhersagen der Restlebensdauer (Remaining Useful Life, RUL) eines Flugzeugtriebwerks anhand eines öffentlichen Datasets mit Beispielwerten. Als Ausgangspunkt bietet die Lösung eine vollständige Implementierung des Geschäftsszenarios, damit Sie diese Art von IoT-Lösung planen und implementieren können, um Ihre eigenen speziellen Geschäftsanforderungen zu erfüllen.

## Logische Architektur

Das folgende Diagramm beschreibt die logischen Komponenten der vorkonfigurierten Lösung:

![][img-architecture]

Die blauen Elemente sind Azure-Dienste, die an dem Standort bereitgestellt werden, den Sie beim Bereitstellen der vorkonfigurierten Lösung auswählen. Sie können die vorkonfigurierte Lösung in der Region USA (Osten), Nordeuropa oder Asien (Osten) bereitstellen.

Einige Ressourcen sind in den Regionen, in denen Sie die vorkonfigurierte Lösung bereitstellen, nicht verfügbar. Die orangefarbenen Elemente im Diagramm stehen für die Azure-Dienste, die in Bezug auf die ausgewählte Region in der nächsten verfügbaren Region bereitgestellt werden (USA (Mitte/Süden), Westeuropa oder Südostasien).

Das grüne Element ist ein simuliertes Gerät, das für ein Flugzeugtriebwerk steht. Weitere Informationen zu diesen simulierten Geräten finden Sie weiter unten.

Die grauen Elemente stehen für Komponenten, mit denen Funktionen für die *Geräteverwaltung* implementiert werden. Von der aktuellen Version der vorkonfigurierten Lösung für vorbeugende Wartung werden diese Ressourcen nicht bereitgestellt. Weitere Informationen zur Geräteverwaltung finden Sie in der [vorkonfigurierten Lösung für die Remoteüberwachung][lnk-remote-monitoring].

## Simulierte Geräte

In der vorkonfigurierten Lösung steht ein simuliertes Gerät für ein Flugzeugtriebwerk. Die Lösung wird mit zwei Triebwerken bereitgestellt, die einem Flugzeug zugeordnet sind. Jedes Triebwerk gibt vier Arten von Telemetriedaten aus: Sensor 9, Sensor 11, Sensor 14 und Sensor 15. Hiermit werden die Daten bereitgestellt, die vom Machine Learning-Modell zum Berechnen der Restlebensdauer des Triebwerks benötigt werden. Jedes simulierte Gerät sendet die folgenden Telemetriemeldungen an IoT Hub:

*Zyklusanzahl*: Ein Zyklus ist ein abgeschlossener Flug mit einer variablen Länge zwischen zwei und zehn Stunden, wobei über die gesamte Flugdauer im Abstand einer halben Stunde Telemetriedaten erfasst werden.

*Telemetrie*: Es sind vier Sensoren vorhanden, die für die Triebwerkattribute stehen. Die Sensoren tragen die generischen Bezeichnungen Sensor 9, Sensor 11, Sensor 14 und Sensor 15. Diese vier Sensoren repräsentieren Telemetriedaten, die ausreichen, um für das Machine Learning-Modell nützliche Ergebnisse in Bezug auf die Restlebensdauer zu erhalten. Dieses Modell wird aus einem öffentlichen Dataset erstellt, das echte Daten von Triebwerksensoren enthält. Weitere Informationen dazu, wie das Modell aus dem ursprünglichen Dataset erstellt wurde, finden Sie unter [Cortana Analytics-Katalog: Vorlage für die vorbeugende Wartung][lnk-cortana-analytics].

Die simulierten Geräte können die folgenden Befehle verarbeiten, die von einem IoT Hub gesendet werden:

| Befehl | Beschreibung |
|---------|-------------|
| StartTelemetry | Steuert den Status der Simulation.<br/>Startet das Senden der Telemetriedaten vom Gerät. |
| StopTelemetry | Steuert den Status der Simulation.<br/>Beendet das Senden der Telemetriedaten vom Gerät. |

IoT Hub führt die Bestätigung der Gerätebefehle durch.

## Azure Stream Analytics-Auftrag

**Auftrag: Telemetrie** verarbeitet den eingehenden Datenstrom mit Gerätetelemetriedaten mit zwei Anweisungen. Mit der ersten Anweisung werden alle Telemetriedaten von den Geräten ausgewählt und an eine Blob Storage-Einheit gesendet, von wo aus sie in der Web-App visualisiert werden. Mit der zweiten Anweisung werden die durchschnittlichen Sensorwerte für ein gleitendes Fenster von zwei Minuten berechnet und über den Event Hub an einen **Ereignisprozessor** gesendet.

## Ereignisprozessor

Der **Ereignisprozessor** übergibt die durchschnittlichen Sensorwerte eines abgeschlossenen Zyklus an eine API, mit der das trainierte Machine Learning-Modell zum Berechnen der Restlebensdauer eines Triebwerks verfügbar gemacht wird.

## Azure Machine Learning

Weitere Informationen dazu, wie das Modell aus dem ursprünglichen Dataset erstellt wurde, finden Sie unter [Cortana Analytics-Katalog: Vorlage für die vorbeugende Wartung][lnk-cortana-analytics].

## Exemplarische Vorgehensweise

In diesem Abschnitt werden die Komponenten der Lösung erläutert, der beabsichtigte Anwendungsfall beschrieben und Beispiele bereitgestellt.

### Dashboard für vorbeugende Wartung

Auf dieser Seite in der Webanwendung werden PowerBI-JavaScript-Steuerelemente (siehe [Repository mit visuellen PowerBI-Elementen][lnk-powerbi]) zum Visualisieren verwendet:

- Ausgabedaten von Stream Analytics-Aufträgen in der Blob Storage-Einheit
- Restlebensdauer und Zyklusanzahl pro Flugzeugtriebwerk

### Beobachten des Verhaltens der Cloudlösung

Sie können die bereitgestellten Ressourcen anzeigen, indem Sie zum Azure-Portal navigieren und dann auf die Ressourcengruppe mit dem von Ihnen gewählten Lösungsnamen zugreifen.

![][img-resource-group]

Beim Bereitstellen der vorkonfigurierten Lösung erhalten Sie eine E-Mail mit einem Link zum Machine Learning-Arbeitsbereich. Sie können auch über die Seite [azureiotsuite.com][lnk-azureiotsuite] Ihrer bereitgestellten Lösung zum Machine Learning-Arbeitsbereich navigieren, wenn der Status **Bereit** lautet.

![][img-machine-learning]

Im Lösungsportal sehen Sie, dass für das Beispiel vier simulierte Geräte bereitgestellt werden, die für zwei Flugzeuge mit zwei Triebwerken pro Flugzeug und mit vier Sensoren pro Triebwerk stehen. Wenn Sie zum ersten Mal zum Lösungsportal navigieren, wird die Simulation angehalten.

![][img-simulation-stopped]

Klicken Sie auf **Simulation starten**, um die Simulation zu starten. Das Dashboard wird dann mit dem Sensorverlauf, der Restlebensdauer, den Zyklen und dem Verlauf der Restlebensdauer gefüllt.

![][img-simulation-running]

Wenn der Wert für die Restlebensdauer kleiner als 160 ist (zu Demonstrationszwecken willkürlich gewählter Schwellenwert), wird im Lösungsportal ein Warnsymbol neben der Anzeige der Restlebensdauer eingeblendet, und die Farbe des Flugzeugtriebwerks im Bild ändert sich in Gelb. Sie sehen, dass die Werte für die Restlebensdauer im Allgemeinen einen Abwärtstrend aufweisen, aber trotzdem relativ stark variieren. Der Grund hierfür sind die variierenden Zykluslängen und die Modellgenauigkeit.

![][img-simulation-warning]

Die vollständige Simulation dauert ca. 35 Minuten, und es werden 148 Zyklen durchgeführt. Der Schwellenwert der Restlebensdauer von 160 wird zum ersten Mal nach ca. fünf Minuten erreicht, und für beide Triebwerke ist der Schwellenwert nach ca. acht Minuten erreicht.

Die Simulation durchläuft das vollständige Dataset für 148 Zyklen, und die endgültigen Werte für die Restlebensdauer und Zyklen werden festgelegt.

Sie können die Simulation jederzeit stoppen. Wenn Sie auf **Simulation starten** klicken, wird die Simulation aber ab dem Anfang des Datasets wiedergegeben.

## Nächste Schritte

Nachdem Sie die vorkonfigurierte Lösung für die vorbeugende Wartung ausgeführt haben, kann es sein, dass Sie sie ändern möchten. Informationen hierzu finden Sie unter [Anleitung zum Anpassen vorkonfigurierter Lösungen][lnk-customize].

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0218_2016-->