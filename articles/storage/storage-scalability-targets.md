<properties 
   pageTitle="Skalierbarkeits- und Leistungsziele für Azure Storage | Microsoft Azure"
   description="Informationen zu den Skalierbarkeits- und Leistungszielen für Azure Storage, einschließlich Kapazität Anforderungsrate sowie eingehende und ausgehende Bandbreite für Standard- und Premium-Speicherkonten. Machen Sie sich mit den Leistungszielen für Partitionen in den einzelnen Azure Storage-Diensten vertraut."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="06/30/2015"
   ms.author="tamram" />

# Skalierbarkeits- und Leistungsziele für Azure Storage

In diesem Thema werden die Skalierbarkeits- und Leistungsaspekte von Microsoft Azure Storage beschrieben. Eine Zusammenfassung anderer Einschränkungen von Azure, finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

>[AZURE.NOTE]Alle Speicherkonten werden auf der neuen, flachen Netzwerktopologie ausgeführt und unterstützen die unten beschriebenen Skalierbarkeits- und Leistungsziele, unabhängig davon, wann sie erstellt wurden. Weitere Informationen zur flachen Netzwerkarchitektur von Azure Storage sowie zur Skalierbarkeit finden Sie unter [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (in englischer Sprache).

<!-- -->

>[AZURE.IMPORTANT]Die hier aufgelisteten Ziele für Skalierbarkeit und Leistung sind hochgesteckte Ziele, die jedoch erreichbar sind. In jedem Fall hängen die von Ihrem Speicherkonto erzielte Anforderungsrate und Bandbreite von der Größe der gespeicherten Objekte, den verwendeten Zugriffsmustern und der Art der von der Anwendung ausgeführten Workload ab. Testen Sie unbedingt Ihren Dienst, um festzustellen, ob seine Leistung Ihren Anforderungen entspricht. Wenn möglich, vermeiden Sie plötzliche Lastspitzen bei der Datenverkehrsrate, und stellen Sie sicher, dass der Datenverkehr über alle Partitionen verteilt ist.

>Wenn Ihre Anwendung die Grenze dessen erreicht, was eine Partition an Workload bewältigen kann, dann gibt Azure Storage den Fehlercode 503 (Server ausgelastet) oder den Fehlercode 500 (Zeitüberschreitung für Vorgang) zurück. In diesem Fall sollte die Anwendung eine exponentielle Backoffrichtlinie für Wiederholungen verwenden. Durch exponentielle Backoffs kann die Auslastung der Partition verringert werden, um die Datenverkehrsspitzen bei dieser Partition auszugleichen.

Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, können Sie die Anwendung so erstellen, dass mehrere Speicherkonten verwendet werden, und die Datenobjekte in diesen Speicherkonten partitionieren. In einem einzelnen Azure-Abonnement sind 100 Speicherkonten zulässig. Informationen zu Volumenpreisen finden Sie unter [Speicherpreisdetails](http://azure.microsoft.com/pricing/details/storage/).

## Skalierbarkeitsziele für Standardspeicherkonten

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Skalierbarkeitsziele für Premium Storage-Konten

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../../includes/azure-storage-limits-premium-storage.md)]

## Speichergrenzwerte - Azure-Ressourcen-Manager

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Partitionen in Azure Storage

Jedes Objekt, das Daten enthält, die in Azure Storage (Blobs, Nachrichten, Entitäten und Dateien) gespeichert werden, gehört zu einer Partition und wird durch einen Partitionsschlüssel identifiziert. Die Partition bestimmt, wie in Azure Storage der Lastenausgleich für Blobs, Nachrichten, Entitäten und Dateien auf Servern erfolgt, sodass die Datenverkehrsanforderungen dieser Objekte erfüllt werden. Der Partitionsschlüssel ist innerhalb des Speicherkontos eindeutig und wird verwendet, um einen Blob, eine Nachricht oder eine Entität zu suchen.

In der Tabelle oben unter [Skalierbarkeitsziele für Standardspeicherkonten](#scalability-targets-for-standard-storage-accounts) sind die Leistungsziele für eine einzelne Partition für jeden Dienst aufgeführt.

Partitionen wirken sich wie folgt auf den Lastenausgleich und die Skalierbarkeit der einzelnen Speicherdienste aus:

- **Blobs**: Der Partitionsschlüssel für ein Blob setzt sich aus dem Containernamen und dem Blob-Namen zusammen. Dies bedeutet, dass jedes Blob eine eigene Partition besitzt. Blobs können daher über mehrere Server verteilt werden, um den Zugriff darauf zu skalieren. Blobs können zwar logisch in Blob-Containern zusammengefasst werden, allerdings wirkt sich dies nicht auf die Partitionierung einer solchen Gruppierung aus.

- **Nachrichten**: Der Partitionsschlüssel für eine Nachricht entspricht dem Namen der Warteschlange, damit alle Nachrichten in einer Warteschlange in einer einzelnen Partition gruppiert und von einem einzelnen Server bedient werden. Verschiedene Warteschlangen können von verschiedenen Servern verarbeitet werden, um einen Lastenausgleich für die gegebene Anzahl von Warteschlangen eines Speicherkonto durchzuführen .

- **Entitäten**: Der Partitionsschlüssel für eine Entität besteht aus dem Tabellennamen und dem Partitionsschlüssel, wobei der Partitionsschlüssel der Wert der erforderlichen benutzerdefinierten **PartitionKey**-Eigenschaft der Entität ist.

	Alle Entitäten mit dem gleichen Partitionsschlüsselwert werden in derselben Partition gruppiert und auf demselben Partitionsserver gespeichert. Dies ist ein wichtiger Punkt, der beim Entwurf von Anwendungen zu berücksichtigt werden muss. In der Anwendung sollten die Vorteile bezüglich der Skalierbarkeit, den die Verteilung von Entitäten auf mehrere Partitionen bietet, und die Vorteile bezüglich des Datenzugriffs, den die Gruppierung von Entitäten in einer einzelnen Partition bietet, gegeneinander abgewogen werden.

	Ein wichtiger Vorteil der Gruppierung mehrerer Entitäten einer Tabelle in einer einzelnen Partition besteht darin, dass es möglich ist, kleine Batchvorgänge in verschiedenen Entitäten in der gleichen Partition auszuführen, da sich eine Partition auf einem einzelnen Server befindet. Wenn Sie Batchvorgänge ausführen möchten, empfiehlt es sich daher, Entitäten mit dem gleichen Partitionsschlüssel zu gruppieren.

	Andererseits kann für Entitäten, die in derselben Tabelle enthalten sind, jedoch zu verschiedenen Partitionen gehören, ein Lastausgleich auf verschiedenen Servern vorgenommen werden, sodass sich eine große Tabelle mit höherer Skalierbarkeit ergibt.

## Siehe auch

- [Speicher – Preisdetails](http://azure.microsoft.com/pricing/details/storage/)
- [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md)
- [Premium-Speicher: Hochleistungsspeicher für Workloads in Azure Virtual Machine](storage-premium-storage-preview-portal/)
- [Azure Storage-Replikation](storage-redundancy.md)
- [Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage](storage-performance-checklist.md)
- [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (in englischer Sprache)



 

<!---HONumber=August15_HO6-->