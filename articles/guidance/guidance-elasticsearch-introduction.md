
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Anleitungen für Elasticsearch für Azure

Elasticsearch ist ein hochgradig skalierbares Open-Source-Suchmodul mit Datenbank. Es ist für Situationen geeignet, in denen eine schnelle Analyse und Ermittlung von Informationen in großen Datasets erforderlich ist. Diese Anleitung untersucht einige wichtige Aspekte, die beim Entwerfen eines Elasticsearch-Clusters zu berücksichtigen sind:

- **[Allgemeine Hinweise][]** enthält eine kurze Einführung in die allgemeine Struktur von Elasticsearch. Anschließend wird beschrieben, wie Sie mit Azure einen Elasticsearch-Cluster implementieren können.
- **[Hinweise zur Datenerfassung][]** beschreibt die Bereitstellungs- und Konfigurationsoptionen, die Sie bei einem Elasticsearch-Cluster mit umfangreicher Datenerfassung berücksichtigen sollten.
- **[Hinweise zu Leistungstests][]** beschreibt, wie Sie eine Umgebung zum Testen der Leistung eines Elasticsearch-Clusters einrichten.
- **[JMeter-Hinweise][]** beschreibt das Erstellen und Verwenden eines JUnit-Samplers, der Daten generieren und in einen Elasticsearch-Cluster hochladen kann.
- **[JMeter-Überlegungen][]** fasst die wichtigsten Erfahrungen beim Erstellen und Ausführen der Datenerfassung und den Abfragetestplänen zusammen. 

  > [AZURE.NOTE] Hierbei wird vorausgesetzt, dass Sie über Grundkenntnisse zu Elasticsearch verfügen. Ausführlichere Informationen finden Sie auf der [Elasticsearch-Website](https://www.elastic.co/products/elasticsearch).

[Allgemeine Hinweise]: guidance-elasticsearch.md
[Hinweise zur Datenerfassung]: guidance-elasticsearch-data-ingestion.md
[Hinweise zu Leistungstests]: guidance-elasticsearch-performance-testing-environment.md
[JMeter-Hinweise]: guidance-elasticsearch-implementing-jmeter.md
[JMeter-Überlegungen]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->