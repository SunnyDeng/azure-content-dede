<properties
   pageTitle="Reliable Actors-Muster- und -Antimuster | Microsoft Azure"
   description="Übersicht über das Reliable Actors-Programmiermodell, Entwurfsmuster, die gut mit Service Fabric Reliable Actors zusammenarbeiten, und zu vermeidende Antimuster."
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

Das Reliable Actors-Programmiermodell in Azure Service Fabric ist eine auf dem Actor-Modell basierende Plattform zum Lösen von realen Problemen auf Cloud-Niveau. Service Fabric ist eine Plattform zum Erstellen von hochzuverlässigen, skalierbaren Anwendungen, die einfach zu entwickeln und zu verwalten sind – sowohl in der Cloud als auch lokal.

Dieser Artikel enthält hilfreiche Informationen bei Problemen in der Praxis. Nachdem Sie sich über die verschiedenen Muster informiert haben, können Sie nachvollziehen, wie Sie das Reliable Actors-Modell zum Erstellen von Lösungen auf Unternehmens- und Cloudebene verwenden können.

## Muster

Dieser Abschnitt enthält einen Satz von Mustern und zugehörige Szenarien, die wir bei unseren Projekten mit Kunden genutzt haben. Diese Muster stellen Klassen von Problemen dar, die für eine Vielzahl von Lösungen anwendbar sind, die unsere Kunden auf Microsoft Azure erstellen. Die Szenarien basieren zwar auf realen Fällen, aber wir haben die meisten domänenspezifischen Aspekte entfernt, um die Muster zu verdeutlichen. Wahrscheinlich werden Sie feststellen, dass ein Großteil des Beispielcodes einfach oder offensichtlich ist. Dieser Code wird aus Gründen der Vollständigkeit angegeben, und nicht, weil er besonders intelligent oder beeindruckend ist.

Die hier vorgestellten Muster erheben nicht den Anspruch, vollständig oder kanonisch zu sein. Es kann durchaus sein, dass einige Entwickler die gleichen Probleme oder Muster auf andere Weise als hier dargestellt lösen.

[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Einige Antimuster](service-fabric-reliable-actors-anti-patterns.md)

### Kurzer Überblick über „Actors“

Das [Dokument](http://dl.acm.org/citation.cfm?id=1624804) von Carl Hewitt und seinen Co-Autoren, das den Ursprung des Actor-Modells darstellt, wurde 1973 veröffentlicht. Dem Actor-Modell wurde aber erst vor relativ kurzer Zeit als hilfreiches Werkzeug für die Bereiche Parallelität und Komplexität in verteilten Systemen mehr Aufmerksamkeit zuteil.

Beim Actor-Modell werden differenzierte Einzelobjekte – Actors („Akteure“) – unterstützt, die voneinander isoliert sind. Sie kommunizieren per asynchroner Nachrichtenübergabe, mit der eine direkte Kommunikation zwischen Actors ermöglicht wird. Ein Actor wird mit Singlethread-Semantik ausgeführt. Dieser Ansatz ist mit der Kapselung des Actor-Zustands und der Isolierung von anderen Actors gekoppelt und vereinfacht das Schreiben von Systemen mit einem hohen Anteil paralleler Vorgänge. Dies wird erreicht, indem die Parallelitätsaspekte aus dem Code eines Actors herausgelöst werden. Die Actor-Erstellung erfolgt dynamisch im Pool der verfügbaren Hardwareressourcen.

[Erlang](http://www.erlang.org/) ist die beliebteste Implementierung des Actor-Modells. Entwickler haben begonnen, das Actor-Modell wiederzuentdecken. Dadurch ist ein neues Interesse an Erlang und der Erstellung von neuen Erlang-ähnlichen Lösungen entstanden, z. B. [Scala](http://www.scala-lang.org/)-Actors, [Akka](http://akka.io), [Akka.NET](http://getakka.net/) und [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Vorteile von Service Fabric Reliable Actors

Das Reliable Actors-Programmiermodell ist eine Implementierung des Actor-Modells, wobei einige Ideen von Erlang und verteilten Objektsystemen übernommen werden. Es wird eine Dereferenzierungsebene für Actors hinzugefügt, und die Actors werden in einem integrierten Programmiermodell bereitgestellt, das die Service Fabric-Plattform nutzt.

Die Hauptvorteile von Reliable Actors sind **Entwicklerproduktivität**, selbst für unerfahrene Programmierer, und **standardmäßige transparente Skalierbarkeit** ohne besonderen Aufwand auf Programmiererseite. Für das Reliable Actors-Programmiermodell wird eine .NET-Bibliothek verwendet, die gemeinsam mit Service Fabric ausgeführt wird. Sie enthält Tools, mit denen die Entwicklung komplexer verteilter Anwendungen deutlich vereinfacht wird. Außerdem werden die Anwendungen mit den Tools standardmäßig skalierbar gestaltet. Nachfolgend wird jeder dieser Vorteile ausführlicher erläutert. Das Programmiermodell steigert die Produktivität von erfahrenen und unerfahrenen Programmierern, indem folgende Schlüsselabstraktionen, Garantien und Systemdienste bereitgestellt werden:

* *Vertrautes Paradigma für die objektorientierte Programmierung (OOP):* Actors sind Klassen in .NET, die deklarierte .NET-Actor-Schnittstellen mit asynchronen Methoden und Eigenschaften implementieren. Daher werden Actors für Programmierer als Remoteobjekte angezeigt, deren Methoden und Eigenschaften direkt aufgerufen werden können. So können Programmierer das bekannte OOP-Paradigma nutzen, bei dem Methodenaufrufe in Nachrichten umgewandelt, an die richtigen Endpunkte weitergeleitet, Methoden des Ziel-Actors aufgerufen sowie Fehler und Ausnahmefälle vollständig transparent behandelt werden.

* *Singlethread-Ausführung von Actors:* Die Reliable Actors-Laufzeit stellt sicher, dass ein Akteur jeweils nur in einem Thread ausgeführt wird. Aus diesem Grund und wegen der Isolierung jedes Actors von anderen Actors kommen Programmierer auf Actor-Ebene niemals mit der Parallelität in Berührung. Programmierer müssen keine Sperren oder anderen Synchronisierungsmechanismen zum Steuern des Zugriffs auf freigegebene Daten verwenden. Allein dieses Feature ermöglicht schon die Entwicklung von verteilten Anwendungen auch für unerfahrene Programmierer.

* *Transparente Aktivierung:* Die Laufzeit aktiviert einen Actor nur bei Bedarf, wenn eine Nachricht zur Verarbeitung vorliegt. Dies bewirkt eine saubere Trennung des Konzepts der logischen Erstellung eines Actors, die für den Anwendungscode sichtbar ist und von diesem gesteuert wird, von der physischen Aktivierung des Actors im Arbeitsspeicher, die für die Anwendung transparent ist. Der Reliable Actors-Ansatz ähnelt einem virtuellen Arbeitsspeicher, da auch hier entschieden wird, wann ein Actor „ausgelagert“ (deaktiviert) oder „eingelagert“ (aktiviert) werden soll. Die Anwendung verfügt über unterbrechungsfreien Zugriff auf den vollständigen „Speicherbereich“ der logisch erstellten Actors, und zwar unabhängig davon, ob diese im physischen Speicher zu einem bestimmten Zeitpunkt vorhanden sind oder nicht. Transparente Aktivierung ermöglicht einen dynamischen, adaptiven Lastenausgleich über die Platzierung und Migration von Actors innerhalb des Pools von Hardware-Ressourcen.

* *Speicherorttransparenz:* Ein Actor-Verweis (Proxy-Objekt), das von einem Programmierer verwendet wird, um die Methoden eines Actors aufzurufen oder an andere Komponenten zu übergeben, enthält nur die logische Identität des Actors. Die Verschiebung der logischen Identität des Akteurs an seinen physischen Speicherort und das entsprechende Routing von Nachrichten werden von der Reliable Actors-Laufzeit auf transparente Weise durchgeführt. Der Anwendungscode kommuniziert mit Actors, die ihren physischen Speicherort nicht mehr kennen. Dieser kann sich im Laufe der Zeit durch Fehler oder die Ressourcenverwaltung ändern. Der Speicherort eines Actors kann sich auch ändern, weil ein Actor zum Zeitpunkt seines Aufrufs deaktiviert ist.

* *Transparente Integration mit persistenter Speicherung:* Das Reliable Actors-Programmiermodell ermöglicht die deklarative Zuordnung des In-Memory-Status von Actors zu einem persistenten Speicher. Es synchronisiert Updates und gewährleistet auf transparente Weise, dass Aufrufer Ergebnisse erst erhalten, nachdem der persistente Status erfolgreich aktualisiert wurde.

* *Hohe Verfügbarkeit, Failover-Unterstützung und Application Lifecycle Management:* Der Status eines Actors wird von der Plattform verwaltet und so repliziert, dass er wiederhergestellt werden kann, wenn beispielsweise ein Knoten im Cluster ausfällt. Service Fabric verwaltet außerdem die Lebensdauer der Anwendung und ermöglicht Anwendungsupgrades ohne Ausfallzeit. Das Reliable Actors-Programmiermodell soll Programmierern einen möglichst erfolgreichen Weg zum Skalieren von Anwendungen und Diensten unterschiedlicher Größenordnungen ebnen. Dies wird durch die Einbeziehung von bewährten Methoden und Mustern sowie durch die Bereitstellung einer effizienten Implementierung von Systemfunktionalität auf niedrigerer Ebene erreicht. Hier sind einige wichtige Faktoren aufgeführt, die Skalierbarkeit und Leistung von Service Fabric-Anwendungen ermöglichen:

  * *Implizite differenzierte Partitionierung des Anwendungsstatus:* Durch die Verwendung von Actors als direkt adressierbare Einheiten nehmen Programmierer eine implizite Untergliederung des Gesamtstatus ihrer Anwendungen vor. Beim Reliable Actors-Programmiermodell wird zwar nicht vorgeschrieben, wie groß oder klein ein Actor sein sollte, aber in den meisten Fällen ist es sinnvoll, eine relativ große Anzahl von Actors im Millionenbereich zu verwenden. Jeder dieser Millionen von Actors stellt eine natürliche Entität der Anwendung dar, z. B. ein Benutzerkonto oder eine Bestellung. Da Actors einzeln adressierbar sind und ihre physischen Standorte von der Laufzeit abstrahiert werden, besteht beim Ausgleichen von Lasten und beim Umgang mit Hotspots eine enorme Flexibilität. Dies erfolgt auf transparente und generische Weise, ohne dass Anwendungsentwickler sich darüber Gedanken machen müssen.

  * *Adaptive Ressourcenverwaltung:* Actors stellen keine Vermutungen zur Positionierung anderer Actors an, mit denen sie interagieren. Aufgrund der Speicherorttransparenz kann die Laufzeit die Zuweisung verfügbarer Hardwareressourcen auf sehr dynamische Weise verwalten und anpassen. Zu diesem Zweck werden differenzierte Entscheidungen zur Platzierung bzw. Migration von Actors im Computecluster als Reaktion auf Last- und Kommunikationsmuster verwendet, ohne dass eingehende Anforderungen fehlschlagen. Durch das Erstellen mehrerer Replikate eines bestimmten Actors kann das Laufzeitmodul den Durchsatz des Actors erhöhen, ohne dass Änderungen am Anwendungscode erforderlich sind.

  * *Multiplex-Kommunikation:* Akteure in Service Fabric haben logische Endpunkte. Messaging zwischen ihnen erfolgt im Multiplexmodus über einen festen Satz physischer All-to-all-Verbindungen (TCP-Sockets). Dies ermöglicht es der Laufzeit, eine sehr große Anzahl (Millionen) von adressierbaren Entitäten zu hosten, ohne dass ein Mehraufwand pro Actor für das Betriebssystem entsteht. Außerdem fallen bei der Aktivierung oder Deaktivierung eines Actors keine Kosten für die Registrierung bzw. Aufhebung der Registrierung eines physikalischen Endpunkts an, z. B. ein TCP-Port oder eine HTTP-URL.

  * *Effiziente Planung:* Die Reliable Actors-Laufzeit plant die Ausführung einer großen Anzahl von Singlethread-Actors in einem benutzerdefinierten Threadpool, indem ein Thread pro physischem Prozessorkern verwendet wird. Actor-Code wird in einem nicht blockierenden, fortsetzungsbasierten Format geschrieben (eine Anforderung des Programmiermodells), sodass der Anwendungscode auf sehr effiziente, „kooperative“ Weise und im Multithread-Modus ausgeführt wird, ohne dass Konflikte auftreten. Dadurch kann das System einen hohen Durchsatz erreichen und mit sehr hoher CPU-Auslastung (90 % oder mehr) bei hervorragenden Stabilität ausgeführt werden. Eine Zunahme der Actor-Anzahl im System und der Last führt nicht zu zusätzlichen Threads oder anderen Betriebssystem-Primitiven. So wird die Skalierbarkeit einzelner Knoten und des gesamten Systems unterstützt.

  * *Explizite Asynchronie:* Das Reliable Actors-Programmiermodell macht die Asynchronie einer verteilten Anwendung explizit und weist Programmierern den Weg zum Schreiben von nicht blockierendem, asynchronem Code. Dadurch wird ein hohes Maß an verteilter Parallelität und Gesamtdurchsatz ermöglicht, ohne dass die explizite Verwendung von Multithreading erforderlich ist.

<!---HONumber=AcomDC_0128_2016-->