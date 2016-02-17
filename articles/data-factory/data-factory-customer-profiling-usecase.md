<properties 
	pageTitle="Anwendungsfall – Erstellen von Kundenprofilen" 
	description="Erfahren Sie, wie Azure Data Factory zum Erstellen eines datengesteuerten Workflows (Pipeline) verwendet wird, um Profile von Spielekunden zu erstellen." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Anwendungsfall – Erstellen von Kundenprofilen

Azure Data Factory ist einer der zahlreichen Dienste, die zum Implementieren der Cortana Analytics Suite von Lösungsoptimierungen verwendet werden. Weitere Informationen zu Cortana Analytics finden Sie unter [Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics) (in englischer Sprache). In diesem Dokument wird ein einfacher Anwendungsfall beschrieben, der Ihnen dabei helfen soll zu verstehen, wie Sie mit Azure Data Factory verbreitete Analyseprobleme lösen können.

Sie benötigen lediglich ein [Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/), um auf diesen einfachen Anwendungsfall zuzugreifen und ihn zu testen. Sie können ein Beispiel bereitstellen, in dem dieser Anwendungsfall implementiert wird, indem Sie die Schritte im Artikel [Beispiele](data-factory-samples.md) ausführen.

## Szenario

Contoso ist ein Spielehersteller, der Spiele für mehrere Plattformen entwickelt. Hierzu zählen Spielekonsolen, mobile Geräte und PCs. Wenn Benutzer diese Spiele spielen, werden große Mengen an Protokolldaten erzeugt, mit denen Verwendungsmuster, Spielweisen und Einstellungen der Benutzer nachverfolgt werden. In Kombination mit demographischen, regionalen und Produktdaten kann Contoso Analysen durchführen, um Hinweise zu geben, wie die Benutzerfreundlichkeit verbessert werden kann und welche Upgrades und Zusatzfunktionen verfügbar sind.

Das Ziel von Contoso ist das Identifizieren von Up-Selling- und Cross-Selling-Chancen basierend auf dem Spielverlaufsprofil der Benutzer und das Entwickeln neuer überzeugender Features, um das Unternehmenswachstum zu fördern und den Kunden eine größere Benutzerfreundlichkeit bereitzustellen. Für diesen Anwendungsfall verwenden wir ein Spieleunternehmen als Beispiel für ein Unternehmen, das das Verhalten für die Benutzer optimieren möchte. Diese Prinzipien gelten jedoch für alle Unternehmen, die ihre Kunden an die Waren und Dienstleistungen binden und die Erfahrungen für ihre Kunden verbessern möchten.

## Herausforderungen

Es gibt viele Herausforderungen, mit denen Spieleunternehmen bei dem Versuch konfrontiert sind, diese Art von Anwendungsfall zu implementieren. Zunächst müssen Daten in verschiedenen Größen und Formen aus mehreren Datenquellen lokal und in der Cloud erfasst werden, um Produktdaten, Verlaufsdaten zum Benutzerverhalten und Benutzerdaten beim Spielen der Spiele auf mehreren Plattformen zu sammeln. Zweitens müssen Spielverwendungsmuster sinnvoll und genau berechnet werden. Drittens müssen Spieleunternehmen die Effektivität des Vorgehens messen, indem sie den Gesamterfolg von Up-Selling und Cross-Selling beim Verkauf von Zusatzfunktionen anhand von Profilen nachverfolgen und ihre zukünftigen Werbekampagnen entsprechend ändern.

## Lösungsübersicht

Dieser einfache Anwendungsfall kann als Beispiel dafür dienen, wie Sie Azure Data Factory zum Erfassen, Vorbereiten, Transformieren, Analysieren und Veröffentlichen von Daten verwenden können.

![Kompletter Workflow](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)In der obigen Abbildung wird dargestellt, wie die Datenpipelines in der Benutzeroberfläche des klassischen Azure-Portals angezeigt werden, nachdem sie bereitgestellt wurden.

1.	Die **PartitionGameLogsPipeline** liest die Rohdaten der Spielereignisse aus dem Blobspeicher und erstellt Partitionen basierend auf Jahr, Monat und Tag.
2.	Die **EnrichGameLogsPipeline** verknüpft partitionierte Spielereignisse mit geografisch codierten Verweisdaten und reichert die Daten an, indem sie die IP-Adressen entsprechenden geografischen Standorten zuordnet.
3.	Die **AnalyzeMarketingCampaignPipeline** verarbeitet die angereichten Daten zusammen mit den Werbedaten, um die endgültige Ausgabe zu erstellen, die Aufschluss über die Wirksamkeit der Marketingkampagne gibt.

In diesem Beispielanwendungsfall wird Azure Data Factory verwendet, um die Aktivitäten zu koordinieren, mit denen die Eingabedaten kopiert, die Daten mit HDInsight-Aktivitäten (Hive- und Pig-Transformationen) transformiert und verarbeitet und die endgültigen Daten in einer Azure SQL-Datenbank ausgegeben werden. Über die Benutzeroberfläche können Sie auch das Netzwerk der Datenpipelines visualisieren, sie verwalten und deren Status überwachen.

## Vorteile

Durch Optimieren der Benutzerprofilanalysen und deren Ausrichtung auf die Unternehmensziele kann das Spieleunternehmen schnell Verwendungsmuster sammeln und die Effektivität der Marketingkampagnen für alle Spiele analysieren.

<!---HONumber=AcomDC_0128_2016-->