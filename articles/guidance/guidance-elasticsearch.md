
<properties
   pageTitle="Anleitungen für Elasticsearch für Azure | Microsoft Azure"
   description="Anleitungen für Elasticsearch für Azure."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Anleitungen für Elasticsearch für Azure

Elasticsearch ist ein hochgradig skalierbares Open-Source-Suchmodul mit Datenbank. Es ist für Situationen geeignet, in denen eine schnelle Analyse und Ermittlung von Informationen in großen Datasets erforderlich ist. Diese Anleitung untersucht einige wichtige Aspekte, die beim Entwerfen eines Elasticsearch-Clusters zu berücksichtigen sind:

- **[Ausführen von Elasticsearch für Azure][]** enthält eine kurze Einführung in die allgemeine Struktur von Elasticsearch. Anschließend wird beschrieben, wie Sie mit Azure einen Elasticsearch-Cluster implementieren können.
- **[Optimierung der Datenerfassungsleistung für Elasticsearch in Azure][]** beschreibt die Bereitstellungs- und Konfigurationsoptionen, die Sie bei einem Elasticsearch-Cluster mit umfangreicher Datenerfassung berücksichtigen sollten.
- **[Optimierung der Datenaggregations- und Abfrageleistung für Elasticsearch in Azure][]** fasst die Optionen zusammen, deren Verwendung Sie erwägen können, wenn es um den besten Weg zur Optimierung Ihres Systems in Bezug auf die Abfrage- und Suchleistung geht.
- **[Konfigurieren der Resilienz und Wiederherstellung unter Elasticsearch in Azure][]** umreißt die Resilienz- und Wiederherstellungsoptionen, die mit Elasticsearch in Azure verfügbar sind.
- **[Erstellen einer Leistungstestumgebung für Elasticsearch in Azure][]** beschreibt, wie Sie eine Umgebung zum Testen der Leistung eines Elasticsearch-Clusters einrichten.
- **[Implementieren eines JMeter-Testplans für Elasticsearch][]** beschreibt das Erstellen und Verwenden eines JUnit-Samplers, der Daten generieren und in einen Elasticsearch-Cluster hochladen kann.
- **[Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Leistung von Elasticsearch][]** fasst die wichtigsten Erfahrungen beim Erstellen und Ausführen der Datenerfassung und der Abfragetestpläne zusammen. 
- **[Ausführen der automatisierten Elasticsearch-Resilienztests][]** fasst das Ausführen der im Artikel oben verwendeten Resilienztests zusammen.
- **[Ausführen der automatisierten Elasticsearch-Leistungstests][]** fasst das Ausführen der im Artikel oben verwendeten Leistungstests zusammen.

> [AZURE.NOTE] Hierbei wird vorausgesetzt, dass Sie über Grundkenntnisse zu Elasticsearch verfügen. Ausführlichere Informationen finden Sie auf der [Elasticsearch-Website](https://www.elastic.co/products/elasticsearch).

[Ausführen von Elasticsearch für Azure]: guidance-elasticsearch-running-on-azure.md
[Optimierung der Datenerfassungsleistung für Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Erstellen einer Leistungstestumgebung für Elasticsearch in Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementieren eines JMeter-Testplans für Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Leistung von Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Optimierung der Datenaggregations- und Abfrageleistung für Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Konfigurieren der Resilienz und Wiederherstellung unter Elasticsearch in Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Ausführen der automatisierten Elasticsearch-Resilienztests]: guidance-elasticsearch-running-automated-resilience-tests.md
[Ausführen der automatisierten Elasticsearch-Leistungstests]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->