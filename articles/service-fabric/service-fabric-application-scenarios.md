<properties 
   pageTitle="Anwendungsszenarios und Entwurf mit Service Fabric" 
   description="Kategorien von Anwendungen Anwendungsentwurf mit zustandsbehafteten und zustandslosen Diensten" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/24/2015"
   ms.author="mfussell"/>

# Service Fabric-Anwendungsszenarios

Service Fabric und Azure bieten eine zuverlässige und flexible Plattform mit Cloudinfrastruktur, auf der Sie zahlreiche Geschäftsanwendungen und Geschäftsdienste ausführen können. Diese Anwendungen und Microservices können zustandslos oder zustandsbehaftet sein und ihre Ressourcen werden zur Maximierung der Effizienz auf die virtuellen Computer verteilt. In der einzigartigen Architektur von Service Fabric können Sie Echtzeitdatenanalysen, In-Memory-Berechnungen, parallele Transaktionen und die Ereignisverarbeitung in Ihren Anwendungen ausführen. Sie können Ihre Anwendungen je nach den sich ändernden Ressourcenanforderungen problemlos zentral hoch- oder herunterskalieren.

Die Service Fabric-Plattform in Azure ist ideal für die folgenden Kategorien von Anwendungen und Diensten geeignet:

- **Hochverfügbare Dienste**: Service Fabric-Dienste bieten ein sehr schnelles Failover. Mehrere sekundäre Dienstreplikate bleiben verfügbar. Wenn ein Knoten aufgrund eines Hardwarefehlers ausfällt, wird eines der sekundären Replikate mit unerheblicher Dienstunterbrechung für die Kunden sofort an ein primäres Replikat weitergeleitet. Dienste können je nach Ihren Ressourcenanforderungen schnell und einfach von nur wenigen auf Tausende von Instanzen zentral hochskaliert und wieder auf einige Instanzen zentral herunterskaliert werden. Mithilfe von Service Fabric können Sie den Lebenszyklus dieser skalierbaren Clouddienste erstellen und verwalten.

- **Skalierbare Dienste**: Einzelne Dienste können partitioniert werden, sodass der Zustand im Cluster horizontal hochskaliert werden kann. Darüber hinaus können mehrere Service Fabric-Dienste erstellt und horizontal hochskaliert werden.
 
- **Berechnung nicht statischer Daten**: Mit Service Fabric können Sie die Ein- und Ausgabe von Daten erstellen und intensive, zustandsbehaftete Anwendungen berechnen. Die Verarbeitungs- und Datenressourcen sind in Service Fabric-Anwendungen zusammen abgelegt. Damit entfällt die Netzwerklatenz, die mit einem externen Datencache oder einer externen Speicherebene verbunden ist, wenn Ihre Anwendung schnelle Lese- und Schreibvorgänge erfordert. Ein Beispiel hierfür ist eine Anwendung, die die Auswahl von Werbeanzeigen mit einer Roundtripzeit von < 100 ms nahezu in Echtzeit ausführt und bei der die Berechnung der Regeln zur Anzeigenauswahl für den Benutzer reaktionsschnell umgesetzt wird.
 
- **Sitzungsbasierte interaktive Anwendungen**: Service Fabric ist sehr nützlich, wenn Ihre Anwendungen, z. B. Onlinespiele oder Instant Messaging, Lese- und Schreibvorgänge mit geringer Latenz erfordern. Mit Service Fabric können Sie diese interaktiven, zustandsbehafteten Anwendungen erstellen, ohne einen separaten Speicher oder Cache erstellen zu müssen, der für zustandslose Anwendungen erforderlich ist.
 
- **Verteilte Graphverarbeitung**: Durch den Ausbau sozialer Netzwerke ist der Bedarf, umfangreiche Graphen parallel zu analysieren, deutlich gestiegen. Die schnelle Skalierung und parallele Ladevorgänge machen Service Fabric zu einer idealen Plattform für die Verarbeitung umfangreicher Graphen. Mit Service Fabric können Sie hoch skalierbare Dienste für Gruppen erstellen, z. B. für soziale Netzwerke, Business Intelligence und wissenschaftliche Forschung.
 
- **Datenanalyse und Workflows**: Die schnellen Lese- und Schreibvorgänge von Service Fabric machen Anwendungen möglich, mit denen Ereignisse oder Datenströme zuverlässig verarbeitet werden müssen. Service Fabric ermöglicht auch Anwendungen, die eine Verarbeitungspipeline beschreiben, bei der Ergebnisse zuverlässig sein müssen und dann ohne Datenverlust in die nächste Verarbeitungsphase weitergeleitet werden müssen, z. B. Transaktions- und Finanzsysteme, bei denen Datenkonsistenz und Berechnungsgewährleistung von größter Wichtigkeit sind.

## Entwerfen von Anwendungen, die aus zustandslosen und zustandsbehafteten Microservices bestehen ##
Die Erstellung von Anwendungen mit Azure Cloud Service-Workerrollen ist ein Beispiel für zustandslose Dienste. Im Gegensatz dazu behalten zustandsbehaftete Microservices den autorisierenden Zustand über die Anforderung und ihre Antwort hinaus bei, stellen jedoch Statusreplikation über einfache APIs bereit. Zustandsbehaftete Dienste gewährleisten eine hohe Verfügbarkeit für alle Anwendungen – nicht nur für Datenbanken und andere Datenspeicher. Dies ist die natürliche Entwicklung im Entwurfsbereich, da sich Anwendungen dahin weiterentwickelt haben, dass sie nicht mehr nur rein relationale Datenbanken für hohe Verfügbarkeit sondern NoSQL-Datenbanken verwenden. Die Entwicklung geht noch weiter, sodass jetzt die Anwendungen selbst die Zustandsverwaltung für die wichtigsten Daten übernehmen, die zur Berechnung hoch verfügbar sein müssen.

Beim Erstellen von Anwendungen, die sich aus Microservices zusammensetzen, haben Sie es normalerweise mit einer Kombination aus zustandslosen Webanwendungen (ASP.NET, Node.js usw.) zu tun, die zustandslose und zustandsbehaftete Geschäftsdienste der mittleren Ebene aufrufen, die alle im gleichen Service Fabric-Cluster bereitgestellt werden und die Service Fabric-Bereitstellungsbefehle verwenden. Die unabhängige Skalierung, Zuverlässigkeit und Ressourcenverwendung für jeden dieser Microservices bietet diese große Flexibilität bei der Entwicklung und Verwaltung des Lebenszyklus.
  
Zustandsbehaftete Microservices vereinfachen Anwendungsentwürfe, da durch sie die Notwendigkeit zusätzlicher Warteschlangen und Caches entfällt, die traditionell erforderlich waren, um die Verfügbarkeits- und Latenzanforderungen einer rein zustandslosen Anwendung zu erfüllen. Da zustandsbehaftete Dienste von Natur aus hoch verfügbar sind und eine geringe Latenz aufweisen, bedeutet dies, dass in der gesamten Anwendung weniger bewegliche Komponenten zu verwalten sind. In den folgenden Abbildungen wird der Unterschied zwischen einer zustandslosen und einer zustandsbehafteten Anwendung veranschaulicht. Bei der zustandsbehafteten Anwendung verringern zustandsbehaftete Dienste durch Nutzung der Programmiermodelle [Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md) und [Reliable Actors](service-fabric-reliable-actors-introduction.md) die Komplexität der Anwendung. Gleichzeitig wird eine hohe Durchsatzrate mit geringer Latenz erzielt.

## Eine mit zustandslosen Diensten erstellte Anwendung##
![Anwendung mit zustandslosen Diensten][Image1]

## Eine mit zustandsbehafteten Diensten erstellte Anwendung##
![Anwendung mit zustandslosen Diensten][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte


Einführung in die Erstellung zustandsloser und zustandsbehafteter Dienste mit den Service Fabric-Programmiermodellen [Reliable Services](service-fabric-reliable-services-quick-start.md) und [Reliable Actors](service-fabric-reliable-actors-get-started.md).

Weitere Informationen finden Sie in den folgenden Themen:

[Service State](service-fabric-concepts-state.md) (in englischer Sprache)

[Availability of services](../service-fabric-concepts-availability-services.md) (in englischer Sprache)

[Scaling Service Fabric Applications](service-fabric-concepts-scalability.md) (in englischer Sprache)

[Partitioning Service Fabric services](service-fabric-concepts-partitioning.md) (in englischer Sprache)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=August15_HO6-->