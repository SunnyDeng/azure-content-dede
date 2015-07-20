<properties
   pageTitle="Beispiele für Antimuster in Azure Service Fabric Actors"
   description="Beispiele möglicher Fallgruben für Kunden, die sich neu mit Azure Service Fabric Actors befassen"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="claudioc"/>

# Entwurfsmuster in Service Fabric Actors: Beispiele für Anti-Muster

Wir haben die folgenden möglichen Fallgruben für Kunden ermittelt, die sich neu mit Azure Service Fabric Actors befassen:

* Behandlung von Azure Service Fabric Actors als Transaktionssystem. Azure Service Fabric Actors ist kein Commit-basiertes Zweiphasensystem, das AKID-Eigenschaften bietet. Wenn die optionale Persistenz nicht implementiert wird und der Computer, auf dem der Actor ausgeführt wird, ausfällt, geht auch dessen aktueller Status verloren. Der Actor kann auf einem anderen Knoten sehr schnell gestartet werden, wenn jedoch die unterstützende Persistenz nicht implementiert wurde, geht der Status verloren. Durch Wiederholungsnutzung, doppelte Filterung und/oder idempotenten Entwurf können Sie jedoch ein hohes Maß an Zuverlässigkeit und Konsistenz erreichen.

* Blockierung. Alle in Azure Service Fabric durchgeführten Aufgaben sollten asynchron sein. Dies ist in der Regel einfach, da asynchrone APIs auf der Microsoft-Plattform jetzt zahlreich vorhanden sind. Wenn jedoch aus irgendeinem Grund eine Interaktion mit einem System erforderlich ist, das nur eine blockierende API bereitstellt, muss diese in einen Wrapper eingefügt werden, der explizit den .NET Threadpool verwendet.

* Übermäßige Planung. Lassen Sie die Umgebung arbeiten. Für Entwickler, die es gewohnt sind, sich Gedanken über gleichzeitige Erfassungen und Sperren zu machen oder Tools zum Kompilieren von Objekten aus XML verwenden, kann es schwierig sein, einfach nur eine Klasse zu kodieren, die so einfache Aufgaben ausführt wie die Zuweisung eines Werts zu einer Variablen oder einem Zeitplan. Geplante Aufgaben sind integriert. Sperren sind nicht erforderlich. Der Status ist kein Todfeind. Für viele, die eine Menge serverseitige Arbeiten in großen Umgebungen durchgeführt haben, ist dies ziemlich gewöhnungsbedürftig.

* Einen einzelnen Actor zum Engpass machen. Häufig gerät man nur allzu leicht in diese Falle, indem unzählige Actors trichterförmig auf die einzelne Instanz eines anderen Actors konzentriert werden. Verwenden Sie den Aggregationsansatz, der unter [Entwurfsmuster für verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md) dargestellt wurde.

* Blindes Zuordnen von Entitätsmodellen. Dies richtet sich an Entwickler, die aus einer relationalen Welt kommen, in der Probleme mithilfe von Entitäten und deren Beziehungen modelliert werden. Während dieser Ansatz für das Verständnis der Subjektdomäne nach wie vor nützlich ist, sollte er mit dienst-orientierter Denkweise gekoppelt und in das Verhalten aufgenommen werden.
 

<!---HONumber=July15_HO2-->