
<properties
   pageTitle="Muster für die Zusammensetzung zustandsbehafteter Dienste | Microsoft Azure"
   description="Entwurfsmuster in Service Fabric Reliable Actors, bei dem zustandsbehaftete Actors zum Verwalten des Status zwischen Dienstaufrufen sowie zum Zwischenspeichern bisheriger Dienstergebnisse verwendet werden."
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

Entwickler haben die letzten eineinhalb Jahrzehnte damit verbracht, zustandslose N-Tier-Dienste im Unternehmen zu erstellen. Sie haben Dienste erstellt, die auf Datenbanken basieren. Sie haben Dienste höherer Ordnung auf anderen Diensten erstellt. Schließlich haben sie Orchestrierungsmodule und nachrichtenbasierte Middleware geschaffen, um diese Dienste zu koordinieren. Als die Benutzerworkloads weiterentwickelt wurden, um den Bedarf an mehr Interaktivität oder besserer Skalierung zu decken, wurden die Schwachstellen der auf zustandslosen Diensten basierenden Architektur (Service-Oriented Architecture, SOA) deutlich.

## Die alte Methode: SOA-Dienste

SOA-Dienste konnten zwar nahtlos horizontal skaliert werden, führten aufgrund ihrer Zustandslosigkeit aber zu Engpässen bei Parallelität und Durchsatz. Hierdurch wurde der Zugriff auf den Speicher immer aufwändiger. Die meisten Entwickler setzten als bewährte Methode für ihre Lösung auf die Einführung des Caching, um die Auslastung des Speichers zu reduzieren. Diese Lösung war aber auch mit Nachteilen verbunden. Es war erforderlich, eine weitere Ebene zu verwalten, gleichzeitigen Zugriff auf den Cache zu ermöglichen und semantische Einschränkungen und Änderungen sowie Konsistenz sicherzustellen. Wie unter [Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md) beschrieben, stellt das Modell mit virtuellen Actors die perfekte Lösung dieser Probleme dar. Einige Entwickler versuchen, ihre SOA-Probleme zu lösen, indem sie die Speicherebene replizieren. Dieser Ansatz ermöglicht aber keine gute Skalierung, und die Grenzen für das Common Alerting Protocol sind schnell erreicht.

Das zweite Problem sind die sich ändernden Anforderungen. Sowohl Benutzer als auch Unternehmen verlangen interaktive Dienste, die standardmäßig innerhalb von Millisekunden auf Anforderungen reagieren können und nicht mehrere Sekunden benötigen. Um diese Anforderung zu erfüllen, begannen Entwickler damit, basierend auf anderen Diensten Fassadendienste zu erstellen. In einigen Fällen wurden Dutzende von Fassadendiensten erstellt, um benutzerorientierte Dienste zu schaffen. Durch das Hinzufügen mehrerer nachgelagerter Dienste kommt es aber schnell zu Latenzproblemen.

Entwickler haben es auch mit Caches und speicherinternen Objektspeichern versucht. In einigen Fällen wurden hierfür unterschiedliche Implementierungen verwendet, um die Leistungsanforderungen zu erfüllen. Bei dieser Vorgehensweise setzen Entwickler normalerweise auf Back-End-Workerprozesse für die regelmäßige Erstellung des Caches. So wird der hohe Aufwand für die bedarfsgesteuerte Cacheauffüllung verringert. Anschließend werden die Workloads aufgelöst, um die asynchronen Vorgänge von den synchronen Vorgängen zu isolieren. So wird mehr Platz für interaktive Vorgänge geschaffen, mit denen auf Zustandsänderungen reagiert wird. Dies ist besonders bei SOA schwierig.

Häufig werden auch weitere Ebenen eingeführt, z. B. Warteschlangen und Worker. Diese können sogar dazu führen, dass die Lösungen noch komplexer werden.

Im Grunde genommen haben Entwickler nach Lösungen gesucht, um „zustandsbehaftete Dienste“ zu erstellen, bei denen „Zustand“ und „Dienstverhalten“ zusammengefasst werden, um benutzerorientierte und interaktive Erfahrungen zu ermöglichen. An diesem Punkt kommt das Azure Service Fabric Reliable Actors-Modell als Dienstkompositionsebene ins Spiel, und nicht als Ersatz für diese Dienste.

Das folgende Diagramm verdeutlicht dies:

![Reliable Actors, Komposition von Diensten und Beibehaltung des Zustands][1]

## Implementieren besserer Lösungen mit Actors

Bei der Komposition von Diensten können Actors entweder zustandslos oder zustandsbehaftet sein.

* Zustandslose Actors können als Proxys für die zugrunde liegenden Dienste verwendet werden. Diese Actors können über den Service Fabric-Cluster hinweg dynamisch skaliert werden und können bestimmte Informationen für den Dienst zwischenspeichern. Dies kann auch der zugehörige Endpunkt sein, nachdem er erkannt wurde.
* Zustandsbehaftete Actors können den Zustand zwischen Dienstaufrufen beibehalten und bisherige Dienstergebnisse zwischenspeichern. Der Status kann persistent oder transient sein.

Dieses Muster ist für viele Szenarien anwendbar. In den meisten Fällen muss ein Actor einen externen Aufruf eines Vorgangs für einen bestimmten Dienst durchführen. Wir möchten dies anhand eines Beispiels aus einer modernen E-Commerce-Anwendung verdeutlichen. Anwendungen dieser Art werden basierend auf Diensten erstellt, die viele verschiedene Funktionen umfassen, z. B. Benutzerprofilverwaltung, Empfehlungen, Warenkorbverwaltung, Wunschlistenverwaltung und Einkauf.

Die meisten E-Commerce-Entwickler entscheiden sich für einen benutzerorientierten Ansatz ihrer Architektur, ähnlich wie bei der Entwicklung von Umgebungen für soziale Netzwerke. Der Grund ist, dass es auch bei E-Commerce-Umgebungen in erster Linie um Benutzer und Produkte geht. Entwicklerlösungen werden häufig umgesetzt, indem eine Fassade mit Diensten bereitgestellt wird, die aus Leistungsgründen normalerweise durch einen Cache unterstützt wird.

Vergleichen Sie dies mit einem auf Actors basierenden Ansatz. Mit einem Benutzer-Actor kann nicht nur das Verhalten des Benutzers dargestellt werden (z. B. Durchsuchen des Katalogs, „Liken“ eines Produkts, Hinzufügen eines Artikels zum Warenkorb oder Empfehlen eines Produkts an einen Freund). Es kann auch der Gesamtzustand des Benutzers dargestellt werden, z. B. sein Profil, Artikel im Warenkorb, Liste der von seinen Freunden empfohlenen Artikel, sein Einkaufsverlauf und der aktuelle geografische Standort.

## Auffüllen des Zustands mit zustandsbehafteten Actors

Betrachten wir zuerst ein Beispiel, in dem ein Benutzer-Actor seinen Zustand aus mehreren Diensten auffüllen muss. Hierfür wird kein Codebeispiel angegeben, da alles, was beim [Muster für intelligenten Cache](service-fabric-reliable-actors-pattern-smart-cache.md) erläutert wurde, auch hier gilt. Ein Benutzer-Actor kann bei der Anmeldung aktiviert und aus Back-End-Diensten mit ausreichend Daten aufgefüllt werden. Der Gesamtzustand und partielle Zustand kann auch bedarfsgesteuert, per Timer oder mit beiden Verfahren aufgefüllt werden, und dies kann im Actor zwischengespeichert werden. Dieses Beispiel mit Diensten für **Profil** und **Wunschliste** ist im Folgenden dargestellt:

![Profil- und Wunschlistendienste][2]

Entwickler können den Zustand für häufig wiederkehrende Benutzer vorab auffüllen und bei der Anmeldung bereitstellen. Außerdem können Entwickler den Zustand zum Zeitpunkt der Anmeldung für Benutzer auffüllen, die den Dienst einmal im Monat nutzen. Diese Muster sind aus dem Abschnitt über den intelligenten Cache bekannt.

Wenn sich Benutzer 23 (Abbildung oben) anmeldet, wird der Benutzer-Actor (23) aktiviert, falls er nicht bereits aktiviert ist. Der Benutzer-Actor ruft dann die relevanten Benutzerprofilinformationen und die Wunschliste aus Back-End-Diensten ab. Über den Benutzer-Actor werden meist auch die Informationen für nachfolgende Aufrufe zwischengespeichert. Wenn ein Element beispielsweise der Wunschliste hinzugefügt werden muss, kann dies wie oben beschrieben mit Write-Behind- oder Write-Through-Vorgängen erreicht werden.

Wir sehen uns als Nächstes ein Beispiel an, in dem ein Benutzer auf die Schaltfläche **Like** (Gefällt mir) klickt, um ein Produkt zu „liken“. Für diese Aktion können mehrere Aufrufe mehrerer Dienste erforderlich sein. Beispiele für Aktionen sind das Senden eines „Like“ an den Katalogdienst, das Auslösen der nächsten Gruppe von Empfehlungen und das Posten eines Updates in einem sozialen Netzwerk.

Dies ist im Folgenden dargestellt:

![Liken eines Produkts und Wunschlisten-, Profil- und Katalogdienste][3]

## Verwenden von Actors bei der Komposition und asynchronen Kommunikation
Das Service Fabric Reliable Actors-Programmiermodell kommt voll zur Geltung, wenn ein Entwickler Anforderung/Antwort-Vorgänge mit asynchronen Vorgängen kombinieren möchte. Durch das „Liken“ eines Produkts wird der Artikel sofort der Wunschliste des Benutzers hinzugefügt. Das Posten in sozialen Netzwerken und das Auslösen der nächsten Gruppe von Empfehlungen kann dann asynchron mit Puffern und Timern durchgeführt werden.

Ein weiterer Vorteil der Verwendung eines Benutzer-Actors für Dienste besteht darin, dass der Actor einen natürlichen Speicherort für den zwischengespeicherten Zustand darstellt. Und was noch wichtiger ist: Der Actor reagiert auch asynchron auf Änderungen des Zustands. Dies ist ein besonders schwieriges Szenario mit zustandslosen Diensten. Beispielsweise kann ein Benutzer im Rahmen eines Benutzerablaufs eine Reihe von Aktionen ausführen, und diese Ereignisse können in Echtzeit in einem Actor erfasst werden. Anschließend kann ein Datenstrom zusammengestellt werden, der zur Ereigniszeit oder asynchron auf einem Timer abgefragt werden kann, um das Verhalten des Actors zu ändern.

An diesem Punkt werden SOA-Puristen zweifellos bemerkt haben, dass es sich dabei nicht um Dienste im Sinne von Actors als Endpunkte handelt, die über ein sprachenunabhängiges Protokoll verfügbar gemacht werden. Das Service Fabric Reliable Actors-Modell ist weder eine dialogfähige Komponente noch eine Plattform für Dienstinteroperabilität. Aber nichts hindert Entwickler an der Verwendung eines Denkansatzes, bei dem die Granularität von SOA-Diensten im Vordergrund steht, wenn sie Actor-Modelle erstellen oder entsprechend mit der Trennung von Zuständigkeiten arbeiten. Diese Dienste werden als Microservices bezeichnet. Außerdem steht es Entwicklern frei, Service Fabric Reliable Actors einen REST- oder SOAP-Endpunkt als Interoperabilitätsschicht voranzustellen.

Die Komposition zustandsbehafteter Dienste gilt auch für Workflows und nicht nur für Transaktionsszenarien, z. B. E-Commerce. Service Fabric ist wie ein Workflow-/Orchestrierungsmodul aufgebaut. Es kann zum Modellieren von Workflows verwendet werden, die Dienstinteraktionen umfassen und den Zustand dieser Interaktionen beibehalten.

Nachteile von „zustandslosen Diensten“ werden bei der Erstellung von skalierbaren Diensten deutlich, mit denen dynamische Benutzeroberflächen bereitgestellt werden. Indem Zustand und Verhalten zusammengeführt werden, hilft das Service Fabric Reliable Actors-Programmiermodell Entwicklern beim Erstellen skalierbarer und interaktiver Benutzeroberflächen, die zusätzlich zu den bestehenden Investitionen eingesetzt werden.


## Nächste Schritte

[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Einige Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=AcomDC_0121_2016-->