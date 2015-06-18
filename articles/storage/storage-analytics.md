<properties 
	pageTitle="Speicheranalyse" 
	description="Verwalten von Parallelität für die BLOB-, Warteschlangen-, Tabellen- und Dateidienste" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# Speicheranalyse

Die Azure-Speicheranalyse führt die Protokollierung aus und stellt Metrikdaten für ein Speicherkonto bereit. Mit diesen Daten können Sie Anforderungen verfolgen, Verwendungstrends analysieren und Probleme mit dem Speicherkonto diagnostizieren.

Zum Verwenden der Speicheranalyse müssen Sie sie einzeln für jeden zu überwachenden Dienst aktivieren.  Die Aktivierung ist im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) möglich. Weitere Informationen finden Sie unter [Vorgehensweise: Überwachen eines Speicherkontos](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Über die Vorgänge [BLOB-Diensteigenschaften abrufen](https://msdn.microsoft.com/de-de/library/hh452239.aspx), [Warteschlangendiensteigenschaften abrufen](https://msdn.microsoft.com/de-de/library/hh452243.aspx) und [Tabellendiensteigenschaften abrufen](https://msdn.microsoft.com/de-de/library/hh452238.aspx) können Sie die Speicheranalyse für jeden Dienst aktivieren.

Die aggregierten Daten werden in einem bekannten BLOB (zur Protokollierung) und in bekannten Tabellen (als Metrik) gespeichert. Der Zugriff erfolgt über APIs für den BLOB-Dienst und Tabellendienst.

Bei der Speicheranalyse ist die Menge der gespeicherten Daten auf 20 TB beschränkt. Diese Beschränkung gilt unabhängig vom Gesamtlimit für Ihr Speicherkonto. Weitere Informationen zu Abrechnungs- und Datenaufbewahrungsrichtlinien finden Sie unter [Speicheranalyse und Speicheranalysekosten](https://msdn.microsoft.com/library/hh360997.aspx). Weitere Informationen zu Begrenzungen für Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](https://msdn.microsoft.com/library/dn249410.aspx).

Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).



<!--HONumber=47-->
 