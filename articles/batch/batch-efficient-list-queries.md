<properties
	pageTitle="Effiziente Listenabfragen"
	description="Erfahren Sie, wie Sie die Anzahl der in einer Liste zurückgegebenen Elemente sowie die für jedes Element zurückgegebenen Informationen reduzieren können."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Effiziente Listenabfragen

Die folgenden Methoden sind Beispiele für Vorgänge, die in nahezu jeder Anwendung, in der Azure Batch verwendet wird, ausgeführt werden müssen, häufig sogar regelmäßig:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

Überwachung ist ein häufiger Anwendungsfall. Bei der Ermittlung der Kapazität und des Status eines Pools müssen beispielsweise alle virtuellen Computer des Pools abgefragt werden. Ein weiteres Beispiel ist die Abfrage der Aufgaben für einen Auftrag, um zu ermitteln, ob sich noch Aufgaben in der Warteschlange befinden. In einigen Fällen sind umfangreiche Daten erforderlich, in anderen Fällen wiederum ist nur eine Zählung der Gesamtanzahl aller Elemente oder der Elemente mit einem bestimmten Status erforderlich.

Dabei ist zu beachten, dass die Anzahl der Elemente, die zurückgegeben werden, sehr groß sein kann, und dass auch der Umfang der Daten, die zum Anzeigen der Liste der Elemente erforderlich sind, sehr groß sein kann. Die Abfrage von vielen Elementen mit umfangreichen Antworten kann zu verschiedenen Problemen führen:

- Die Antwortzeiten der Batch-API können zu langsam werden. Je größer die Anzahl der Elemente, desto länger ist die vom Batch-Dienst benötigte Antwortzeit. Bei einer großen Anzahl von Elementen müssen diese in Blöcke aufgeteilt werden. Daher müssen möglicherweise mehrere Dienst-API-Aufrufe von der Clientbibliothek an den Dienst durchgeführt werden, um alle Elemente für die Liste abzurufen.
- Je mehr Elemente zu verarbeiten sind, desto länger dauert die API-Verarbeitung durch die Anwendung, die Batch aufruft.
- Je mehr Elemente vorhanden sind und/oder je größer die Elemente werden, desto mehr Speicher wird in der Anwendung verbraucht, die Batch aufruft.
- Mehr Elemente und/oder größere Elemente führen zu einem erhöhten Netzwerkdatenverkehr. Die Übertragung dauert länger, und je nach Architektur der Anwendung erhöhen sich möglicherweise die Kosten für Daten, die außerhalb der Region des Batch-Kontos übertragen werden.

Die Batch-API bietet die Möglichkeit, sowohl die Anzahl der in einer Liste zurückgegebenen Elemente als auch die für jedes Element zurückgegebenen Informationen zu reduzieren. Ein Parameter vom Typ [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) kann für Auflistungsvorgänge angegeben werden. Bei "DetailLevel" handelt es sich um eine abstrakte Basisklasse, sodass ein Objekt vom Typ [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) erstellt und als Parameter übergeben werden muss.

Für alle APIs gelten folgende Bedingungen:

- Jeder Eigenschaftenname ist eine Zeichenfolge, die der Eigenschaft des Objekts zugeordnet ist.
- Bei allen Eigenschaftennamen muss die Groß- und Kleinschreibung beachtet werden, bei Eigenschaftswerten dagegen nicht.
- Datum/Uhrzeit-Zeichenfolgen können eines der beiden folgenden Formate aufweisen, und ihnen muss "DateTime" vorangestellt sein.
	- W3CDTF \(z. B. "creationTime gt DateTime’2011-05-08T08:49:37Z’"\)
	- RFC1123 \(z. B. "creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’"\)
- Boolesche Zeichenfolgen sind "true" oder "false".
- Wenn eine ungültige Eigenschaft oder ein ungültiger Operator angegeben ist, wird eine Ausnahme mit der internen Ausnahme "400 \(Ungültige Anforderung\)" erstellt.
- Der DetailLevel-Parameter mit Select- und Expand-Klauseln kann auch an die entsprechenden Get-Methoden übergeben werden, z. B. an "IPoolManager.GetPool\(\)".

Das ODataDetailLevel-Objekt verfügt über drei öffentliche Eigenschaften, die entweder im Konstruktor angegeben oder direkt festgelegt werden können. Alle drei Eigenschaften sind Zeichenfolgen:

- [FilterClause](#filter): Filtern und eventuell Reduzieren der Anzahl der zurückgegebenen Elemente
- [SelectClause](#select): Angeben der spezifischen zurückgegebenen Eigenschaftswerte, sodass die Element- und Antwortgröße reduziert wird
- [ExpandClause](#expand): Zurückgeben aller erforderlichen Daten in einem Aufruf anstatt in mehreren Aufrufen

### <a id="filter"></a> FilterClause

Die Anzahl der zurückgegebenen Elemente kann durch eine Filterzeichenfolge reduziert werden. Ein oder mehrere Eigenschaftswerte können angegeben werden, um sicherzustellen, dass nur die erforderlichen Elemente zurückgegeben werden. Beispiel: nur Elemente mit aktiven Arbeitsaufgaben auflisten; nur aktuell ausgeführte Aufgaben für einen Auftrag auflisten; nur virtuelle Computer auflisten, auf denen Aufgaben ausgeführt werden können.

Eine [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) ist eine Zeichenfolge, die aus einem oder mehreren Ausdrücken besteht, wobei ein Ausdruck aus einem Eigenschaftennamen, einem Operator und einem Wert besteht. Die Eigenschaften, die angegeben werden können, sind spezifisch für jeden API-Aufruf. Dies gilt auch für die für jede Eigenschaft unterstützten Operatoren. Mehrere Ausdrücke können mithilfe der logischen Operatoren "and" und "or" kombiniert werden.

Beispiel für einen Filter zum Auflisten von Aufgaben:

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

Die Eigenschaftswerte, die für jedes Element zurückgegeben werden, können mithilfe einer select-Zeichenfolge begrenzt werden. Für ein Element kann eine Liste mit Eigenschaften angegeben werden. Dann werden nur die entsprechenden Eigenschaftswerte zurückgegeben.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) ist eine Zeichenfolge mit einer durch Trennzeichen getrennten Liste mit Eigenschaftennamen. Es können alle Eigenschaften in dem mit dem Auflistungsvorgang zurückgegebenen Element angegeben werden.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Die Anzahl der API-Aufrufe kann durch eine expand-Zeichenfolge reduziert werden. Ausführlichere Informationen zu jedem Listenelement können mit dem API-Aufruf für eine Liste abgerufen werden, anstatt die Liste abzurufen und dann für jedes Element in der Liste einen Aufruf durchzuführen.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) ähnelt der select-Klausel. Mit der expand-Klausel wird festgelegt, ob bestimmte Daten in den Ergebnissen zurückgegeben werden. Die expand-Klausel wird nur für die Arbeitsaufgabenliste, die Aufgabenliste, die Poolliste und die Auftragsliste unterstützt. Zum gegenwärtigen Zeitpunkt werden nur statistische Informationen unterstützt. Wenn alle Eigenschaften erforderlich sind und keine select-Klausel festgelegt ist, müssen statistische Informationen mit der expand-Klausel abgerufen werden. Wenn eine Teilmenge der Eigenschaften mit einer select-Klausel abgerufen werden, können statistische Informationen in der select-Klausel angegeben und die expand-Klausel auf "null" gesetzt werden.

> [AZURE.NOTE]Es wird empfohlen, dass Sie immer filter- und select-Klauseln für Ihre API-Aufrufe zur Listenerstellung verwenden, um die maximale Effizienz und optimale Leistung der Anwendung zu gewährleisten.

<!---HONumber=July15_HO5-->