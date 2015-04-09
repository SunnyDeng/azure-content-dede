<properties 
	pageTitle="Verwalten der Kapazität und Leistung von DocumentDB | Azure" 
	description="Erfahren Sie, wie Sie DocumentDB elastisch an die Kapazitätsanforderungen Ihrer Anwendung anpassen." 
	services="documentdb" 
	authors="mimig1, johnfmacintyre" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mimig"/>

#Verwalten der Kapazität und Leistung von DocumentDB
DocumentDB ist ein vollständig verwalteter, skalierbarer dokumentorientierter NoSQL-Datenbankdienst.  Mit DocumentDB müssen Sie keine virtuellen Computer mieten, Software bereitstellen, Datenbanken überwachen oder sich Gedanken über die Notfallwiederherstellung machen. DocumentDB wird von Microsoft-Entwicklern betrieben und ständig überwacht, um erstklassige Verfügbarkeit, Leistung und Datensicherheit zu gewährleisten.  

Sie können sich mit DocumentDB vertraut machen, indem Sie über das [Microsoft Azure-Vorschauverwaltungsportal](https://portal.azure.com/) ein Datenbankkonto erstellen. DocumentDB wird in kombinierbaren Kapazitätseinheiten (Capacity Units, CU) aus SSD-gestütztem Speicher und Durchsatz angeboten. Sie können DocumentDB elastisch an den Bedarf an Leistung und Speicher einer Anwendung anpassen. 

Jede CU ist mit einem Kontingent von Sammlungen zum Speichern von Dokumentdaten, bereitgestelltem Dokumentspeicher und bereitgestelltem Durchsatz ausgestattet, der in Anforderungseinheiten gemessen wird. Wenn sich die Kapazitätsanforderungen Ihrer Anwendung ändern, können Sie die Menge an bereitgestellter Kapazität in Ihrem Datenbankkonto nach oben oder nach unten skalieren. Unter einem Datenbankkonto bereitgestellte Kapazität ist für alle Datenbanken und Auflistungen verfügbar, die innerhalb des Kontos vorhanden sind bzw. erstellt werden.

> [AZURE.NOTE] Jede Sammlung kann sowohl für Speicher als auch Durchsatz bis zur maximal unterstützten [Grenze](documentdb-limits.md) skaliert werden. Der Durchsatz wird gleichmäßig bis zur maximalen Grenze pro Sammlung an alle Sammlungen verteilt.

Wenn Ihre Anwendung die Leistungsebene für eine oder mehrere Sammlungen überschreitet, werden die Anforderungen pro Sammlung beschränkt. Dies bedeutet, dass einige Anforderungen erfolgreich sind, während andere aufgrund der Beschränkung fehlschlagen.

##<a name="DBaccount"></a>Datenbankkonto und Verwaltungsressourcen
Als Azure-Abonnent können Sie ein oder mehrere DocumentDB-Datenbankkonten bereitstellen. Jedes Konto verfügt über ein Kontingent von administrativen Ressourcen wie Datenbanken, Benutzern und Berechtigungen. Diese Ressourcen unterliegen [Grenzen und Kontingenten](documentdb-limits.md). Wenden Sie sich an den Support, wenn Sie zusätzliche administrative Ressourcen benötigen.   

##<a name="DBstorage"></a> Datenbanken mit unbegrenztem Dokumentspeicher
Eine einzelne DocumentDB-Datenbank kann eine nahezu unbegrenzte Menge Dokumentspeicher enthalten, der von Auflistungen partitioniert wird. Auflistungen bilden die Transaktionsdomänen für die Dokumente, die darin enthalten sind. Eine DocumentDB-Datenbank ist standardmäßig elastisch und reicht von wenigen GB bis hin zu Terabyte von SSD-gestütztem Dokumentspeicher und bereitgestelltem Durchsatz. Im Gegensatz zu einer herkömmlichen RDBMS-Datenbank ist eine Datenbank in DocumentDB nicht auf einen einzigen Computer begrenzt.   

Mit DocumentDB können Sie weitere Sammlungen oder Datenbanken bzw. beides erstellen, wenn die Größenanforderungen Ihrer Anwendung zunehmen. Tatsächlich wird DocumentDB von vielen Erstanbieteranwendungen innerhalb von Microsoft in großem Maße genutzt, indem sehr große DocumentDB-Datenbanken erstellt werden, die jeweils Tausende von Auflistungen mit mehreren Terabytes Dokumentspeicher enthalten. Sie können eine Datenbank durch Hinzufügen oder Entfernen von Sammlungen vergrößern oder verkleinern, um den Größenanforderungen Ihrer Anwendung zu entsprechen. Sie können in Abhängigkeit von der Angebotsverfügbarkeit und der erworbenenen Menge an Kapazitätseinheiten eine beliebige Anzahl von Auflistungen innerhalb einer Datenbank erstellen. Der SSD-gestützte Speicher und für Sie bereitgestellte Durchsatz kann über die Auflistungen unter den Datenbanken in Ihrem Datenbankkonto aufgeteilt werden. 

##<a name="DBCollections"></a>Datenbanksammlungen
Jede DocumentDB-Datenbank kann eine oder mehrere Auflistungen enthalten. Eine Auflistung liefert den Umfang für Dokumentspeicher und Abfrageausführung. Eine Auflistung ist auch eine Transaktionsdomäne für ale Dokumente, die darin enthalten sind. Sie können eine beliebige Anzahl von Auflistungen erstellen, um den Skalierungsanforderungen Ihrer Anwendungen gerecht zu werden. Um Auflistungen zu erstellen, müssen Sie zuerst eine oder mehrere Kapazitätseinheiten (CU) kaufen. Jede Kapazitätseinheit umfasst ein Kontingent von Auflistungen. Wenn Sie das Auflistungskontingent für Ihr Konto erreichen, können Sie weitere Kapazitätseinheiten erwerben.  

>[AZURE.NOTE] Jede Sammlung unterstützt Speicher für bis zu 10 GB Dokumentdaten. 

##<a name="ProvStorage"></a>Bereitgestellter Speicher und Durchsatz als Kapazitätseinheiten
Sie können kombinierbare Einheiten von SSD-gestütztem Dokumentspeicher und Durchsatz als Kapazitätseinheiten (CU) bereitstellen. Sie können DocumentDB mit vorhersagbarer Leistung elastisch skalieren, indem Sie weitere Kapazitätseinheiten erwerben, um die Anforderungen Ihrer Anwendung im Hinblick auf Skalierung, Speicher und Durchsatz zu erfüllen.  
 
Jede CU ist mit 3 elastischen Auflistungen, 10 GB SSD-gestütztem bereitgestelltem Dokumentspeicher und 2000 Anforderungseinheiten (RU) bereitgestelltem Durchsatz ausgestattet. Die bereitgestellte Speicher- und Durchsatzkapazität einer CU wird über die von Ihnen erstellten DocumentDB-Auflistungen verteilt.   

##<a name="ProvThroughput"></a>Anforderungseinheiten und Datenbankvorgänge
DocumentDB ermöglicht vielfältige Datenbankvorgänge, einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger, die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Verarbeitungskosten im Zusammenhang mit diesen Vorgängen variieren basierend auf CPU, E/A und Speicher, die/der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

Anforderungseinheiten werden für jedes Datenbankkonto basierend auf der Anzahl erworbener Kapazitätseinheiten bereitgestellt. In der DocumentDB-Vorschau wird jeder erstellten Sammlung eine Grenze für Anforderungseinheiten zugewiesen. Die Zuordnung wird berechnet, indem alle Anforderungseinheiten der erworbenen Kapazitätseinheiten gleichmäßig auf die Anzahl der innerhalb eines Kontos erstellten Sammlungen verteilt werden. Bei einem Konto mit 2 erworbenen Kapazitätseinheiten und 4 erstellten Sammlungen werden jeder Sammlung beispielsweise 2 Kapazitätseinheiten x 2.000 Anforderungseinheiten / 4 Sammlungen für eine Zuteilung von 1.000 Anforderungseinheiten pro Sammlung zugewiesen. Wenn Azure DocumentDB allgemein verfügbar ist, verteilt der Dienst die Anforderungseinheiten nicht mehr auf Sammlungen in einem Konto. Anforderungseinheiten werden basierend auf Leistungsebenen erworben und bestimmten Sammlungen zugewiesen. Weitere Informationen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels.md). 

>[AZURE.NOTE] Während der Vorschau von Azure DocumentDB können einer Sammlung höchstens 2.000 Anforderungseinheiten zugeordnet werden. 

Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitsrate für ihr Konto überschreiten, werden gedrosselt, bis die Rate unter die reservierte Ebene für jede Sammlung fällt. Wenn die Anwendung einen höheren Durchsatz benötigt, können Sie zusätzliche Kapazitätseinheiten erwerben.

Eine Anforderungseinheit ist eine normalisierte Kennzahl für die Anforderungsverarbeitungskosten. Eine einzelne Anforderungseinheit stellt die Verarbeitungskapazität dar, die erforderlich ist, um ein einzelnes, aus 10 eindeutigen Eigenschaftswerten bestehendes JSON-Dokument mit 1 KB zu lesen. Bei der Gebühr für Anforderungseinheiten wird angenommen, dass die Konsistenzebene auf den Standardwert "Session" festgelegt ist und alle Dokumente automatisch indiziert werden. Eine Anforderung zum Einfügen, Ersetzen oder Löschen des gleichen Dokuments verbraucht mehr Verarbeitungsleistung des Diensts und daher mehr Anforderungseinheiten. Jede Anforderungsantwort des Diensts enthält einen benutzerdefinierten Header (x-ms-request-charge), der die für die Anforderung verbrauchten Anforderungseinheiten misst. Auf diesen Header kann auch über die SDKs zugegriffen werden. Im .Net SDK ist "RequestCharge" eine Eigenschaft des "ResourceResponse"-Objekts.

Es gibt verschiedene Faktoren, die sich auf die Anforderungseinheiten auswirken, die für einen Vorgang in einem DocumentDB-Datenbankkonto verbraucht werden. Zu diesen Faktoren zählen folgende:

- Dokumentgröße - je größer ein Dokument ist, desto mehr Einheiten werden für das Lesen und Schreiben der Daten verbraucht.
- Anzahl der Eigenschaften - eine standardmäßige Indizierung aller Eigenschaften vorausgesetzt, werden mehr Einheiten für das Schreiben eines Dokuments verbraucht, wenn die Anzahl der Eigenschaften steigt.
- Datenkonsistenz - bei Verwendung der Datenkonsistenzebenen "Strong" oder "Bounded Staleness" werden zusätzliche Einheiten zum Lesen von Dokumenten verbraucht.
- Indizierte Eigenschaften - eine Indexrichtlinie für jede Sammlung gibt an, welche Eigenschaften standardmäßig indiziert werden. Sie können den Verbrauch von Anforderungseinheiten reduzieren, indem Sie die Anzahl der indizierten Eigenschaften begrenzen.
- Dokumentindizierung - standardmäßig wird jedes Dokument automatisch indiziert. Sie verbrauchen weniger Anforderungseinheiten, wenn Sie einige Dokumente nicht indizieren.

Abfragen, gespeicherte Prozeduren und Trigger verbrauchen Anforderungseinheiten basierend auf der Komplexität des ausgeführten Vorgangs. Untersuchen Sie während der Entwicklung Ihrer Anwendung den "x-ms-request-charge"-Header, um herauszufinden, wie viel Anforderungseinheiten die einzelnen Vorgänge verbrauchen.

##<a name="Collections"></a>Sammlungen und bereitgestellter Durchsatz
Der bereitgestellte Durchsatz für Ihr Datenbankkonto wird einheitlich über alle Auflistungen hinweg bis zur maximalen Durchsatzebene (Anforderungseinheiten) für eine einzelne Auflistung zugeteilt. Wenn Sie beispielsweise eine einzige Kapazitätseinheit erwerben und eine einzige Auflistung erstellen, steht der gesamte bereitgestellte Durchsatz der CU für die Auflistung zur Verfügung. Wenn eine weitere Auflistung erstellt wird, wird der bereitgestellte Durchsatz gleichmäßig über die beiden Auflistungen verteilt, wobei jede die Hälfte des gesamten bereitgestellten Durchsatzes erhält.  

##<a name="Consistency"></a>Auswahl der Konsistenzebene und Durchsatz
Die Auswahl der standardmäßigen Konsistenzebene hat Auswirkungen auf den Durchsatz und die Latenz.  Sie können die standardmäßige Konsistenzebene sowohl programmgesteuert als auch über das Azure-Portal festlegen. Sie können die Konsistenzebene auch pro Anforderung überschreiben. Standardmäßig ist die Konsistenzebene auf "Session" festgelegt, wodurch monotone Lese-/Schreibvorgänge und Read-Your-Write-Garantie ermöglicht wird. Die Sitzungskonsistenz ist ideal für benutzerzentrierte Anwendungen geeignet und liefert einen optimalen Kompromiss zwischen Konsistenz und Leistung.    

Unter der Vorgehensweise: Verwalten der DocumentDB-Konsistenzeinstellungen in [Verwalten eines DocumentDB-Kontos](documentdb-manage-account.md).

##<a name="IndexOverhead"></a>Bereitgestellter Dokumentspeicher und Index-Overhead
Mit jeder gekauften CU werden Ihrem Konto 10 GB SSD-gestützter Dokumentspeicher bereitgestellt. Die 10 GB Dokumentspeicher umfassen die Dokumente plus den Speicher für den Index. Standardmäßig wird eine DocumentDB-Auflistung so konfiguriert, dass alle Dokumente automatisch indiziert werden, ohne dass explizit irgendwelche sekundären Indizes oder Schemas erforderlich wären. Der typische Index-Overhead bei in hohem Maße genutzten Erstanbieteranwendungen, die DocumentDB verwenden, liegt zwischen 2 und 20 %. Durch die von DocumentDB verwendete Indizierungstechnologie wird sichergestellt, dass der Index-Overhead, unabhängig von den Werten der Eigenschaften, bei den Standardeinstellungen 80 % der Größe der Dokumente nicht überschreitet.  

Standardmäßig werden alle Dokumente von DocumentDB automatisch indiziert. Wenn Sie den Index-Overhead jedoch optimieren möchten, können Sie beim Einfügen oder Ersetzen eines Dokuments bestimmte Dokumente aus der Indizierung ausschließen. Sie können eine DocumentDB-Auflistung so konfigurieren, dass alle Dokumente innerhalb der Auflistung von der Indizierung ausgeschlossen werden. Sie können eine DocumentDB-Auflistung auch so konfigurieren, dass nur bestimmte Eigenschaften oder Pfade mit Platzhaltern in Ihren JSON-Dokumenten selektiv indiziert werden.  Durch das Ausschließen von Eigenschaften oder Dokumenten wird auch der Schreibdurchsatz verbessert, was bedeutet, dass Sie weniger Anforderungseinheiten verbrauchen.   
 
##<a name="NextSteps"></a>Nächste Schritte
Unter der Vorgehensweise: Verwalten der DocumentDB-Konsistenzeinstellungen in [Verwalten eines DocumentDB-Kontos](documentdb-manage-account.md).

Anweisungen für die Überwachung der Leistungsebenen im Azure-Vorschauportal finden Sie unter [Überwachen eines DocumentDB-Kontos](documentdb-monitor-accounts.md).

<!--HONumber=49-->