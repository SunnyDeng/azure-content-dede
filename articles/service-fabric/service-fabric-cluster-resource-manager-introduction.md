<properties
   pageTitle="Einführung in den Clusterressourcen-Manager von Service Fabric | Microsoft Azure"
   description="Eine Einführung in den Clusterressourcen-Manager von Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Einführung in den Clusterressourcen-Manager von Service Fabric
Zur Verwaltung von IT-Systemen oder einer Gruppe von Diensten wurden üblicherweise verschiedene Computer beschafft und für diese Dienste oder Systeme eingerichtet. Viele wichtige Dienste wurden in eine „Web“- und eine „Daten“- oder „Speicher“-Schicht unterteilt, ggf. mit anderen speziellen Komponenten wie einem Cache. Andere Anwendungstypen verfügten über eine Messagingschicht, auf der Anforderungen ein- und ausgehen, und waren mit einer Verarbeitungsschicht verbunden, auf der die für das Messaging erforderlichen Analysen und Transformationen erfolgen. Für jeden Aufgabenbereich gab es bestimmte fest zugeordnete Computer oder Computergruppen, so z. B. für die Datenbank und die Webserver. Wenn ein bestimmter Workload eine Überlastung bestimmter Computer bewirkte, wurden weitere für die Ausführung dieses Workloads konfigurierte Computer hinzugefügt, oder einige der Computer wurden durch größere Computer ersetzt. Ganz einfach. Wenn ein Computer ausfiel, wurde dieser Teil der gesamten Anwendung mit niedriger Kapazität ausgeführt, bis der Computer wiederhergestellt werden konnte. Noch immer recht einfach (wenn auch nicht unbedingt spaßig).

Lassen Sie uns nun annehmen, dass es einen Bedarf an einer horizontalen Hochskalierung gibt und Sie sich für den Ansatz mit Containern und/oder Microservices entschieden haben. Plötzlich sehen Sie sich Hunderten oder sogar Tausenden Computern, Dutzenden von verschiedenen Typen von Diensten, womöglich Hunderten von verschiedenen Instanzen dieser Dienste gegenüber, von denen jeder zum Zweck der Hochverfügbarkeit über eine oder mehrere Instanzen oder Replikate verfügt.

Dann ist die Verwaltung Ihrer Umgebung nicht mehr so einfach wie das Verwalten einiger Computer, die einzelnen Typen von Workloads fest zugeordnet sind. Ihre Server sind virtuell und haben keine Namen mehr (denn Ihr Modell hat sich schließlich von einem [Haustierbestand in eine Rinderherde](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) geändert), und Computer sind nicht einzelnen Typen von Workloads zugeordnet. Die Konfiguration dreht sich weniger um die Computer und mehr um die Dienste selbst.

Als Folge dieser Entkopplung und Aufteilung Ihrer zuvor monolithisch geschichteten App in verschiedene Dienste müssen Sie nun mit wesentlich mehr Kombinationen zurechtkommen. Wer entscheidet, welche Arten von Workloads auf spezieller Hardware oder nebeneinander ausgeführt werden können? Wenn ein Computer ausfällt... was wurde nochmal darauf ausgeführt? Wer ist verantwortlich, dass er wieder den Betrieb aufnimmt? Warten Sie, bis der (virtuelle) Computer wieder aktiv wird, oder werden Ihre Workloads weiter ausgeführt? Sind Benutzereingriffe erforderlich, um den Betrieb wieder aufzunehmen? Und was ist mit Upgrades in dieser Art von Umgebung? Wir werden Hilfe brauchen, und Ihnen wird klar, dass eine Einstellungswelle und der Versuch, die Komplexität mit Personal in den Griff zu bekommen, nicht die richtige Antwort ist.

Vorgehensweise

## Einführung in Orchestratoren
„Orchestrator“ ist der allgemeine Begriff für eine Softwarekomponente, mit deren Hilfe Administratoren diese Arten der Bereitstellung verwalten können. Orchestratoren sind die Komponenten, die Anforderungen wie „Ich möchte 5 Kopien dieses Diensts in meiner Umgebung ausführen“ erfüllen und versuchen, diesen Zustand zu erhalten. Orchestratoren (nicht Menschen) kommen zum Einsatz, wenn ein Computer ausfüllt oder ein Workload aus einem unerwarteten Grund nicht abgeschlossen wird. Die meisten Orchestratoren sind nicht nur mit Ausfällen beschäftigt, sondern helfen auch bei Neubereitstellungen, Upgrades und Optimierung der Ressourcennutzung. Doch grundlegend geht es stets um das Beibehalten eines gewünschten Konfigurationszustands in der Umgebung. Sie möchten einem Orchestrator angeben können, was Sie wünschen, und ihm die Hauptarbeit überlassen. Chronos auf Basis von Mesosphere, Fleet, Swarm, Kubernetes und Service Fabric sind allesamt Orchestratoren oder verfügen über integrierte Orchestratoren. Weitere werden ständig entwickelt, da die Komplexität der Verwaltung realer Bereitstellungen in verschiedenen Arten von Umgebungen und unter unterschiedlichen Bedingungen zunimmt und sich ändert.

## Orchestrierung als Dienst
Die Aufgabe des Orchestrators in einem Service Fabric-Cluster übernimmt hauptsächlich der Clusterressourcen-Manager. Der Clusterressourcen-Manager von Service Fabric ist einer der Systemdienste in Service Fabric, der in jedem Cluster automatisch gestartet wird. Im Allgemeinen kümmert sich der Ressourcen-Manager um drei Aufgaben:

1. Erzwingen von Regeln
2. Optimieren der Umgebung
3. Unterstützen anderer Prozesse

Bevor wir alle Funktionen des Ressourcen-Managers in Augenschein nehmen, werfen wir einen kurzen Blick auf seine Funktionsweise. Der Clusterressourcen-Manager von Service Fabric

## Allgemeine Architektur und Implementierung
Bevor wir die Beschreibung des Clusterressourcen-Managers und aller seiner Features vertiefen, wollen wir uns zunächst ansehen, wie eigentlich seine Funktionsweise ist.

### Was der Clusterressourcen-Manager nicht ist
Bei herkömmlichen Web-Apps mit „n“ Schichten gab es stets einen bestimmten „Lastenausgleich“ (auch Netzwerk- oder Anwendungslastenausgleich [NLB/ALB]), und zwar abhängig von dessen Position im Netzwerkstapel. Einige Load Balancer sind hardwarebasiert, wie z. B. das Big-IP-Angebot von F5, während andere softwarebasiert sind, wie z. B. Microsoft-Netzwerklastenausgleich (NLB). In diesen Architekturen ist es die Aufgabe des Lastenausgleichs sicherzustellen, dass alle zustandslosen Front-End-Computer bzw. die verschiedenen Computer im Cluster (ungefähr) dieselbe Verarbeitungslast erhalten. Hierfür gab es verschiedene Strategien, wie z. B. das Senden jedes unterschiedlichen Aufrufs an einen anderen Server, die Sitzungsbindung oder die tatsächliche Schätzung und Aufrufzuteilung basierend auf den erwarteten Kosten und der aktuellen Computerlast.

Beachten Sie, dass dies bestenfalls ein Mechanismus war, um sicherzustellen, dass die Webschicht ausgewogen ausgelastet war. Strategien für einen Lastenausgleich auf der Datenschicht waren vollkommen anders und hingen vom Datenspeichermechanismus ab, wobei der Fokus meist auf dem Sharding und Zwischenspeichern von Daten, von der Datenbank verwalteten Sichten und gespeicherten Prozeduren usw. lag.

Während einige dieser Strategien interessant sind, ist der Clusterressourcen-Manager von Service Fabric mit einem NLB oder Cache nicht vergleichbar. Ein NLB stellt sicher, dass die Front-Ends gleichmäßig ausgelastet sind, indem Datenverkehr dorthin verlagert wird, wo die Dienste ausgeführt werden. Der Ressourcen-Manager von Service Fabric verfolgt einen vollkommen anderen Ansatz, denn Service Fabric verschiebt Dienste dorthin, wo es am sinnvollsten ist. Dies können z. B. Knoten sein, die nicht viel zu tun haben, da die vorhandenen Dienste gerade nicht viel Arbeit verrichten. Es können auch Knoten vermieden werden, die kurz vor einem Upgrade stehen oder aufgrund einer Nutzungsspitze der Dienste, die darauf ausgeführt werden, überlastet sind. Da der Ressourcen-Manager von Service Fabric für das Verschieben von Diensten (und nicht das Übermitteln von Netzwerkverkehr dorthin, wo sich Dienste bereits befinden) zuständig ist, ist der Ressourcen-Manager vielseitiger und bietet außerdem zusätzliche Funktionen für das Steuern, wohin und wie Dienste verschoben werden.

## Nächste Schritte
- Informationen über die Architektur und den Informationsfluss innerhalb des Clusterressourcen-Managers finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-architecture.md).
- Der Clusterressourcen-Manager bietet viele Optionen für die Beschreibung des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).
- Weitere Informationen zu den anderen Optionen, die für die Konfiguration von Diensten zur Verfügung stehen, finden Sie im Thema zu den anderen verfügbaren Clusterressourcen-Manager-Konfigurationen, [Konfigurieren von Diensten](service-fabric-cluster-resource-manager-configure-services.md).
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
- Der Clusterressourcen-Manager arbeitet mit den Verwaltungsfunktionen von Service Fabric. Weitere Informationen hierzu finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-management-integration.md)
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md).

<!---HONumber=AcomDC_0316_2016-->