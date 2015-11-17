<properties
   pageTitle="Reliable Services-Architektur | Microsoft Azure"
   description="Übersicht über die Reliable Services-Architektur für zustandsbehaftete und zustandslose Dienste."
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# Architektur für zustandsbehaftete und zustandslose Reliable Services

Reliable Services von Service Fabric können zustandsbehaftet oder zustandslos sein. Jeder Diensttyp wird mit einer bestimmten Architektur ausgeführt, die in diesem Artikel beschrieben ist. Weitere Informationen zu den Unterschieden zwischen zustandsbehafteten und zustandslosen Diensten finden Sie in der [Übersicht über Reliable Services](service-fabric-reliable-services-introduction.md).

## Zustandsbehaftete Reliable Services

### Architektur eines zustandsbehafteten Diensts
![Architekturdiagramm](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Zustandsbehafteter Reliable Service

Ein zustandsbehafteter Reliable Service kann von der Klasse StatefulService oder StatefulServiceBase abgeleitet werden. Beide Basisklassen werden von Service Fabric bereitgestellt und bieten verschiedene Stufen der Unterstützung und Abstraktion für den zustandsbehafteten Dienst, um eine Verbindung mit Service Fabric herzustellen und als Dienst im Service Fabric-Cluster teilzunehmen. StatefulService wird von StatefulServiceBase abgeleitet. StatefulServiceBase bietet Diensten mehr Flexibilität, erfordert jedoch mehr Kenntnisse über Service Fabric. Weitere Informationen zum Schreiben von Diensten mit den Klassen „StatefulService“ und „StatefulServiceBase“ finden Sie in der [Übersicht über Reliable Services](service-fabric-reliable-services-introduction.md) und im Artikel über die [erweiterte Verwendung von Reliable Services](service-fabric-reliable-services-advanced-usage.md).

Beide Basisklassen verwalten die Lebensdauer und die Rolle der Dienstimplementierung. Die Dienstimplementierung kann die virtuellen Methoden beider Basisklassen überschreiben, wenn sie an diesen Punkten in ihrem Lebenszyklus Aufgaben auszuführen hat oder wenn ein Objekt für den Kommunikationslistener erstellt werden soll. Eine Dienstimplementierung kann ihr eigenes Kommunikationslistener-Objekt implementieren und ICommunicationListener verfügbar machen. In der vorangegangenen Abbildung wird der Kommunikationslistener jedoch von Service Fabric implementiert, da die Dienstimplementierung einen von Service Fabric implementierten Kommunikationslistener verwendet.

Der zustandsbehaftete Reliable Service verwendet den zuverlässigen Zustands-Manager, um die Vorteile zuverlässiger Auflistungen zu nutzen. Zuverlässige Auflistungen sind lokale Datenstrukturen mit einer hohen Verfügbarkeit für den Dienst, d. h., sie sind unabhängig von Dienst-Failovern immer verfügbar. Jeder Typ einer zuverlässigen Auflistung wird von einem zuverlässigen Zustandsanbieter implementiert. Weitere Informationen über zuverlässige Auflistungen finden Sie in der [Übersicht über zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md).

### Reliable State Manager und Zustandsanbieter

Der zuverlässige Zustands-Manager ist die Verwaltungskomponente für zuverlässige Zustandsanbieter. Sie beinhaltet Funktionen zum Erstellen, Löschen, Auflisten und Sicherstellen der Persistenz und hohen Verfügbarkeit der zuverlässigen Zustandsanbieter. Eine Instanz eines zuverlässigen Zustandsanbieters repräsentiert eine persistente und hochverfügbare Datenstruktur, z. B. ein Wörterbuch oder eine Warteschlange. Jeder zuverlässige Zustandsanbieter stellt eine Schnittstelle zur Verfügung, über die der zustandsbehaftete Dienst mit dem zuverlässigen Zustandsanbieter interagiert. IReliableDictionary wird beispielsweise verwendet, um eine Verbindung mit dem zuverlässigen Wörterbuch herzustellen, während mit IReliableQueue eine Verbindung mit der zuverlässigen Warteschlange hergestellt wird. Alle zuverlässigen Zustandsanbieter implementieren die IReliableState-Schnittstelle.

Der Reliable State Manager verfügt über eine Schnittstelle namens „IReliableStateManager“, die den Zugriff auf den Manager aus Ihrem zustandsbehafteten Dienst ermöglicht. Schnittstellen zu zuverlässigen Zustandsanbietern werden über IReliableStateManager zurückgegeben.

Der Reliable State Manager basiert auf einer dynamischen Plug-In-Architektur, sodass neue Typen zuverlässiger Auflistungen dynamisch integriert werden können.

Das zuverlässige Wörterbuch und die zuverlässige Warteschlange basieren auf der Implementierung eines leistungsfähigen, versionierten, differenziellen Speichers.

### Transaktionsreplikator

Der Transaktionsreplikator stellt sicher, dass der Zustand des Diensts (d. h. der Zustand innerhalb des zuverlässigen Zustands-Managers und der zuverlässigen Auflistungen) in allen Replikaten, die den Dienst ausführen, konsistent und im Protokoll persistent ist. Der zuverlässige Zustands-Manager wird über einen privaten Mechanismus mit dem Transaktionsreplikator verbunden.

Der Transaktionsreplikator kommuniziert den Zustand mithilfe eines Netzwerkprotokolls an andere Replikate der Dienstinstanz, sodass alle Replikate über aktuelle Zustandsinformationen verfügen.

Der Transaktionsreplikator verwendet ein Protokoll, um Zustandsinformationen auch dann beizubehalten, wenn ein Prozess unterbrochen wird oder ein Knoten ausfällt. Die Schnittstelle zum Protokoll wird über einen privaten Mechanismus hergestellt.

### Protokoll

Die Protokollierungskomponente bietet einen leistungsfähigen persistenten Speicher, der für das Schreiben auf herkömmlichen rotierenden Festplatten oder Solid-State-Festplatten und auch für die effiziente Speicherplatznutzung optimiert werden kann. Das Protokoll ist darauf ausgelegt, dass sich der persistenten Speicher (Datenträger) lokal auf den Knoten befindet, die den zustandsbehafteten Dienst ausführen. Dies ermöglicht gegenüber einem persistenten, nicht lokal auf dem Knoten befindlichen Speicher geringere Latenzen und einen höheren Durchsatz.

Die Protokollierungskomponente verwendet zwei Arten von Protokolldateien. Es gibt eine knotenübergreifende freigegebene Protokolldatei, die auf einem Datenträger gespeichert werden sollte, der nur für diese Protokolldatei verwendet wird. Diese Datei befindet sich im Knotenarbeitsverzeichnis von Service Fabric. Jedes Replikat für den Dienst verfügt ebenfalls über eine dedizierte Protokolldatei. Diese wird im Arbeitsverzeichnis des Diensts gespeichert. Das freigegebene Protokoll ist ein Übergangsbereich für die Zustandsinformationen, während die dedizierte Protokolldatei das letztendliche Ziel ist, in dem die Informationen dauerhaft gespeichert werden. Die Zustandsinformationen werden in diesem Fall zunächst in die freigegebene Protokolldatei geschrieben und anschließend im Hintergrund in der dedizierten Protokolldatei gespeichert. Auf diese Weise erfolgt das Schreiben in das freigegebene Protokoll mit der geringsten Latenz und dem höchsten Durchsatz, sodass der Dienst schneller arbeiten kann.

Wenn die Protokollierungskomponente jedoch mithilfe der Einstellung "OptimizeForLocalSSD" für Solid-State-Festplatten optimiert wurde, werden die Zustandsinformationen direkt in die dedizierte Protokolldatei geschrieben und umgehen die freigegebene Protokolldatei. Da bei Solid-State-Festplatten keine Verzögerungen aufgrund von Konflikten bei der Lesekopfbewegung auftreten, gibt es beim direkten Schreiben in die dedizierte Protokolldatei keine Abzüge.

Wenn die Protokollierungskomponente mithilfe der Einstellung "OptimizeLogForLowerDiskUsage" für die Minimierung der Speicherplatzbelegung optimiert wurde, werden für die dedizierten Protokolldateien NTFS-Dateien mit geringer Datendichte erstellt. Da Protokolldateien in der Regel nicht immer vollständig mit Statusinformationen gefüllt sind, kann durch die Verwendung von Dateien mit geringer Datendichte der Speicherplatz überdimensioniert werden, um Platz für mehr Replikate zu bieten. Wenn die Protokollierungskomponente nicht auf diese Weise konfiguriert ist, wird der Speicherplatz für die Protokolldateien vorab zugeordnet. Die Protokollierungskomponente kann in diesem Fall direkt in die leistungsstärkste Datei geschrieben werden.

Abgesehen von einer minimalen Benutzermodusschnittstelle zum Protokoll wird das Protokoll als Kernelmodustreiber geschrieben. Durch die Ausführung als Kernelmodustreiber kann das Protokoll allen Diensten, die es verwenden, die höchste Leistung bereitstellen.

Weitere Informationen zum Konfigurieren des Protokolls finden Sie unter [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md).

## Zustandsloser Reliable Service

### Architektur eines zustandslosen Diensts
![Architekturdiagramm](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Zustandsloser Reliable Service

Zustandslose Dienstimplementierungen werden von der Klasse StatelessService oder StatelessServiceBase abgeleitet, wobei StatelessServiceBase mehr Flexibilität als StatelessService bietet. Beide Basisklassen verwalten die Lebensdauer und die Rolle des Diensts. Die Dienstimplementierung kann die virtuellen Methoden beider Basisklassen überschreiben, wenn der Dienst an diesen Punkten in seinem Lebenszyklus Aufgaben auszuführen hat oder wenn ein Objekt für den Kommunikationslistener erstellt werden soll. Ein Dienst kann sein eigenes Kommunikationslistener-Objekt implementieren und ICommunicationListener verfügbar machen. In der vorangegangenen Abbildung wird der Kommunikationslistener jedoch von Service Fabric implementiert, da die Dienstimplementierung einen von Service Fabric implementierten Kommunikationslistener verwendet.

Weitere Informationen zum Schreiben von Diensten mit den Klassen „StatelessService“ und „StatelessServiceBase“ finden Sie in der [Übersicht über Reliable Services](service-fabric-reliable-services-introduction.md) und im Artikel zur [erweiterten Verwendung von Reliable Services](service-fabric-reliable-services-advanced-usage.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen zu Service Fabric finden Sie unter:

[Übersicht über Reliable Services](service-fabric-reliable-services-introduction.md)

[Schnellstart](service-fabric-reliable-services-quick-start.md)

[Übersicht über zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)

[Erweiterte Verwendung von Reliable Services](service-fabric-reliable-services-advanced-usage.md)

[Konfigurieren von Reliable Services](service-fabric-reliable-services-configuration.md)
 

<!---HONumber=Nov15_HO2-->