<properties 
	pageTitle="Bereitstellen einer hochverfügbaren Branchenanwendung in Azure" 
	description="Sie können eine webbasierte, hochverfügbare Branchenanwendung mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in fünf Phasen in Azure bereitstellen." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Bereitstellen einer hochverfügbaren Branchenanwendung in Azure

Dieser Artikel enthält Links zu den schrittweisen Anweisungen für die Bereitstellung einer hochverfügbaren, webbasierten, nur im Intranet verfügbaren Branchenanwendung mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure-Infrastrukturdiensten. Die Anwendung wird auf folgenden Computern gehostet:

- Zwei Webserver
- Zwei Datenbankserver
- Einen Mehrheitsknotenserver für den Cluster
- Zwei Domänencontroller

Hier die Konfiguration mit Platzhalternamen für die Server:

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Mindestens zwei Computer pro Rolle stellen eine hohe Verfügbarkeit sicher. Alle virtuellen Computer befinden sich an einem einzigen Azure-Standort (auch als "Region" bezeichnet). Jede Gruppe virtueller Computer einer bestimmten Rolle befindet sich in einer eigenen Verfügbarkeitsgruppe.

Sie stellen diese Konfiguration in den folgenden Phasen bereit:

- [Phase 1: Konfigurieren von Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md): Erstellen Sie Speicherkonten, Verfügbarkeitsgruppen und ein standortübergreifendes virtuelles Netzwerk.
- [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-high-availability-LOB-application-phase2.md): Erstellen und konfigurieren Sie Replikate der Domänencontroller für Active Directory Domain Services (AD DS).
- [Phase 3: Konfigurieren der SQL Server-Infrastruktur](virtual-machines-workload-high-availability-LOB-application-phase3.md): Erstellen und konfigurieren Sie die virtuellen Computer, auf denen SQL Server ausgeführt wird, und aktivieren Sie SQL Server-AlwaysOn-Verfügbarkeitsgruppen.
- [Phase 4: Konfigurieren der Webserver](virtual-machines-workload-high-availability-LOB-application-phase4.md): Erstellen und konfigurieren Sie die beiden virtuellen Webservercomputer.
- [Phase 5: Hinzufügen der Anwendungsdatenbanken zu einer SQL Server-AlwaysOn-Verfügbarkeitsgruppe](virtual-machines-workload-high-availability-LOB-application-phase5.md): Bereiten Sie die Datenbanken für die Branchenanwendung vor, und fügen Sie sie zu einer SQL Server-AlwaysOn-Verfügbarkeitsgruppe hinzu.

Diese Bereitstellung folgt der [Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664) und beinhaltet die neuesten Empfehlungen.

Hierbei handelt es sich um eine genehmigte, vordefinierte Architektur. Berücksichtigen Sie Folgendes:

- Wenn Sie über Erfahrung mit der Implementierung von webbasierten Branchenanwendungen verfügen, können Sie die Anweisungen in den Phasen 3 bis 5 gerne abwandeln und die für Ihre Anforderungen am besten geeignete Anwendungsinfrastruktur erstellen. 
- Wenn Sie bereits über eine Azure Hybrid Cloud-Implementierung verfügen, können Sie die Anweisungen in den Phasen 1 und 2 anpassen oder auch überspringen, um die virtuellen Computer für die neue Anwendung im geeigneten Subnetz zu hosten.
- Alle Server befinden sich im gleichen Subnetz des virtuellen Azure-Netzwerks. Wenn Sie zusätzliche Sicherheit ähnlich der Subnetzisolierung bereitstellen möchten, verwenden Sie [Netzwerksicherheitsgruppen](../virtual-networks/virtual-networks-nsg.md).

Zum Erstellen einer Test- und Entwicklungsumgebung bzw. einer Machbarkeitsstudie für diese Konfiguration lesen Sie [Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Weitere Informationen zum Entwickeln von IT-Workloads für Azure finden Sie in den [Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Nächster Schritt

Zum Starten der Konfiguration für diesen Workload wechseln Sie zu [Phase 1: Konfigurieren von Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

## Zusätzliche Ressourcen

[Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: SharePoint Server 2013-Farm](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO8-->