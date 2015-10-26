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
   ms.date="08/21/2015"
   ms.author="mfussell"/>

# Service Fabric-Anwendungsszenarios

Microsoft Azure Service Fabric bietet eine zuverlässige und flexible Plattform, mit der Sie verschiedenste Geschäftsanwendungen und -dienste erstellen und ausführen können. Diese Anwendungen und Microservices können zustandslos oder zustandsbehaftet sein und ihre Ressourcen werden zur Maximierung der Effizienz auf die virtuellen Computer verteilt. In der einzigartigen Architektur von Service Fabric können Sie Echtzeitdatenanalysen, In-Memory-Berechnungen, parallele Transaktionen und die Ereignisverarbeitung in Ihren Anwendungen ausführen. Sie können Ihre Anwendungen je nach den sich ändernden Ressourcenanforderungen problemlos zentral (bzw. horizontal oder vertikal) hoch- oder herunterskalieren.

Die Service Fabric-Plattform in Azure ist ideal für die folgenden Kategorien von Anwendungen und Diensten geeignet:

- **Hochverfügbare Dienste**: Service Fabric-Dienste bieten ein sehr schnelles Failover. Mit Service Fabric können Sie mehrere sekundäre Dienstreplikate erstellen. Wenn ein Knoten, Prozess oder individueller Dienst aufgrund eines hardwarebedingten oder anderen Fehlers ausfällt, wird eines der sekundären Replikate umgehend zu einem primären Replikat – mit vernachlässigbarer Dienstunterbrechung für die Kunden. 

- **Skalierbare Dienste**: Einzelne Dienste können partitioniert werden, sodass der Zustand im Cluster horizontal hochskaliert werden kann. Darüber hinaus können einzelne Dienste im Handumdrehen erstellt und entfernt werden. Dienste können je nach Ressourcenanforderungen schnell und einfach horizontal von einigen wenigen Instanzen auf einigen wenigen Knoten zu Tausenden Instanzen auf vielen Knoten hochskaliert und umgehend wieder herunterskaliert werden. Mithilfe von Service Fabric können Sie diese Dienste erstellen und deren gesamten Lebenszyklus verwalten.
 
- **Berechnung nicht statischer Daten**: Mit Service Fabric können Sie zustandsbehaftete Anwendungen mit einer Vielzahl von Daten, Ein- und Ausgaben und hohem Rechenaufwand erstellen. Service Fabric ermöglicht die Zusammenlegung von Verarbeitung (Berechnung) und Daten in Clientanwendungen. Wenn Ihre Anwendung auf Daten zugreifen muss, ist heutzutage in der Regel mit einer Netzwerklatenz zu rechnen, die auf die Verwendung eines externen Datencache oder einer Speicherebene zurückzuführen ist. Mit zustandsbehafteten Service Fabric-Diensten lässt sich diese Latenz beseitigen, was eine höhere Leistung bei Lese- und Schreibvorgängen ermöglicht. Ein Beispiel: Angenommen, Ihre Anwendung unterbreitet Kunden nahezu in Echtzeit Vorschläge. Bei den Anforderungen muss eine Roundtrip-Zeit von unter 100 ms erreicht werden. Im Gegensatz zum standardmäßigen Implementierungsmodell, bei dem die benötigten Daten aus einem Remotespeicher abgerufen werden müssen, bieten die Latenz- und Leistungsmerkmale von Service Fabric-Diensten (bei denen die Berechnung der Vorschlagsauswahl mit den Daten und Regeln zusammengelegt wird) den Benutzern eine schnell reagierende Umgebung.
 
- **Sitzungsbasierte interaktive Anwendungen**: Service Fabric ist sehr nützlich, wenn Ihre Anwendungen, z. B. Onlinespiele oder Instant Messaging, Lese- und Schreibvorgänge mit geringer Latenz erfordern. Mit Service Fabric können Sie diese interaktiven, zustandsbehafteten Anwendungen ohne einen separaten Speicher oder Cache erstellen, der bei zustandslosen Apps erforderlich wäre (und zu einer höheren Latenz sowie zu potenziellen Konsistenzproblemen führt).
 
- **Verteilte Graphverarbeitung**: Durch den Ausbau sozialer Netzwerke ist der Bedarf, umfangreiche Graphen parallel zu analysieren, deutlich gestiegen. Die schnelle Skalierung und parallele Ladevorgänge machen Service Fabric zu einer idealen Plattform für die Verarbeitung umfangreicher Graphen. Mit Service Fabric können Sie hoch skalierbare Dienste für Gruppen erstellen, z. B. für soziale Netzwerke, Business Intelligence und wissenschaftliche Forschung.
 
- **Datenanalyse und Workflows**: Die schnellen Lese- und Schreibvorgänge von Service Fabric machen Anwendungen möglich, mit denen Ereignisse oder Datenströme zuverlässig verarbeitet werden müssen. Service Fabric ermöglicht auch Anwendungen, die eine Verarbeitungspipeline beschreiben, bei der Ergebnisse zuverlässig sein müssen und dann ohne Datenverlust in die nächste Verarbeitungsphase weitergeleitet werden müssen, z. B. Transaktions- und Finanzsysteme, bei denen Datenkonsistenz und Berechnungsgewährleistung von größter Wichtigkeit sind.

## Entwerfen von Anwendungen, die aus zustandslosen und zustandsbehafteten Microservices bestehen ##
Die Erstellung von Anwendungen mit Azure Cloud Service-Workerrollen ist ein Beispiel für zustandslose Dienste. Im Gegensatz dazu behalten zustandsbehaftete Microservices den autoritativen Zustand über die Anforderung und die Antwort hinaus bei. Dadurch lässt sich über einfache APIs, die für replikationsgestützte Transaktionsgarantien sorgen, eine hohe Verfügbarkeit und Konsistenz erreichen. Zustandsbehaftete Service Fabric-Dienste bieten hohe Verfügbarkeit für alle Arten von Anwendungen – nicht nur für Datenbanken und andere Datenspeicher. Hierbei handelt es sich um einen natürlichen Prozess: Anwendungen haben bereits den Wandel von der Verwendung rein relationaler Datenbanken für hohe Verfügbarkeit zur Verwendung von NoSQL-Datenbanken vollzogen. Nun werden die Anwendung selbst hochverfügbar, und Daten werden zur Leistungsoptimierung innerhalb der Anwendung verwaltet, ohne dafür Kompromisse bei Zuverlässigkeit, Konsistenz und Verfügbarkeit einzugehen.

Bei der Erstellung von Anwendungen, die sich aus Microservices zusammensetzen, haben Sie es normalerweise mit einer Kombination aus zustandslosen Web-Apps (ASP.NET, Node.js usw.) zu tun, die zustandslose und zustandsbehaftete Geschäftsdienste der mittleren Ebene aufrufen, welche alle im gleichen Service Fabric-Cluster bereitgestellt werden und die Service Fabric-Bereitstellungsbefehle verwenden. Jeder dieser Dienste ist in puncto Skalierung, Zuverlässigkeit und Ressourcenverwendung unabhängig, was eine deutlich höhere Flexibilität bei der Entwicklung sowie bei der Verwaltung des Lebenszyklus bedeutet.
  
Zustandsbehaftete Microservices vereinfachen Anwendungsentwürfe, da durch sie die Notwendigkeit zusätzlicher Warteschlangen und Caches entfällt, die traditionell erforderlich waren, um die Verfügbarkeits- und Latenzanforderungen einer rein zustandslosen Anwendung zu erfüllen. Da zustandsbehaftete Dienste an sich bereits hochverfügbar sind und eine niedrige Latenz bieten, fällt der Verwaltungsaufwand in Ihrer Anwendung insgesamt geringer aus. In den folgenden Abbildungen wird der Unterschied zwischen einer zustandslosen und einer zustandsbehafteten Anwendung veranschaulicht. Zustandsbehaftete Dienste verringern durch Nutzung der Programmiermodelle [Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md) und [Reliable Actors](service-fabric-reliable-actors-introduction.md) die Komplexität der Anwendung. Gleichzeitig wird eine hohe Durchsatzrate mit geringer Latenz erzielt.

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
 
 

<!---HONumber=Oct15_HO3-->