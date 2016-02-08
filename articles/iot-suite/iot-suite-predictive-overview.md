<properties
 pageTitle="Übersicht über die vorkonfigurierte Lösung für vorhersagbaren Wartungsbedarf | Microsoft Azure"
 description="Beschreibung der vorkonfigurierten Azure IoT-Lösung für vorhersagbaren Wartungsbedarf | Microsoft Azure"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/01/2015"
 ms.author="stevehob"/>

# Übersicht über die vorkonfigurierte Lösung für vorhersagbaren Wartungsbedarf

Die vorkonfigurierte Lösung für *vorhersagbaren Wartungsbedarf* ist eine der [vorkonfigurierten Lösungen][lnk_preconfigured_solutions], die Bestandteil der [Microsoft Azure IoT-Suite][lnk_iot_suite] sind. Diese Lösung integriert die Erfassung von Gerätetelemetrie in Echtzeit mit einem Vorhersagemodell, das [Azure Machine Learning][lnk_machine_learning] verwendet.


Mit Azure IoT-Suite können Unternehmen schnell und einfach in Echtzeit Verbindungen herstellen, Ressourcen überwachen und Daten analysieren. Die vorkonfigurierte Lösung für vorhersagbaren Wartungsbedarf nutzt diese Daten anhand reichhaltiger Dashboards und Visualisierungen, um Unternehmen mit neuen Einsichten zu versorgen, die zur Effizienzsteigerung und zum Ausbau von Einnahmequellen dienen können.

## Das Szenario

Fabrikam ist eine regionale Fluggesellschaft, deren Schwerpunkt auf hervorragender Kundenzufriedenheit zu wettbewerbsfähigen Preisen liegt. Eine Ursache von Flugverspätungen sind Wartungsprobleme, und insbesondere die Wartung von Flugzeugtriebwerken ist anspruchsvoll. Ein Triebwerksausfall während des Flugs muss unter allen Umständen vermieden werden. Daher setzt Fabrikam auf eine regelmäßige Inspektion und ein Programm zur planmäßigen Wartung. Allerdings nutzen sich nicht alle Flugzeugtriebwerke in gleicher Weise ab. Ein gewisser Anteil der Wartung wird ausgeführt, obwohl er nicht erforderlich ist. Wichtiger noch, es können Umstände auftreten, die ein Flugzeug am Boden festhalten, bis eine Wartung durchgeführt wird. Dies führt zu teuren Verzögerungen, insbesondere dann, wenn sich das Flugzeug an einem Standort befindet, an dem die benötigten Techniker oder Ersatzteile nicht verfügbar sind.

Die Triebwerke der Fabrikam-Flotte sind mit Sensoren ausgestattet, die den Triebwerkzustand während des Flugs erfassen. Fabrikam nutzt zur Erfassung der Sensordaten während des Flugs die Azure IoT-Suite. Nach jahrelanger Erfassung der Betriebs- und Fehlerdaten haben die Datenanalysten von Fabrikam ein Modell zur Vorhersage der Restnutzungsdauer (Remaining Useful Life, RUL) eines Flugzeugtriebwerks erstellt. Dieses Modell beruht auf einer gefundenen Korrelation zwischen Daten von vier der Triebwerksensoren und dem Triebwerkverschleiß, der letztlich zum Ausfall führen kann. Obwohl Fabrikam die reguläre Inspektion aus Sicherheitsgründen fortsetzt, kann das Unternehmen jetzt anhand der Modelle nach jedem Flug mithilfe der von den Triebwerken erfassten Telemetriedaten die RUL für jedes Triebwerk berechnen. Fabrikam kann jetzt Vorhersagen zu künftigen Fehlerpunkten treffen, und Wartung und Reparatur im Voraus planen.

Durch die Vorhersage des geeigneten Wartungszeitpunkts kann Fabrikam eine Optimierung durchführen und die Betriebskosten senken. Die Wartungskoordinatoren arbeiten mit Planern, um zu gewährleisten, dass die Wartungsarbeiten mit dem Eintreffen des Flugzeugs an einem bestimmten Standort zusammenfallen und dass genügend Zeit für die Außerbetriebnahme des Flugzeugs zur Verfügung steht, ohne den Flugplan zu beeinträchtigen. Fabrikam kann Techniker entsprechend entsenden, um sicherzustellen, dass die Flugzeuge effizient und ohne Leerlauf gewartet werden. Bestandskontrollmanager erhalten Wartungspläne, damit Sie den Bestellvorgang und den Ersatzteilbestand optimieren können. All dies ermöglicht es Fabrikam, die Standzeiten der Maschinen zu minimieren und die Betriebskosten bei unveränderter Sicherheit für Passagiere und Crew zu senken.

## Aufbau der Lösung für vorhersagbaren Wartungsbedarf

Informationen dazu, wie [Azure IoT-Suite][lnk_iot_suite] die vom Kunden benötigten Fähigkeiten bereitstellt, um das Potenzial des vorhersagbaren Wartungsbedarfs zu erschließen, finden Sie auf [dieser Infografik][lnk_infographic].

Zur Demonstration dieser Möglichkeiten verwendet die vorgestellte Beispiellösung die über IoT-Suite-Dienste erfassten Gerätetelemetriedaten und bereitet diese anhand einer Webanwendung auf. Dabei kommt ein vorhandenes Azure Machine Learning-Modell zum Einsatz, das als Vorlage zur Verfügung steht. Microsoft hat ein [Regressionsmodell][lnk_regression_model] erstellt und die vollständige Vorlage sowie die Daten<sup>[1]</sup> und eine schrittweise Anleitung veröffentlicht.

Die vorkonfigurierte Azure IoT-Lösung für vorhersagbaren Wartungsbedarf verwendet ein anhand der genannten Vorlage erstelltes Regressionsmodell. Es ist in Ihrem Azure-Abonnement bereitgestellt und über eine veröffentlichte API verfügbar gemacht. Die Lösung umfasst eine Teilmenge der Testdaten mit vier (von insgesamt 100) Triebwerken und vier (von insgesamt 21) Sensordatenströmen, die ein genaues Ergebnis aus dem trainierten Modell bereitstellen.

*[1] A. Saxena und K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set“, NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA* (in englischer Sprache)

## Nächste Schritte

Informationen darüber, wie Azure IoT Szenarios mit vorhersagbarem Wartungsbedarf ermöglicht, finden Sie unter [Capture value from the Internet of Things][lnk_capture_value] (in englischer Sprache).

Sehen Sie sich die [exemplarische Vorgehensweise][lnk-predictive-walkthrough] zur vorkonfigurierten Lösung für die vorbeugende Wartung an.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/de-DE/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

<!---HONumber=AcomDC_0128_2016-->