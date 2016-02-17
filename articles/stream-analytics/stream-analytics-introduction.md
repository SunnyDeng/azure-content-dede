<properties 
	pageTitle="Einführung in Stream Analytics | Microsoft Azure" 
	description="Erfahren Sie mehr über Stream Analytics, einen verwalteten Dienst, der Ihnen bei der Analyse der Streamingdaten aus dem Internet der Dinge (IoT) in Echtzeit hilft." 
	keywords="Analysen als Dienst, verwaltete Dienste, Streamingverarbeitung, Stream Analytics, was ist Stream Analytics"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>


# Was ist Stream Analytics?

Azure Stream Analytics (ASA) ist ein vollständig verwaltetes, kostengünstiges Echtzeit-Ereignisverarbeitungsmodul, mit dem Datenanalysen durchgeführt werden können. Stream Analytics erleichtert die Einrichtung von analytischen Echtzeitberechnungen von Datenströmen von Geräten, Sensoren, Websites, Sozialen Medien, Anwendungen, Infrastruktursystemen und mehr.

Mit wenigen Mausklicks im Azure-Portal können Sie einen Stream Analytics-Auftrag erstellen, indem Sie die Eingabequelle von Streamingdaten, die Ausgabensenke für die Ergebnisse des Auftrags und eine Datentransformation ausgedrückt in einer SQL-ähnlichen Sprache angeben. Sie können die Skalierung und Geschwindigkeit Ihres Auftrags im Azure-Portal überwachen und anpassen, um die Verarbeitung von Ereignissen von einigen wenigen Kilobyte auf bis zu ein Gigabyte pro Sekunde zu skalieren.

Stream Analytics nutzt die jahrelange Arbeit von Microsoft Research bei der Entwicklung von hoch optimierten Streaming-Modulen für zeitempfindliche Verarbeitung sowie Sprachintegrationen für deren intuitive Spezifikationen.

## Für was kann ich Stream Analytics verwenden?
Große Datenmengen werden heute mit hoher Geschwindigkeit über das Netz übertragen. Organisationen, die diese Streamingdaten in Echtzeit verarbeiten und nutzen können, verbessern die Effizienz unter Umständen erheblich und setzen sich auf dem Markt von den Mitbewerbern ab. Szenarios für Streaming Analytics in Echtzeit finden sich in allen Branchen: personalisierte Aktienhandelsanalysen und Warnungen von Finanzdienstleistern in Echtzeit, Betrugserkennung in Echtzeit, Daten- und Identitätsschutzdienste, zuverlässige Erfassung und Analyse von Daten, die von Sensoren und Stellgliedern generiert wurden und in physische Objekte eingebettet sind (IoT), Webklickstream-Analyse und CRM-Anwendungen, die Warnungen ausgeben, wenn die Benutzerfreundlichkeit innerhalb eines Zeitraums beeinträchtigt wird. Unternehmen suchen die flexibelste, zuverlässigste und kostengünstigste Möglichkeit, um eine solche Streaming-Datenanalyse in Echtzeit durchzuführen, um in einer stark durch Wettbewerb geprägten, modernen Geschäftswelt erfolgreich zu sein.

## Wichtige Funktionen und Vorteile
-   **Einfache Bedienung:** Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell zum Beschreiben der Transformationen. Zur Optimierung der Bedienfreundlichkeit verwendet Stream Analytics eine SQL-Variante und beseitigt die Notwendigkeit für Kunden, sich mit den technischen Aspekten des Stromverarbeitungssystems auseinandersetzen zu müssen. Mithilfe der [Stream Analytics-Abfragesprache](https://msdn.microsoft.com/library/azure/dn834998.aspx) können Sie im Abfrageeditor des Browsers die IntelliSense-Funktion zur automatischen Vervollständigung nutzen, um schnell und einfach zeitliche Funktionen zu implementieren, darunter zeitlich basierte Verknüpfungen, Fensteraggregate, zeitliche Filter sowie andere allgemeine Vorgänge wie z. B. Verknüpfungen, Aggregate, Projektionen und Filter. Darüber hinaus ermöglichen browserinterne Abfragetests einer Beispieldatendatei eine schnelle und iterative Entwicklung.  

-   **Skalierbarkeit**: Stream Analytics kann einen hohen Ereignisdurchsatz von bis zu 1 GB/s verarbeiten. Die Integration in [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) ermöglicht es der Lösung, Millionen Ereignisse pro Sekunde zu erfassen, die von verbundenen Geräten, aus Klickstreams, aus Protokolldateien usw. stammen. Um dies zu erreichen, nutzt Stream Analytics die Partitionierungsmöglichkeit von Event Hubs, sodass sich pro Partition bis zu 1 MB/s ergeben. Benutzer können die Berechnung in eine Anzahl von logischen Schritten innerhalb der Abfragedefinition partitionieren, die jeweils die Möglichkeit haben, weiter partitioniert zu werden, um die Skalierbarkeit zu erhöhen.

-   **Zuverlässigkeit, Wiederholbarkeit und schnelle Wiederherstellung**: Stream Analytics ist ein verwalteter Dienst in der Cloud und trägt dank der integrierten Wiederherstellungsfunktionen zur Vermeidung von Datenverlusten bei und bietet bei Fehlern Business Continuity. Mit der Möglichkeit, den Zustand intern beizubehalten, kann der Dienst wiederholbare Ergebnisse bereitstellen, wobei sichergestellt wird, dass Ereignisse archiviert und die Verarbeitung in Zukunft erneut angewendet werden können und immer dieselben Ergebnisse erzielt werden. Dies ermöglicht es Kunden, zeitlich zurückzugehen und Berechnungen bei Ursachenanalysen und Was-wäre-wenn-Analysen zu untersuchen.

-   **Geringe Kosten**: Als Clouddienst wurde der Stream Analytics-Dienst optimiert, damit Benutzer zu Beginn von den geringen Kosten profitieren können und um Echtzeitanalyselösungen zu verwalten. Das nutzungsbasierte Zahlungsmodell des Diensts basiert auf der Streamingeinheitennutzung sowie auf den vom System verarbeiteten Daten. Die Nutzung wird auf Grundlage der Menge an verarbeiteten Ereignissen und dem Umfang der im Cluster bereitgestellten Rechenleistung berechnet, um die jeweiligen Stream Analytics-Aufträge zu verarbeiten.

-   **Verweisdaten**: Stream Analytics bietet Benutzern die Möglichkeit, Verweisdaten festzulegen und zu verwenden. Dabei kann es sich um historische Daten oder um einfache Nicht-Streamingdaten handeln, die im Lauf der Zeit weniger häufig geändert werden. Das System vereinfacht die Verwendung von Verweisdaten, damit sie wie alle anderen eingehenden Ereignisstreams mit anderen in Echtzeit erfassten Ereignisstreams verbunden werden, um Transformationen durchzuführen.

-   **Konnektivität**: Stream Analytics stellt direkt eine Verbindung zu den Azure Event Hubs für die Streamaufnahme und zum Azure-Blob-Dienst für historische Daten her. Ergebnisse von Stream Analytics können in Azure-Speicherblobs oder -tabellen, Azure SQL-Datenbank, Event Hubs, Azure Service Bus-Themen oder -Warteschlangen und Power BI geschrieben werden, um sie dort zu visualisieren, mit Workflows weiter zu verarbeiten, über [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) für eine Batchanalyse zu verwenden oder als Ereignisreihe erneut zu verarbeiten. Bei Event Hubs können mehrere Stream Analytics zusammen mit anderen Datenquellen und Verarbeitungsmodulen zusammengestellt werden, ohne den Streamingcharakter der Berechnung zu verlieren.

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte
Sie haben nun Stream Analytics kennengelernt, einen verwalteten Dienst für Stream Analytics für Daten aus dem Internet der Dinge. Weitere Informationen zu diesem Dienst finden Sie unter:

- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->