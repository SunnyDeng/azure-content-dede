<properties 
	pageTitle="Speicheranalyse und Speicheranalysekosten" 
	description="Erfahren Sie, wie Sie die Speicheranalyse zum besseren Verständnis Ihrer Speicherdienstabrechnung nutzen können." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# Speicheranalyse und Speicheranalysekosten

In diesem Thema werden die Kosten für die Speicheranalyse sowie das Verwenden von Metrik- und Protokollierungsdaten zum Überprüfen der Speicherdienstabrechnung erläutert.


## Kosten der Speicheranalyse

Die Speicheranalyse wird vom Speicherkontobesitzer aktiviert. Standardmäßig ist sie nicht aktiviert. Alle Metrikdaten werden von den Diensten eines Speicherkontos geschrieben. Deshalb ist jeder Schreibvorgang, der von der Speicheranalyse ausgeführt wird, gebührenpflichtig. Darüber hinaus ist die Menge des von Metrikdaten genutzten Speichers ebenfalls gebührenpflichtig.

Die folgenden Aktionen der Speicheranalyse sind gebührenpflichtig:

- Anforderungen zum Erstellen von BLOBs für die Protokollierung

- Anforderungen zum Erstellen von Tabellenentitäten für Metriken

Wenn Sie eine Datenbeibehaltungsrichtlinie konfiguriert haben und die Speicheranalyse ältere Protokollierungs- und Metrikdaten löscht, werden Ihnen keine Löschtransaktionen in Rechnung gestellt. Löschtransaktionen von einem Client sind jedoch gebührenpflichtig. Weitere Informationen zu Beibehaltungsrichtlinien finden Sie unter [Festlegen einer Beibehaltungsrichtlinie für Speicheranalysedaten](https://msdn.microsoft.com/library/azure/hh343263.aspx).

## Grundlegendes zu gebührenpflichtigen Anforderungen

Jede Anforderung, die an den Speicherdienst eines Kontos erfolgt, ist entweder gebührenpflichtig oder nicht. Die Speicheranalyse protokolliert jede einzelne an einen Dienst gerichtete Anforderung, einschließlich einer Statusmeldung, die angibt, wie die Anforderung behandelt wurde. Entsprechend speichert die Speicheranalyse Metriken sowohl für einen Dienst als auch für die API-Vorgänge dieses Diensts, einschließlich der Prozentsätze und Anzahl bestimmter Statusmeldungen. Diese gesamten Funktionen können Ihnen helfen, die gebührenpflichtigen Anforderungen zu analysieren, Verbesserungen an der Anwendung vorzunehmen und Probleme bei den Anforderungen an Dienste zu diagnostizieren. Weitere Informationen zur Abrechnung finden Sie unter [Grundlagen zur Abrechnung von Azure-Speicher - Bandbreite, Transaktionen und Kapazität](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Wenn Sie Speicheranalysedaten überprüfen, können Sie anhand der Tabellen im Thema [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/azure/hh343260.aspx) bestimmen, welche Anforderungen gebührenpflichtig sind. Anschließend können Sie die Protokolle und Metrikdaten mit den Statusmeldungen vergleichen, um zu ermitteln, ob Ihnen eine bestimmte Anforderung in Rechnung gestellt wurde. Sie können auch die Tabellen im obigen Thema verwenden, um die Verfügbarkeit für einen Speicherdienst oder einen einzelnen API-Vorgang zu bestimmen.

## Nächste Schritte
[Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/azure/hh343260.aspx) 

[Informationen zur Protokollierung durch die Speicheranalyse ](https://msdn.microsoft.com/library/azure/hh343262.aspx) 

[Informationen zu Metriken der Speicheranalyse](../storage-about-analytics-metrics.md) 

<!--HONumber=47-->
