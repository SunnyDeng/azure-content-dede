<properties 
   pageTitle="Übersicht über Service Fabric" 
   description="Eine Übersicht über Service Fabric und das Konzept der Anwendungen, die sich aus Microservices zusammensetzen. Service Fabric ist eine Plattform für verteilte Systeme zum Erstellen skalierbarer, zuverlässiger und einfach zu verwaltender Anwendungen für die Cloud." 
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
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Übersicht über Service Fabric
Service Fabric ist eine Plattform für verteilte Systeme zum Erstellen skalierbarer, zuverlässiger und einfach zu verwaltender Anwendungen für die Cloud. Service Fabric bietet einfache Lösungen für die komplexen Herausforderungen bei der Entwicklung und Verwaltung von Cloudanwendungen. Mit Service Fabric gehören komplexe Infrastrukturprobleme der Vergangenheit an. Stattdessen können sich Entwickler und Administratoren auf das Implementieren geschäftskritischer, anspruchsvoller Workloads konzentrieren, die skalierbar, zuverlässig und einfach zu verwalten sind. Service Fabric ist die Middlewareplattform der nächsten Generation für das Erstellen und Verwalten leistungsstarker Tier-1-Dienste mit Cloudskalierung.

## Aus Microservices bestehende Anwendungen ##
Service Fabric ermöglicht das Erstellen und Verwalten skalierbarer und zuverlässiger Anwendungen, die sich aus Microservices zusammensetzen, die in einem gemeinsam genutzten Computerpool (auch als Service Fabric-Cluster bezeichnet) mit sehr hoher Dichte ausgeführt werden. Die Plattform bietet eine ausgereifte Laufzeit für das Erstellen verteilter, skalierbarer zustandsloser und zustandsbehafteter Microservices sowie umfassende Verwaltungsfunktionen zum Organisieren, Bereitstellen, Überwachen, Aktualisieren/Patchen und Löschen bereitgestellter Anwendungen.

Service Fabric unterstützt bereits jetzt zahlreiche Microsoft-Dienste, darunter Azure SQL-Datenbank, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure Event Hubs, zentrale Azure Services, Skype for Business und viele mehr.

Dienst Fabric wurde konzipiert, um das Erstellen von Diensten zu ermöglichen, die direkt in der Cloud "entstehen". Die Dienste können zu Beginn klein ausfallen und je nach Bedarf auf Hunderte oder Tausenden von Computern skaliert werden, indem Service Fabric-Cluster in Verfügbarkeitsgruppen in einer oder mehreren Regionen erstellt werden.

Heutige Internetdienste setzen sich aus Microservices zusammen. Beispiele für Microservices sind Protokollgateways, Benutzerprofile, Einkaufswagen, Bestandsverarbeitungsvorgänge, Warteschlangen, Caches usw. Service Fabric ist eine Microservices-Plattform, in der jeder Microservice einen eindeutigen Namen erhält und entweder zustandslos oder zustandsbehaftet sein kann.

Service Fabric bietet umfassende Verwaltungsfunktionen für die Laufzeit und den Lebenszyklus der Anwendungen, die sich aus diesen Microservices zusammensetzen. Die in Containern gehosteten Microservices werden im Service Fabric-Cluster bereitgestellt und aktiviert. Der Anstieg der Dichte, der beim Verschieben von Containern auf Microservices erreicht wird, entspricht in etwa dem Anstieg der Dichte beim Verschieben von Computern auf Container. Ein einzelner mit Service Fabric erstellter Azure SQL Datenbank-Cluster kann beispielsweise aus Hunderten von Rechnern bestehen, die Zehntausende von Containern ausführen, die wiederum Hunderttausende von Datenbanken hosten (wobei jede Datenbank ein zustandsbehafteter Service Fabric-Microservice ist). Dies gilt auch für Event Hubs und die übrigen oben genannten Dienste. Aus diesem Grund trifft im Zusammenhang mit Service Fabric-Funktionen der Begriff "Hyperskalierung" zu: Container ermöglichen eine hohe Dichte und Microservices eine extrem hohe Skalierbarkeit.

![Service Fabric-Plattform][Image1]

## Zustandsbehaftete und zustandslose Service Fabric-Microservices

Zustandslose Microservices (z. B. Protokollgateways, Webproxys) behalten keinen veränderbaren Zustand über die Anforderung und ihre Antwort vom Dienst hinaus bei. Ein zustandsloser Dienst ist zum Beispiel die Workerrolle in Azure Cloud Services. Zustandsbehaftete Microservices (z. B. Benutzerkonten, Datenbanken, Geräte, Einkaufswagen, Warteschlangen) behalten einen veränderbaren, autorisierenden Zustand über die Anforderung und ihre Antwort hinaus bei. Heutige Internetanwendungen bestehen aus einer Kombination aus zustandslosen und zustandsbehafteten Microservices.
 
Warum sind zustandsbehaftete Microservices wichtig? Warum können nicht ausschließlich zustandslose Dienste verwendet werden? Hierfür gibt es zwei Gründe:

(1) Die Möglichkeit zum Erstellen fehlertoleranter OLTP-Dienste mit hohem Durchsatz und niedriger Latenz (z. B. interaktive Geschäfte, Suchfunktionen, Internet der Dinge (IoT)-Systeme, Handelssysteme, Kreditkartenverarbeitungs-, Betrugserkennungssysteme, Verwaltung personenbezogener Datensätze), indem Code und Daten nah beieinander auf dem gleichen Computer beibehalten werden.

2) Vereinfachung des Anwendungsentwurfs, da zustandsbehaftete Microservices zusätzliche Warteschlangen und Caches überflüssig machen, die bislang erforderlich waren, um Verfügbarkeits- und Latenzanforderungen einer rein zustandslosen Anwendung zu erfüllen. Da zustandsbehaftete Dienste an sich bereits hochverfügbar sind und eine niedrige Latenz bieten, fällt der Aufwand für die Verwaltung verschiebbarer Elemente in Ihrer Anwendung insgesamt geringer aus.

Weitere Informationen zu Anwendungsmustern und -entwürfen in Service Fabric finden Sie unter [Anwendungsszenarien](../service-fabric-application-scenarios).

## Anwendungslebenszyklusverwaltung
Service Fabric bietet erstklassige Unterstützung für die gesamte Anwendungslebenszyklusverwaltung (Application Lifecycle Management, ALM) für Cloudanwendungen: von der Entwicklung über die Bereitstellung, die tägliche Verwaltung und die Wartung bis zur endgültigen Außerbetriebnahme.

Dank der ALM-Funktionen von Service Fabric profitieren Anwendungsadministratoren/IT-Operatoren von einfache Workflows, um Anwendungen mit geringem Arbeitsaufwand vorzubereiten, bereitzustellen, zu patchen und zu überwachen. Diese integrierten Workflows reduzieren den Aufwand für IT-Operationen erheblich, der andernfalls erforderlich ist, um die kontinuierliche Verfügbarkeit von Anwendungen zu gewährleisten.

Die meisten Anwendungen bestehen aus einer Kombination aus zustandslosen und zustandsbehafteten Microservices und anderen ausführbaren Dateien/Laufzeiten, die gemeinsam bereitgestellt werden. Dank der leistungsstarken Typen für die Anwendungen und die gepackten Microservices in Service Fabric ist die Bereitstellung mehrerer Anwendungsinstanzen möglich, die unabhängig voneinander verwaltet und aktualisiert werden können. Maßgeblich dabei ist, dass Service Fabric *jede beliebige* ausführbare Datei oder Laufzeit zuverlässig bereitstellen kann. So können Sie beispielsweise ASP.NET 5, node.js, Skripts und alle anderen beliebigen Komponenten bereitstellen, aus denen sich Ihre Anwendung zusammensetzt.
  
Weitere Informationen zur Verwaltung des Anwendungslebenszyklus finden Sie unter [Anwendungslebenszyklus](../service-fabric-application-lifecycle).

## Wichtige Funktionen
Mit Service Fabric können Sie Folgendes tun:

- Hochgradig skalierbare Anwendungen mit Selbstreparatur entwickeln.

- Anwendungen nach dem Ansatz "Rechenzentrum auf dem eigenen Computer" entwickeln: Der Code für die lokale Entwicklungsumgebung ist mit dem Code, der in den Azure-Rechenzentren verwendet wird, identisch.
 
- Anwendungen bestehend aus Microservices, ausführbaren Dateien und anderen Anwendungsframeworks Ihrer Wahl (z. B. ASP.NET, node.js) entwickeln.

- Zustandslose und zustandsbehaftete hoch zuverlässige (Micro)Services entwickeln.

- Den Anwendungsentwurf durch die Nutzung zustandsbehafteter (Micro)Services anstelle von Caches und Warteschlangen vereinfachen.
 
- Anwendungen in Sekunden bereitstellen.

- Anwendungen in Azure oder lokalen Clouds mit Windows Server ohne Codeänderungen bereitstellen. Anwendungen einmal schreiben und in einem beliebigen Service Fabric-Cluster bereitstellen.

- Anwendungen mit höherer Dichte als bei Verwendung von virtuellen Computern bereitstellen; es können Hunderte oder Tausende von Anwendungen pro Computer bereitgestellt werden.

- Verschiedene Versionen derselben Anwendung gleichzeitig bereitstellen, die jeweils unabhängig voneinander aktualisiert werden können.
 
- Lebenszyklus zustandsbehafteter Anwendungen ohne Ausfallzeiten verwalten, einschließlich Upgrades mit und ohne Funktionsbeeinträchtigung.

- Anwendungen mit der Schnittstelle für .NET-APIs, PowerShell oder REST verwalten.
 
- Microservices innerhalb der Anwendungen unabhängig voneinander aktualisieren und patchen.

- Integrität der Anwendungen überwachen und diagnostizieren und Richtlinien für automatische Reparaturen festlegen.

- Service Fabric-Cluster auf einfache Weise nach oben oder unten skalieren und dabei sicher sein, dass die Skalierung der Anwendungen entsprechend den verfügbaren Ressourcen erfolgt.

- Den selbstreparierenden Ressource Balancer beim Organisieren einer erneuten Bereitstellung der Anwendungen im Service Fabric-Cluster anzeigen, um eine Wiederherstellung nach Fehlern durchzuführen oder die Lastenverteilung basierend auf den verfügbaren Ressourcen zu optimieren.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen finden Sie unter [Technische Übersicht](../service-fabric-technical-overview).

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=58_postMigration-->