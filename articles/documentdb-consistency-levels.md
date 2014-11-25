<properties title="Consistency levels in DocumentDB" pageTitle="Consistency levels in DocumentDB" description="DocumentDB has four consistency levels with associated performance levels to help application developers make predictable consistency-availability-latency trade-offs." metaKeywords="Optional" services="documentdb" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="Optional" scriptId="Optional" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Konsistenzebenen in DocumentDB

Entwickler werden oft mit der Herausforderung konfrontiert, zwischen zwei Extremen, der strikten und der letztendlichen Konsistenz (Eventual Consistency), zu wählen. In der Realität liegen mehrere Konsistenzebenen zwischen diesen zwei Extremen. In den meisten realen Szenarien profitieren Anwendungen von den feinen Kompromissen zwischen Konsistenz, Verfügbarkeit und Latenz. DocumentDB bietet vier klar abgegrenzte Konsistenzebenen mit den dazugehörigen Leistungsstufen. Damit können Anwendungsentwickler vorhersagbare Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz schließen.

Alle Systemressourcen wie Datenbankkonten, Datenbanken, Sammlungen, Benutzer und Berechtigungen sind für Lesevorgänge und Abfragen strikt konsistent. Die Konsistenzebenen werden nur auf benutzerdefinierte Ressourcen angewendet. Für Abfragen und Lesevorgänge für benutzerdefinierte Ressourcen wie Dokumente, Anhänge, gespeicherte Prozeduren, Trigger und UDFs bietet DocumentDB vier eigene Konsistenzebenen: Strong, Bounded-Staleness, Session und Eventual. Mit diesen granularen, gut abgegrenzten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Leistung geschlossen werden. Diese Konsistenzebenen stützen sich auf vorhersagbare Leistungsstufen und stellen konsistente Ergebnisse für Ihre Anwendung sicher.

## Konsistenzebenen

Sie können die Standardkonsistenzebene für Ihr Datenbankkonto konfigurieren, die dann auf alle Sammlungen (in allen Datenbanken) in diesem Datenbankkonto angewendet wird. Standardmäßig wird für alle Lesevorgänge und Abfragen für benutzerdefinierte Ressourcen die Standardkonsistenzebene, die im Datenbankkonto festgelegt ist, verwendet. Die Konsistenzebene bestimmter Lesevorgänge/Abfragen kann jedoch durch Angabe des [x-ms-consistency-level]-Anforderungsheaders herabgesetzt werden. Es werden vier Konsistenzebenen von dem DocumentDB-Replikationsprotkoll unterstützt. Sie werden weiter unten genauer beschrieben.

> [AZURE.NOTE] In zukünftigen Versionen soll das Überschreiben der Standardkonsistenzebene basierend auf einer Sammlung unterstützt werden.

**Strong**: Mit der Konsistenzebene "Strong" wird gewährleistet, dass ein Schreibvorgang erst sichtbar ist, nachdem er dauerhaft vom Mehrheitsquorum der Replikate bestätigt wurde. Ein Schreibvorgang wird entweder synchron dauerhaft sowohl von den primären und dem Quorum der sekundären Replikate bestätigt oder abgebrochen. Ein Lesevorgang wird immer von dem Mehrheitslesequorum bestätigt. Ein Client kann niemals einen unbestätigten oder unvollständigen Schreibvorgang sehen, wodurch gewährleistet wird, dass er immer auf die neuesten bestätigten Schreibvorgänge zugreift.

Mit der Konsistenzebene "Strong" wird eine absolute Datenkonsistenz gewährleistet, jedoch die niedrigste Stufe an Lese- und Schreibleistung geboten.

**Bounded Staleness**: Mit der Konsistenzebene "Bounded Staleness" wird eine Gesamtreihenfolge bei der Weitergabe von Schreibvorgängen gewährleistet, die Lesevorgänge sind jedoch unter Umständen gegenüber den Schreibvorgängen, bestenfalls durch K-Präfixe, verzögert. Der Lesevorgang wird immer vom Mehrheitsquorum der Replikate bestätigt. Die Antwort auf diese Leseanforderung gibt ihre relative Aktualität (in Form von K) an.

"Bounded Staleness" bietet besser vorhersagbares Verhalten für die Lesekonsistenz mit der niedrigsten Latenz bei Schreibvorgängen. Da Lesevorgänge von einem Mehrheitsquorum bestätigt werden, ist die Leselatenz nicht die niedrigste, die vom System geboten wird.

**Session**: Anders als die globalen Konsistenzmodellen, die von den Konsistenzebenen "Strong" und "Bounded Staleness" geboten werden, ist die Konsistenzebene "Session" auf eine bestimmte Clientsitzung zugeschnitten. Die Konsistenzebene "Session" ist meistens ausreichend, da sie monotone Lese- und Schreibvorgänge gewährleistet, sowie das Lesen der eigenen Schreibvorgänge ermöglicht. Eine Leseanforderung für die Konsistenzebene "Session" wird bei einem Replikat verwendet, das als die vom Client geforderte Version (Bestandteil des Sitzungscookies) dienen kann.

"Session" bietet vorhersagbare Lesedatenkonsistenz für eine Sitzung mit der niedrigsten Latenz bei Schreibvorgängen. Die Latenz bei Lesevorgängen ist ebenfalls niedrig, da der Schreibvorgang bis auf wenige Ausnahmen von einem einzelnen Replikat verarbeitet wird.

**Eventual**: Die Konsistenzebene "Eventual" stellt die schwächste Form von Konsistenz dar, bei der ein Client über einen längeren Zeitraum ältere Werte als die ihm zuvor angezeigten abrufen kann. Wenn keine weiteren Schreibvorgänge vorhanden sind, kommt es bei den Replikaten innerhalb einer Gruppe letztendlich zur Konvergenz. Die Leseanforderung wird von jedem sekundären Replikat verarbeitet.

"Eventual" bietet die schwächste Lesekonsistenz, jedoch die niedrigste Latenz für Lese- und Schreibvorgänge.

## Abfragekonsistenz

Bei benutzerdefinierten Ressourcen entspricht die Konsistenzebene der Abfragen standardmäßig der der Lesevorgänge. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments der Sammlung standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Dokumentlesevorgängen berücksichtigen. Obwohl DocumentDB für Schreibvorgänge optimiert ist und beständige Mengen an Dokumentschreibvorgängen zusammen mit der synchronen Indexwartung und der Bereitstellung konsistenter Abfragen unterstützt, können Sie bestimmte Sammlungen so konfigurieren, dass ihr Index flexibel aktualisiert wird. Die verzögerte Indizierung steigert die Schreibleistung und ist ideal für Sammelerfassungsszenarien mit einer starken Lesearbeitsauslastung geeignet.

| Indizierungsmodus     | Lesevorgänge                                                         | Abfragen                                                             |
|-----------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| Konsistent (Standard) | Wählen Sie "Strong", "Bounded Staleness", "Session" oder "Eventual". | Wählen Sie "Strong", "Bounded Staleness", "Session" oder "Eventual". |
| Verzögert             | Wählen Sie "Strong", "Bounded Staleness", "Session" oder "Eventual". | Eventual                                                             |

Wie bei Leseanforderungen kann die Konsistenzebene einer bestimmten Abfrageanforderung durch Angabe des [x-ms-consistency-level]-Anforderungsheaders herabgesetzt werden.

## Referenzen

-   Doug Terry. Replicated Data Consistency explained through baseball.
    [][]<http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf></a>
-   Doug Terry. Session Guarantees for Weakly Consistent Replicated Data.
    [][1]<http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html></a>
-   Daniel Abadi. Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story.
    [][2]<http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html></a>
-   Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums.
    [][3]<http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf></a>
-   Werner Vogels. Eventual Consistent - Revisited.
    [][4]<http://allthingsdistributed.com/2008/12/eventually_consistent.html></a>

  []: http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf
  [1]: http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html
  [2]: http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html
  [3]: http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf
  [4]: http://allthingsdistributed.com/2008/12/eventually_consistent.html
