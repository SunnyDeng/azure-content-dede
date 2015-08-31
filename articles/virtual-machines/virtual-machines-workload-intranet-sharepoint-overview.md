<properties
	pageTitle="Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure"
	description="In Azure können Sie SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in fünf Phasen bereitstellen."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure

Dieses Thema enthält Links zu schrittweisen Anweisungen für die Bereitstellung einer Intranet-internen SharePoint 2013-Farm mit SQL Server AlwaysOn-Verfügbarkeitsgruppen mit der Azure-Dienstverwaltung. Die Farm enthält die folgenden Computer:

- Zwei SharePoint-Webserver
- Zwei SharePoint-Anwendungsserver
- Zwei Datenbankserver
- Einen Mehrheitsknotenserver für den Cluster
- Zwei Domänencontroller

Hier die Konfiguration mit Platzhalternamen für die Server:

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Zwei Computer pro Rolle stellen eine hohe Verfügbarkeit sicher. Alle virtuellen Computer befinden sich in der gleichen Region. Jede Gruppe virtueller Computer einer bestimmten Rolle befindet sich in einer eigenen Verfügbarkeitsgruppe.

Sie stellen diese Konfiguration in den folgenden Phasen bereit:

- [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md): Erstellen Sie ein Speicherkonto, Cloud-Dienste und ein standortübergreifendes virtuelles Netzwerk.
- [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md): Erstellen und konfigurieren Sie Replikate der Domänencontroller für Active Directory Domain Services (AD DS).
- [Phase 3: Konfigurieren der SQL Server-Infrastruktur](virtual-machines-workload-intranet-sharepoint-phase3.md): Erstellen und konfigurieren Sie die virtuellen Computer für SQL Server, bereiten Sie sie für die Verwendung mit SharePoint vor und erstellen Sie einen Cluster.
- [Phase 4: Konfigurieren der SharePoint-Server](virtual-machines-workload-intranet-sharepoint-phase4.md): Erstellen und konfigurieren Sie die vier virtuellen SharePoint-Computer.
- [Phase 5: Erstellen der Verfügbarkeitsgruppe und Hinzufügen der SharePoint-Datenbanken](virtual-machines-workload-intranet-sharepoint-phase5.md): Bereiten Sie die Datenbanken vor und erstellen Sie eine SQL Server-AlwaysOn-Verfügbarkeitsgruppe.

Diese SharePoint-Bereitstellung mit SQL Server AlwaysOn umfasst die aktuellsten Empfehlungen und begleitet ab nun die [Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Die Konfiguration dieser vordefinierten Architektur wird Phase für Phase in einer ausführlichen Anleitung besprochen, mit der Sie innerhalb der Azure-Infrastrukturdienste eine betriebsbereite, hoch verfügbare SharePoint-Farm erstellen können. Weitere Informationen zur Architektur einer SharePoint 2013-Implementierung in Azure finden Sie unter [Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Berücksichtigen Sie Folgendes:

- Als erfahrener SharePoint-Implementierer können Sie die Anweisungen in den Phasen 3 bis 5 gerne abwandeln und die für Ihre Ansprüche optimale Farm erstellen.
- Wenn Sie bereits über eine Azure Hybrid Cloud-Implementierung verfügen, können Sie die Anweisungen in Phase 1 und 2 anpassen oder auch überspringen, um die neue SharePoint-Farm im entsprechenden Subnetz bereitzustellen.
- Alle Server befinden sich im gleichen Subnetz des virtuellen Azure-Netzwerks. Wenn Sie zusätzliche Sicherheit ähnlich der Subnetzisolierung bereitstellen möchten, verwenden Sie [Netzwerksicherheitsgruppen](https://msdn.microsoft.com/library/azure/dn848316.aspx).

Zum Erstellen einer Test- bzw. Entwicklungsumgebung zum Beispiel für Machbarkeitsstudien dieser Konfiguration lesen Sie [Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Weitere Informationen zu SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen finden Sie unter [Configure SQL Server 2012 AlwaysOn Availability Groups for SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx) (Konfigurieren von SQL Server 2012 AlwaysOn-Verfügbarkeitsgruppen für SharePoint 2013).

## Nächster Schritt

Zum Starten der Konfiguration für diese Arbeitsauslastung gehen Sie zu [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).


## Zusätzliche Ressourcen

[Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=August15_HO8-->