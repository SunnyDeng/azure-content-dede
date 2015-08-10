<properties
 pageTitle="Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster | Microsoft Azure"
 description="Erfahren Sie mehr über die Bereitstellung eines HPC Pack-Clusters in Azure, die einen Head-Knoten unter Windows Server mit Linux Compute-Knoten enthält."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/27/2015"
 ms.author="danlep"/>

# Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure

Dieser Artikel veranschaulicht die Verwendung eines Azure PowerShell-Skripts zum Einrichten eines Microsoft HPC Pack-Clusters in Azure, das einen Head-Knoten unter Windows Server sowie mehrere Compute-Knoten unter einer CentOS Linux-Distribution enthält. Wir zeigen außerdem verschiedene Methoden zum Verschieben von Datendateien auf den Linux-Compute-Knoten. Mit diesem Cluster können Sie Linux HPC-Workloads in Azure ausführen.

Das folgende Diagramm gibt einen allgemeinen Überblick über den zu erstellenden HPC Pack-Cluster.

![HPC-Cluster mit Linux-Knoten][scenario]

## Bereitstellen eines HPC Pack-Clusters mit Linux Compute-Knoten

Verwenden Sie das Bereitstellungsskript Microsoft HPC Pack-IaaS \(\*\*New-HpcIaaSCluster.ps1\*\*\) zum Automatisieren der Clusterbereitstellung in Azure-Infrastrukturdiensten \(IaaS\). Dieses Azure PowerShell-Skript verwendet ein HPC Pack-VM-Image in Azure Marketplace für die schnelle Bereitstellung, und bietet eine umfassende Reihe von Konfigurationsparametern, um die Bereitstellung einfach und flexibel zu gestalten. Sie können das Skript zum Bereitstellen von virtuellen Azure-Netzwerken, Speicherkonten, Clouddiensten, Domänencontrollern, optional getrennten SQL Server-Datenbankservern, Cluster-Head-Knoten, Compute-Knoten, Broker-Knoten, Azure-PaaS \("Burst"\)-Knoten und Linux-Compute-Knoten \(Linux-Unterstützung wurde in [HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx) eingeführt\) verwenden.

Einen Überblick über die Optionen für die Bereitstellung von HPC Pack-Clusters finden Sie im [Handbuch mit den ersten Schritten für HPC Pack 2012 R2 und HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx).

### Voraussetzungen

* **Clientcomputer**: Sie benötigen einen Windows-basierten Clientcomputer für die Ausführung des Skripts zu Clusterbereitstellung.

* **Azure PowerShell**: [Installieren und konfigurieren Sie Azure PowerShell](../powershell-install-configure.md) \(ab Version 0.8.10\) auf Ihrem Clientcomputer.

* **HPC Pack-IaaS-Bereitstellungsskript**: Laden Sie die neueste Version des Skripts aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) herunter, und entpacken Sie sie. Führen Sie `New-HPCIaaSCluster.ps1 –Version` aus, um die Version des Skripts zu überprüfen. Dieser Artikel basiert auf der Skriptversion 4.4.0 oder höher.

* **Azure-Abonnement**: Sie können ein Abonnement entweder im Azure Global- oder Azure China-Dienst nutzen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

* **Kernkontingent**: Unter Umständen muss das Kontingent für die Kerne erhöht werden. Dies gilt insbesondere, wenn Sie mehrere Clusterknoten mit Multicore-VM-Größen bereitstellen. Für das Beispiel in diesem Artikel benötigen Sie mindestens 24 Kerne. Um ein Kontingent zu erhöhen, können Sie [eine gebührenfreie Onlinekundensupport-Anforderung öffnen](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

### Erstellen der Konfigurationsdatei
Das HPC Pack-IaaS-Bereitstellungsskript verwendet als Eingabe eine XML-Konfigurationsdatei mit der Beschreibung der Infrastruktur des HPC-Clusters. Ersetzen Sie Werte für Ihre Umgebung in der folgenden Beispielkonfigurationsdatei zum Bereitstellen eines kleinen Clusters bestehend aus einem Head-Knoten und zwei Linux-Compute-Knoten. Weitere Informationen zur Konfigurationsdatei finden Sie in der Datei „Manual.rtf“ \(im Skriptordner\) oder in der [Skriptdokumentation](https://msdn.microsoft.com/library/azure/dn864734.aspx).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
<VMSize>A4</VMSize>
<EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

Hier finden Sie kurze Beschreibungen der Elemente in der Konfigurationsdatei.

* **IaaSClusterConfig**: Stammelement der Konfigurationsdatei.

* **Abonnement**: Das Azure-Abonnement, das zur Bereitstellung des HPC Pack-Clusters verwendet wird. Stellen Sie mit dem folgenden Befehl sicher, dass der Name des Azure-Abonnements konfiguriert und auf dem Clientcomputer eindeutig ist. In diesem Beispiel verwenden wir das Azure-Abonnement "Abonnement-1".

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]Alternativ können Sie die Abonnement-ID für das Abonnement angeben, das Sie verwenden möchten. Sehen Sie sich die Datei „Manual.rtf“ im Skriptordner an.

* **Speicherkonto**: Alle permanenten Daten für das HPC Pack-Cluster werden im angegebenen Speicherkonto gespeichert \(in diesem Beispiel „Allvhdsje“\). Wenn das Speicherkonto nicht vorhanden ist, erstellt das Skript in der unter **Speicherort** angegebenen Region.

* **Speicherort**: Die Azure-Region, in der das HPC Pack-Cluster bereitgestellt wird \(Japan Ost in diesem Beispiel\).

* **VNet**: Einstellungen des virtuellen Netzwerks und des Subnetzes, in dem der HPC-Cluster erstellt werden wird. Sie können das virtuelle Netzwerk und das Subnetz selbst erstellen, bevor Sie dieses Skript ausführen, oder das Skript erstellt ein virtuelles Netzwerk mit dem Adressraum 192.168.0.0/20 und das Subnetz mit dem Adressraum 192.168.0.0/23. In diesem Beispiel erstellt das Skript das virtuelle Netzwerk „centos7rdmavnetje“ und das Subnetz „CentOS7RDMACluster“.

* **Domäne**: Einstellungen der Active Directory-Domäne für den HPC Pack-Cluster. Alle Windows-VMs, die vom Skript erstellt wurden, treten der Domäne bei. Das Skript unterstützt derzeit drei Domänenoptionen: ExistingDC, NewDC und HeadNodeAsDC. In diesem Beispiel wird der Head-Knoten als Domänencontroller konfiguriert. Der vollqualifizierte Domänenname ist „hpc.local“.

* **Datenbank**: Datenbankeinstellungen für den HPC Pack-Cluster. Das Skript unterstützt derzeit drei Datenbankoptionen: ExistingDB, NewRemoteDB und LocalDB. In diesem Beispiel wird eine lokale Datenbank auf dem Head-Knoten erstellt.

* **Head-Knoten**: Einstellungen für den Hauptknoten des HPC Pack. In diesem Beispiel wird ein Head-Knoten der Größe A7 mit dem Namen „CentOS7RDMA-HN“ im Clouddienst „centos7rdma-je“ erstellt. Um das Übermitteln von HPC-Aufträgen von Remoteclientcomputern \(keiner Domäne angehörend\) zu unterstützen, aktivirt das Skript die HPC-Auftragsplaner-REST-API und das HPC-Webportal.

* **LinuxComputeKnoten**: Einstellungen für den Linux Compute-Knoten des HPC Pack. In diesem Beispiel erstellen wir zwei CentOS 7-Linux-Compute-Knoten der Größe A7 \(„CentOS7RDMA-LN1“ und „CentOS7RDMA-LN2“\) im Clouddienst „centos7rdma-je“.

### Zusätzliche Hinweise zu Linux Compute-Knoten

* HPC Pack unterstützt derzeit die folgenden Linux-Distributionen für Compute-Knoten: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 und SUSE Linux Enterprise Server 12.

* Im Beispiel in diesem Artikel wird eine bestimmte im Azure Marketplace verfügbare CentOS-Version zum Erstellen des Clusters verwendet. Wenn Sie die anderen verfügbaren Images verwenden möchten, führen Sie das Azure PowerShell-Cmdlet **Get-Azurevmimage** aus, um das richtige zu finden. Führen Sie beispielsweise den folgenden Befehl zum Auflisten aller 7.0 CentOS-Images aus: ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Suchen Sie das benötigte Image und ersetzen Sie den Wert **ImageName** in der Konfigurationsdatei.

* Linux-Images, die RDMA-Verbindungen für VMs der Größe A8 und A9 unterstützen, sind verfügbar. Wenn Sie ein Image mit installierten und aktivierten Linux RDMA-Treibern angeben, wird das HPC Pack-IaaS-Bereitstellungsskript diese bereitstellen. Sie können z. B. den Image-Namen `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` für den aktuellen SUSE Linux Enterprise Server 12 angeben – optimiert für Hochleistungs-Compute-Images im Marketplace.


* Zum Aktivieren von Linux-RDMA auf dem virtuellen Linux-Computer, der aus unterstützten Images zur Erstellung von MPI-Aufträgen ausgeführt wird, installieren und konfigurieren Sie eine bestimmte MPI-Bibliothek auf den Linux-Knoten nach Clusterbereitstellung gemäß den Anforderungen Ihrer Anwendung. Weitere Informationen zur Verwendung von RDMA in Linux-Knoten in Azure finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).

* Stellen Sie sicher, dass Sie alle Linux RDMA-Knoten in einem Dienst bereitstellen, damit die RDMA-Netzwerkverbindung zwischen den Knoten funktioniert.


## Ausführen des HPC Pack-IaaS-Bereitstellungsskripts

1. Öffnen Sie die PowerShell-Konsole auf dem Clientcomputer als Administrator.

2. Navigieren Sie zum Skriptordner \(in diesem Beispiel „E:\\IaaSClusterScript“\).

    ```
cd E:\IaaSClusterScript
```

3. Führen Sie den folgenden Befehl aus, um den HPC Pack-Cluster bereitzustellen. In diesem Beispiel wird davon ausgegangen, dass sich die Konfigurationsdatei unter „E:\\HPCDemoConfig.xml“ befindet.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

    Das Skript generiert automatisch eine Protokolldatei, da der **-LogFile**-Parameter nicht angegeben ist. Die Protokolle werden nicht in Echtzeit geschrieben, sondern am Ende der Prüfung und der Bereitstellung gesammelt, sodass beim Beenden eines PowerShell-Prozesses bei gleichzeitiger Ausführung des Skripts einige Protokolle verloren gehen.

    a. Da im obigen Befehl kein **AdminPassword** angegeben wird, werden Sie dazu aufgefordert, das Kennwort für den Benutzer *MyAdminName* einzugeben.

    b. Das Skript beginnt dann mit der Überprüfung der Konfigurationsdatei. Je nach Netzwerkverbindung dauert dies Zehntelsekunden bis zu einigen Minuten.

    ![Überprüfen][validate]

    c. Nachdem die Überprüfungen erfolgreich waren, führt das Skript die Ressourcen auf, die für den HPC-Cluster erstellt werden. Geben Sie *Y* ein, um fortzufahren.

    ![Ressourcen][resources]

    d. Das Skript beginnt dann mit der Bereitstellung des HPC Pack-Clusters und schließt die Konfiguration ohne weitere manuelle Schritte ab. Dies kann einige Minuten dauern.

    ![Bereitstellen][deploy]

4. Nachdem die Konfiguration erfolgreich abgeschlossen ist, öffnen Sie Remotedesktop auf dem Head-Knoten, und öffnen Sie den HPC Cluster-Manager zum Überprüfen des Status des HPC Pack-Clusters. Sie können Linux-Compute-Knoten auf die gleiche Weise wie Windows-Compute-Knoten verwalten und überwachen. Beispielsweise sehen Sie die Linux-Knoten in der Knoten-Verwaltung aufgeführt.

    ![Knoten-Verwaltung][management]

    Außerdem sehen Sie die Linux-Knoten in der Heat Map-Ansicht.

    ![Heat Map][heatmap]

## Gewusst wie: Verschieben von Daten in einem Cluster mit Linux-Knoten

Sie haben mehrere Optionen zum Verschieben von Daten zwischen Linux-Knoten und dem Windows-Head-Knoten des Clusters. Wir stellen drei allgemeine Methoden vor.

* **Azure-Datei**: Stellt eine Dateifreigabe zum Speichern von Datendateien im Azure-Speicher her. Sowohl Windows-Knoten als auch Linux-Knoten können eine Azure-Dateifreigabe als ein Laufwerk oder Ordner gleichzeitig bereitstellen, auch wenn sie in verschiedenen virtuellen Netzwerken bereitgestellt werden.

* **Head-Knoten-SMB-Freigabe**: Lädt einen freigegebenen Ordner des Head-Knotens auf Linux-Knoten.

* **Head-Knoten-NFS-Server**: Bietet eine Lösung zur Dateifreigabe für eine gemischte Windows- und Linux-Umgebung.

### Azure-Datei

Der [Azure-Dateidienst](https://azure.microsoft.com/services/storage/files/) stellt Dateifreigaben mit dem SMB 2.1-Standardprotokoll bereit. Azure-VMs und Clouddienste können Dateidaten in verschiedenen Anwendungskomponenten über eingebundene Freigaben teilen, und lokale Anwendungen können über die Dateispeicher-API auf freigegebene Dateien zugreifen. Weitere Informationen finden Sie unter [Verwenden des Azure-Dateispeichers mit PowerShell und .NET](../storage/storage-dotnet-how-to-use-files.md).

Zum Erstellen einer Azure-Dateifreigabe finden Sie eine detaillierte Anleitung unter [Einführung in den Microsoft Azure-Dateidienst](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Zum Einrichten bestehender Verbindungen finden Sie Informationen unter [Beibehalten der Verbindung zu Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

In diesem Beispiel erstellen wir eine Azure-Dateifreigabe mit dem Namen „Rdma“ auf unseren Speicherkonto „Allvhdsje“. Um die Freigabe auf den Head-Knoten zu laden, öffnen Sie ein Befehlsfenster und geben Sie die folgenden Befehle ein:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

In diesem Beispiel ist „Allvhdsje“ der Name des Speicherkontos, „Storageaccountkey“ ist der Speicherkontoschlüssel und „Rdma“ ist der Name der Azure-Dateifreigabe. Die Azure-Dateifreigabe wird auf Z: auf dem Head-Knoten bereitgestellt.

Führen Sie zum Bereitstellen der Azure-Dateifreigabe auf Linux-Knoten einen **Clusrun**-Befehl auf dem Head-Knoten aus. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** ist ein nützliches Tool für HPC Pack zum Ausführen administrativer Aufgaben auf mehreren Knoten. \(Siehe auch [ClusRun für Linux-Knoten](#CLusrun-for-Linux-nodes) in diesem Artikel.\)

Öffnen Sie ein Windows PowerShell-Fenster und geben Sie die folgenden Befehle ein.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Der erste Befehl erstellt einen Ordner namens „/rdma“ auf allen Knoten in der „LinuxNodes“-Gruppe. Der zweite Befehl lädt die Azure-Dateifreigabe „allvhdsjw.file.core.windows.net/rdma“ auf den Ordner „/rdma“ mit Dir- und Datei-Modus-Bits auf 777 gesetzt. Im zweiten Befehl ist „Allvhdsje“ der Name Ihres Speicherkontos und „Storageaccountkey“ ist der Speicherkontoschlüssel.

>[AZURE.NOTE]Das „\`“-Symbol im zweiten Befehl ist ein Escapesymbol für PowerShell. “\`,” bedeutet, das „,“ \(Komma\) ist ein Teil des Befehls.

### Head-Knoten-Freigabe

Alternativ können Sie einen freigegebenen Ordner des Head-Knotens auf Linux-Knoten laden. Dies ist die einfachste Methode zum Freigeben von Dateien, allerdings müssen der Head-Knoten und alle Linux-Knoten im gleichen virtuellen Netzwerk bereitgestellt werden. Gehen Sie wie folgt vor:

1. Erstellen Sie einen Ordner auf dem Head-Knoten, und geben Sie ihn für alle Benutzer mit Lese-/Schreibberechtigungen frei. Beispielsweise geben wir „D:\\OpenFOAM“ auf dem Head-Knoten als „\\CentOS7RDMA-HN\\OpenFOAM“ frei. Hier ist „CentOS7RDMA-HN“ der Hostname des Head-Knotens.

    ![Berechtigungen für die Dateifreigabe][fileshareperms]

    ![Dateifreigabe][filesharing]

2. Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie die folgenden Befehle aus, um den freigegebenen Ordner bereitzustellen.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>,password='<password>’,dir_mode=0777`,file_mode=0777
```

Der erste Befehl erstellt einen Ordner namens „/openfoam“ auf allen Knoten in der „LinuxNodes“-Gruppe. Der zweite Befehl lädt den freigegebenen Ordner „//CentOS7RDMA-HN/OpenFOAM“ in den Ordner, mit Dir- und Datei-Modus-Bits auf 777 festgelegt. Der Benutzername und das Kennwort im Befehl sollten gleich dem Benutzernamen und dem Kennwort eines Benutzers auf dem Head-Knoten sein.

>[AZURE.NOTE]Das „\`“-Symbol im zweiten Befehl ist ein Escapesymbol für PowerShell. “\`,” bedeutet, das „,“ \(Komma\) ist ein Teil des Befehls.


### NFS-Server

Der NFS-Dienst ermöglicht Benutzern die Freigabe und das Migrieren von Dateien zwischen Computern des Betriebssystems Windows Server 2012 mithilfe des SMB-Protokolls und Linux-Computern mithilfe des NFS-Protokolls. Der NFS-Server und alle anderen Knoten müssen im gleichen virtuellen Netzwerk bereitgestellt werden. Dies bietet eine bessere Kompatibilität mit Linux-Knoten im Vergleich zu einer SMB-Freigabe. Beispielsweise werden Dateilinks unterstützt.

1. Zum Installieren und Einrichten eines NFS-Servers führen Sie die Schritte unter [Server for Network File System First Share End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx) \(auf Englisch\) aus.

    Erstellen Sie z. B. eine NFS-Freigabe mit dem Namen „Nfs“ mit den folgenden Eigenschaften.

    ![NFS-Autorisierung][nfsauth]

    ![Berechtigungen für die NFS-Freigabe][nfsshare]

    ![Berechtigungen für die NFS NTFS-Freigabe][nfsperm]

    ![NFS-Verwaltungseigenschaften][nfsmanage]

2. Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie den folgenden Befehl aus, um die NFS-Freigabe bereitzustellen.

    ```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
```

    Der erste Befehl erstellt einen Ordner namens „/nfsshared“ auf allen Knoten in der „LinuxNodes“-Gruppe. Der zweite Befehl lädt die NFS-Freigabe „CentOS7RDMA-HN:/nf“ auf den Ordner. Hier ist „CentOS7RDMA-HN:/nf“ der Remotepfad der NFS-Freigabe.

## So senden Sie Aufträge ab
Es gibt verschiedene Methoden zum Übermitteln von Aufträgen an das HPC Pack-Cluster.

* HPC Cluster-Manager oder HPC-Auftrags-Manager-GUI

* HPC-Webportal

* REST-API

Das Senden eines Auftrags an den Cluster in Azure über HPC Pack-GUI-Tools und dem HPC-Webportal ist das gleiche wie für Windows-Compute-Knoten. Informationen finden Sie unter [HPC Pack-Auftrags-Manager](https://technet.microsoft.com/library/ff919691.aspx) und [So senden Sie Aufträge von einem lokalen Client ab](https://msdn.microsoft.com/library/azure/dn689084.aspx).

Zum Übermitteln von Aufträgen über die REST-API sehen Sie nach unter [Erstellen und Übermitteln von Aufträgen mithilfe der REST-API in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Informationen finden Sie auch im Python-Beispiel im [HPC Pack-SDK](https://www.microsoft.com/download/details.aspx?id=47756), um Aufträge von einem Linux-Client zu senden.

## ClusRun für Linux-Knoten

Das HPC Pack-Tool **Clusrun** kann zum Ausführen von Befehlen auf Linux-Knoten verwendet werden, entweder über ein Befehlsfenster oder den HPC Cluster-Manager. Hier einige Beispiele.

* Anzeigen aktueller Benutzernamen aller Knoten im Cluster

    ```
> clusrun whoami
```

* Installieren Sie das **Gdb**-Debugger-Tool mit **Yum** auf allen Knoten in der Linux-Knotengruppe, und starten Sie sie nach 10 Minuten erneut.

    ```
> clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
```

* Erstellen Sie ein Shellskript, das 1 bis 10 pro Sekunde auf Knoten im Cluster anzeigt, führen Sie es aus, und zeigen Sie die Ausgaben von jedem Knoten sofort an.

    ```
> clusrun /interleaved echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh```

>[AZURE.NOTE]Sie müssen möglicherweise bestimmte Escapezeichen in „Clusrun“-Befehlen verwenden. Geben Sie „^“ in einem Befehlsfenster und „'“ in PowerShell ein, um Sonderzeichen zu transformieren. Beispielsweise müssen in PowerShell die Kommas und Semikolons durch „'“ bzw. „'“ transformiert werden. Diese Zeichen erfordern keine Transformation in einem Befehlsfenster.




## Nächste Schritte

* Verwenden Sie **Clusrun** zum Installieren der Linux-Anwendung auf dem Linux-Compute-Knoten und zum Übermitteln eines Auftrags mit dem HPC Pack-Cluster.

* Versuchen Sie, das Cluster auf eine größere Anzahl von Knoten zu skalieren, oder stellen Sie Compute-Knoten der Größe [A8 oder A9](virtual-machines-a8-a9-a10-a11-specs.md) zum Ausführen von MPI-Workloads bereit.


<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=July15_HO5-->