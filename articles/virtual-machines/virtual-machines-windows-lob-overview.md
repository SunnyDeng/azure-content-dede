<properties 
	pageTitle="Bereitstellen einer Branchenanwendung | Microsoft Azure" 
	description="Sie stellen eine webbasierte, hochverfügbare Branchenanwendung mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in fünf Phasen in Azure bereit." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="josephd"/>

# Bereitstellen einer hochverfügbaren Branchenanwendung in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

Dieser Artikel enthält Links zu den schrittweisen Anweisungen für die Bereitstellung einer hochverfügbaren, webbasierten, nur im Intranet verfügbaren Branchenanwendung mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure-Infrastrukturdiensten. Die Anwendung wird auf folgenden Computern gehostet:

- Zwei Webserver
- Zwei Datenbankserver
- Einen Mehrheitsknotenserver für den Cluster
- Zwei Domänencontroller

Hier die Konfiguration mit Platzhalternamen für die Server:

![](./media/virtual-machines-windows-lob-overview/workload-lobapp-phase4.png)
 
Mindestens zwei Computer pro Rolle stellen eine hohe Verfügbarkeit sicher. Alle virtuellen Computer befinden sich an einem einzigen Azure-Standort (auch als "Region" bezeichnet). Jede Gruppe virtueller Computer einer bestimmten Rolle befindet sich in einer eigenen Verfügbarkeitsgruppe.

## Stückliste

Diese Basiskonfiguration erfordert den folgenden Satz von Azure-Diensten und -Komponenten:

- Sieben virtuelle Computer
- Vier zusätzliche Datenträger für die Domänencontroller und die virtuellen Computer, auf denen SQL Server ausgeführt wird
- Drei Verfügbarkeitsgruppen
- Ein standortübergreifendes virtuelles Netzwerk
- Zwei Speicherkonten

Nachfolgend sind die virtuellen Computer und deren Standardgrößen für diese Konfiguration aufgeführt.

Element | Beschreibung des virtuellen Computers | Katalogimage | Standardgröße 
--- | --- | --- | --- 
1\. | Erster Domänencontroller | Windows Server 2012 R2 Datacenter | D1
2\. | Zweiter Domänencontroller | Windows Server 2012 R2 Datacenter | D1
3\. | Primärer Datenbankserver | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4\. | Sekundärer Datenbankserver | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5\. | Mehrheitsknoten des Clusters | Windows Server 2012 R2 Datacenter | D1
6\. | Erster Webserver | Windows Server 2012 R2 Datacenter | D3
7\. | Zweiter Webserver | Windows Server 2012 R2 Datacenter | D3

Nutzen Sie zum Berechnen der geschätzten Kosten für diese Konfiguration den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

1. Klicken Sie unter **Module** auf **Compute** und dann so oft auf **Virtuelle Computer**, bis Sie eine Liste mit sieben virtuellen Computern erstellt haben.
2. Wählen Sie für jeden virtuellen Computer Folgendes:
	- Die gewünschte Region
	- **Windows** für den Typ
	- **Standard** für die Preisstufe
	- Die Standardgröße in der vorherigen Tabelle oder die gewünschte Größe für die **Instanzgröße**

> [AZURE.NOTE] Der Azure-Preisrechner bezieht nicht die zusätzlichen Kosten für die SQL Server-Lizenz für die beiden virtuellen Computer ein, auf denen SQL Server 2014 Enterprise ausgeführt wird. Weitere Informationen finden Sie unter [Virtual Machines-Preise – SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Bereitstellungsphasen

Sie stellen diese Konfiguration in den folgenden Phasen bereit:

- [Phase 1: Konfigurieren von Azure](virtual-machines-windows-ps-lob-ph1.md): Erstellen Sie Speicherkonten, Verfügbarkeitsgruppen und ein standortübergreifendes virtuelles Netzwerk.
- [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-windows-ps-lob-ph2.md): Erstellen und konfigurieren Sie Replikate der Domänencontroller für Active Directory Domain Services (AD DS).
- [Phase 3: Konfigurieren der SQL Server-Infrastruktur](virtual-machines-windows-ps-lob-ph3.md): Erstellen und konfigurieren Sie die virtuellen Computer, auf denen SQL Server ausgeführt wird, und aktivieren Sie SQL Server-AlwaysOn-Verfügbarkeitsgruppen.
- [Phase 4: Konfigurieren der Webserver](virtual-machines-windows-ps-lob-ph4.md): Erstellen und konfigurieren Sie die beiden virtuellen Webservercomputer.
- [Phase 5: Hinzufügen der Anwendungsdatenbanken zu einer SQL Server-AlwaysOn-Verfügbarkeitsgruppe](virtual-machines-windows-ps-lob-ph5.md): Bereiten Sie die Datenbanken für die Branchenanwendung vor, und fügen Sie sie zu einer SQL Server-AlwaysOn-Verfügbarkeitsgruppe hinzu.

Diese Bereitstellung folgt der [Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664) und beinhaltet die neuesten Empfehlungen.

Hierbei handelt es sich um eine genehmigte, vordefinierte Architektur. Berücksichtigen Sie Folgendes:

- Wenn Sie über Erfahrung mit der Implementierung von webbasierten Branchenanwendungen verfügen, können Sie die Anweisungen in den Phasen 3 bis 5 gerne abwandeln und die für Ihre Anforderungen am besten geeignete Anwendungsinfrastruktur erstellen. 
- Wenn Sie bereits über eine Azure Hybrid Cloud-Implementierung verfügen, können Sie die Anweisungen in den Phasen 1 und 2 anpassen oder auch überspringen, um die virtuellen Computer für die neue Anwendung im geeigneten Subnetz zu hosten.
- Alle Server befinden sich im gleichen Subnetz des virtuellen Azure-Netzwerks. Wenn Sie zusätzliche Sicherheit ähnlich der Subnetzisolierung bereitstellen möchten, verwenden Sie [Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-nsg.md).

Zum Erstellen einer Test- und Entwicklungsumgebung bzw. einer Machbarkeitsstudie für diese Konfiguration lesen Sie [Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Weitere Informationen zum Entwickeln von IT-Workloads für Azure finden Sie in den [Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-linux-infrastructure-service-guidelines.md).

## Nächster Schritt

Zum Starten der Konfiguration für diese Arbeitsauslastung gehen Sie zu [Phase 1: Konfigurieren von Azure](virtual-machines-windows-ps-lob-ph1.md).

<!---HONumber=AcomDC_0323_2016-->