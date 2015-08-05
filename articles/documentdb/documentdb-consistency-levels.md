<properties 
	pageTitle="Konsistenzebenen in DocumentDB | Microsoft Azure" 
	description="DocumentDB verfügt über vier Konsistenzebenen mit zugehörigen Leistungsstufen, die Anwendungsentwickler dabei unterstützen, vorhersehbare Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz einzugehen." 
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
	ms.date="06/25/2015" 
	ms.author="mimig"/>

# Verwenden von Konsistenzebenen zum Maximieren der Verfügbarkeit und Leistung in DocumentDB

Entwickler werden oft mit der Herausforderung konfrontiert, zwischen zwei Extremen, der strikten und der letztendlichen Konsistenz (Eventual Consistency), zu wählen. In der Realität liegen mehrere Konsistenzebenen zwischen diesen zwei Extremen. In den meisten realen Szenarien profitieren Anwendungen von gut ausbalancierten Kompromissen zwischen Konsistenz, Verfügbarkeit und Latenz. DocumentDB bietet vier klar abgegrenzte Konsistenzebenen mit den dazugehörigen Leistungsstufen. Damit können Anwendungsentwickler vorhersagbare Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz schließen.
 
Alle Systemressourcen wie Datenbankkonten, Datenbanken, Sammlungen, Benutzer und Berechtigungen sind für Lesevorgänge und Abfragen strikt konsistent. Die Konsistenzebenen werden nur auf benutzerdefinierte Ressourcen angewendet. Für Abfragen und Lesevorgänge für benutzerdefinierte Ressourcen wie Dokumente, Anhänge, gespeicherte Prozeduren, Trigger und UDFs bietet DocumentDB vier eigene Konsistenzebenen:

 - Strong
 - Bounded staleness 
 - Session
 - Eventual 

Mit diesen genau definierten und abgegrenzten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Leistung geschlossen werden. Diese Konsistenzebenen stützen sich auf vorhersagbare Leistungsstufen und stellen konsistente Ergebnisse für Ihre Anwendung sicher.

## Konsistenzebenen für Datenbanken

Sie können eine Standardkonsistenzebene für Ihr Datenbankkonto konfigurieren, die für alle Sammlungen (in allen Datenbanken) in diesem Datenbankkonto gilt. Standardmäßig wird für alle Lesevorgänge und Abfragen für benutzerdefinierte Ressourcen die Standardkonsistenzebene verwendet, die für das Datenbankkonto festgelegt ist. Die Konsistenzebene einer bestimmten Lese-/Abfrageanforderung kann jedoch durch Angabe des [x-ms-consistency-level]-Anforderungsheaders herabgesetzt werden. Es werden vier Arten von Konsistenzebenen vom DocumentDB-Replikationsprotokoll unterstützt. Sie werden weiter unten genauer beschrieben.

>[AZURE.NOTE]In zukünftigen Versionen soll das Überschreiben der Standardkonsistenzebene basierend auf einer Sammlung unterstützt werden.

**Strong**: Mit der Konsistenzebene "Strong" wird gewährleistet, dass ein Schreibvorgang erst sichtbar ist, nachdem er dauerhaft vom Mehrheitsquorum der Replikate bestätigt wurde. Ein Schreibvorgang wird entweder synchron dauerhaft sowohl von den primären und dem Quorum der sekundären Replikate bestätigt oder abgebrochen. Ein Lesevorgang wird immer von dem Mehrheitslesequorum bestätigt. Ein Client kann niemals einen unbestätigten oder unvollständigen Schreibvorgang sehen, wodurch gewährleistet wird, dass er immer auf die neuesten bestätigten Schreibvorgänge zugreift.
 
Mit der Konsistenzebene "Strong" wird eine absolute Datenkonsistenz gewährleistet, jedoch die niedrigste Stufe an Lese- und Schreibleistung geboten.

**Bounded staleness**: Mit der Konsistenzebene "Bounded staleness" wird die Gesamtreihenfolge bei der Weitergabe von Schreibvorgängen gewährleistet, wobei die Lesevorgänge gegenüber den Schreibvorgängen um höchstens K-Präfixe verzögert sein können. Der Lesevorgang wird immer vom Mehrheitsquorum der Replikate bestätigt. Die Antwort auf diese Leseanforderung gibt ihre relative Aktualität (in Form von K) an.

"Bounded staleness" bietet besser vorhersagbares Verhalten für die Lesekonsistenz mit der niedrigsten Latenz bei Schreibvorgängen. Da Lesevorgänge von einem Mehrheitsquorum bestätigt werden, ist die Leselatenz nicht die niedrigste, die vom System geboten wird.

>[AZURE.NOTE]"Bounded staleness" garantiert gleichbleibende Lesevorgänge nur auf explizite Leseanforderungen. Die wiederholte Serverantwort für Schreibanforderungen bietet keine Garantien für "Bounded staleness".

**Session**: Die Konsistenzebene "Session" bietet kein globales Konsistenzmodell wie die Konsistenzebenen "Strong" und "Bounded Staleness", sondern ist auf eine bestimmte Clientsitzung zugeschnitten. Die Konsistenzebene "Session" ist meistens ausreichend, da sie monotone Lese- und Schreibvorgänge gewährleistet, sowie das Lesen der eigenen Schreibvorgänge ermöglicht. Eine Leseanforderung für die Konsistenzebene "Session" wird bei einem Replikat verwendet, das als die vom Client geforderte Version (Bestandteil des Sitzungscookies) dienen kann.

Die Konsistenzebene "Session" bietet vorhersagbare Lesedatenkonsistenz für eine Sitzung mit der niedrigsten Latenz bei Schreibvorgängen. Die Latenz bei Lesevorgängen ist ebenfalls niedrig, da der Schreibvorgang bis auf wenige Ausnahmen von einem einzelnen Replikat verarbeitet wird.

**Eventual**: Die Konsistenzebene "Eventual" stellt die schwächste Form von Konsistenz dar, bei der ein Client im Laufe der Zeit ältere Werte als die ihm zuvor angezeigten abrufen kann. Wenn keine weiteren Schreibvorgänge vorhanden sind, kommt es bei den Replikaten innerhalb einer Gruppe letztendlich zur Konvergenz. Die Leseanforderung wird von jedem sekundären Index verarbeitet.

Die Konsistenzebene "Eventual" bietet die schwächste Lesekonsistenz, jedoch die niedrigste Latenz für Lese- und Schreibvorgänge.

### Ändern der Konsistenzebene einer Datenbank

1.  Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Alle durchsuchen**.

2.  Klicken Sie auf dem Blatt **Alle durchsuchen** auf **DocumentDB-Konten**.

3. Wählen Sie auf dem Blatt **DocumentDB-Konten** das zu ändernde Datenbankkonto aus.

4. Klicken Sie auf dem Kontoblatt im Fokus **Konfiguration** auf die Kachel **Standardkonsistenz**.

5. Wählen Sie die neue Konsistenzebene aus, und klicken Sie auf **Speichern**.

	![Screenshot, der die Kachel" Standardkonsistenz", die Einstellungen für die Konsistenz und die Schaltfläche "Speichern" zeigt](./media/documentdb-consistency-levels/database-consistency-level.png)

## Konsistenzebenen für Abfragen

Bei benutzerdefinierten Ressourcen entspricht die Konsistenzebene der Abfragen standardmäßig der der Lesevorgänge. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments der Sammlung standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Dokumentlesevorgängen berücksichtigen. Obwohl DocumentDB für Schreibvorgänge optimiert ist und beständige Mengen an Dokumentschreibvorgängen zusammen mit der synchronen Indexwartung und der Bereitstellung konsistenter Abfragen unterstützt, können Sie bestimmte Sammlungen so konfigurieren, dass ihr Index flexibel aktualisiert wird. Die verzögerte Indizierung steigert die Schreibleistung noch weiter und ist ideal für Sammelerfassungsszenarien mit einer starken Lesearbeitsauslastung geeignet.

Indizierungsmodus|	Lesevorgänge|	Abfragen  
-------------|-------|---------
Konsistent (Standard)|	Wählen Sie "Strong", "Bounded Staleness", "Session" oder "Eventual".|	Wählen Sie "Strong", "Bounded Staleness", "Session" oder "Eventual".|
Verzögert|	Wählen Sie "Strong", "Bounded Staleness", "Session" oder "Eventual".|	Eventual  

Wie bei Leseanforderungen kann die Konsistenzebene einer bestimmten Abfrageanforderung durch Angabe des [x-ms-consistency-level]-Anforderungsheaders herabgesetzt werden.

## Nächste Schritte

Wenn Sie weitere Informationen zu Konsistenzebenen und deren Vor- und Nachteile möchten, empfehlen wir die folgenden Ressourcen:

-	Doug Terry. Replicated Data Consistency explained through baseball. [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Sitzungsgarantien für schwach konsistente replizierte Daten. [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme: CAP ist nur ein Teil der Wahrheit. [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums. [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Eventual Consistent - Revisited. [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
 

<!---HONumber=July15_HO4-->