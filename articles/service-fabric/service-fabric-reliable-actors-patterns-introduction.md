<properties
   pageTitle="Einführung in Muster und Antimuster von Azure Service Fabric Actors"
   description="Entwurfsmuster, die für Service Fabric Actors gut geeignet sind"
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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Einführung in Reliable Actors-Entwurfsmuster
Das Reliable Actors-Programmiermodell von Service Fabric ist eine auf dem Actor-Modell basierende Plattform zum Lösen von realen Problemen auf Cloud-Niveau. Azure Service Fabric ist eine Plattform zum Erstellen von hochzuverlässigen, skalierbaren Anwendungen sowohl in der Cloud als auch lokal, die einfach zu entwickeln und zu verwalten sind. Dieser Artikel soll praktische Informationen zu praktischen Probleme bieten. Nach der Lektüre der verschiedenen Muster sollten Sie ein Verständnis darüber gewonnen haben, wie Sie das Service Fabric Actor-Modell zum Erstellen von Lösungen auf Unternehmens- oder Cloud-Ebene verwenden können.

## Muster
Dieser Abschnitt enthält einen Satz von Mustern und zugehörige Szenarien, die wir bei unseren Projekten mit Kunden genutzt haben. Diese Muster stellen Klassen von Problemen dar, die für eine Vielzahl von Lösungen anwendbar sind, die unsere Kunden auf Microsoft Azure erstellen. Die Szenarien basieren zwar auf realen Fällen, jedoch haben wir die meisten der domänenspezifischen Aspekte entfernt, um die Muster für den Leser zu verdeutlichen. Wahrscheinlich werden Sie feststellen, dass ein Großteil des Beispielcodes einfach oder offensichtlich ist. Dieser Code wird aus Gründen der Vollständigkeit angegeben, und nicht, weil er besonders intelligent oder beeindruckend ist.

Die in diesem Dokument dargelegten Muster erheben nicht den Anspruch, vollständig oder kanonisch zu sein – einige Entwickler lösen möglicherweise ein Problem oder Muster auf andere Weise als von uns angegeben.

[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

### Erfahren Sie mehr über Actors, ein kurzer Rückblick
Das [Dokument](http://dl.acm.org/citation.cfm?id=1624804) von Hewitt et al., das der Ursprung des Actor-Modells ist, wurde 1973 veröffentlicht. Jedoch erst vor vergleichsweise kurzer Zeit hat das Actor-Modell als Möglichkeit für den Umgang mit Parallelität und Komplexität in verteilten Systemen mehr Aufmerksamkeit gewonnen. Das Actor-Modell unterstützt differenzierte Einzelobjekte – Actors – die voneinander isoliert sind. Sie kommunizieren über asynchrone Nachrichtenübergabe, die direkte Kommunikation zwischen Actors ermöglicht. Ein Actor wird mit Singlethread-Semantik ausgeführt. Zusammen mit der Kapselung des Actor-Status und der Isolierung von anderen Actors vereinfacht dies das Schreiben von hochgradig parallelen Systemen, indem Parallelitätsprobleme aus dem Code des Actors entfernt werden. Die Actor-Erstellung erfolgt dynamisch in den Pool der verfügbaren Hardwareressourcen.

[Erlang](http://www.erlang.org/) ist die beliebteste Implementierung des Actor-Modells. Entwickler haben begonnen, das Actor-Modell wiederzuentdecken. Dadurch ist ein neues Interesse an Erlang und der Erstellung von neuen Erlang-ähnlichen Lösungen entstanden: [Scala](http://www.scala-lang.org/)-Actors, [Akka](http://akka.io), [Akka.net](http://getakka.net/), [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Ein kurzer Blick auf Azure Service Fabric
Azure Fabric Actors ist eine Implementierung des Actor-Modells, das einige Ideen von Erlang und verteilten Objektsystemen übernimmt, eine Actor-Dereferenzierungsebene hinzufügt und dies in einem integrierten Programmiermodell bereitstellt, das die Azure Service Fabric-Plattform nutzt.

Die Hauptvorteile von Azure Fabric Actors sind: (1) **Entwicklerproduktivität**, selbst für nicht erfahrene Programmierer, und 2) **transparente Skalierbarkeit als Standard**, ohne besonderen Aufwand auf Programmiererseite. Azure Fabric Actors ist eine .NET-Bibliothek, die auf Azure Fabric sowie Tools ausgeführt wird, welche die Entwicklung komplexer verteilter Anwendungen stark vereinfachen und die resultierenden Anwendungen skalierbar konzipieren. Nachfolgend wird jeder dieser Vorteile ausführlicher erläutert. Das Azure Fabric Actors-Programmiermodell steigert die Produktivität von erfahrenen und nicht erfahrenen Programmierern durch das Angebot der folgenden Schlüsselabstraktionen, Garantien und Systemdienste.

* *Bekanntes Paradigma für die objektorientierte Programmierung (OOP)*. Actors sind Klassen in .NET, die deklarierte .NET-Actor-Schnittstellen mit asynchronen Methoden und Eigenschaften implementieren. Daher werden Actors für den Programmierer als Remote-Objekte angezeigt, deren Methoden/Eigenschaften direkt aufgerufen werden können. Dies bietet dem Programmierer das bekannte OOP-Paradigma, indem Methodenaufrufe in Nachrichten umgewandelt, an die richtigen Endpunkte weitergeleitet, Methoden des Ziel-Actors aufgerufen und Fehler sowie Ausnahmefälle auf vollständig transparente Weise behandelt werden.

* *Singlethread-Ausführung von Actors.* Die Azure Fabric-Laufzeit stellt sicher, dass ein Actor auf nur einem Thread gleichzeitig ausgeführt wird. In Kombination mit der Isolierung von anderen Actors, wird der Programmierer auf Actor-Ebene nie mit Parallelität konfrontiert und muss daher auch keine Sperren oder anderen Synchronisierungsmechanismen verwenden, um den Zugriff auf freigegebene Daten zu steuern. Dieses Feature allein macht die Entwicklung von verteilten Anwendungen auch für nicht erfahrene Programmierer handhabbar.

* *Transparente Aktivierung.* Die Azure Fabric-Laufzeit aktiviert einen Actor nur bei Bedarf, wenn eine Verarbeitungsnachricht vorliegt. Dies bewirkt eine saubere Trennung zwischen dem Konzept der logischen Erstellung eines Actors, die für den Anwendungscode sichtbar ist und von diesem gesteuert wird, und der physischen Aktivierung des Actors im Arbeitsspeicher, die für die Anwendung transparent ist. Azure Fabric Actors ähnelt einem virtueller Arbeitsspeicher, da es ebenfalls entscheidet, wann ein Actor "ausgelagert" (deaktiviert) oder "eingelagert" (aktiviert) werden soll. Die Anwendung verfügt über unterbrechungsfreien Zugriff auf den vollständigen "Speicherbereich" der logisch erstellten Actors, und zwar unabhängig davon, ob diese im physischen Speicher zu einem bestimmten Zeitpunkt vorhanden sind oder nicht. Transparente Aktivierung ermöglicht einen dynamischen, adaptiven Lastenausgleich über die Platzierung und Migration von Actors innerhalb des Pools von Hardware-Ressourcen.

* *Speicherorttransparenz.* Ein Actor-Verweis (Proxy-Objekt), das der Programmierer verwendet, um die Methoden des Actors aufzurufen oder an andere Komponenten zu übergeben, enthält nur die logische Identität des Actors. Die Verschiebung der logischen Identität des Actors zu seinem physischen Speicherort und das entsprechende Routing von Nachrichten werden vom Azure-Fabric-Laufzeitmodul auf transparente Weise durchgeführt. Der Anwendungscode kommuniziert mit Actors, die ihren physischen Speicherort nicht mehr kennen, der sich im Laufe der Zeit durch Fehler oder die Ressourcenverwaltung ändern kann oder weil ein Actor zum Zeitpunkt seines Aufrufs deaktiviert ist.

* *Transparente Integration mit persistenter Speicherung.* Azure Fabric Actors ermöglicht die deklarative Zuordnung des In-Memory-Status von Actors zum persistenten Speicher. Es synchronisiert Updates und gewährleistet auf transparente Weise, dass Aufrufer Ergebnisse erst erhalten, nachdem der persistente Status erfolgreich aktualisiert wurde.

* *Hohe Verfügbarkeit, Failover-Unterstützung und Application Lifecycle Management.* Der Status von Azure Fabric Actors wird von der Plattform verwaltet und so repliziert, dass er wiederhergestellt werden kann, wenn z. B. ein Knoten im Cluster ausfällt. Azure Service Fabric verwaltet außerdem die Lebensdauer der Anwendung und ermöglicht Anwendungs-Upgrades vollkommen ohne Ausfallzeit. Das Programmiermodell Azure Fabric Actors soll Programmierern einen möglichst erfolgreichen Weg zum Skalieren einer Anwendung oder eines Dienstes über mehrere Größenordnungen hinweg ebnen. Dies wird erreicht durch die Einbeziehung von bewährten Methoden und Mustern sowie durch die Bereitstellung einer effizienten Implementierung von Systemfunktionalität auf niedrigerer Ebene. Hier werden einige wichtige Faktoren aufgeführt, die Skalierbarkeit und Leistung von Azure Fabric-Anwendungen ermöglichen.

* *Implizite differenzierte Partitionierung des Anwendungsstatus.* Durch die Verwendung von Actors als direkt adressierbare Einheiten nehmen Programmierer eine implizite Untergliederung des Gesamtstatus ihrer Anwendungen vor. Während das Azure Fabric Actors-Programmiermodell nicht vorschreibt, wie groß oder klein ein Actor sein sollte, ist es in den meisten Fällen sinnvoll, eine relativ große Anzahl von Actors – Millionen oder mehr – zu haben, wobei jeder eine natürliche Entität der Anwendung darstellt, wie z. B. ein Benutzerkonto, eine Bestellung usw. Mit Actors, die einzeln adressierbar sind, und deren physischer Speicherort durch die Laufzeit abstrahiert wird, besitzt Azure Fabric Actors eine enorm hohe Flexibilität beim Ausgleichen von Lasten und Umgang mit Hotspots auf transparente und generische Weise, ohne dass der Anwendungsentwickler sich darum kümmern muss.

* *Adaptive Ressourcenverwaltung.* Mit Actors, die keine Annahmen hinsichtlich der Positionierung von anderen Actors machen, mit denen sie interagieren, und aufgrund der Speicherorttransparenz, kann das Azure Fabric-Laufzeitmodul die Zuweisung verfügbarer Hardware-Ressourcen auf eine sehr dynamische Weise verwalten und anpassen, und zwar durch differenzierte Entscheidungen zur Platzierung bzw. Migration von Actors im Compute-Cluster als Reaktion auf Last- und Kommunikationsmuster, ohne dass eingehende Anforderungen fehlschlagen. Durch Erstellen mehrerer Replikate eines bestimmten Actors kann das Laufzeitmodul den Durchsatz des Actors bei Bedarf erhöhen, ohne dass Änderungen am Anwendungscode erforderlich sind.

* *Multiplex-Kommunikation.* Actors in Azure Fabric besitzen logische Endpunkte. Messaging zwischen ihnen erfolgt im Multiplexmodus über einen festen Satz physischer Verbindungen von allen zu allen (TCP-Sockets). Dies ermöglicht es der Azure Fabric Actors-Laufzeit, eine sehr große Anzahl (Millionen) von adressierbaren Entitäten zu hosten, ohne dass ein Mehraufwand pro Actor für das Betriebssystem entsteht. Außerdem fallen bei der Aktivierung/Deaktivierung eines Actors keine Kosten für die Registrierung/Aufhebung der Registrierung eines physikalischen Endpunkts an, wie z. B. ein TCP-Port oder eine HTTP-URL.

* *Effiziente Planung.* Das Azure Fabric-Laufzeitmodul plant die Ausführung einer großen Anzahl von Singlethread-Actors in einem benutzerdefinierten Threadpool mit einem Thread pro physischem Prozessorkern. Mit Actor-Code, der im nicht-blockierende, fortsetzungsbasierten Format geschrieben wird (eine Anforderung des Azure Fabric Actors-Programmiermodells), wird der Anwendungscode auf sehr effiziente, "kooperative" Weise und im Multithread-Modus ausgeführt, ohne dass Konflikte auftreten. Dadurch kann das System einen hohen Durchsatz erreichen und mit sehr hoher CPU-Auslastung (bis zu 90 + %) bei hervorragenden Stabilität ausgeführt werden. Die Tatsache, dass eine Zunahme der Actor-Anzahl im System und der Last nicht zu zusätzlichen Threads oder anderen Betriebssystem-Primitiven führt, unterstützt die Skalierbarkeit einzelner Knoten und des gesamten Systems.

* *Explizite Asynchronie.* Das Programmiermodell Azure Fabric Actors expliziert die asynchrone Natur einer verteilten Anwendung und weist Programmierern den Weg zum Schreiben von nicht-blockierendem, asynchronem Code. Dadurch wird ein hohes Maß an verteilter Parallelität und Gesamtdurchsatz ermöglicht, ohne dass die explizite Verwendung von Multithreading erforderlich ist.

<!---HONumber=Oct15_HO2-->