<properties
   pageTitle="Umgebungsunabhängige Bereitstellung mit Service Fabric (Windows Server und Linux)| Microsoft Azure"
   description="Service Fabric-Cluster können unter Windows Server und Linux ausgeführt werden, sodass Service Fabric-Anwendungen überall dort bereitgestellt und gehostet werden können, wo Windows Server und Linux ausgeführt werden können."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/16/2015"
   ms.author="kunalds"/>

# Umgebungsunabhängige Bereitstellung (Deploy Anywhere) unter Windows Server und Linux mit Service Fabric
Service Fabric ermöglicht die Erstellung von Service Fabric-Clustern auf VMs oder Computern unter Windows Server oder Linux. Dies ermöglicht Service Fabric-Anwendungen die Bereitstellung und Ausführung in beliebigen Umgebungen, ob lokal oder in der Cloud, in denen sich verschiedene miteinander verbundene Computer mit Windows Server oder Linux befinden. Service Fabric bietet ein Setup-Paket zum Erstellen von Service Fabric-Clustern. Ein entscheidender Vorteil dieses Features ist, dass es beim Erstellen einer Anwendung mit Service Fabric keine Anbieterabhängigkeit gibt, da Sie wählen, wo diese Anwendung ausgeführt werden. Dadurch bietet sich auch ein größerer Potenzial, einen größeren Kundenstamm zu erreichen, da verschiedene Kunden unterschiedliche Anforderungen an die Umgebung zur Ausführung Ihrer Anwendungen haben können. Unternehmen im Gesundheitswesen und Finanzsektor haben ggf. andere Anforderungen als ein Autohersteller oder Reiseanbieter. Die Technical Preview-Version dieses Features wird voraussichtlich im 1. Quartal 2016 veröffentlicht.

## Unterstützte Betriebssysteme
Sie können Cluster auf VMs oder Computern mit diesen Betriebssystemen erstellen: Windows Server 2012 R2, Windows Server 2016 und Linux

## Unterstützte Programmiersprachen
Sie können Service Fabric-Anwendungen mit diesen Programmiersprachen schreiben: C# und Java

## Erstellung und Konfiguration von Clustern
Service Fabric bietet ein Setup-Paket, das Sie aus dem [Microsoft Download Center](https://www.microsoft.com/de-DE/download) herunterladen können. Nachdem Sie dieses Paket heruntergeladen haben, müssen Sie eine Konfigurationsdatei bearbeiten, indem Sie die Einstellungen für Ihren Cluster angeben. Nachdem die Clustereinstellungen bearbeitet wurden, führen Sie ein Setupskript aus, mit dessen Hilfe der Cluster auf den Computern erstellt wird, die Sie in Ihren Clustereinstellungen angegeben haben. Die genauen Details des Setupvorgangs werden bekanntgegeben, sobald die Technical Preview-Version dieser Funktionalität im 1. Quartal 2016 veröffentlicht wird.

## Clusterverwaltung 
Für Updates des Clusters müssen Sie die Clustereinstellungsdatei ändern und ein Updateskript ausführen, damit die Änderungen wirksam werden.

## Cloudbereitstellungen im Vergleich zu lokalen Bereitstellungen
Der Prozess zum lokalen Erstellen eines Service Fabric-Clusters wird vergleichbar sein mit dem Prozess zum Erstellen eines Clusters in einer Cloud Ihrer Wahl, in der Sie über eine Gruppe von VMs verfügen. Die ersten Schritte zum Bereitstellen der VMs werden von der Cloud oder lokalen Umgebung gesteuert, die Sie nutzen. Doch sobald Sie über eine Gruppe von VMs verfügen, die über ein Netzwerk verbunden sind, sind die nächsten Schritte zum Einrichten des Service Fabric-Pakets, Bearbeiten der Clustereinstellungen und Ausführen der Skripts zum Erstellen und Verwalten des Clusters ähnlich. Dadurch wird sichergestellt, dass Ihre Kompetenz und Erfahrung beim Betreiben und Verwalten von Service Fabric-Clustern bei Wahl einer neuen Hostingumgebung übertragbar sind.

## Vorteile des Features „Deploy Anywhere“ (umgebungsunabhängige Bereitstellung)
* Keine Anbieterabhängigkeit.
* Einmal entwickelte Service Fabric-Anwendungen können bei minimalen oder sogar ohne Änderungen in mehreren Hostingumgebungen ausgeführt werden.
* Kenntnisse der Erstellung von Service Fabric-Anwendungen lassen sich auf andere Hostingplattformen übertragen.
* Erfahrungen beim Betrieb von Service Fabric-Clustern lassen sich von einer Bereitstellungsumgebung auf andere übertragen.
* Die Kundenreichweite wird nicht durch Einschränkungen bei der Hostingumgebung begrenzt.
* Zusätzliches Maß an Zuverlässigkeit und Schutz vor weitreichenden Ausfällen, indem die Dienste in eine andere Bereitstellungsumgebung verlagert werden, sollte es in einem Datencenter oder bei einem Cloudanbieter einen Totalausfall geben.

## Unterschiede zwischen der umgebungsunabhängigen Bereitstellung und in Azure gehosteten Service Fabric-Clustern
Das Betreiben von Service Fabric-Clustern mithilfe von Azure Hosted Service bietet verschiedene Vorteile im Vergleich zur Option der umgebungsunabhängigen Bereitstellung. Wenn also keine besonderen Anforderungen vorliegen, wo Sie Ihre Cluster ausführen, empfehlen wir deren Ausführung als in Azure gehostete Cluster. In Azure bieten wir die Integration mit anderen Azure-Features und -Diensten, die Betrieb und Verwaltung des Clusters vereinfachen:* Azure-Portal: Das Azure-Portal erleichtert das Erstellen und Verwalten von Clustern.* Azure-Ressourcen-Manager: Der ARM vereinfacht die Verwaltung aller vom Cluster genutzten Ressourcen als Einheit sowie die Kostentransparenz und Abrechnung.* Diagnose: In Azure bieten wir Ihnen Integration mit der Azure-Diagnose und Operational Insights zu Diagnosezwecken.* Automatische Skalierung: Für in Azure gehostete Cluster bieten wir die integrierte automatische Skalierungsfunktionalität. Bei Verwenden der umgebungsunabhängigen Bereitstellung in anderen Umgebungen müssen Sie eine eigene automatische Skalierungsfunktion entwickeln oder eine manuelle Skalierung mithilfe der APIs vornehmen, die Service Fabric für die Skalierung von Clustern verfügbar macht.

<!---HONumber=Nov15_HO4-->