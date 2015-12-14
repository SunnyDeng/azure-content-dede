<properties 
	pageTitle="Details zur DocumentDB-Ausgabe | Microsoft Azure" 
	description="DocumentDB als Azure Stream Analytics-Ausgabe"
	keywords="DocumentDB-Ausgabe, DOCDB-Ausgabe, Stream Analytics und DocumentDB"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# Grundlegendes zu DocumentDB als Azure Stream Analytics-Ausgabe

Azure Stream Analytics unterstützt nun [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) als Ausgabe, was eine Datenarchivierung und Abfragen unstrukturierter JSON-Daten mit kurzer Wartezeit ermöglicht. In diesem Dokument wird beschrieben, wie diese Integration am besten implementiert wird. Falls Sie noch nicht mit DocumentDB vertraut sind, sehen Sie sich zum Einstieg den [Lernpfad für DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) an.

Die Azure DocumentDB-Ausgabe in Stream Analytics ermöglicht das Schreiben der Ergebnisse Ihrer Streamingverarbeitung in Ihre DocumentDB-Sammlungen. Stream Analytics erstellt keine Sammlungen in Ihrer Datenbank, sondern fordert deren vorherige Erstellung an. Dies ist so, damit die Abrechnungskosten von DocumentDB-Sammlungen für Sie transparent bleiben und Sie die Leistung, Konsistenz und Kapazität Ihrer Sammlungen direkt mithilfe der [DocumentDB-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) optimieren können. Wir empfehlen den Einsatz einer DocumentDB-Datenbank pro Streamingauftrag, um Ihre Sammlungen für einen Streamingauftrag logisch zu trennen.

Nachstehen sind einige der DocumentDB-Sammlungsoptionen beschrieben.

## Konsistenz

Entsprechend Ihren Anwendungsanforderungen lässt DocumentDB das Optimieren der Datenbank und Sammlungen zu und wählt Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz. Abhängig von den Lesekonsistenzebenen, die Ihr Szenario hinsichtlich Lese- und Schreiblatenz benötigt, können Sie in Ihrem Datenbankkonto eine Konsistenzebene wählen. Außerdem aktiviert DocumentDB für jeden CRUD-Vorgang in Ihrer Sammlung die synchrone Indizierung. Dies ist eine andere nützliche Option zum Steuern der Schreib-/Leseleistung in DocumentDB. Weitere Informationen zu diesem Thema finden Sie im Artikel [Ändern der Datenbank- und Abfragekonsistenzebenen](../articles/documentdb-consistency-levels.md).

## Leistung

DocumentDB-Sammlungen können auf drei Leistungsstufen (S1, S2 oder S3) erstellt werden, die den verfügbaren Durchsatz für CRUD-Vorgänge für diese Sammlung bestimmen. Darüber hinaus wird die Leistung von den Indizierungs-/Konsistenzebenen Ihrer Sammlung beeinflusst. [In diesem Artikel](../articles/documentdb-performance-levels.md) finden Sie grundlegende Informationen zu diesen Leistungsstufen.

## Einfügungen/Aktualisierungen (Upserts)

Die Stream Analytics-Integration mit DocumentDB ermöglicht Ihnen das Einfügen oder Aktualisieren von Datensätzen in Ihrer DocumentDB-Sammlung basierend auf einer angegebenen Dokument-ID-Spalte. Dies wird auch als *Upsert* (Kunstwort aus Update und Insert) bezeichnet.

Stream Analytics nutzt einen optimistischen Upsert-Ansatz, bei dem Aktualisierungen nur erfolgen, wenn eine Einfügung aufgrund eines Dokument-ID-Konflikts keinen Erfolg hat. Diese Aktualisierung wird von Stream Analytics mit einem PATCH-Befehl ausgeführt. Dadurch sind Teilaktualisierungen des Dokuments möglich, was bedeutet, dass das Hinzufügen neuer Eigenschaften oder Austauschen einer vorhandenen Eigenschaft schrittweise erfolgt. Beachten Sie, dass Änderungen der Werte von Arrayeigenschaften in Ihrem JSON-Dokument dazu führen, dass das gesamte Array überschrieben wird, d. h. nicht zusammengeführt wird.

## Partitionierung

Mit DocumentDB-Sammlungen können Sie Ihre Daten basierend auf den Abfragemustern und Leistungsanforderungen Ihrer Anwendung partitionieren. Jede Sammlung kann (maximal) bis zu 10 GB Daten enthalten. Derzeit besteht keine Möglichkeit, eine Sammlung zentral hochzuskalieren (oder überlaufen zu lassen). Für eine horizontale Skalierung ermöglicht Stream Analytics das Schreiben mehrerer Sammlungen mit einem gegebenen Präfix (siehe die nachfolgenden Nutzungsdetails). Stream Analytics verwendet die konsistente [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)-Strategie basierend auf der vom Benutzer angegebenen „PartitionKey“-Spalte, um seine Ausgabedatensätze zu partitionieren. Die Anzahl der Sammlungen mit dem angegebenen Präfix zur Startzeit des Streamingauftrags wird als Anzahl der Ausgabepartitionen verwendet, in der Auftrag parallel schreibt (DocumentDB-Sammlungen = Ausgabepartitionen). Für eine einzelne S3-Sammlung mit verzögerter Indizierung, in die nur Einfügungen erfolgen, kann ein Schreibdurchsatz von 0,4 MB/s erwartet werden. Mithilfe mehrerer Sammlungen können Sie einen höheren Durchsatz und eine höhere Kapazität ermöglichen.

Wenn Sie beabsichtigen, die Anzahl der Partitionen künftig zu erhöhen, müssen Sie den Auftrag beenden, die Daten in Ihren vorhandenen Sammlungen in neuen Sammlungen neu partitionieren und dann den Stream Analytics-Auftrag neu starten. Weitere Informationen zum Verwenden von „PartitionResolver“ und Neupartitionierung sowie Beispielcode werden in einem Folgeartikel enthalten sein. Der Artikel [Partitionierung in DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) enthält auch dazugehörige Details.

## DocumentDB-Einstellungen

Beim Erstellen einer DocumentDB-Datenbank als Ausgabe in Stream Analytics wird eine Aufforderung zur Eingabe von Informationen eingeblendet. Dieser Abschnitt enthält eine Erklärung der Definition der Eigenschaften.
  
![Bildschirm mit DocumentDB als Stream Analytics-Ausgabe](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)

-   **Ausgabealias**: Ein Alias zum Verweisen auf diese Ausgabe in Ihrer ASA-Abfrage.  
-   **Kontoname**: Der Name oder Endpunkt-URI des DocumentDB-Kontos.  
-   **Kontoschlüssel**: Der gemeinsame Zugriffsschlüssel für das DocumentDB-Konto.  
-   **Datenbank**: Der Name der DocumentDB-Datenbank.  
-   **Muster für Sammlungsnamen**: Das Muster für die zu verwendenden Sammlungen. Das Sammlungsnamenformat kann mit dem optionalen Token {partition} gebildet werden, wobei Partitionen bei 0 beginnen. Es folgen Beispiele gültiger Eingaben: 1.) MyCollection: Eine Sammlung mit dem Namen „MyCollection“ muss vorhanden sein. 2.) MyCollection{partition}: Solche Auflistungen müssen vorhanden sein: „MyCollection0“, „MyCollection1“, „MyCollection2“ usw.  
-   **Partitionsschlüssel**: Der Name des Felds in Ausgabeereignissen, das zur Angabe des Schlüssels für die sammlungsübergreifende Partitionierung der Ausgabe verwendet wird. Für die Ausgabe einer einzelnen Sammlung kann eine beliebige Ausgabespalte wie „PartitionId“ verwendet werden.  
-   **Dokument-ID**: Optional. Der Name des Felds in Ausgabeereignissen, das zur Angabe des Primärschlüssels verwendet wird, auf dem Einfüge- und Aktualisierungsvorgänge basieren.  

<!---HONumber=AcomDC_1203_2015-->