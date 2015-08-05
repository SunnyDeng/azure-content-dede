<properties
   pageTitle="Service Fabric-Architektur"
   description="Service Fabric ist eine Plattform für verteilte Systeme zum Erstellen skalierbarer, zuverlässiger und einfach zu verwaltender Anwendungen für die Cloud. Dieser Artikel beschreibt die Architektur von Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="rsinha"/>

# Service Fabric-Architektur

Service Fabric baut wie jede andere Plattform auf mehreren Subsystemen auf. Die Subsysteme sind über verschiedene Ebenen miteinander verbundenen und ermöglichen Anwendungsentwicklern das Schreiben von Service Fabric-Anwendungen, die

* hoch verfügbar, 
* skalierbar, 
* einfach zu verwalten und 
* einfach zu testen sind.

Die folgende Abbildung zeigt die Service Fabric-Architektur und die wichtigsten Subsysteme.

![](media/service-fabric-architecture/service-fabric-architecture.png)

In einem verteilten System ist die Fähigkeit zur sicheren Kommunikation zwischen den verschiedenen Knoten entscheidend. Die sichere Kommunikation zwischen den verschiedenen Knoten wird durch die unterste Ebene, das Transportsubsystem, bereitgestellt. Über dem Transportsubsystem liegt das Verbundsubsystem, das die verschiedenen Knoten zu einer einzelnen Einheit (sogenannte Cluster) verbindet. Hierdurch kann das System Fehler erkennen, eine Leader Election durchführen und Daten konsistent weiterleiten. Über dem Verbundsystem liegt das Zuverlässigkeitssubsystem, das für eine hohe Zuverlässigkeit der Service Fabric-Dienste verantwortlich ist. Hierbei werden Mechanismen wie Replikation, Ressourcenverwaltung und Failoververwaltung genutzt. Über dem Verbundsubsystem liegt außerdem das Hostingsubsystem, das den Lebenszyklus einer einzelnen Anwendung auf einem einzelnen Knoten verwaltet. Das Clusterverwaltungssubsystem verwaltet den Lebenszyklus von Anwendungen und Diensten auf mehreren Computern. Das Prüfbarkeitssubsystem ermöglicht Anwendungsentwicklern, Dienste mit simulierten Fehlern zu testen, bevor die Anwendungen und Dienste in der Produktionsumgebung bereitgestellt werden. Service Fabric bietet auch die Möglichkeit, Dienstspeicherorte über das Kommunikationssubsystem aufzulösen. Das für Entwickler verfügbare Anwendungsprogrammiermodell baut auf diesen Subsystemen auf.

## Transportsubsystem
Das Transportsubsystem implementiert einen Punkt-zu-Punkt-Kanal für die Datagramkommunikation. Dieser Kanal wird für die Kommunikation in Service Fabric-Clustern und zwischen Service Fabric-Clustern und -Clients verwendet. Der Kanal unterstützt unidirektionale und Abfrage-Antwort-Kommunikationsmodelle und bildet so die Grundlage für das Implementieren von Broadcast und Multicast in den unteren Ebenen. Das Transportsubsystem sichert die Kommunikation mithilfe von X509-Zertifikaten oder der Windows-Sicherheit. Dieses Subsystem wird intern von Service Fabric verwendet und steht Entwicklern bei der Anwendungsprogrammierung nicht zur Verfügung.

## Verbundsubsystem
Um die Bedeutung einer Gruppe von Knoten in einem verteilten System zu erörtern, muss das gesamte System als Ganzes verstanden werden. Das Verbundsubsystem verwendet die vom Transportsubsystem bereitgestellten Kommunikationsstammfunktionen und fügt die verschiedenen Knoten zu einem einzelnen einheitlichen System zusammen, Es stellt die Stammfunktionen des verteilten Systems bereit, die von den anderen Subsystemen benötigt werden, d. h. Fehlererkennung, Leader Election und konsistente Weiterleitung. Das Verbundsubsystem baut auf verteilten Hashtabellen mit einem 128-Bit-Tokenspeicher auf und erstellt eine Ringtopologie über den Knoten. Jedem Knoten in dem Ring wird eine Teilmenge des Tokenspeichers für den Besitz des Tokens zugewiesen. Zur Fehlererkennung verwendet die Ebene einen Leasing-Mechanismus, der auf Überwachung von Heart Beats und Vermittlung basiert. Das Verbundsubsystem stellt mit komplexen Beitritts- und Austrittsprotokollen sicher, dass jeweils nur ein Benutzer im Besitz des Tokens sein kann. Dies ist die Voraussetzung, um Leader Election und konsistente Weiterleitung garantieren zu können.

## Zuverlässigkeitssubsystem
Das Zuverlässigkeitssubsystem stellt den Mechanismus für den Hochverfügbarkeitsstatus des Service Fabric-Diensts bereit. Hierbei werden der _Replicator_, _Failover-Manager_ und _Resource Balancer_ verwendet.

* Der Replicator stellt sicher, dass Statusänderungen im primären Dienstreplikat automatisch in sekundären Replikaten repliziert werden, um Konsistenz zwischen den primären und sekundären Replikaten in einer Replikatgruppe zu gewährleisten. Der Replicator ist für die Quorumverwaltung zwischen den Replikaten in der Replikatgruppe verantwortlich. Er interagiert mit der Failovereinheit, um die Liste der zu replizierenden Vorgänge abzurufen. Der Reconfiguration Agent (RA) stellt die Konfiguration der Replikatgruppe bereit, in der angegeben ist, in welchen Replikaten die Vorgänge repliziert werden sollen. Der standardmäßige Replicator in Service Fabric lautet Fabric Replicator. Dienstentwickler können Fabric Replicator verwenden, um hoch verfügbare und zuverlässige Dienste zu entwickeln.
* Der Failover-Manager stellt sicher, dass die Last automatisch auf die verfügbaren Knoten umverteilt wird, wenn Konten zu einem Cluster hinzugefügt oder aus dem Cluster entfernt werden. Fällt ein Knoten im Cluster aus, konfiguriert der Cluster die Dienstreplikate automatisch neu, sodass kein Verfügbarkeitsverlust auftritt.
* Die Resource Balancer platziert Dienstreplikate in der Fehlerdomäne im Cluster und stellt sicher, dass alle Failovereinheiten betriebsbereit sind. Der Resource Balancer platziert außerdem Ressourcenausgleichsdienste im zugrunde liegenden gemeinsam verwendeten Pool an Clusterknoten, um eine optimale einheitliche Lastenverteilung zu erzielen.

## Verwaltungssubsystem
Das Verwaltungssubsystem stellt den End-to-End-Dienst und die Lebenszyklusverwaltung für Anwendungen bereit. PowerShell-Cmdlets und Verwaltungs-APIs ermöglichen das Vorbereiten, Bereitstellen, Anwenden von Patches, Upgraden und Löschen von Anwendungen ohne Verfügbarkeitsverlust. Das Verwaltungssubsystem nutzt hierzu folgende Dienste:

* Cluster Manager – Der primäre Dienst, der mit dem Failover-Manager aus dem Zuverlässigkeitssubsystem interagiert, um die Anwendungen gemäß den Einschränkungen für die Dienstplatzierung zu platzieren. Der Resource Balancer in der Failoverschicht stellt sicher, dass die Einschränkungen immer eingehalten werden. Der Cluster Manager verwaltet den Lebenszyklus der Anwendungen von der Bereitstellung bis zum Aufheben der Bereitstellung. Er ist im Health Manager (im nächsten Absatz beschrieben) integriert, um sicherzustellen, dass die semantische Integrität der Anwendungsverfügbarkeit während eines Upgrades nicht beeinträchtigt wird.
* Health Manager – Dieser Dienst ermöglicht die Überwachung der Integrität von Anwendungen, Diensten und Clusterentitäten. Clusterentitäten (z. B. Knoten, Dienstpartitionen und Replikate) können Zustandsinformationen melden, die dann im zentralen Integritätsspeicher aggregiert werden. Diese Integritätsinformationen bieten eine allgemeine Momentaufnahme der Integrität der Dienste und Knoten, die auf mehreren Knoten im Cluster verteilt sind. Anhand dieser Informationen können Sie erforderliche Korrekturmaßnahmen treffen. Zum Abfragen von Integritätsereignissen, die an das Integritätssubsystem gemeldet wurden, werden Integritätsabfrage-APIs verwendet. Die Integritätsabfrage-APIs geben die im Integritätsspeicher gespeicherten rohen Integritätsdaten oder die aggregierten, interpretierten Integritätsdaten für eine bestimmte Clusterentität zurück.
* Image Store – Dieser Dienst ermöglicht das Speichern und Verteilen von Anwendungsbinärdateien. Der Dienst bietet einen einfachen verteilten Dateispeicher, in den die Anwendungen hochgeladen bzw. aus dem die Anwendungen heruntergeladen werden.


## Hostingsubsystem
Der Cluster Manager informiert das Hostingsubsystem (auf jedem Knoten ausgeführt) über die Dienste, die er für das Verwalten eines bestimmten Knotens benötigt. Das Hostingsubsystem verwaltet dann die Lebenszyklus der Anwendung auf diesem Knoten. Es interagiert mit den Zuverlässigkeits- und Integritätskomponenten, um sicherzustellen, dass die Replikate ordnungsgemäß platziert sind und deren Integrität gewährleistet ist.

## Kommunikationssubsystem
Dieses Subsystem sorgt mithilfe des Naming-Diensts für zuverlässiges Messaging und Diensterkennung. Der Naming-Dienst löst die Dienstnamen an einem Speicherort im Cluster auf und ermöglicht Benutzern, Dienstnamen und -eigenschaften zu verwalten. Durch Verwendung des Naming-Diensts können Clients mit allen Knoten im Cluster sicher kommunizieren, um Dienstnamen aufzulösen und Dienstmetadaten abzurufen. Service Fabric-Benutzer können mithilfe einer einfachen Naming-Client-API Dienste und Clients entwickeln, die den aktuellen Netzwerkstandort unabhängig von Knotendynamik oder Clustergröße auflösen können.

## Prüfbarkeitssubsystem
Das Prüfbarkeitssubsystem besteht aus mehreren Tools, die speziell zum Testen von mit Service Fabric erstellten Diensten entwickelt wurden. Entwickler können mit diesen Tools auf einfache Weise sinnvolle Fehler herbeiführen und Testszenarien ausführen, um die zahlreichen Zustände und Übergänge, die ein Dienst im Laufe seines Lebenszyklus durchläuft, sicher und kontrolliert zu prüfen. Das Prüfbarkeitssubsystem bietet außerdem einen Mechanismus für das Ausführen langer Testläufe, in denen mehrere Fehler ohne Verfügbarkeitsverlust durchlaufen werden. Benutzern wird hierfür eine Testproduktionsumgebung bereitgestellt.
 

<!---HONumber=July15_HO4-->