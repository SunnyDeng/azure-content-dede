<properties
	pageTitle="Bereitstellen einer SharePoint Server 2013-Farm | Microsoft Azure"
	description="Sie stellen in fünf Phasen eine hochverfügbare SharePoint Server 2013-Farm mithilfe von SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure bereit."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

Dieses Thema enthält Links zu schrittweisen Anweisungen für die Bereitstellung einer Intranet-internen SharePoint 2013-Farm mit SQL Server-AlwaysOn-Verfügbarkeitsgruppen. Die Farm enthält die folgenden Computer:

- Zwei SharePoint-Webserver
- Zwei SharePoint-Anwendungsserver
- Zwei Datenbankserver
- Einen Mehrheitsknotenserver für den Cluster
- Zwei Domänencontroller

Hier die Konfiguration mit Platzhalternamen für die Server:

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Zwei Computer pro Rolle stellen eine hohe Verfügbarkeit sicher. Alle virtuellen Computer befinden sich in der gleichen Region. Jede Gruppe virtueller Computer einer bestimmten Rolle befindet sich in einer eigenen Verfügbarkeitsgruppe.

## Stückliste

Diese Basiskonfiguration erfordert den folgenden Satz von Azure-Diensten und -Komponenten:

- Neun virtuelle Computer
- Vier zusätzliche Datenträger für die Domänencontroller und SQL-Server
- Vier Verfügbarkeitsgruppen
- Ein standortübergreifendes virtuelles Netzwerk
- Ein Speicherkonto
- Ein Azure-Abonnement

Nachfolgend sind die virtuellen Computer und deren Standardgrößen für diese Konfiguration aufgeführt.

Element | Beschreibung des virtuellen Computers | Katalogimage | Standardgröße
--- | --- | --- | ---
1\. | Erster Domänencontroller | Windows Server 2012 R2 Datacenter | A2
2\. | Zweiter Domänencontroller | Windows Server 2012 R2 Datacenter | A2
3\. | Erster Datenbankserver | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | Zweiter Datenbankserver | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | Mehrheitsknoten des Clusters | Windows Server 2012 R2 Datacenter | A1
6\. | Erster SharePoint-Anwendungsserver | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4
7\. | Zweiter SharePoint-Anwendungsserver | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4
8\. | Erster SharePoint-Webserver | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4
9\. | Zweiter SharePoint-Webserver | Microsoft SharePoint Server 2013-Testversion – Windows Server 2012 R2 | A4

Nutzen Sie zum Berechnen der geschätzten Kosten für diese Konfiguration den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

1. Klicken Sie unter **Module** auf **Compute** und dann so oft auf **Virtuelle Computer**, bis Sie eine Liste mit neun virtuellen Computern erstellt haben.
2. Wählen Sie für jeden virtuellen Computer Folgendes:
	- Die gewünschte Region
	- **Windows** für den Typ
	- **Standard** für die Preisstufe
	- Die Standardgröße in der vorherigen Tabelle oder die gewünschte Größe für die **Instanzgröße**

> [AZURE.NOTE] Der Azure-Preisrechner bezieht nicht die zusätzlichen Kosten für die SQL Server-Lizenz für die beiden virtuellen Computer ein, auf denen SQL Server 2014 Enterprise ausgeführt wird. Weitere Informationen finden Sie unter [Virtual Machines-Preise – SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Bereitstellungsphasen

Sie stellen diese Konfiguration in den folgenden Phasen bereit:

- [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md): Erstellen Sie ein Speicherkonto, Verfügbarkeitsgruppen und ein standortübergreifendes virtuelles Netzwerk.
- [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md): Erstellen und konfigurieren Sie Replikate der Domänencontroller für Active Directory Domain Services (AD DS).
- [Phase 3: Konfigurieren der SQL Server-Infrastruktur](virtual-machines-workload-intranet-sharepoint-phase3.md): Erstellen und konfigurieren Sie die virtuellen Computer für SQL Server, bereiten Sie sie für die Verwendung mit SharePoint vor und erstellen Sie einen Cluster.
- [Phase 4: Konfigurieren der SharePoint-Server](virtual-machines-workload-intranet-sharepoint-phase4.md): Erstellen und konfigurieren Sie die vier virtuellen SharePoint-Computer.
- [Phase 5: Erstellen der Verfügbarkeitsgruppe und Hinzufügen der SharePoint-Datenbanken](virtual-machines-workload-intranet-sharepoint-phase5.md): Bereiten Sie die Datenbanken vor und erstellen Sie eine SQL Server-AlwaysOn-Verfügbarkeitsgruppe.

Diese SharePoint-Bereitstellung mit SQL Server AlwaysOn umfasst die aktuellsten Empfehlungen und begleitet ab nun die [Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Die Konfiguration dieser vordefinierten Architektur wird Phase für Phase in einer ausführlichen Anleitung besprochen, mit der Sie innerhalb der Azure-Infrastrukturdienste eine betriebsbereite, hoch verfügbare SharePoint-Farm erstellen können. Weitere Informationen zur Architektur einer SharePoint 2013-Implementierung in Azure finden Sie unter [Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Berücksichtigen Sie Folgendes:

- Als erfahrener SharePoint-Implementierer können Sie die Anweisungen in den Phasen 3 bis 5 gerne abwandeln und die für Ihre Ansprüche optimale Farm erstellen.
- Wenn Sie bereits über eine Azure Hybrid Cloud-Bereitstellung verfügen, können Sie die Anweisungen in Phase 1 und 2 anpassen oder auch überspringen, um die neue SharePoint-Farm im entsprechenden Subnetz bereitzustellen.
- Alle Server befinden sich im gleichen Subnetz des virtuellen Azure-Netzwerks. Wenn Sie zusätzliche Sicherheit ähnlich der Subnetzisolierung bereitstellen möchten, verwenden Sie [Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-nsg.md).

Zum Erstellen einer Test- bzw. Entwicklungsumgebung zum Beispiel für Machbarkeitsstudien dieser Konfiguration lesen Sie [Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Weitere Informationen zu SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen finden Sie unter [Configure SQL Server 2012 AlwaysOn Availability Groups for SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx) (Konfigurieren von SQL Server 2012 AlwaysOn-Verfügbarkeitsgruppen für SharePoint 2013).

> [AZURE.NOTE] Microsoft hat die SharePoint Server 2016 IT Preview veröffentlicht. Damit Sie diese Vorschau einfach installieren und testen können, verwenden Sie ein Azure Virtual Machine-Katalogimage, auf dem SharePoint Server 2016 IT Preview und alle erforderlichen Komponenten vorinstalliert sind. Weitere Informationen finden Sie unter [Test the SharePoint Server 2016 IT Preview in Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/) (in englischer Sprache).

## Nächster Schritt

- Starten Sie die Konfiguration dieser Workload mit [Phase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

<!---HONumber=AcomDC_0128_2016-->