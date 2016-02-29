<properties
   pageTitle="Einführung in Azure Container Service | Microsoft Azure"
   description="Azure Container Service (ACS) bietet eine Möglichkeit zur Vereinfachung der Erstellung, Konfiguration und Verwaltung eines Clusters virtueller Computer, die für die Ausführung von Anwendungen in Containern vorkonfiguriert sind."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Container, Microservices, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Einführung in Azure Container Service

Azure Container Service (ACS) bietet eine Möglichkeit zur Vereinfachung der Erstellung, Konfiguration und Verwaltung eines Clusters virtueller Computer, die für die Ausführung von Anwendungen in Containern vorkonfiguriert sind. Dank der Verwendung einer optimierten Konfiguration von beliebten Open Source-Planungs- und -Orchestrierungstools gestattet Ihnen ACS, Ihre vorhandenen Kenntnisse zu nutzen, bzw. den Rückgriff auf einen großen und wachsenden Pool von Communityfachkenntnissen zur Bereitstellung und Verwaltung von containerbasierten Anwendungen in Microsoft Azure.

<br /> ![ACS bietet eine Möglichkeit zum Verwalten von in Containern ausgeführten Anwendungen auf mehreren Hosts in Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

ACS nutzt Docker, um sicherzustellen, dass Ihre Anwendungscontainer vollständig portierbar sind. Es unterstützt außerdem Ihre Auswahl von Marathon, Chronos und Apache Mesos oder Docker Swarm, um sicherzustellen, dass diese Anwendungen auf Tausende und sogar Zehntausende von Containern skaliert werden können.

Der Azure Container Service ermöglicht Ihnen die Nutzung der Unternehmensfunktionen von Azure, während gleichzeitig die Anwendungsportierbarkeit erhalten bleibt, auch auf den Orchestrierungsebenen.

Verwenden von Azure Container Service
-----------------------------

Unser Ziel mit dem Azure Container Service ist es, unter Verwendung von Open Source-Tools und -Technologien, die heutzutage bei unseren Kunden beliebt sind, eine Containerhostingumgebung bereitzustellen. Zu diesem Zweck machen wir die Standard-API-Endpunkte für Docker und den von Ihnen ausgewählten Orchestrator verfügbar. Mithilfe dieser Endpunkte können Sie jede Software nutzen, die mit diesen Endpunkten kommunizieren kann. Beispielsweise können Sie im Fall des Docker Swarm-Endpunkts Docker Compose auswählen, während Sie sich bei Apache Mesos für die Verwendung der DCOS CLI entscheiden können.

Erstellen eines Docker-Clusters mithilfe von Azure Container Service
-------------------------------------------------------

Um mit der Nutzung des Azure Container Service (ACS) zu beginnen, wird ein ACS-Cluster mithilfe einer Azure-Ressourcen-Manager-Vorlage bereitgestellt. Diese Bereitstellung kann mit verschiedenen Größen und Verfügbarkeitsoptionen konfiguriert werden, wozu Apache Mesos oder Docker Swarm verwendet wird. Azure-Ressourcen-Manager-Vorlagen können über den Azure-Port, die Azure-Befehlszeilenschnittstelle oder PowerShell bereitgestellt werden. Die Vorlagen können auch so geändert werden, dass sie eine zusätzliche oder erweiterte Azure-Konfiguration enthalten. Weitere Informationen zu Bereitstellung und ACS-Clustern finden Sie unter [Bereitstellen eines Azure Container Service-Clusters](./container-service-deployment.md).

Bereitstellen einer Anwendung
------------------------

Während der Vorschauphase bieten wir eine Auswahl zwischen Docker Swarm und Apache Mesos (mit DCOS Marathon- und DCOS Chronos-Frameworks) als Orchestrierung.

### Verwenden von Apache Mesos

Apache Mesos ist ein Open Source-Projekt, das bei der Apache Software Foundation beheimatet ist. Es führt einige der [größten Namen der IT-Branche](http://mesos.apache.org/documentation/latest/powered-by-mesos/) als Benutzer und Mitwirkende auf.

![Für Swarm konfigurierter ACS mit angezeigten Agents und Mastern.](media/acs-intro/acs-mesos.png)

Mesos weist einen beeindruckenden Funktionsumfang auf.

-   Skalierbarkeit auf bis zu 10.000 Knoten

-   Fehlertolerant replizierte Master und Slaves mithilfe von ZooKeeper

-   Unterstützung für Docker-Container

-   Systemeigene Isolierung zwischen Aufgaben mit Linux-Containern

-   Zeitplanung von mehreren Ressourcen (Arbeitsspeicher, CPU, Datenträger und Ports)

-   Java-, Python- und C++-APIs für die Entwicklung neuer paralleler Anwendungen

-   Webbenutzeroberfläche zum Anzeigen von Clusterzuständen

Mesos bietet Unterstützung für eine große Anzahl von [Frameworks](http://mesos.apache.org/documentation/latest/frameworks/), die für die Planung von Arbeitslasten im Azure Container Service verwendet werden können. Standardmäßig enthält ACS die Marathon- und Chronos-Frameworks.

#### Verwenden von Marathon und Chronos

Marathon ist ein clusterweites Initialisierungs- und Kontrollsystem für Dienste in cgroups oder, im Fall von ACS, in Docker-Containern. Es ist ein idealer Partner für Chronos, einen fehlertoleranten Auftragsplaner für Mesos, der Abhängigkeiten und zeitbasierte Pläne verarbeitet.

Marathon und Chronos bieten eine Webbenutzeroberfläche, über die Sie Ihre Anwendung bereitstellen können. Zugriff auf diese erfolgt über eine URL, die etwa wie `http://DNS\_PREFIX.REGION.cloudapp.azure.com` aussieht, wobei sowohl DNS\_PREFIX als auch REGION zur Zeit der Bereitstellung definiert werden. Natürlich können Sie auch Ihren eigenen DNS-Namen angeben. Weitere Informationen zum Ausführen eines Containers auf der Marathon-Webbenutzeroberfläche finden Sie unter [Containerverwaltung über die Webbenutzeroberfläche](./container-service-mesos-marathon-ui.md).

Sie können auch die REST-APIs für die Kommunikation mit Marathon und Chronos verwenden. Es gibt eine Reihe von verfügbaren Clientbibliotheken für jedes Tool, die eine Vielzahl von Sprachen abdecken, und natürlich können Sie auch das HTTP-Protokoll in jeder Sprache verwenden. Darüber hinaus bieten viele beliebte DevOps-Tools Unterstützung für diese Planer. Dies bietet maximale Flexibilität für Ihr Arbeitsteam, wenn es mit einem ACS-Cluster arbeitet. Weitere Informationen zum Ausführen eines Containers mithilfe der Marathon-REST-API finden Sie unter [Containerverwaltung über die REST-API](./container-service-mesos-marathon-rest.md).

### Verwenden von Docker Swarm

Docker Swarm bietet eine systemeigene Clusterfunktion für Docker. Da Docker die Standard-API von Docker Swarm bedient, kann jedes Tool, das bereits mit einem Docker-Daemon kommuniziert, Swarm verwenden, um transparent auf mehreren Hosts in Azure Container Service zu skalieren.

![Für die Verwendung von Apache Mesos konfigurierter ACS mit angezeigter Jumpbox, Agents und Mastern.](media/acs-intro/acs-swarm.png)

Unterstützte Tools für die Verwaltung von Containern auf einem Swarm-Cluster sind u. a. folgende:

-   Dokku

-   Docker CLI und Docker Compose

-   Krane

-   Jenkins

Videos
------
AzureCon-Ankündigung:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Erste Schritte mit ACS:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0218_2016-->