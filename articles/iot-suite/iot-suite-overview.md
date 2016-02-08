<properties
	pageTitle="Übersicht über Microsoft Azure IoT Suite | Microsoft Azure"
	description="Hier finden Sie eine Übersicht über Azure IoT Suite, u. a. Informationen zum Packen und vorkonfigurierten Lösungen."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Übersicht über Azure IoT Suite

Die Azure IoT-Dienste bieten zahlreiche Funktionen. Diese Dienste auf Unternehmensniveau ermöglichen Folgendes:

- Sammeln von Daten von Geräten
- Analysieren von Datenströmen während des Betriebs
- Speichern und Abfragen von großen Datasets
- Anzeigen von Echtzeit- und Verlaufsdaten
- Integrieren mit Back-Office-Systemen

Die Azure IoT Suite kombiniert häufig genutzte Azure-Dienste mit benutzerdefinierten Erweiterungen als vorkonfigurierte Lösungen. Diese vorkonfigurierten Lösungen sind Basisimplementierungen von häufig verwendeten IoT-Lösungsmustern, mit denen Sie die Bereitstellung Ihrer IoT-Lösungen beschleunigen können. Mithilfe der [IoT-SDKs (Software Development Kits)][lnk-sdks] können Sie die vorkonfigurierten Lösungen einfach an Ihre Anforderungen anpassen oder als Beispiele für die Entwicklung neuer Lösungen nutzen.

Das folgende Video bietet eine Einführung in Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT-Dienste in Azure IoT Suite

Kern der Azure IoT Suite ist der [IoT Hub][lnk-iot-hub]-Dienst. Dieser Dienst stellt die D2C- und C2D-Messagingfunktionen (Gerät zu Cloud und Cloud zu Gerät) und fungiert als Gateway für die Cloud und andere wichtige Dienste der IoT-Suite.

Die Datenanalyse während des Betriebs wird von [Azure Stream Analytics][lnk-asa] bereitgestellt. Die IoT Suite nutzt diesen Dienst für die Verarbeitung eingehender Telemetriedaten, für die Aggregation und für das Erkennen von Ereignissen. Die vorkonfigurierten Lösungen verwenden Stream Analytics auch für die Verarbeitung informativer Meldungen, die Daten wie Metadaten oder Antworten auf Befehle von Geräten enthalten.

[Azure Storage][lnk-azure-storage] und [Azure DocumentDB][lnk-document-db] bieten Datenspeicherfunktionen. Die vorkonfigurierten Lösungen verwenden den Blob-Speicher, um Telemetriedaten zu speichern und für die Analyse verfügbar zu machen. Die vorkonfigurierten Lösungen nutzen die indizierten Speicherfunktion bei halbstrukturierten Daten von DocumentDB, um Gerätemetadaten zu verwalten. Dadurch können die Lösungen heterogene Geräte mit unterschiedlichen Speicheranforderungen verwalten.

[Azure-Web-Apps][lnk-web-apps] und [Microsoft Power BI][lnk-power-bi] bieten Datenvisualisierungsfunktionen. Die Flexibilität von Power BI ermöglicht Kunden das schnelle Erstellen eigener interaktiver Dashboards, die Daten der IoT Suite verwenden.

Eine Übersicht über die Architektur einer typischen IoT-Lösung finden Sie unter [Microsoft Azure und das Internet der Dinge (IoT)][iot-suite-what-is-azure-iot].

## Vorkonfigurierte Lösungen

Die IoT Suite enthält vorkonfigurierte Lösungen, die einen schneller Einstieg und Einblick in die mit der Azure IoT Suite möglichen Szenarien ermöglichen. Sie können die vorkonfigurierten Lösungen für Ihr Azure-Abonnement bereitstellen und dann eine vollständige End-to-End IoT-Lösung bereitstellen.

## Nächste Schritte

Weitere Informationen zu den vorkonfigurierten Lösungen der IoT Suite finden Sie unter [Was sind vorkonfigurierte Azure IoT-Suite-Lösungen?][lnk-what-are-preconfig]

Informationen zum Einstieg in eine der vorkonfigurierten Lösungen finden Sie unter [Erste Schritte mit den vorkonfigurierten IoT-Lösungen][lnk-preconfig-start].

Weitere Informationen zum Azure IoT Hub-Dienst finden Sie in der [Dokumentation zu IoT Hub][lnk-iot-hub]


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0128_2016-->