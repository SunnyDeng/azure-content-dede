<properties
 pageTitle="Erstellen eines HPC Pack-Hauptknotens in einem virtuellen Azure-Computer | Microsoft Azure"
 description="Sie erfahren, wie Sie das Azure-Portal und das Ressourcen-Manager-Bereitstellungsmodell verwenden, um einen Microsoft HPC Pack-Hauptknoten in einem virtuellen Azure-Computer zu erstellen."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Erstellen des Hauptknotens eines HPC Pack-Clusters auf einem virtuellen Azure-Computer mit einem Marketplace-Image

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Bereitstellungsmodell.


Dieser Artikel veranschaulicht, wie Sie das [Microsoft HPC Pack-Image für einen virtuellen Computer](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) im Azure Marketplace verwenden, um den Hauptknoten eines HPC-Clusters mithilfe des Azure-Portals zu erstellen. Das HPC Pack-VM-Image basiert auf Windows Server 2012 R2 Datacenter mit vorinstalliertem HPC Pack 2012 R2 Update 3. Verwenden Sie diesen Hauptknoten für eine Proof of Concept-Bereitstellung von HPC Pack in Azure. Sie können dem Cluster dann Compute-Ressourcen zum Ausführen von HPC-Workloads hinzufügen.


![HPC Pack-Hauptknoten][headnode]

>[AZURE.TIP]Für eine Produktionsbereitstellung eines vollständigen HPC Pack-Clusters in Azure sollten Sie eine automatisierte Methode verwenden, z.B. das [HPC Pack-IaaS-Bereitstellungsskript](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md), oder eine Resource Manager-Vorlage wie die Vorlage [HPC Pack cluster for Windows workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) (HPC Pack-Cluster für Windows-Workloads). Zusätzliche Vorlagen finden Sie unter [HPC Pack cluster options in Azure](virtual-machines-windows-hpcpack-cluster-options.md) (HPC Pack-Clusteroptionen in Azure).


## Überlegungen zur Planung

* **Active Directory-Domäne** – der HPC Pack-Hauptknoten muss einer Active Directory-Domäne in Azure beigetreten sein, bevor Sie die HPC-Dienste auf der VM starten. Wie in diesem Artikel gezeigt, können Sie die VM, die Sie für den Hauptknoten als Domänencontroller erstellen, für eine Proof of Concept-Bereitstellung höher stufen, bevor Sie die HPC-Dienste starten. Eine weitere Möglichkeit ist das Bereitstellen eines separaten Domänencontrollers und einer Gesamtstruktur in Azure, der der virtuelle Computer beitritt.

* **Virtuelles Azure-Netzwerk** – wie in diesem Artikel gezeigt, geben Sie ein virtuelles Azure-Netzwerk (VNet) an oder erstellen es, wenn Sie den HPC Pack-Hauptknoten mit dem Ressourcen-Manager-Bereitstellungsmodell im Azure-Portal bereitstellen. Sie müssen das VNet später verwenden, um Clusterserverknoten-VMs dem HPC-Cluster hinzuzufügen, oder wenn Sie den Hauptknoten einer vorhandenen Active Directory-Domäne beitreten lassen.

    
## Schritte zum Erstellen des Hauptknotens

Dies sind allgemeine Schritte zum Erstellen einer Azure-VM für den HPC Pack-Hauptknoten mit dem Ressourcen-Manager-Bereitstellungsmodell im Azure-Portal.


1. Wenn Sie eine neue Active Directory-Gesamtstruktur in Azure mit separaten Domänencontroller-VMs erstellen möchten, ist die Verwendung einer [Ressourcen-Manager-Vorlage](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/) eine Möglichkeit. Für eine einfache Proof of Concept-Bereitstellung können Sie diesen Schritt auslassen und die Hauptknoten-VM, wie in einem späteren Schritt beschrieben, selbst als Domänencontroller konfigurieren.
    
2. Wählen Sie im [Azure-Portal](https://portal.azure.com) das HPC Pack 2012 R2-Image aus dem Azure Marketplace. Klicken Sie hierzu auf **Neu** und suchen Sie im Marketplace das **HPC Pack**. Wählen Sie **HPC Pack 2012 R2 unter Windows Server 2012 R2**.

3. Wählen Sie auf der Seite **HPC Pack 2012 R2 unter Windows Server 2012 R2** das **Ressourcen-Manager**-Bereitstellungsmodell, und klicken Sie dann auf **Erstellen**.

    ![HPC Pack-Image][marketplace]

4. Verwenden Sie das Portal, um die Einstellungen zu konfigurieren und die VM zu erstellen. Ausführliche Schritte finden Sie in dem Tutorial [Erstellen eines virtuellen Windows-Computers im Azure-Portal-Portal](virtual-machines-windows-hero-tutorial.md). Für eine erste Bereitstellung können Sie in der Regel viele Standard- oder empfohlene Einstellungen übernehmen.

    **Überlegungen zu VNet**

   * Geben Sie bei der Erstellung eines neuen VNets in **Einstellungen** einen privaten Netzwerk-Adressbereich an, z. B. 10.0.0.0/16, und einen Subnetzadressbereich, z. B. 10.0.0.0/24.
    
4. Wenn der virtuelle Computer, den Sie erstellt haben, ausgeführt wird, [stellen Sie die Verbindung mit dem virtuellen Computer her](virtual-machines-windows-connect-logon.md). 

5. Führen Sie den Beitritt der VM zu einer vorhandenen Gesamtstruktur aus, oder erstellen Sie auf der VM selbst eine neue Domänengesamtstruktur.

    **Überlegungen zur Active Directory-Domäne**

    * Wenn Sie die VM in einem Azure-VNet mit einer vorhandenen Domänengesamtstruktur erstellt haben, verwenden Sie den standardmäßigen Server-Manager oder Windows PowerShell-Tools, um ihren Beitritt zur Domänengesamtstruktur auszuführen. Führen Sie anschließend einen Neustart durch.

    * Wenn Sie die VM in einem VNet ohne vorhandene Domänengesamtstruktur erstellt haben, stufen Sie sie zum Domänencontroller hoch. Verwenden Sie hierzu den standardmäßigen Server-Manager oder Windows PowerShell-Tools zum Installieren und Konfigurieren der Active Directory-Domänendienste-Rolle. Ausführliche Schritte finden Sie unter [Installieren einer neuen Windows Server 2012 Active Directory-Gesamtstruktur](https://technet.microsoft.com/library/jj574166.aspx).

5. Wenn der virtuelle Computer ausgeführt wird und Mitglied einer Active Directory-Gesamtstruktur ist, starten Sie die HPC Pack-Dienste auf dem Hauptknoten. Gehen Sie dazu folgendermaßen vor:

    a. Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie ein Domänenkonto verwenden, das Mitglied der lokalen Administratorgruppe ist. Beispielsweise können Sie das Administratorkonto nutzen, das Sie beim Erstellen des virtuellen Hauptknotencomputers eingerichtet haben.

    b. Starten Sie zur Durchführung einer Standardkonfiguration des Hauptknotens Windows PowerShell als Administrator, und geben Sie Folgendes ein:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Es kann mehrere Minuten dauern, bis die HPC Pack-Dienste starten.

    Geben Sie `get-help HPCHNPrepare.ps1` ein, um weitere Konfigurationsoptionen für den Hauptknoten anzuzeigen.


## Nächste Schritte

* Sie können jetzt mit dem Hauptknoten Ihres HPC Pack-Clusters arbeiten. Starten Sie z. B. den HPC-Cluster-Manager, und führen Sie die [Aufgabenliste für die Bereitstellung](https://technet.microsoft.com/library/jj884141.aspx) aus.
* Fügen Sie [Azure-Burstknoten](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) in einem Clouddienst hinzu, um die bedarfsgesteuerte Clustercomputekapazität heraufzusetzen. 

* Führen Sie eine Test-Workload auf dem Cluster aus. Ein Beispiel hierzu finden Sie im [Leitfaden für die ersten Schritte](https://technet.microsoft.com/library/jj884144) von HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0323_2016-->