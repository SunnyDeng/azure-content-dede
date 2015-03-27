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
	ms.date="01/13/2015" 
	ms.author="mimig"/>

# Verwalten der Kapazität und Leistung von DocumentDB
DocumentDB ist ein vollständig verwalteter, skalierbarer dokumentorientierter NoSQL-Datenbankdienst.  Mit DocumentDB müssen Sie keine virtuellen Computer mieten, Software bereitstellen, Datenbanken überwachen oder sich Gedanken über die Notfallwiederherstellung machen. DocumentDB wird von Microsoft-Entwicklern betrieben und ständig überwacht, um erstklassige Verfügbarkeit, Leistung und Datensicherheit zu gewährleisten.  

Sie können sich mit DocumentDB vertraut machen, indem Sie über das [Microsoft Azure-Vorschauverwaltungsportal](https://portal.azure.com/) ein Datenbankkonto erstellen. DocumentDB wird in kombinierbaren Kapazitätseinheiten (Capacity Units, CU) aus SSD-gestütztem Speicher und Durchsatz angeboten. Sie können DocumentDB elastisch an den Bedarf an Leistung und Speicher einer Anwendung anpassen. 

Jede CU ist mit einem Kontingent von Sammlungen zum Speichern von Dokumentdaten, bereitgestelltem Dokumentspeicher und bereitgestelltem Durchsatz ausgestattet, der in Anforderungseinheiten gemessen wird. Wenn sich die Kapazitätsanforderungen Ihrer Anwendung ändern, können Sie die Menge an bereitgestellter Kapazität in Ihrem Datenbankkonto nach oben oder nach unten skalieren. Unter einem Datenbankkonto bereitgestellte Kapazität ist für alle Datenbanken und Auflistungen verfügbar, die innerhalb des Kontos vorhanden sind bzw. erstellt werden.

> [AZURE.NOTE] Jede Sammlung kann sowohl für Speicher als auch Durchsatz bis zur maximal unterstützten [Grenze](http://azure.microsoft.com/documentation/articles/documentdb-limits/) skaliert werden. Der Durchsatz wird gleichmäßig bis zur maximalen Grenze pro Sammlung an alle Sammlungen verteilt.

Wenn Ihre Anwendung Leistungsstufen für eine oder mehrere Sammlungen überschreitet, werden Anforderungen auf einer Basis pro Auflistung eingeschränkt. Das bedeutet, dass einige Anwendungsanforderungen möglicherweise erfolgreich durchgeführt, andere jedoch eingeschränkt werden.

## <a name="DBaccount"></a>Datenbankkonto und Verwaltungsressourcen
Als Azure-Abonnent können Sie ein oder mehrere DocumentDB-Datenbankkonten bereitstellen. Jedes Konto verfügt über ein Kontingent von administrativen Ressourcen wie Datenbanken, Benutzern und Berechtigungen. Diese Ressourcen unterliegen [Grenzen und Kontingenten](http://azure.microsoft.com/documentation/articles/documentdb-limits/). Wenden Sie sich an den Support, wenn Sie zusätzliche administrative Ressourcen benötigen.   

## <a name="DBstorage"></a>Datenbanken mit unbegrenztem Dokumentspeicher
Eine einzelne DocumentDB-Datenbank kann eine nahezu unbegrenzte Menge Dokumentspeicher enthalten, der von Auflistungen partitioniert wird. Auflistungen bilden die Transaktionsdomänen für die Dokumente, die darin enthalten sind. Eine DocumentDB-Datenbank ist standardmäßig elastisch und reicht von wenigen GB bis hin zu Terabyte von SSD-gestütztem Dokumentspeicher und bereitgestelltem Durchsatz. Im Gegensatz zu einer herkömmlichen RDBMS-Datenbank ist eine Datenbank in DocumentDB nicht auf einen einzigen Computer begrenzt.   

Mit DocumentDB können Sie weitere Sammlungen oder Datenbanken bzw. beides erstellen, wenn die Größenanforderungen Ihrer Anwendung zunehmen. Tatsächlich wird DocumentDB von vielen Erstanbieteranwendungen innerhalb von Microsoft in großem Maße genutzt, indem sehr große DocumentDB-Datenbanken erstellt werden, die jeweils Tausende von Auflistungen mit mehreren Terabytes Dokumentspeicher enthalten. Sie können eine Datenbank durch Hinzufügen oder Entfernen von Sammlungen vergrößern oder verkleinern, um den Größenanforderungen Ihrer Anwendung zu entsprechen. Sie können in Abhängigkeit von der Angebotsverfügbarkeit und der erworbenenen Menge an Kapazitätseinheiten eine beliebige Anzahl von Auflistungen innerhalb einer Datenbank erstellen. Der SSD-gestützte Speicher und für Sie bereitgestellte Durchsatz kann über die Auflistungen unter den Datenbanken in Ihrem Datenbankkonto aufgeteilt werden. 

## <a name="DBCollections"></a>Datenbanksammlungen
Jede DocumentDB-Datenbank kann eine oder mehrere Auflistungen enthalten. Eine Auflistung liefert den Umfang für Dokumentspeicher und Abfrageausführung. Eine Auflistung ist auch eine Transaktionsdomäne für ale Dokumente, die darin enthalten sind. Sie können eine beliebige Anzahl von Auflistungen erstellen, um den Skalierungsanforderungen Ihrer Anwendungen gerecht zu werden. Um Auflistungen zu erstellen, müssen Sie zuerst eine oder mehrere Kapazitätseinheiten (CU) kaufen. Jede Kapazitätseinheit umfasst ein Kontingent von Auflistungen. Wenn Sie das Auflistungskontingent für Ihr Konto erreichen, können Sie weitere Kapazitätseinheiten erwerben.  

>[AZURE.NOTE] Jede Sammlung unterstützt Speicher für bis zu 10 GB Dokumentdaten. 

## <a name="ProvStorage"></a>Bereitgestellter Speicher und Durchsatz als Kapazitätseinheiten
Sie können kombinierbare Einheiten von SSD-gestütztem Dokumentspeicher und Durchsatz als Kapazitätseinheiten (CU) bereitstellen. Sie können DocumentDB mit vorhersagbarer Leistung elastisch skalieren, indem Sie weitere Kapazitätseinheiten erwerben, um die Anforderungen Ihrer Anwendung im Hinblick auf Skalierung, Speicher und Durchsatz zu erfüllen.  
 
Jede CU ist mit 3 elastischen Auflistungen, 10 GB SSD-gestütztem bereitgestelltem Dokumentspeicher und 2000 Anforderungseinheiten (RU) bereitgestelltem Durchsatz ausgestattet. Die bereitgestellte Speicher- und Durchsatzkapazität einer CU wird über die von Ihnen erstellten DocumentDB-Auflistungen verteilt.   

## <a name="ProvThroughput"></a>Anforderungseinheiten und Datenbankvorgänge
DocumentDB ermöglicht vielfältige Datenbankvorgänge, einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger, die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf CPU, E/A und Speicher, die bzw. der für den jeweiligen Vorgang erforderlich ist.  Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.   

Anforderungseinheiten werden für jedes Datenbankkonto basierend auf der Anzahl erworbener Kapazitätseinheiten bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitsrate für ihr Konto überschreiten, werden gedrosselt, bis die Rate unter das reservierte Niveau für das Konto fällt. Wenn die Anwendung einen höheren Durchsatz benötigt, können Sie zusätzliche Kapazitätseinheiten erwerben.  

Die folgende Tabelle enthält eine Liste der verschiedenen Datenbankvorgänge und die pro CU verfügbaren Anforderungseinheiten. Mithilfe der Tabelle können Sie planen, wie ein bestimmter Datenbankvorgang den bereitgestellten Durchsatz verbraucht, der mit einer CU verknüpft ist.  Wir setzen voraus, dass die Dokumentgröße 1 KB ist, bestehend aus 10 eindeutigen Eigenschaftswerten. Die Standardkonsistenzstufe ist auf "Sitzung" festgelegt und alle Dokumente werden automatisch von DocumentDB indiziert. 

|Datenbankvorgänge|Anzahl der Vorgänge pro Sekunde pro CU|
|-------------------|--------------------------------------|
|Lesen eines einzelnen Dokuments	|2000
|Einfügen/Ersetzen/Löschen eines einzigen Dokuments	|500
|Abfragen einer Sammlung mit einem einfachen Prädikat und Zurückgeben eines einzelnen Dokuments	|1.000
|Gespeicherte Prozedur mit 50 Dokumenteinfügungen	|20

Sie können der Tabelle entnehmen, dass die von Einfüge-/Ersetzungs-/Löschvorgängen verbrauchten Anforderungseinheiten für automatisch indizierte Dokumente etwa die vierfache Menge des Lesens eines Dokuments beträgt. Die Tabelle oben dient nur Referenzzwecken. In der Praxis sieht es jedoch folgendermaßen aus:  

-	Die Größen Ihrer Dokumente können variieren (und entsprechen nicht 1 KB). 
-	Ihre Dokumente verfügen möglicherweise über mehr als 10 Eigenschaften.
-	Sie können die standardmäßige Konsistenzebene auf "Strong", "Bounded Staleness" oder "Eventual" festlegen.
-	Sie können festlegen, dass einige Dokumente nicht indiziert werden. 
-	Sie können festlegen, dass nur einige Eigenschaften indiziert werden, und auswählen, dass nicht alles von DocumentDB automatisch indiziert wird.
-	Ihre Abfragen und gespeicherten Prozeduren können weitaus komplexer sein.  

 Sie können die Anforderungseinheiten für eine bestimmte Anforderung erhalten, indem Sie den x-ms-request-charge-Antwortheader der Anforderung überprüfen.  

## <a name="Collections"></a>Sammlungen und bereitgestellter Durchsatz
Der bereitgestellte Durchsatz für Ihr Datenbankkonto wird einheitlich über alle Auflistungen hinweg bis zur maximalen Durchsatzebene (Anforderungseinheiten) für eine einzelne Auflistung zugeteilt. Wenn Sie beispielsweise eine einzige Kapazitätseinheit erwerben und eine einzige Auflistung erstellen, steht der gesamte bereitgestellte Durchsatz der CU für die Auflistung zur Verfügung. Wenn eine weitere Auflistung erstellt wird, wird der bereitgestellte Durchsatz gleichmäßig über die beiden Auflistungen verteilt, wobei jede die Hälfte des gesamten bereitgestellten Durchsatzes erhält.  

## <a name="Consistency"></a>Auswahl der Konsistenzebene und Durchsatz
Die Auswahl der standardmäßigen Konsistenzebene hat Auswirkungen auf den Durchsatz und die Latenz.  Sie können die standardmäßige Konsistenzebene sowohl programmgesteuert als auch über das Azure-Portal festlegen. Sie können die Konsistenzebene auch pro Anforderung überschreiben. Standardmäßig ist die Konsistenzebene auf "Session" festgelegt, wodurch monotone Lese-/Schreibvorgänge und Read-Your-Write-Garantie ermöglicht wird. Die Sitzungskonsistenz ist ideal für benutzerzentrierte Anwendungen geeignet und liefert einen optimalen Kompromiss zwischen Konsistenz und Leistung.   

-	Die Konsistenzebenen "Session" und "Eventual" liefern ca. 2.000 Leseanforderungen für Dokumente und 500 Einfüge-/Ersetzungs-/Löschanforderungen.
-	Die Konsistenzebenen "Strong" und "Bounded staleness" liefern ca. 1.200 Leseanforderungen für Dokumente und etwa 500 Einfüge-/Ersetzungs-/Löschanforderungen. Die Einfüge-/Ersetzungs-/Löschanforderungen bei "Bounded Staleness" weisen eine wesentlich niedrigere Latenz als bei "Strong" auf.  

Unter der Vorgehensweise: Verwalten der DocumentDB-Konsistenzeinstellungen in [Verwalten eines DocumentDB-Kontos](../documentdb-manage-account/)finden Sie Anweisungen zum Ändern der Konsistenzebene im Azure-Vorschauverwaltungsportal.

## <a name="IndexOverhead"></a>Bereitgestellter Dokumentspeicher und Indexoverhead
Mit jeder gekauften CU werden Ihrem Konto 10 GB SSD-gestützter Dokumentspeicher bereitgestellt. Die 10 GB Dokumentspeicher umfassen die Dokumente plus den Speicher für den Index. Standardmäßig wird eine DocumentDB-Auflistung so konfiguriert, dass alle Dokumente automatisch indiziert werden, ohne dass explizit irgendwelche sekundären Indizes oder Schemas erforderlich wären. Der typische Index-Overhead bei in hohem Maße genutzten Erstanbieteranwendungen, die DocumentDB verwenden, liegt zwischen 2 und 20 %. Durch die von DocumentDB verwendete Indizierungstechnologie wird sichergestellt, dass der Index-Overhead, unabhängig von den Werten der Eigenschaften, bei den Standardeinstellungen 80 % der Größe der Dokumente nicht überschreitet.  

Standardmäßig werden alle Dokumente von DocumentDB automatisch indiziert. Wenn Sie den Index-Overhead jedoch optimieren möchten, können Sie beim Einfügen oder Ersetzen eines Dokuments bestimmte Dokumente aus der Indizierung ausschließen. Sie können eine DocumentDB-Auflistung so konfigurieren, dass alle Dokumente innerhalb der Auflistung von der Indizierung ausgeschlossen werden. Sie können eine DocumentDB-Auflistung auch so konfigurieren, dass nur bestimmte Eigenschaften oder Pfade mit Platzhaltern in Ihren JSON-Dokumenten selektiv indiziert werden.  Durch das Ausschließen von Eigenschaften oder Dokumenten wird auch der Schreibdurchsatz verbessert, was bedeutet, dass Sie weniger Anforderungseinheiten verbrauchen.   
 
## <a name="NextSteps"></a>Nächste Schritte
Unter der Vorgehensweise: Verwalten der DocumentDB-Konsistenzeinstellungen in [Verwalten eines DocumentDB-Kontos](../documentdb-manage-account/)finden Sie Anleitungen zur Verwaltung Ihres DocumentDB-Kontos im Azure-Vorschauportal.

Unter [Überwachen eines DocumentDB-Kontos](../documentdb-monitor-accounts/)finden Sie Anleitungen zum Überwachen der Systemleistungsebenen im Azure-Vorschauportal.

<!--HONumber=47-->
