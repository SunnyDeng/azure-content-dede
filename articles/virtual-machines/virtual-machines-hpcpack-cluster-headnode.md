<properties
 pageTitle="Erstellen eines HPC Pack-Hauptknotens in einem virtuellen Azure-Computer | Microsoft Azure"
 description="Es wird beschrieben, wie Sie das Azure-Portal und das klassische Bereitstellungsmodell verwenden, um einen Microsoft HPC Pack-Hauptknoten in einem virtuellen Azure-Computer zu erstellen."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Erstellen des Hauptknotens eines HPC Pack-Clusters auf einem virtuellen Azure-Computer mit einem Marketplace-Image

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel bezieht sich auf das Erstellen einer Ressource mit dem klassischen Bereitstellungsmodell.

In diesem Artikel wird veranschaulicht, wie Sie das [Microsoft HPC Pack-Image für einen virtuellen Computer](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) im Azure Marketplace verwenden, um den Hauptknoten eines Windows HPC-Clusters in Azure mit dem klassischen Bereitstellungsmodell (Service Management) zu erstellen. Für den Hauptknoten muss der Beitritt zu einer Active Directory-Domäne in einem virtuellen Azure-Netzwerk durchgeführt werden. Sie können diesen Hauptknoten für eine Machbarkeitsstudien-Bereitstellung von HPC Pack in Azure verwenden und dem Cluster zum Ausführen von HPC-Workloads Compute-Ressourcen hinzufügen.


![HPC Pack-Hauptknoten][headnode]

>[AZURE.NOTE]Derzeit basiert das HPC Pack-VM-Image auf Windows Server 2012 R2 Datacenter mit vorinstalliertem HPC Pack 2012 R2 Update 2. Außerdem ist Microsoft SQL Server 2014 Express vorinstalliert.


Für eine Produktionsbereitstellung eines HPC Pack-Clusters in Azure empfehlen wir Ihnen eine automatisierte Bereitstellungsmethode, z. B. das [HPC Pack-IaaS-Bereitstellungsskript](virtual-machines-hpcpack-cluster-powershell-script.md) oder eine [Schnellstartvorlage](https://azure.microsoft.com/documentation/templates/) des Azure-Ressourcen-Managers.

## Überlegungen zur Planung

* **Active Directory-Domäne:** Der HPC Pack-Hauptknoten muss einer Active Directory-Domäne in Azure beigetreten sein, bevor Sie die HPC-Dienste starten. Eine Möglichkeit ist das Bereitstellen eines separaten Domänencontrollers und einer Gesamtstruktur in Azure, der der virtuelle Computer beitreten kann. Für eine Machbarkeitsstudienbereitstellung können Sie den virtuellen Computer heraufstufen, den Sie für den Hauptknoten als Domänencontroller erstellen, bevor Sie die HPC-Dienste starten.

* **Virtuelles Azure-Netzwerk:** Wenn Sie planen, dem HPC-Cluster Cluster-Computeknoten-VMs hinzuzufügen, oder wenn Sie einen separaten Domänencontroller für den Cluster erstellen, müssen Sie den Hauptknoten in einem virtuellen Azure-Netzwerk (VNet) bereitstellen. Ohne VNet können Sie Azure weiterhin Azure-„Burstknoten“ hinzufügen.

## Schritte zum Erstellen des Hauptknotens

Unten sind die allgemeinen Schritte zum Erstellen eines virtuellen Azure-Computers für den HPC Pack-Hauptknoten angegeben. Sie können verschiedene Azure-Tools nutzen, um diese Schritte im klassischen Azure-Bereitstellungsmodell (Service Management) auszuführen.


1. Falls Sie planen, ein VNet für den virtuellen Hauptknotencomputer zu erstellen, helfen Ihnen die Informationen unter [Erstellen eines virtuellen Netzwerks (klassisch) über das Azure-Vorschauportal](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md) weiter.

    **Überlegungen**

    * Sie können die Standardkonfiguration für den Adressraum und die Subnetze des virtuellen Netzwerks übernehmen.

    * Wenn Sie planen, für den HPC Pack-Hauptknoten eine rechenintensive Instanzgröße zu verwenden (A8 - A11), oder dem Cluster später Compute-Ressourcen hinzufügen, sollten Sie eine Region wählen, in der die Instanzen verfügbar sind. Stellen Sie beim Verwenden von A8- oder A9-Instanzen für MPI-Workloads auch sicher, dass sich der Adressraum des virtuellen Netzwerks nicht mit dem Adressraum überschneidet, der vom RDMA-Netzwerk in Azure (172.16.0.0/12) reserviert ist. Weitere Informationen hierzu finden Sie unter [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](virtual-machines-a8-a9-a10-a11-specs.md).

2. Wenn Sie eine neue Active Directory-Gesamtstruktur auf einem separaten virtuellen Computer erstellen müssen, helfen Ihnen die Informationen unter [Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk](../active-directory/active-directory-new-forest-virtual-machine.md) weiter.

    **Überlegungen**

    * Für viele Testbereitstellungen können Sie einen einzelnen Domänencontroller in Azure erstellen. Zum Sicherstellen einer hohen Verfügbarkeit der Active Directory-Domäne können Sie einen zusätzlichen Backup-Domänencontroller bereitstellen.

    * Für eine einfache Machbarkeitsstudienbereitstellung können Sie diesen Schritt auslassen und den virtuellen Hauptknotencomputer später zu einem Domänencontroller heraufstufen.

3. Erstellen Sie im Azure-Verwaltungsportal oder Azure-Vorschauportal einen klassischen virtuellen Computer, indem Sie im Azure Marketplace das HPC Pack 2012 R2-Image auswählen. (Die Schritte für das Verwaltungsportal finden Sie [hier](virtual-machines-windows-tutorial-classic-portal.md).)

    **Überlegungen**

    * Wählen Sie für den virtuellen Computer mindestens die Größe A4.

    * Wenn Sie den Hauptknoten in einem VNet bereitstellen möchten, sollten Sie das VNet in der Konfiguration des virtuellen Computers angeben.

    * Es wird empfohlen, dass Sie einen neuen Clouddienst für den virtuellen Computer erstellen.

4. Nach der Erstellung des virtuellen Computers und dem Beginn der Ausführung führen Sie dafür den Beitritt zu einer vorhandenen Gesamtstruktur durch oder erstellen darauf eine neue Domänengesamtstruktur.

    **Überlegungen**

    * Wenn Sie den virtuellen Computer in einem Azure VNet mit einer vorhandenen Domänengesamtstruktur erstellt haben, stellen Sie eine Verbindung mit dem virtuellen Computer her. Verwenden Sie dann standardmäßige Server-Manager- oder Windows PowerShell-Tools, um den Beitritt zur Domänengesamtstruktur durchzuführen. Führen Sie anschließend einen Neustart durch.

    * Wenn der virtuelle Computer nicht in einem Azure VNet oder in einem VNet ohne vorhandene Domänengesamtstruktur erstellt wurde, stufen Sie ihn zu einem Domänencontroller herauf. Stellen Sie hierzu eine Verbindung mit dem virtuellen Computer her, und verwenden Sie dann standardmäßige Server-Manager- oder Windows PowerShell-Tools. Ausführliche Schritte finden Sie unter [Installieren einer neuen Windows Server 2012 Active Directory-Gesamtstruktur](https://technet.microsoft.com/library/jj574166.aspx).

5. Wenn der virtuelle Computer ausgeführt wird und Mitglied einer Active Directory-Gesamtstruktur ist, starten Sie die HPC Pack-Dienste auf dem Hauptknoten. Gehen Sie dazu folgendermaßen vor:

    a. Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie ein Domänenkonto verwenden, das Mitglied der lokalen Administratorgruppe ist. Beispielsweise können Sie das Administratorkonto nutzen, das Sie beim Erstellen des virtuellen Hauptknotencomputers eingerichtet haben.

    b. Starten Sie zur Durchführung einer Standardkonfiguration des Hauptknotens Windows PowerShell als Administrator, und geben Sie Folgendes ein:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Es kann mehrere Minuten dauern, bis die HPC Pack-Dienste starten.

    Geben Sie `get-help HPCHNPrepare.ps1` ein, um weitere Konfigurationsoptionen für den Hauptknoten anzuzeigen.


## Nächste Schritte

* Sie können jetzt mit dem Hauptknoten des Windows HPC-Clusters arbeiten. Beispielsweise können Sie den HPC-Cluster-Manager starten oder die HPC PowerShell-Cmdlets verwenden.

* [Fügen Sie Ihrem Cluster virtuelle Computeknoten-Computer hinzu](virtual-machines-hpcpack-cluster-node-manage.md), oder fügen Sie in einem Clouddienst [Azure-Burstknoten](virtual-machines-hpcpack-cluster-node-burst.md) hinzu.

* Führen Sie eine Test-Workload auf dem Cluster aus. Ein Beispiel hierzu finden Sie im [Leitfaden für die ersten Schritte](https://technet.microsoft.com/library/jj884144) von HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=Oct15_HO1-->