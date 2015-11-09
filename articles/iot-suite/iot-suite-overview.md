<properties
	pageTitle="Übersicht über Microsoft Azure IoT Suite | Microsoft Azure"
	description="Hier finden Sie eine Übersicht über Azure IoT Suite, u. a. Informationen zum Packen und vorkonfigurierten Lösungen."
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Übersicht über Azure IoT Suite

Microsoft bietet eine Breite Palette von Funktionen mit den Azure IoT-Diensten an. Diese Dienste liefern Funktionen auf Unternehmensebene zum Sammeln von Daten von Geräten, Analysieren von Datenströmen in Bewegung, Speichern und Abfragen großer Datensätze, Visualisieren von Echtzeit- und Verlaufsdaten und Integrieren mit wichtigen Back-Office-Systemen an. Die Azure IoT Suite bündelt eine Reihe von häufig genutzten Diensten und erweitert die Fähigkeiten zum Reduzieren der Amortisierung für Kunden. Diese Erweiterungen umfassen vorkonfigurierte Lösungen, die die Basisimplementierung der am häufigsten verwendeten Lösungsmuster bieten. In Kombination mit den IoT-SDKs können Kunden die vorkonfigurierten Lösungen leicht anpassen oder als Beispiele für die Entwicklung neuer Lösungen nutzen.

Das folgende Video bietet eine Einführung in Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT-Dienste in Azure IoT Suite

Kern der Azure IoT Suite ist der IoT Hub-Dienst. Dieser Dienst stellt den Nachrichtenaustausch Gerät-zu-Cloud und Cloud-zu-Dienst bereit. Er fungiert als Gateway zur Cloud und zu anderen wichtigen Diensten der IoT Suite.

Die Datenanalyse während des Betriebs wird von Azure Stream Analytics bereitgestellt. Die IoT Suite nutzt diesen Dienst für die Verarbeitung eingehender Telemetrienachrichten, für die Aggregation und für das Erkennen von Ereignissen. Er wird auch für die Verarbeitung informativer Meldungen verwendet, die als Gerätemetadaten oder Befehlsantwort verwendet werden können.

Datenspeicherfunktionen werden durch eine Kombination von Azure Storage und Azure DocumentDB ermöglicht. Azure Storage ermöglicht die Verwendung von BLOB-Speicher für die Telemetrie, um diese zu speichern und für zukünftige Analysen aufzubewahren. DocumentDB wird aufgrund seiner indizierten Speicherfunktion bei halbstrukturierten Daten verwendet, um Gerätemetadaten zu verwalten. Dies ermöglicht die Verwaltung heterogener Geräte, da unterschiedliche Geräten auch unterschiedliche Inhalte aufweisen können.

Für die Visualisierung der Daten wird eine Kombination von Azure Websites und Microsoft Power BI genutzt. Die Flexibilität von Power BI ermöglicht Kunden das schnelle Erstellen eigener interaktiver Dashboards aus Daten der IoT Suite.

Weitere Informationen zur Architektur und zur Verwendung dieser Dienste finden Sie im Artikel [Microsoft Azure und das Internet der Dinge (IoT)](iot-suite-what-is-azure-iot.md).

## Vorkonfigurierte Lösungen

Vorkonfigurierte Lösungen finden Sie in der Azure IoT Suite. Sie ermöglichen Kunden einen schnellen Einstieg sowie das Analysieren von Szenarios, die durch die Azure IoT Suite möglich werden.

Die erste verfügbare vorkonfigurierte Lösung ist die [Remoteüberwachung](iot-suite-what-are-preconfigured-solutions.md).

<!---HONumber=Nov15_HO1-->