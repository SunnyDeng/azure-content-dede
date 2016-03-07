<properties
   pageTitle="Übersicht über Service Fabric | Microsoft Azure"
   description="Eine Übersicht über Service Fabric und das Konzept der Anwendungen, die sich aus Microservices zusammensetzen, um Skalierung und Resilienz zu bieten. Service Fabric ist eine Plattform für verteilte Systeme und ermöglicht das Erstellen skalierbarer, zuverlässiger und einfach zu verwaltender Anwendungen für die Cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Übersicht über Service Fabric
Service Fabric ist eine Plattform für verteilte Systeme, über die skalierbare und zuverlässige Microservices einfach gepackt, bereitgestellt und verwaltet werden können und die Lösungen für die komplexen Herausforderungen bei der Entwicklung und Verwaltung von Cloudanwendungen bietet. Mit Service Fabric gehören komplexe Infrastrukturprobleme der Vergangenheit an. Stattdessen können sich Entwickler und Administratoren auf das Implementieren geschäftskritischer, anspruchsvoller Workloads konzentrieren, die skalierbar, zuverlässig und einfach zu verwalten sind. Service Fabric ist die Middlewareplattform der nächsten Generation für das Erstellen und Verwalten leistungsstarker Tier-1-Anwendungen mit Cloudskalierung.

## Aus Microservices bestehende Anwendungen
Service Fabric ermöglicht das Erstellen und Verwalten skalierbarer und zuverlässiger Anwendungen, die sich aus Microservices zusammensetzen, die in einem gemeinsam genutzten Computerpool (auch als Service Fabric-Cluster bezeichnet) mit sehr hoher Dichte ausgeführt werden. Die Plattform bietet eine ausgereifte Laufzeit für das Erstellen verteilter, skalierbarer zustandsloser und zustandsbehafteter Microservices sowie umfassende Anwendungsverwaltungsfunktionen zum Organisieren, Bereitstellen, Überwachen, Upgraden/Patchen und Löschen bereitgestellter Anwendungen.

Warum ist ein Microserviceansatz wichtig? Die beiden wichtigsten Gründe sind:

1. Mit Microservices können Sie verschiedene Teile einer Anwendung je nach den entsprechenden Anforderungen skalieren.

2. Entwicklungsteams können beim Einführen von Änderungen flexibler vorgehen und dadurch Funktionen für Ihre Kunden schneller und häufiger bereitstellen.

Service Fabric liegt schon heute zahlreichen Microsoft-Diensten zugrunde. Hierzu zählen unter anderem Azure SQL-Datenbank, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT, zentrale Azure Services, Skype for Business und viele mehr.

Service Fabric wurde konzipiert, um das Erstellen von Diensten zu ermöglichen, die direkt in der Cloud „entstehen“. Die Dienste können zu Beginn klein ausfallen und je nach Bedarf auf Hunderte oder Tausende von Computern skaliert werden.

Internetdienste setzen sich heutzutage aus Microservices zusammen. Beispiele für Microservices sind etwa Protokollgateways, Benutzerprofile, Einkaufswagen, Bestandsverarbeitungsvorgänge, Warteschlangen und Caches. Service Fabric ist eine Microservices-Plattform, in der jeder Microservice einen eindeutigen Namen erhält und entweder zustandslos oder zustandsbehaftet sein kann.

Service Fabric bietet umfassende Verwaltungsfunktionen für die Laufzeit und den Lebenszyklus der Anwendungen, die sich aus diesen Microservices zusammensetzen. Die in Containern gehosteten Microservices werden im Service Fabric-Cluster bereitgestellt und aktiviert. Die Zunahme der Dichte, die durch den Umstieg von Containern auf Microservices erreicht wird, entspricht in etwa der Zunahme der Dichte durch den Umstieg von virtuellen Maschinen auf Container. Ein einzelner, mit Service Fabric erstellter Azure SQL-Datenbank-Cluster kann beispielsweise aus Hunderten von Rechnern bestehen, die Zehntausende von Containern ausführen, die wiederum Hunderttausende von Datenbanken hosten. (Jede Datenbank ist dabei ein zustandsbehafteter Service Fabric-Microservice.) Dies gilt auch für Event Hubs und die übrigen oben genannten Dienste. Aus diesem Grund trifft im Zusammenhang mit Service Fabric-Funktionen der Begriff „Hyperskalierung“ zu. Container bieten eine hohe Dichte, Microservices eine extrem hohe Skalierbarkeit.

Weitere Informationen zum Microservice-Ansatz finden Sie im Artikel [Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen](service-fabric-overview-microservices.md).

![Service Fabric-Plattform][Image1]

## Zustandsbehaftete und zustandslose Service Fabric-Microservices

Zustandslose Microservices (Protokollgateways, Webproxys und Ähnliches) behalten über die Anforderung und ihre Antwort vom Dienst hinaus keinen veränderbaren Zustand bei. Ein zustandsloser Dienst ist zum Beispiel die Workerrolle in Azure Cloud Services. Zustandsbehaftete Microservices (Benutzerkonten, Datenbanken, Geräte, Einkaufswagen, Warteschlangen und Ähnliches) behalten einen veränderbaren, autorisierenden Zustand über die Anforderung und ihre Antwort hinaus bei. Heutige Internetanwendungen bestehen aus einer Kombination aus zustandslosen und zustandsbehafteten Microservices.

Warum können nicht ausschließlich zustandslose Dienste verwendet werden? Die beiden wichtigsten Gründe sind:

1. Die Möglichkeit zum Erstellen fehlertoleranter OLTP-Dienste mit hohem Durchsatz und niedriger Latenz (z. B. interaktive Geschäfte, Suchfunktionen, Internet der Dinge (IoT)-Systeme, Handelssysteme, Kreditkartenverarbeitungs- und Betrugserkennungssysteme sowie die Verwaltung personenbezogener Datensätze), indem Code und Daten ortsnah auf dem gleichen Computer behalten werden.

2. Vereinfachung des Anwendungsentwurfs, da zustandsbehaftete Microservices zusätzliche Warteschlangen und Caches überflüssig machen. Diese waren bislang erforderlich, um Verfügbarkeits- und Latenzanforderungen einer rein zustandslosen Anwendung zu erfüllen. Da zustandsbehaftete Dienste an sich bereits hochverfügbar sind und eine niedrige Latenz bieten, fällt der Verwaltungsaufwand in Ihrer Anwendung insgesamt geringer aus.

Weitere Informationen zu Anwendungsmustern und -entwürfen in Service Fabric finden Sie unter [Anwendungsszenarien](service-fabric-application-scenarios.md).

## Anwendungslebenszyklusverwaltung
Service Fabric bietet erstklassige Unterstützung für die gesamte Anwendungslebenszyklusverwaltung (Application Lifecycle Management, ALM) von Cloudanwendungen: von der Entwicklung über die Bereitstellung, die tägliche Verwaltung und die Wartung bis hin zur endgültigen Außerbetriebnahme.

Dank der ALM-Funktionen von Service Fabric profitieren Anwendungsadministratoren/IT-Operatoren von einfache Workflows, um Anwendungen mit geringem Arbeitsaufwand vorzubereiten, bereitzustellen, zu patchen und zu überwachen. Diese integrierten Workflows entlasten IT-Operatoren bei der Gewährleistung der kontinuierlichen Anwendungsverfügbarkeit erheblich.

Die meisten Anwendungen bestehen aus einer Kombination aus zustandslosen und zustandsbehafteten Microservices und anderen ausführbaren Dateien/Laufzeiten, die gemeinsam bereitgestellt werden. Dank leistungsstarker Typen für Anwendungen und gepackte Microservices ermöglicht Service Fabric die Bereitstellung mehrerer Anwendungsinstanzen, die jeweils unabhängig voneinander verwaltet und aktualisiert werden können. Maßgeblich dabei ist, dass Service Fabric *jede beliebige* ausführbare Datei oder Laufzeit zuverlässig bereitstellen kann. So können Sie beispielsweise ASP.NET 5, Node.js, Skripts und alle anderen beliebigen Komponenten bereitstellen, aus denen sich Ihre Anwendung zusammensetzt.

Weitere Informationen zur Verwaltung des Anwendungslebenszyklus finden Sie unter [Anwendungslebenszyklus](service-fabric-application-lifecycle.md).

## Wichtige Funktionen
Mit Service Fabric können Sie Folgendes tun:

- Hochgradig skalierbare Anwendungen mit Selbstreparatur entwickeln.

- Anwendungen nach dem Modell „Rechenzentrum auf dem eigenen Computer“ entwickeln: Der Code für die lokale Entwicklungsumgebung ist der gleiche Code, der auch in den Azure-Rechenzentren verwendet wird.

- Anwendungen bestehend aus Microservices mit dem Service Fabric-Programmiermodell entwickeln oder einfach ausführbare Gastanwendungsdateien und andere Anwendungsframeworks Ihrer Wahl hosten, z. B. ASP.NET oder Node.js.

- Zustandslose und zustandsbehaftete hoch zuverlässige Microservices entwickeln.

- Den Anwendungsentwurf durch die Nutzung zustandsbehafteter Microservices anstelle von Caches und Warteschlangen vereinfachen.

- Anwendungen in Sekunden bereitstellen.

- Anwendungen ohne Codeänderungen in Azure oder lokalen Clouds mit Windows Server oder Linux bereitstellen. Anwendungen einmal schreiben und an beliebiger Stelle in einem beliebigen Service Fabric-Cluster bereitstellen.

- Anwendungen mit höherer Dichte als bei Verwendung von virtuellen Computern bereitstellen; es können Hunderte oder Tausende von Anwendungen pro Computer bereitgestellt werden.

- Verschiedene Versionen derselben Anwendung gleichzeitig bereitstellen, die jeweils unabhängig voneinander aktualisiert werden können.

- Lebenszyklus zustandsbehafteter Anwendungen ohne Ausfallzeiten verwalten – einschließlich Upgrades mit und ohne Funktionsbeeinträchtigung.

- Anwendungen mit .NET-APIs, PowerShell oder REST-Schnittstellen verwalten.

- Microservices innerhalb der Anwendungen unabhängig voneinander aktualisieren und patchen.

- Integrität der Anwendungen überwachen und diagnostizieren und Richtlinien für automatische Reparaturen festlegen.

- Service Fabric-Cluster auf einfache Weise skalieren und dabei sicher sein, dass die Skalierung der Anwendungen unter Berücksichtigung der verfügbaren Ressourcen erfolgt.

- Den selbstreparierenden Resource Balancer beim Organisieren einer erneuten Bereitstellung der Anwendungen im Service Fabric-Cluster überwachen, um nach Fehlern eine Wiederherstellung durchzuführen oder die Lastenverteilung basierend auf den verfügbaren Ressourcen zu optimieren.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

* Weitere Informationen:
	* [Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen](service-fabric-overview-microservices.md)
	* [Technische Übersicht](service-fabric-technical-overview.md)
* Einrichten der Service Fabric-[Entwicklungsumgebung](service-fabric-get-started.md)  
* Auswählen eines [Programmiermodell-Frameworks](service-fabric-choose-framework.md) für Ihren Dienst


[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0224_2016-->