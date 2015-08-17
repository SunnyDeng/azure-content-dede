<properties 
	pageTitle="Verwalten der DocumentDB-Kapazität | Microsoft Azure" 
	description="Sie erfahren, wie Sie DocumentDB an die Kapazitätsanforderungen Ihrer Anwendung anpassen." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2015" 
	ms.author="mimig"/>

# Verwalten von DocumentDB-Kapazitätsanforderungen
DocumentDB ist ein vollständig verwalteter, skalierbarer dokumentorientierter NoSQL-Datenbankdienst. Mit DocumentDB müssen Sie keine virtuellen Computer mieten, Software bereitstellen, Datenbanken überwachen oder sich Gedanken über die Notfallwiederherstellung machen. DocumentDB wird von Microsoft-Entwicklern betrieben und ständig überwacht, um erstklassige Verfügbarkeit, Leistung und Datensicherheit zu gewährleisten.

Sie können sich mit DocumentDB vertraut machen, indem Sie über das [Azure-Vorschauportal](documentdb-create-account.md) ein [Datenbankkonto erstellen](https://portal.azure.com/). DocumentDB wird in Einheiten aus SSD-gestütztem Speicher und Durchsatz angeboten. Diese Einheiten werden bereitgestellt, indem in Ihrem Datenbankkonto Datenbanksammlungen erstellt werden. Jede Sammlung umfasst 10 GB an Datenbankspeicher mit reserviertem Durchsatz. Wenn sich die Durchsatzanforderungen Ihrer Anwendung ändern, können Sie diese Einstellung dynamisch ändern, indem Sie die [Leistungsebene](documentdb-performance-levels.md) für jede Sammlung festlegen.

Wenn Ihre Anwendung die Leistungsebene für eine oder mehrere Sammlungen überschreitet, werden die Anforderungen pro Sammlung beschränkt. Dies bedeutet, dass einige Anforderungen erfolgreich sind, während andere aufgrund der Beschränkung fehlschlagen.

## Datenbankkonto und Verwaltungsressourcen
Als Azure-Abonnent können Sie ein oder mehrere DocumentDB-Datenbankkonten bereitstellen. Jedes Datenbankonto verfügt über ein Kontingent von administrativen Ressourcen wie Datenbanken, Benutzern und Berechtigungen. Diese Ressourcen unterliegen [Grenzen und Kontingenten](documentdb-limits.md). [Wenden Sie sich an den Support](documentdb-increase-limits.md), wenn Sie zusätzliche administrative Ressourcen benötigen.

## Datenbanken mit unbegrenztem Dokumentspeicher
Eine einzelne DocumentDB-Datenbank kann eine nahezu unbegrenzte Menge Dokumentspeicher enthalten, der von Auflistungen partitioniert wird. Auflistungen bilden die Transaktionsdomänen für die Dokumente, die darin enthalten sind. Die Größe einer DocumentDB-Datenbank ist flexibel und reicht von wenigen Gigabyte bis hin zu SSD-gestütztem Dokumentspeicher im Terabyte-Bereich mit bereitgestelltem Durchsatz. Im Gegensatz zu einer herkömmlichen RDBMS-Datenbank ist eine Datenbank in DocumentDB nicht auf einen einzigen Computer begrenzt.

Mit DocumentDB können Sie weitere Sammlungen oder Datenbanken bzw. beides erstellen, wenn Sie Ihre Anwendungen skalieren müssen. DocumentDB wird von vielen Erstanbieteranwendungen innerhalb von Microsoft in großem Maße genutzt, indem sehr große DocumentDB-Datenbanken erstellt werden, die jeweils Hunderte oder Tausende von Sammlungen mit mehreren Terabyte Dokumentspeicher enthalten. Sie können eine Datenbank durch Hinzufügen oder Entfernen von Sammlungen vergrößern oder verkleinern, um den Größenanforderungen Ihrer Anwendung zu entsprechen.

## Datenbanksammlungen
Jede DocumentDB-Datenbank kann eine oder mehrere Auflistungen enthalten. Sammlungen dienen als hoch verfügbare Datenpartitionen für die Speicherung und Verarbeitung von Dokumenten. Jede Sammlung kann Dokumente mit heterogenem Schema speichern. Die automatischen Indizierungs- und Abfragefunktionen von DocumentDB ermöglichen Ihnen das einfache Filtern und Abrufen von Dokumenten. Eine Auflistung liefert den Umfang für Dokumentspeicher und Abfrageausführung. Eine Auflistung ist auch eine Transaktionsdomäne für ale Dokumente, die darin enthalten sind. Sammlungen wird der Durchsatz basierend auf der dafür angegebenen Leistungsebene zugeordnet. Dies kann über das Azure-Vorschauportal oder eines der SDKs dynamisch angepasst werden.

Sie können eine beliebige Anzahl von Sammlungen erstellen, um den Skalierungsanforderungen Ihrer Anwendungen in Bezug auf die Speicherung und den Durchsatz gerecht zu werden. Sammlungen können über das [Azure-Vorschauportal](https://portal.azure.com/) oder eines der [DocumentDB SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellt werden.

>[AZURE.NOTE]Jede Sammlung unterstützt Speicher für bis zu 10 GB Dokumentdaten.
 
## Anforderungseinheiten und Datenbankvorgänge
DocumentDB ermöglicht vielfältige Datenbankvorgänge, einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger, die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Verarbeitungskosten im Zusammenhang mit diesen Vorgängen variieren basierend auf CPU, E/A und Speicher, die/der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

Anforderungseinheiten werden basierend auf der Leistungsebene bereitgestellt und zugewiesen, die für eine Sammlung festgelegt wird. Jede Sammlung wird bei der Erstellung mit einer Leistungsebene versehen. Diese Leistungsebene bestimmt die Verarbeitungskapazität einer Sammlung in Anforderungseinheiten pro Sekunde. Leistungsebenen können während der gesamten Lebensdauer einer Sammlung angepasst werden, um sie auf die veränderten Verarbeitungsanforderungen und Zugriffsmuster der Anwendung abzustimmen. Weitere Informationen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels.md).

>[AZURE.IMPORTANT]Sammlungen sind fakturierbare Entitäten. Die Kosten richten sich nach der Leistungsebene, die der Sammlung zugewiesen ist.

Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Für Anwendungen, die die bereitgestellte Anforderungseinheitsrate für eine Sammlung überschreiten, werden Anforderungen an die Sammlung gedrosselt, bis die Rate unter das reservierte Niveau fällt. Wenn Ihre Anwendung eine höhere Durchsatzebene erfordert, können Sie die Leistungsebene von vorhandenen Sammlungen anpassen oder die Anwendungsanforderungen auf neue Sammlungen verteilen.

Eine Anforderungseinheit ist eine normalisierte Kennzahl für die Anforderungsverarbeitungskosten. Eine einzelne Anforderungseinheit stellt die Verarbeitungskapazität dar, die erforderlich ist, um ein einzelnes, aus 10 eindeutigen Eigenschaftswerten bestehendes JSON-Dokument mit 1 KB zu lesen (per „self link“). Bei der Gebühr für Anforderungseinheiten wird angenommen, dass die Konsistenzebene auf den Standardwert "Session" festgelegt ist und alle Dokumente automatisch indiziert werden. Eine Anforderung zum Einfügen, Ersetzen oder Löschen des gleichen Dokuments verbraucht mehr Verarbeitungsleistung des Diensts und daher mehr Anforderungseinheiten. Jede Antwort des Diensts enthält einen benutzerdefinierten Header (x-ms-request-charge), der die für die Anforderung verbrauchten Anforderungseinheiten misst. Auf diesen Header kann auch über die [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) zugegriffen werden. Im .Net SDK ist „RequestCharge“ eine Eigenschaft des ResourceResponse-Objekts.

>[AZURE.NOTE]Die Baseline einer Anforderungseinheit für ein Dokument mit 1 KB entspricht einem einfachen GET-Vorgang des Dokuments per „self link“.

Es gibt verschiedene Faktoren, die sich auf die Anforderungseinheiten auswirken, die für einen Vorgang in einem DocumentDB-Datenbankkonto genutzt werden. Zu diesen Faktoren zählen folgende:

- Dokumentgröße. Je größer ein Dokument, desto mehr Einheiten werden für das Lesen und Schreiben der Daten genutzt.
- Anzahl der Eigenschaften. Eine standardmäßige Indizierung aller Eigenschaften vorausgesetzt, werden mehr Einheiten für das Schreiben eines Dokuments genutzt, wenn die Anzahl der Eigenschaften steigt.
- Datenkonsistenz. Bei Verwendung der Datenkonsistenzebenen "Strong" oder "Bounded Staleness" werden zusätzliche Einheiten zum Lesen von Dokumenten genutzt.
- Indizierte Eigenschaften. Eine Indexrichtlinie für jede Sammlung gibt an, welche Eigenschaften standardmäßig indiziert werden. Sie können die Nutzung von Anforderungseinheiten reduzieren, indem Sie die Anzahl der indizierten Eigenschaften begrenzen. 
- Dokumentindizierung. Standardmäßig wird jedes Dokument automatisch indiziert. Sie nutzen weniger Anforderungseinheiten, wenn Sie einige Dokumente nicht indizieren.

Abfragen, gespeicherte Prozeduren und Trigger nutzen Anforderungseinheiten basierend auf der Komplexität des ausgeführten Vorgangs. Untersuchen Sie während der Entwicklung Ihrer Anwendung den "x-ms-request-charge"-Header, um herauszufinden, wie viel Anforderungseinheiten die einzelnen Vorgänge verbrauchen.

## Auswahl der Konsistenzebene und Durchsatz
Die Auswahl der standardmäßigen Konsistenzebene hat Auswirkungen auf den Durchsatz und die Latenz. Sie können die standardmäßige Konsistenzebene sowohl programmgesteuert als auch über das Azure-Vorschauportal festlegen. Sie können die Konsistenzebene auch pro Anforderung überschreiben. Standardmäßig ist die Konsistenzebene auf "Session" festgelegt, wodurch monotone Lese-/Schreibvorgänge und Read-Your-Write-Garantie ermöglicht wird. Die Sitzungskonsistenz ist ideal für benutzerzentrierte Anwendungen geeignet und liefert einen optimalen Kompromiss zwischen Konsistenz und Leistung.

Anleitungen zum Ändern Ihrer Konsistenzebene im Azure-Vorschauportal finden Sie unter [Verwalten eines DocumentDB-Kontos](documentdb-manage-account.md#consistency). Weitere Informationen zu Konsistenzebenen finden Sie unter [Verwenden von Konsistenzebenen](documentdb-consistency-levels.md).

## Bereitgestellter Dokumentspeicher und Index-Overhead
Mit jeder erstellten Sammlung werden für Sie 10 GB an SSD-gestütztem Dokumentspeicher bereitgestellt. Die 10 GB Dokumentspeicher umfassen die Dokumente plus den Speicher für den Index. Standardmäßig wird eine DocumentDB-Auflistung so konfiguriert, dass alle Dokumente automatisch indiziert werden, ohne dass explizit irgendwelche sekundären Indizes oder Schemas erforderlich wären. Basierend auf den Anwendungen, die DocumentDB nutzen, liegt der Index-Overhead normalerweise zwischen 2 und 20 %. Durch die von DocumentDB verwendete Indizierungstechnologie wird sichergestellt, dass der Index-Overhead, unabhängig von den Werten der Eigenschaften, bei den Standardeinstellungen 80 % der Größe der Dokumente nicht überschreitet.

Standardmäßig werden alle Dokumente von DocumentDB automatisch indiziert. Wenn Sie den Indizierungsaufwand jedoch optimieren möchten, können Sie beim Einfügen oder Ersetzen eines Dokuments bestimmte Dokumente aus der Indizierung ausschließen (siehe [Indizierungsrichtlinien für DocumentDB](documentdb-indexing-policies.md)). Sie können eine DocumentDB-Auflistung so konfigurieren, dass alle Dokumente innerhalb der Auflistung von der Indizierung ausgeschlossen werden. Sie können eine DocumentDB-Sammlung auch so konfigurieren, dass nur bestimmte Eigenschaften oder Pfade mit Platzhaltern in Ihren JSON-Dokumenten selektiv indiziert werden (siehe [Konfigurieren der Indizierungsrichtlinie einer Sammlung](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection)). Durch das Ausschließen von Eigenschaften oder Dokumenten wird auch der Schreibdurchsatz verbessert, was bedeutet, dass Sie weniger Anforderungseinheiten verbrauchen.
 
## Nächste Schritte
Anweisungen zur Überwachung der Leistungsebenen im Azure-Vorschauportal finden Sie unter [Überwachen eines DocumentDB-Kontos](documentdb-monitor-accounts.md).

Weitere Informationen zum Auswählen von Leistungsebenen für Sammlungen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels).
 

<!---HONumber=August15_HO6-->