<properties
 pageTitle="Verarbeiten von Fahrzeugsensordaten mit Apache Storm auf HDInsight | Microsoft Azure"
 description="Erfahren Sie, wie Sie Fahrzeugsensordaten von Event Hubs mit Apache Storm auf HDInsight verarbeiten. Fügen Sie Modelldaten aus DocumentDB hinzu, und speichern Sie die Ausgabe im Speicher."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/23/2015"
ms.author="larryfr"/>

#Verarbeiten von Fahrzeugsensordaten von Azure Event Hubs mit Apache Storm auf HDInsight

Erfahren Sie, wie Sie Fahrzeugsensordaten von Azure Event Hubs mit Apache Storm auf HDInsight verarbeiten. In diesem Beispiel werden Sensordaten von Azure Event Hubs gelesen, die Daten werden durch Verweisen auf in Azure DocumentDB gespeicherte Daten angereichert und schließlich mit Hadoop File System (HDFS) im Azure-Speicher abgespeichert.

![Architekturdiagramm für HDInsight und das Internet der Dinge (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##Übersicht

Das Hinzufügen von Sensoren zu Fahrzeugen ermöglicht sowohl die Voraussage von Anlagenprobleme basierend auf Vergangenheitsdatentrends als auch Verbesserungen für zukünftige Versionen aufgrund von Nutzungsverhaltenanalyse. Es kann herkömmlichen MapReduce-Batchverarbeitung für diese Analyse verwendet werden, dabei muss es jedoch möglich sein, Daten schnell und effizient von allen Fahrzeugen in Hadoop bevor zu laden, bevor die MapReduce-Verarbeitung beginnen kann. Darüber wollen Sie möglicherweise Analysen für kritischen Fehlerpfade (Motortemperatur, Bremsen usw.) in Echtzeit durchführen.

Azure Event Hubs wurden zum Behandeln großer Menge von Daten entworfen, die von Sensoren generierte wurden und Apache-Storm auf HDInsight kann für zusätzliche MapReduce-Verarbeitung zum Laden und Verarbeiten der Daten vor dem Speichern in HDFS (unterstützt durch Azure-Speicher) verwendet werden.

##Lösung

Telemetriedaten für Motortemperatur, Umgebungstemperatur und Geschwindigkeit des Fahrzeugs werden von Sensoren zusammen mit Fahrgestellnummer (FIN) und einem Zeitstempel an Event Hubs gesendet. Von dort liest eine Storm-Topologie, die auf einem Apache-Storm auf HDInsight-Cluster ausgeführt wird, die Daten, verarbeitet diese und speichert sie in HDFS.

Während der Verarbeitung wird die FIN verwendet, um Modellinformationen aus Azure DocumentDB abzurufen. Dies wird dem Datenstrom hinzugefügt, bevor er gespeichert wird.

Die in der Storm-Topologie verwendet Komponenten sind:

* **EventHubSpout** - Liest Daten aus den Azure-Event Hubs

* **TypeConversionBolt** - Konvertiert die JSON-Zeichenfolge aus den Event Hubs in ein Tupel, das die individuellen Datenwerte für Motortemperatur, Umgebungstemperatur, Geschwindigkeit, FIN und Zeitstempel enthält.

* **DataReferencBolt** - Sucht das Fahrzeugmodell über die FIN in DocumentDB

* **WasbStoreBolt** - Speichert die Daten in HDFS (Azure-Speicher)

Es folgt eine Darstellung dieser Lösung:

![Storm-Topologie](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE]Dies ist eine vereinfachte Darstellung und jede Komponente in der Lösung kann mehrere Instanzen haben. Beispielsweise werden mehrere Instanzen jeder Komponenten in der Topologie auf die Knoten in Storm auf HDInsight-Cluster verteilt.

##Implementierung

Eine vollständige, automatisierte Lösung für dieses Szenario ist als Teil des <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight-Storm-Beispiele</a> Repository auf GitHub verfügbar. Um dieses Beispiel zu verwenden, führen Sie die Schritte in [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md) aus.

## Nächste Schritte

Weitere Beispiel-Storm-Topologien finden Sie unter [Beispieltopologien für Storm auf HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=Oct15_HO1-->