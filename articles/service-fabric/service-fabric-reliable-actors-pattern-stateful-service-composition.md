
<properties
   pageTitle="Reliable Actors: Entwurfsmuster für die Komposition zustandsbehafteter Dienste"
   description="Entwurfsmuster in Service Fabric Reliable Actors, das statusbehaftete Actors zum Verwalten des Status zwischen Dienstaufrufen sowie zum Zwischenspeichern bisheriger Dienstergebnisse verwendet. Der Status kann persistent oder transient sein."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Reliable Actors-Entwurfsmuster: Komposition zustandsbehafteter Dienste
Entwickler haben die letzten eineinhalb Jahrzehnte damit verbracht, zustandsfreie N-Tier-Dienste im Unternehmen erstellen. Sie haben Dienste auf Datenbanken erstellt, sie haben höherwertige Dienste über anderen Diensten erstellt, und sie haben Orchestrierungs-Module und nachrichtenorientierte Middleware erstellt, um diese Dienste zu koordinieren. Mit der steigenden Arbeitsauslastung von Benutzern, gleichgültig ob ein höheres Maß an Interaktivität oder Skalierung gefordert wurde, begann die statusfreie serviceorientierte Architektur, ihre Schwachstellen zu zeigen.

## Die alte Methode: SOA-Dienste
SOA-Dienste konnten zwar aufgrund ihrer Zustandsfreiheit nahtlos in horizontaler Richtung skaliert werden, erstellten jedoch einen Engpass auf Speicherebene – Parallelität und Durchsatz. Der Zugriff auf den Speicher wurde zunehmend kostspieliger. Als gängige Praktik führten die meisten Entwickler Zwischenspeicherung in ihre Lösungen ein, um die Anforderungen an den Speicher zu reduzieren. Dieses Konzept war jedoch nicht ohne Nachteile – Verwaltung einer weiteren Ebene, Parallelzugriff auf den Cache, semantische Einschränkungen und Änderungen und schließlich auch Konsistenz. Wie weiter oben im Muster für den intelligenten Cache erläutert, bietet das virtuelle Actor-Modell eine ideale Lösung dafür.

Einige Entwickler haben versucht, das Problem durch die Replikation ihrer Speicherebene zu lösen. Dieser Ansatz bot jedoch keine guten Skalierungsmöglichkeiten und erreichte schnell CAP-Grenzen. Das zweite Problem entstand im Zusammenhang mit sich ändernden Anforderungen. Sowohl Endbenutzer als auch Unternehmen verlangen interaktive Dienste – die auf Anforderungen in Millisekunden anstatt in Sekunden als Norm reagieren. Um dem zu entsprechen, begannen Entwickler Fassadendienste über anderen Diensten zu entwickeln, in einigen Fällen zehn und mehr Dienste, um benutzerzentrische Dienste zu erstellen. Bei der Komposition mehrerer Downstream-Dienste zeigten sich jedoch schnell Latenzprobleme.

Und erneut kamen Entwickler auf Caches und Speicher mit In-Memory-Objekten zurück, in einigen Fällen unterschiedliche Implementierungen, um die Leistungsanforderungen zu erfüllen. Sie begannen, Backend-Worker-Prozesse zu erstellen, um den Cache in regelmäßigen Abständen zu erstellen und dadurch das kostspielige Auffüllen des Caches bei Bedarf zu minimieren. Schließlich begannen sie, Workloads zu zerlegen, um asynchrone Vorgänge von synchronen zu trennen und mehr Platz für interaktive Vorgänge zu gewinnen, um auf Änderungen im Status zu reagieren, die in SOA besonders schwierig sind.

Sie führten weitere Ebenen ein, wie z. B. Warteschlangen und Worker, und erhöhten dadurch die Komplexität ihrer Lösungen. Im Grunde genommen begannen Entwickler nach Lösungen zu suchen, um "statusbehaftete Dienste" zu erstellen, oder anders ausgedrückt, "Status" und "Dienstverhalten" zusammenzufassen, um benutzerzentrische und interaktive Erfahrungen zu ermöglichen. Und hier kommt Azure Service Fabric Actors als Dienstkompositionsebene ins Spiel, und nicht als Ersatz für diese Dienste.

Das folgende Diagramm veranschaulicht diesen Punkt:

![][1]

## Bessere Lösung durch Actors
Bei der Komposition von Diensten können Actors entweder statusfrei oder statusbehaftet sein.

* Statusfreie Actors können als Proxys für die zugrunde liegenden Dienste verwendet werden. Diese Actors können über den Azure Service Fabric-Cluster hinweg dynamisch skaliert werden und können bestimmte Informationen im Zusammenhang mit dem Dienst zwischenspeichern, wie z. B. dessen Endpunkt, sobald er erkannt wird.
* Ein statusbehafteter Actor kann den Status zwischen Dienstaufrufen beibehalten sowie bisherige Dienstergebnisse zwischenspeichern. Der Status kann persistent oder transient sein.

Dieses Muster ist auch für viele Szenarien anwendbar. In den meisten Fällen müssen Actors externe Aufrufe durchführen, um einen Vorgang für einen bestimmten Dienst aufzurufen. Dies soll anhand eines Beispiels mit modernen e-Commerce-Anwendungen veranschaulicht werden. Diese Anwendungen basieren auf Diensten, die verschiedene Funktionen bereitstellen, wie z. B. Benutzerprofilverwaltung, Empfehlungen, Warenkorbverwaltung, Wunschlistenverwaltung, Einkauf und vieles mehr.

Die meisten Entwickler entscheiden sich für einen benutzerzentrischen Ansatz ihrer Architektur, ähnlich wie bei der Entwicklung von sozialen Erfahrungen, da es bei e-Commerce-Erfahrungen in erster Linie um Benutzer und Produkte geht. In der Regel wird dies durch das Versenden einer Fassade von Diensten erreicht, die höchstwahrscheinlich aus Leistungsgründen durch einen Cache unterstützt wird.

Sprechen wir nun über einen Actor-basierten Ansatz. Ein Benutzer-Actor kann sowohl das Verhalten des Benutzers darstellen (Durchsuchen des Katalogs, Zustimmung zu einem Produkt, Hinzufügen eines Artikels zum Warenkorb, Empfehlen eines Produkts an einen Freund) als auch dessen zusammengesetzten Status – sein Profil, Artikel im Warenkorb, Liste der von seinen Freunden empfohlenen Artikel, seinen Einkaufsverlauf, aktuellen geografischen Standort usw.

## Verwenden von statusbehafteten Actors
Betrachten wir zuerst ein Beispiel, in dem der Benutzer-Actor seinen Status aus mehreren Diensten auffüllen muss. Hierfür wird kein Codebeispiel angegeben, da alles, was beim Muster für intelligenten Cache erläutert wurde, auch hier gilt. Der Benutzer-Actor kann zum Zeitpunkt der Anmeldung aktiviert und mit ausreichend Daten aus Back-End-Diensten ausgefüllt werden. Gesamtstatus und partieller Status können natürlich, wie in vielen Fällen weiter oben in diesem Artikel ersichtlich war, bei Bedarf, auf einen Timer oder mit beidem zusammen, aufgefüllt und im Actor zwischengespeichert werden. Dieses Beispiel mit Profil und Wunschliste ist im Folgenden dargestellt:

![][2]

Beispielsweise können wir den Status häufiger Benutzer vorab ausfüllen und bei der Anmeldung fertigstellen, oder ihn für Benutzer, die den Dienst monatlich besuchen, bei der Anmeldung ausfüllen. Diese Muster sind aus dem Abschnitt über den intelligenten Cache bekannt.

Bei der Anmeldung von Benutzer 23 wird der Benutzer-Actor (23) aktiviert, sofern dies nicht bereits der Fall ist, und ruft die relevanten Benutzerprofilinformationen und Wunschliste aus Back-End-Diensten ab. Der Benutzer-Actor stellt speichert wahrscheinlich die Informationen für nachfolgende Aufrufe im Cache. Und falls wir beispielsweise der Wunschliste einen Artikel hinzufügen möchten, können wir dies über einen Write-behind- oder Write-through-Vorgang durchführen, wie bereits weiter oben erwähnt. Betrachten wir dann ein Beispiel, in dem der Benutzer auf die Schaltfläche "Gefällt mir" klickt und ihm ein Produkt gefällt. Diese Aktion kann mehrere Aufrufe an mehrere Dienste erfordern, wie nachfolgend dargestellt: Senden eines "Gefällt mir" an den Katalog-Dienst, Auslösen des nächsten Empfehlungssatzes und ggf. Posten eines Updates an ein soziales Netzwerk.

Dies ist im Folgenden dargestellt:

![][3]

## Wie Actors-Komposition und asynchrone Kommunikation hilfreich sein können
Tatsächlich glänzt Azure Service Fabric Actors bei der Erstellung von Vorgängen im Anforderungs-/Antwortformat zusammen mit asynchronen Vorgängen. Während z. B. durch "Produkt gefällt mir" der betreffende Artikel sofort der Wunschliste des Benutzers hinzugefügt wird, kann es sich beim Posten an soziale Netzwerke und Auslösen des nächsten Empfehlungssatzes um asynchrone Vorgänge handeln, die Puffer und Timer verwenden.

Ein weiterer wichtiger Vorteil der Verwendung eines Benutzer-Actors besteht darin, dass ein Actor einen natürlichen Ort für den zwischengespeicherten Status bietet und vor allem auf Statusänderungen asynchron reagiert. Dies ist ein besonders schwieriges Szenario mit zustandslosen Diensten. Ein Benutzer führt beispielsweise eine Reihe von Aktionen aus, vielleicht im Rahmen einer "Benutzer-Reise". Diese Ereignisse können in Echtzeit im Actor erfasst werden, und wir können einen Datenstrom zusammenstellen, der zum Ereigniszeitpunkt oder asynchron auf einem Timer abgefragt werden kann, um das Verhalten des Actors zu ändern.

An diesem Punkt werden SOA-Puristen zweifellos bemerkt haben, dass es sich dabei nicht um Dienste im Sinne von Actors als Endpunkte handelt, die über ein sprachenunabhängiges Protokoll verfügbar gemacht werden. Azure Service Fabric Actors ist weder eine dialogfähige Komponente noch eine Plattform für Dienst-Interoperabilität. Dennoch kann uns nichts daran hindern, an die Granularität von SOA-Diensten zu denken, wenn wir Actors modellieren, oder auch beim Modellieren der Trennung von Belangen. Solche Dienste werden als "Mikrodienste" bezeichnet. Ebenso gibt es absolut nichts, was uns daran hindert, einen REST-Endpunkt oder einen SOAP-Endpunkt als Interop-Schicht vor Azure Service Fabric Actors zu platzieren.

Die Komposition statusbehafteter Dienste gilt auch für Workflows und nicht nur für transaktionale Szenarien, wie z. B. e-Commerce. Azure Service Fabric ist als Workflow-/Orchestrierungs-Modul konzipiert und kann daher verwendet werden, um Workflows im Zusammenhang mit Dienstaktivitäten zu modellieren und den Status dieser Interaktionen zu verwalten.

Nachteile von "zustandslosen Diensten" sehen wir bei der Erstellung von skalierbaren Diensten, um dynamische Erfahrungen zu ermöglichen. Azure Service Fabric Actors hilft Entwicklern, insbesondere durch das Zusammenführen von Status und Verhalten, skalierbare und interaktive Erfahrungen zusätzlich zu bestehenden Investitionen zu erstellen.


## Nächste Schritte
[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=Oct15_HO3-->