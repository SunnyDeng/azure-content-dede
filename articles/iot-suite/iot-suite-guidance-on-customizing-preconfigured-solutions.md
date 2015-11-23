<properties
	pageTitle="Anpassen von vorkonfigurierten Lösungen | Microsoft Azure"
	description="Dieses Dokument bietet eine Anleitung zum Anpassen vorkonfigurierter Azure IoT Suite-Lösungen."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="stevehob"/>

# Anpassen vorkonfigurierter Lösungen

Die vorkonfigurierten Lösungen in der Azure IoT Suite veranschaulichen das Bereitstellen einer umfassenden Lösung basierend auf dem Zusammenwirken der Dienste in der Suite. Von hier ausgehend gibt es verschiedene Möglichkeiten zur Erweiterung und Anpassung der Lösung für bestimmte Szenarios. Diese allgemeinen Anpassungsmöglichkeiten werden in den folgenden Abschnitten beschrieben.

## Suchen des Quellcodes

Der Quellcode für die vorkonfigurierte Lösung ist auf GitHub in den folgenden Repositorys verfügbar:

- Remoteüberwachung: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Dieser Quellcode wird bereitgestellt, um ein Muster zum Implementieren der Kernfunktionen der Remoteüberwachung mithilfe der Azure IoT-Suite zu veranschaulichen.

## Ändern der vorkonfigurierten Regeln

Die Remoteüberwachungslösung enthält zwei [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytics)-Aufträge, um die im Dashboard angezeigte Telemetrie- und Alarmlogik zu implementieren.

Der erste Auftrag wählt alle Daten aus dem eingehenden Telemetriedatenstrom und erstellt zwei verschiedene Ausgaben. Der Auftrag wird mit **[Lösungsname]-Telemetry** benannt.

- Die erste Ausgabe ruft alle Daten mit `SELECT *` ab und speichert die Ausgaben in einem BLOB-Speicher. Aus diesem BLOB-Speicher liest das Dashboard Rohwerte zum Erstellen der Diagramme.
- Die zweite Ausgabe führt eine `AVG()`-, `MIN()`- und `MAX()`-Berechnung über einem gleitenden Zeitraum von 5 Minuten aus. Diese Daten werden auf den Drehsteuerungen des Dashboards angezeigt.

Auf der Stream Analytics-Benutzeroberfläche können Sie diese Aufträge direkt bearbeiten, um die Logik zu ändern oder spezifische Logik für das Szenario hinzuzufügen.

Der zweite Auftrag bearbeitet die Werte für "Gerät bis Schwellenwert", die auf der Seite **Regeln** der Lösung erstellt werden. Dieser Auftrag verarbeitet den für jedes Gerät festgelegten Schwellenwert als Referenzdaten. Er bestimmt durch Vergleich des Schwellenwerts, ob dieser größer als (`>`) der tatsächliche Wert ist. Sie können diesen Auftrag ändern, um z. B. den Vergleichsoperator zu ändern.

> [AZURE.NOTE]Das Remoteüberwachungs-Dashboard hängt von bestimmten Daten ab. Deshalb kann das Ändern der Aufträge dazu führen, dass das Dashboard fehlschlägt.

## Hinzufügen eigener Regeln

Sie können nicht nur die vorkonfigurierten Azure Stream Analytics-Aufträge ändern, sondern auch im Azure-Vorschauportal neue Aufträge hinzufügen oder vorhandenen Aufträgen neue Abfragen hinzufügen.

## Anpassen von Geräten

Eine der am häufigsten Erweiterungsaktivitäten ist das Arbeiten mit speziellen Geräten für Ihr Szenario. Es gibt mehrere Methoden zum Arbeiten mit Geräten. Dazu gehören das Anpassen eines simulierten Geräts an Ihr Szenario und das Verknüpfen des physischen Geräts mit der Lösung mithilfe des [IoT-Geräte-SDK][].

Unter [Verbinden von Geräten mit der IoT Suite](iot-suite-connecting-devices.md) finden Sie eine schrittweise Anleitung zum Hinzufügen von Geräten zur vorkonfigurierten Remoteüberwachungslösung.

### Erstellen eines eigenen simulierten Geräts

Der Quellcode der Remoteüberwachungslösung (auf den oben verwiesen wird) enthält einen .NET-Simulator. Dieser Simulator wird als Teil der Lösung bereitgestellt und kann geändert werden, um andere Metadaten oder Telemetrie zu senden oder auf andere Befehle zu reagieren.

Darüber hinaus stellt Azure IoT ein [C-SDK-Beispiel](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) bereit, das mit der vorkonfigurierten Remoteüberwachungslösung verwendet werden kann.

### Erstellen und Verwenden eines eigenen (physischen) Geräts

Die [Azure IoT-SDKs](https://github.com/Azure/azure-iot-sdks) bieten Bibliotheken zum Verbinden zahlreicher Gerätetypen (Sprachen und Betriebssysteme) mit IoT-Lösungen.

## Nächste Schritte

Weitere Informationen zu IoT-Geräten finden Sie auf der [Azure IoT-Entwicklerwebsite](http://azure.microsoft.com/develop/iot), die entsprechende Links und Dokumente enthält.

[IoT-Geräte-SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=Nov15_HO3-->