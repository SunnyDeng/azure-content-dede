<properties
   pageTitle="Umgebungsunabhängige Bereitstellung mit Azure Service Fabric unter Windows Server und Linux| Microsoft Azure"
   description="Service Fabric-Cluster können unter Windows Server und Linux ausgeführt werden, sodass Service Fabric-Anwendungen überall dort bereitgestellt und gehostet werden können, wo Windows Server oder Linux ausgeführt werden können."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/10/2016"
   ms.author="chackdan"/>

# Umgebungsunabhängige Bereitstellung (Deploy Anywhere) unter Windows Server und Linux mit Service Fabric
Mit „Deploy Anywhere“ (umgebungsunabhängige Bereitstellung) ermöglicht Azure Service Fabric die Erstellung von Service Fabric-Clustern auf beliebigen VMs oder Computern unter Windows Server oder Linux. Dadurch lassen sich Service Fabric-Anwendungen in jeder lokalen oder cloudbasierten Umgebung bereitstellen und ausführen, in der sich miteinander verbundene Computer unter Windows Server oder Linux befinden.

 Service Fabric bietet ein Setuppaket zum Erstellen von Service Fabric-Clustern. Ein entscheidender Vorteil des Deploy Anywhere-Features besteht darin, dass Sie beim Erstellen einer Anwendung mit Service Fabric unabhängig vom Anbieter entscheiden können, wo die Anwendungen ausgeführt werden sollen. Dadurch können Sie sich einen größeren Kundenstamm erschließen, da Kunden hinsichtlich der Umgebungen, in denen sie Ihre Anwendungen ausführen möchten, unter Umständen unterschiedliche Anforderungen haben. So haben Unternehmen im Gesundheitswesen und Finanzsektor ggf. andere Anforderungen als ein Autohersteller oder Reiseanbieter.

Die Technical Preview-Version dieses Features wird voraussichtlich im 1. Quartal 2016 veröffentlicht.

## Unterstützte Betriebssysteme
Sie können Cluster auf virtuellen Computern oder Computern unter folgenden Betriebssystemen erstellen: * Windows Server 2012 * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Unterstützte Programmiersprachen
Service Fabric-Anwendungen können mit folgenden Programmiersprachen erstellt werden: * C# * Java

## Erstellung und Konfiguration von Clustern
Service Fabric umfasst ein Setup-Paket, das Sie herunterladen können. Nach dem Herunterladen dieses Pakets müssen Sie eine Konfigurationsdatei bearbeiten, um die Einstellungen für Ihren Cluster anzugeben. Nach der Bearbeitung der Clustereinstellungen muss ein Setupskript ausgeführt werden, das den Cluster mit den Computern erstellt, die Sie in Ihren Clustereinstellungen angegeben haben.

Die genauen Details des Setupvorgangs werden bekanntgegeben, sobald die Preview-Version dieser Funktionalität im 1. Quartal 2016 veröffentlicht wird.

## Gegenüberstellung von cloudbasierten und lokalen Bereitstellungen
Der Prozess zum lokalen Erstellen eines Service Fabric-Clusters ist vergleichbar mit der Erstellung eines Clusters in einer beliebigen Cloud mit einer Gruppe von VMs. Die ersten Schritte zum Bereitstellen der VMs sind abhängig von Ihrer Cloudumgebung bzw. von Ihrer lokalen Umgebung. Sobald Sie jedoch über eine Gruppe von VMs verfügen, die über ein Netzwerk verbunden sind, sind die Schritte zum Einrichten des Service Fabric-Pakets, Bearbeiten der Clustereinstellungen und Ausführen der Skripts zum Erstellen und Verwalten des Clusters ähnlich. Dadurch ist sichergestellt, dass Ihre Kompetenz und Erfahrung beim Betreiben und Verwalten von Service Fabric-Clustern bei Bedarf auf andere Hostingumgebungen übertragbar sind.

## Vorteile des Deploy Anywhere-Features
* Aufgrund fehlender Anbieterabhängigkeit können Sie frei wählen, wo Ihre Anwendungen ausgeführt werden sollen.
* Einmal entwickelte Service Fabric-Anwendungen können mit minimalen oder sogar ganz ohne Änderungen in mehreren Hostingumgebungen ausgeführt werden.
* Kenntnisse der Erstellung von Service Fabric-Anwendungen lassen sich auf andere Hostingplattformen übertragen.
* Erfahrungen beim Betrieb von Service Fabric-Clustern lassen sich von einer Bereitstellungsumgebung auf andere übertragen.
* Die Kundenreichweite wird nicht durch Einschränkungen bei der Hostingumgebung begrenzt.
* Die Möglichkeit, Dienste im Falle eines Ausfalls in einem Datencenter oder bei einem Cloudanbieter in eine andere Bereitstellungsumgebung zu verlagern, sorgt für ein zusätzliches Maß an Zuverlässigkeit und schützt vor weitreichenden Ausfällen.

## Vorteile verwalteter Service Fabric-Cluster unter Azure gegenüber Service Fabric-Clustern, die mit Deploy Anywhere erstellt und verwaltet werden
Die Verwendung von Service Fabric-Clustern unter Azure bietet gegenüber der Deploy Anywhere-Option einige Vorteile. Wenn Sie also hinsichtlich des Ausführungsorts Ihrer Cluster keine besondere Präferenz haben, empfiehlt sich die Ausführung unter Azure. Unter Azure profitieren Sie von der Integration anderer Azure-Features und -Dienste, die den Betrieb und die Verwaltung des Clusters vereinfachen.

* **Azure-Portal:** Erleichtert das Erstellen und Verwalten von Clustern.
* **Azure-Ressourcen-Manager:** Vereinfacht die Verwaltung aller vom Cluster genutzten Ressourcen als Einheit sowie die Abrechnung und sorgt für Kostentransparenz.
* Ein **Azure Service Fabric**-Cluster ist eine ARM-Ressource, sodass Sie ihn genau wie andere ARM-Ressourcen in Azure modellieren können.
* **Diagnose:** Integration mit Azure Diagnostics und Operational Insights.
* **Automatische Skalierung:** Integrierte automatische Skalierungsfunktion für in Azure gehostete Cluster. Bei Verwendung der umgebungsunabhängigen Bereitstellung in anderen Umgebungen müssen Sie eine eigene automatische Skalierung entwickeln oder mithilfe der APIs, die Service Fabric für die Skalierung von Clustern verfügbar macht, eine manuelle Skalierung vornehmen.

<!---HONumber=AcomDC_0224_2016-->