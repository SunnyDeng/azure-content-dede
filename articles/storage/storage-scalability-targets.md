<properties 
   pageTitle="Skalierbarkeits- und Leistungsziele für Azure Storage | Azure"
   description="Erfahren Sie mehr über die Skalierbarkeits- und Leistungsziele für ein Azure Storage-Konto, einschließlich Kapazität Anforderungsrate sowie eingehende und ausgehende Bandbreite. Machen Sie sich mit den Leistungszielen für Partitionen in den einzelnen Azure Storage-Diensten vertraut."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" /> <tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="04/24/2015"
   ms.author="tamram" />

# Skalierbarkeits- und Leistungsziele für Azure Storage

In diesem Thema werden die Skalierbarkeits- und Leistungsaspekte von Microsoft Azure Storage beschrieben. Eine Zusammenfassung anderer Einschränkungen von Azure, finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

>[AZURE.NOTE] Alle Speicherkonten werden auf der neuen, flachen Netzwerktopologie ausgeführt und unterstützen die unten beschriebenen Skalierbarkeits- und Leistungsziele, unabhängig davon, wann sie erstellt wurden. Weitere Informationen zu der flachen Netzwerkarchitektur von Azure Storage sowie zur Skalierbarkeit finden Sie im Blog des Azure Storage-Teams.

<!-- -->

>[AZURE.IMPORTANT] Die hier aufgelisteten Ziele für Skalierbarkeit und Leistung sind hochgesteckte Ziele, die jedoch erreichbar sind. In jedem Fall hängen die von Ihrem Speicherkonto erzielte Anforderungsrate und Bandbreite von der Größe der gespeicherten Objekte, den verwendeten Zugriffsmustern und der Art der von der Anwendung ausgeführten Arbeitslast ab. Testen Sie unbedingt Ihren Dienst, um festzustellen, ob seine Leistung Ihren Anforderungen entspricht. Wenn möglich, vermeiden Sie plötzliche Lastspitzen bei der Datenverkehrsrate, und stellen Sie sicher, dass der Datenverkehr über alle Partitionen verteilt ist.

>Wenn Ihre Anwendung die Grenze dessen erreicht, was eine Partition an Arbeitsauslastung bewältigen kann, dann gibt Azure Storage den Fehlercode 503 (Server ausgelastet) oder den Fehlercode 500 (Zeitüberschreitung für Vorgang) zurück. In diesem Fall sollte die Anwendung eine exponentielle Backoffrichtlinie für Wiederholungen verwenden. Durch exponentielle Backoffs kann die Auslastung der Partition verringert werden, um die Datenverkehrsspitzen bei dieser Partition auszugleichen.

Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, sollten Sie die Anwendung so erstellen, dass mehrere Speicherkonten verwendet werden, und die Datenobjekte in diesen Speicherkonten partitionieren. In einem einzelnen Azure-Abonnement sind 100 Speicherkonten zulässig. Informationen zu Volumenpreisen finden Sie unter [Speicherpreisdetails](http://azure.microsoft.com/pricing/details/storage/).

## Skalierbarkeitsziele für Speicherkonten

Die Skalierbarkeitsziele für ein Speicherkonto hängen von der Region ab, in dem das Konto erstellt wurde. In den folgenden Abschnitten werden die Skalierbarkeitsziele für Regionen in den USA und für europäische und asiatische Regionen beschrieben.

Weitere Informationen zur Architektur und zu den Skalierbarkeitszielen für Speicherkonten finden Sie im [Blog des Azure Storage-Teams](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/04/windows-azure-s-flat-network-storage-and-2012-scalability-targets.aspx).

### Skalierbarkeitsziele für Standardspeicherkonten in US-Regionen

In der folgende Tabelle werden die Skalierbarkeitsziele für Standardspeicherkonten in US-Regionen basierend auf der ausgewählten Redundanzebene beschrieben:

<table>
<tr>
<th>Gesamtkapazität des Kontos</th>
<th>Gesamtanforderungsrate (ausgehend von einer Objektgröße von 1&#160;KB)</th>
<th>Gesamtbandbreite für ein georedundantes Speicherkonto</th>
<th>Gesamtbandbreite für ein lokal redundantes Speicherkonto</th>
<tr>
<td>500&#160;TB</td>
<td>Bis zu 20.000 Entitäten oder Meldungen pro Sekunde</td>
<td><ul>
<li>*Eingehend: Bis zu 10&#160;Gbit/s</li>
<li>*Ausgehend: Bis zu 20&#160;Gbit/s</li>
</ol>
</td>
<td><ul>
<li>*Eingehend: Bis zu 20&#160;Gbit/s</li>
<li>*Ausgehend: Bis zu 30&#160;Gbit/s</li>
</tr>
</table>

*"Eingehend" bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden.

*"Ausgehend" bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

### Skalierbarkeitsziele für Standardspeicherkonten in europäischen und asiatischen Regionen

In der folgende Tabelle werden die Skalierbarkeitsziele für Standardspeicherkonten in europäischen und asiatischen Regionen basierend auf der ausgewählten Redundanzebene beschrieben:

<table>
<tr>
<th>Gesamtkapazität des Kontos</th>
<th>Gesamtanforderungsrate (ausgehend von einer Objektgröße von 1&#160;KB)</th>
<th>Gesamtbandbreite für ein georedundantes Speicherkonto</th>
<th>Gesamtbandbreite für ein lokal redundantes Speicherkonto</th>
<tr>
<td>500&#160;TB</td>
<td>Bis zu 20.000 Entitäten oder Meldungen pro Sekunde</td>
<td><ul>
<li>*Eingehend: Bis zu 5 Gbit/s</li>
<li>*Ausgehend: Bis zu 10 Gbit/s</li>
</ol>
</td>
<td><ul>
<li>*Eingehend: Bis zu 10&#160;Gbit/s</li>
<li>*Ausgehend: Bis zu 15 Gbit/s</li>
</tr>
</table>

*"Eingehend" bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden.

*"Ausgehend" bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

### Skalierbarkeitsziele für Premium Storage-Konten

Die folgende Tabelle enthält die Skalierbarkeitsziele für Premium Storage-Konten, die in den Regionen West US, East US 2 und Westeuropa verfügbar sind:

<table>
<tr>
<th>Gesamtkapazität des Kontos</th>
<th>Gesamtbandbreite für ein lokal redundantes Premium Storage-Konto</th>
<tr>
<td><ul>
<li>Kapazität des Datenträgers: 35 TB</li>
<li>Kapazität für Momentaufnahmen: 10 TB</li>
</td>
<td>Bis zu 50&#160;GB pro Sekunde für eingehenden und ausgehenden Datenverkehr</td>
</table>

*"Eingehend" bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden.

*"Ausgehend" bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

Informationen zu Premium Storage-Datenträgern finden Sie unter [Premium Storage:  Hochleistungsspeicher für Azure Virtual Machine-Arbeitsauslastungen](storage-premium-storage-preview-portal.md).

## Partitionen in Azure Storage

Die folgende Tabelle enthält die Leistungsziele für eine einzelne Partition für jeden Dienst:

<table>
<tr>
<th>Zieldurchsatz bei Einzel-Blob</th>
<th>Zieldurchsatz bei Einzelwarteschlange (Meldungen mit 1&#160;KB)</th>
<th>Zieldurchsatz bei einzelner Tabellenpartition (Entitäten mit 1&#160;KB)</th>
<th>Zieldurchsatz für eine einzelne Datei (Vorschau)</th><tr>
<td>Bis zu 60&#160;MB pro Sekunde, oder bis zu 500 Anforderungen pro Sekunde</td>
<td>Bis zu 2000 Meldungen pro Sekunde</td>
<td>Bis zu 2000 Entitäten pro Sekunde</td>
<td>Bis 60 MB pro Sekunde</td>
</table>

Jedes Objekt, das Daten enthält, die in Azure Storage (Blobs, Nachrichten, Entitäten und Dateien) gespeichert werden, gehört zu einer Partition und wird durch einen Partitionsschlüssel identifiziert. Die Partition bestimmt, wie in Azure Storage der Lastenausgleich für Blobs, Nachrichten, Entitäten und Dateien auf Servern erfolgt, sodass die Datenverkehrsanforderungen dieser Objekte erfüllt werden. Der Partitionsschlüssel ist innerhalb des Speicherkontos eindeutig und wird verwendet, um einen Blob, eine Nachricht oder eine Entität zu suchen.

Bei Tabellen werden alle Entitäten mit demselben Partitionsschlüsselwert in derselben Partition gruppiert und auf demselben Partitionsserver gespeichert. Dies ist ein wichtiger Punkt, der beim Entwurf von Anwendungen zu berücksichtigt werden muss. In der Anwendung sollten die Vorteile bezüglich der Skalierbarkeit, den die Verteilung von Entitäten auf mehrere Partitionen bietet, und die Vorteile bezüglich des Datenzugriffs, den die Gruppierung von Entitäten in einer einzelnen Partition bietet, gegeneinander abgewogen werden. Ein wichtiger Vorteil der Gruppierung von Entitäten in Partitionen besteht darin, dass es möglich ist, atomare Vorgänge mit verschiedenen Entitäten einer Partitionen auszuführen, da sich eine Partition auf einem einzelnen Server befindet.

Partitionen wirken sich wie folgt auf den Lastenausgleich und die Skalierbarkeit der einzelnen Speicherdienste aus:

- **Blobs**: Der Partitionsschlüssel für ein Blob setzt sich aus dem Containernamen und dem Blob-Namen zusammen. Dies bedeutet, dass jedes Blob eine eigene Partition besitzt. Blobs können daher über mehrere Server verteilt werden, um den Zugriff darauf zu skalieren. Blobs können zwar logisch in Blob-Containern zusammengefasst werden, allerdings wirkt sich dies nicht auf die Partitionierung einer solchen Gruppierung aus.

- **Nachrichten**: Der Partitionsschlüssel für eine Nachricht entspricht dem Namen der Warteschlange, damit alle Nachrichten in einer Warteschlange in einer einzelnen Partition gruppiert und von einem einzelnen Server bedient werden. Verschiedene Warteschlangen können von verschiedenen Servern verarbeitet werden, um einen Lastenausgleich für die gegebene Anzahl von Warteschlangen eines Speicherkonto durchzuführen .

- **Entitäten**: Der Partitionsschlüssel für eine Entität besteht aus dem Tabellennamen und dem Partitionsschlüssel, wobei der Partitionsschlüssel der Wert der erforderlichen benutzerdefinierten **PartitionKey**-Eigenschaft der Entität ist.

	Entitäten innerhalb einer Tabelle können die gleichen Partitionsschlüsselwerte haben. Wenn dies der Fall ist, werden sie in derselben Partition gruppiert. Eine Anwendung kann unteilbare Batchtransaktionen mit Entitäten innerhalb der gleichen Partition durchführen, da sie vom selben Server bereitgestellt werden.

	Für Entitäten, die sich in derselben Tabelle enthalten sind, aber zu verschiedenen Partitionen gehören, kann ein Lastenausgleich auf verschiedenen Servern durchgeführt werden, sodass sich eine große Tabelle mit höherer Skalierbarkeit ergibt.

## Weitere Informationen

- [Speicher – Preisdetails](http://azure.microsoft.com/pricing/details/storage/)
- [Azure Storage: Ein hoch verfügbarer Cloud-Speicherdienst mit starker Konsistenz](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md)


<!--HONumber=54--> 