<properties
 pageTitle="Einrichten eines Windows RDMA-Clusters zum Ausführen von MPI-Anwendungen | Microsoft Azure"
 description="Erfahren Sie, wie Sie einen Windows HPC Pack-Cluster mit VMs der Größe A8 oder A9 erstellen, um das Azure RDMA-Netzwerk zum Ausführen von MPI-Apps zu verwenden."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="01/13/2016"
 ms.author="danlep"/>

# Einrichten eines Windows RDMA-Clusters mit HPC Pack sowie A8- und A9-Instanzen zum Ausführen von MPI-Anwendungen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


In diesem Artikel wird beschrieben, wie Sie einen Windows RDMA-Cluster mit [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) und [rechenintensiven Instanzen der Größe A8 und A9](virtual-machines-a8-a9-a10-a11-specs.md) in Azure einrichten, um parallele MPI-Anwendungen (Message Passing Interface) auszuführen. Wenn Sie Windows Server-basierte A8- und A9-Instanzen für die Ausführung in einem HPC Pack-Cluster einrichten, erfolgt die Kommunikation der MPI-Anwendungen in Azure effizient über ein auf RDMA-Technologie (Remote Direct Memory Access) basierendem Netzwerk mit niedriger Latenz und hohem Durchsatz.

Weitere Informationen zum Ausführen von MPI-Workloads auf virtuellen Linux-Computern, die auf das Azure RDMA-Netzwerk zugreifen, finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).

## Optionen für die Bereitstellung von HPC Pack-Clustern
Microsoft HPC Pack ist ein empfohlenes Tool zum Erstellen von Windows Server-basierten HPC-Clustern in Azure. Bei Verwendung mit A8- und A9-Instanzen bietet HPC Pack eine effiziente Methode zum Ausführen Windows-basierter MPI-Anwendungen, die auf das RDMA-Netzwerk in Azure zugreifen. HPC Pack enthält eine Laufzeitumgebung für die Microsoft-Implementierung von Message Passing Interface für Windows (MSMPI).

In diesem Artikel werden zwei Szenarien zur Bereitstellung von A8- und A9-Clusterinstanzen mit Microsoft HPC Pack vorgestellt.

* Szenario 1: Bereitstellen rechenintensiver Workerrolleninstanzen (PaaS)

* Szenario 2: Bereitstellen von Computeknoten in rechenintensiven virtuellen Computern (IaaS)

## Voraussetzungen

* **Überprüfen [Sie Hintergrundinformationen und Hinweise](virtual-machines-a8-a9-a10-a11-specs.md)** zu den rechenintensiven Instanzen.


* **Azure-Abonnement** – Wenn Sie über kein Konto verfügen, können Sie in wenigen Minuten ein kostenloses Testkonto einrichten. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).


* **Kernnutzungskontingent** – Sie müssen möglicherweise das Kernnutzungskontingent erhöhen, um einen Cluster mit virtuellen A8- oder A9-Computern bereitzustellen. Beispielsweise benötigen Sie mindestens 128 Kerne, wenn Sie acht A9-Instanzen mit HPC Pack bereitstellen möchten. Um ein Kontingent zu erhöhen, können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

## Szenario 1: Bereitstellen rechenintensiver Workerrolleninstanzen (PaaS)


Fügen Sie aus einem vorhandenen HPC Pack-Cluster zusätzliche Computeressourcen in Azure-Workerrolleninstanzen (Azure-Knoten) hinzu, die in einem Clouddienst (PaaS) ausgeführt werden. Diese Funktion wird auch als "Burst auf Azure" mit HPC Pack bezeichnet und unterstützt verschiedene Größen für die Workerrolleninstanzen. Um rechenintensive Instanzen zu verwenden, geben Sie einfach die Größe A8 oder A9 beim Hinzufügen von Azure-Knoten an.

Im Folgenden werden die Überlegungen und Schritte zum Burst auf Azure-Instanzen der Größe A8 oder A9 aus einem vorhandenen (normalerweise lokalen) Cluster dargestellt. Verwenden Sie ähnliche Verfahren, um einem HPC Pack-Hauptknoten, der in einer Azure-VM bereitgestellt wird, Workerrolleninstanzen hinzufügen.

>[AZURE.NOTE] Ein Tutorial zum Burst auf Azure mit HPC Pack finden Sie unter [Einrichten eines Hybridclusters mit HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Beachten Sie die Überlegungen in den folgenden Schritten, die speziell für Azure-Knoten der Größen A8 und A9 gelten.

![Burst auf Azure][burst]

### Überlegungen zur Verwendung von A8- und A9-Instanzen

* **Proxyknoten** – In jeder Burst-auf-Azure-Bereitstellung mit rechenintensiven Instanzen stellt HPC Pack automatisch mindestens zwei Instanzen der Größe A8 als Proxyknoten bereit, zusätzlich zu den von Ihnen angegebenen Azure-Workerrolleninstanzen. Die Proxyknoten verwenden Kerne, die dem Abonnement zugeordnet sind, und verursachen zusammen mit den Azure-Workerrolleninstanzen Gebühren.

### Schritte

4. **Bereitstellen und Konfigurieren eines HPC Pack 2012 R2-Hauptknotens**

    Laden Sie das neueste HPC Pack-Installationspaket aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922) herunter. Anforderungen und Anleitungen zur Vorbereitung der Azure-Burstbereitstellung finden Sie im [Handbuch mit ersten Schritten für HPC Pack](https://technet.microsoft.com/library/jj884144.aspx) und unter [Zugriff auf Azure mit Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Konfigurieren eines Verwaltungszertifikats im Azure-Abonnement**

    Konfigurieren Sie ein Zertifikat, um die Verbindung zwischen dem Hauptknoten und Azure zu sichern. Optionen und Verfahren finden Sie unter [Szenarien zum Konfigurieren des Azure-Verwaltungszertifikats für HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Für Testbereitstellungen installiert HPC Pack ein Zertifikat mit dem Namen „Default Microsoft HPC Azure Management“, das Sie schnell in Ihr Azure-Abonnement hochladen können.

6. **Erstellen eines neuen Clouddiensts und eines Speicherkontos**

    Verwenden Sie das klassische Azure-Portal, um einen Clouddienst und ein Speicherkonto für die Bereitstellung in einer Region zu erstellen, in der rechenintensive Instanzen verfügbar sind.

7. **Erstellen einer Azure-Knotenvorlage**

    Verwenden Sie den Assistenten zum Erstellen von Knotenvorlagen in HPC Cluster Manager. Die Schritte finden Sie unter [Erstellen einer Azure-Knotenvorlage](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) im Thema mit den Schritten zum Bereitstellen von Azure-Knoten mit Microsoft HPC Pack.

    Für Anfangstests wird empfohlen, eine manuelle Verfügbarkeitsrichtlinie in der Vorlage zu konfigurieren.

8. **Hinzufügen von Knoten zum Cluster**

    Verwenden Sie den Assistenten zum Hinzufügen von Knoten in HPC Cluster Manager. Weitere Informationen finden Sie unter [Hinzufügen von Azure-Knoten zum Windows HPC-Cluster](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Wenn Sie die Größe der Knoten angeben, wählen Sie A8 oder A9 aus.

9. **Starten (Bereitstellen) der Knoten und Onlineschalten der Knoten zum Ausführen von Aufträgen**

    Wählen Sie die Knoten aus, und verwenden Sie die Aktion **Starten** in HPC Cluster Manager. Wenn die Bereitstellung abgeschlossen ist, wählen Sie die Knoten aus, und verwenden Sie die Aktion **Online schalten** in HPC Cluster Manager. Die Knoten sind zum Ausführen von Aufträgen bereit.

10. **Übermitteln von Aufträgen an den Cluster**

    Verwenden Sie HPC Pack-Tools zur Auftragsübermittlung, um Clusteraufträge auszuführen. Weitere Informationen finden Sie unter [Microsoft HPC Pack: Auftragsverwaltung](http://technet.microsoft.com/library/jj899585.aspx).

11. **Beenden von Knoten (Aufheben der Bereitstellung)**

    Wenn Sie die Aufträge ausgeführt haben, schalten Sie die Knoten offline, und verwenden Sie die Aktion **Beenden** in HPC Cluster Manager.





## Szenario 2: Bereitstellen von Computeknoten in rechenintensiven virtuellen Computern (IaaS)

In diesem Szenario stellen Sie den HPC Pack-Hauptknoten und Clusterserverknoten auf virtuellen Computern bereit, die zu einer Active Directory-Domäne in einem virtuellen Azure-Netzwerk gehören. HPC Pack bietet eine Reihe von [Bereitstellungsoptionen in Azure-VMs](virtual-machines-hpcpack-cluster-options.md), u. a. automatisierte Bereitstellungsskripts und Azure-Schnellstartvorlagen. Beispielsweise wird in den unten aufgeführten Überlegungen und Schritten die Verwendung des [HPC Pack-IaaS-Bereitstellungsskripts](virtual-machines-hpcpack-cluster-powershell-script.md) empfohlen, um den Großteil dieses Prozesses zu automatisieren.

![Cluster in Azure-VMs][iaas]

### Überlegungen zur Verwendung von A8- und A9-Instanzen

* **Virtuelles Netzwerk** – Geben Sie ein neues virtuelles Netzwerk in einer Region an, in der die A8- und A9-Instanzen verfügbar sind.


* **Windows Server-Betriebssystem** – Um RDMA-Verbindungen zu unterstützen, geben Sie ein Windows Server 2012 R2- oder Windows Server 2012-Betriebssystem für die Computeknoten-VMs der Größe A8 oder A9 an.


* **Clouddienste** – Es wird empfohlen, den Hauptknoten in einem Clouddienst und die A8- und A9-Computeknoten in einem anderen Clouddienst bereitzustellen.


* **Größe des Hauptknotens** – Verwenden Sie beim Hinzufügen von Computeknoten-VMs der Größe A8 oder A9 eine Größe von mindestens A4 (sehr groß) für den Hauptknoten.

* **HpcVmDrivers-Erweiterung** – Das Bereitstellungsskript installiert den Azure-VM-Agent und die HpcVmDrivers-Erweiterung automatisch bei der Bereitstellung von Computeknoten der Größe A8 oder A9 mit einem Windows Server-Betriebssystem. Mit HpcVmDrivers werden auf den Computeknoten-VMs Treiber installiert, damit eine Verbindung mit dem RDMA-Netzwerk hergestellt werden kann.

* **Clusternetzwerkkonfiguration** – Das Bereitstellungsskript konfiguriert automatisch den HPC Pack-Cluster in Topologie 5 (alle Knoten im Unternehmensnetzwerk). Diese Topologie ist für alle HPC Pack-Clusterbereitstellungen auf VMs erforderlich, einschließlich solcher mit Computeknoten der Größe A8 oder A9. Ändern Sie die Topologie des Clusternetzwerks später nicht.

### Schritte

1. **Erstellen eines Clusterhauptknotens und von Computeknoten-VMs durch Ausführen des IaaS-Bereitstellungsskripts von HPC Pack auf einem Clientcomputer**

    Laden Sie das HPC Pack-Paket mit dem IaaS-Bereitstellungsskript aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922) herunter.

    Erstellen Sie zur Vorbereitung des Clientcomputers die Skriptkonfigurationsdatei, und führen Sie das Skript aus. Weitere Informationen finden Sie unter [Erstellen eines HPC-Clusters mit dem IaaS-Bereitstellungsskript von HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md). Informationen zur Bereitstellung von Computeknoten der Größen A8 und A9 finden Sie unter den Überlegungen weiter unten in diesem Artikel.

2. **Onlineschalten der Computeknoten zum Ausführen von Aufträgen**

    Wählen Sie die Knoten aus, und verwenden Sie die Aktion **Online schalten** in HPC Cluster Manager. Die Knoten sind zum Ausführen von Aufträgen bereit.

3. **Übermitteln von Aufträgen an den Cluster**

    Stellen Sie eine Verbindung mit dem Hauptknoten her, um Aufträge zu übermitteln, oder richten Sie dafür einen lokalen Computer ein. Weitere Informationen finden Sie unter [Übermitteln von Aufträgen an einen HPC-Cluster in Azure](virtual-machines-hpcpack-cluster-submit-jobs.md).

4. **Offlineschalten und Beenden der Knoten (Aufheben der Zuordnung)**

    Wenn Sie die Aufträge ausgeführt haben, schalten Sie die Knoten in HPC Cluster Manager offline. Verwenden Sie dann Azure-Verwaltungstools, um sie herunterzufahren.




## Ausführen von MPI-Anwendungen auf den A8- und A9-Instanzen

### Beispiel: Ausführen von "mpipingpong" auf einem HPC Pack-Cluster

Um eine HPC Pack-Bereitstellung der rechenintensiven Instanzen zu überprüfen, führen Sie den HPC Pack-Befehl **mpipingpong** in dem Cluster aus. Mit **mpipingpong** werden wiederholt Datenpakete zwischen Knotenpaaren gesendet, um Latenz- und Durchsatzwerte sowie Statistiken für das RDMA-fähige Anwendungsnetzwerk zu berechnen. Dieses Beispiel zeigt ein typisches Muster für die Ausführung eines MPI-Auftrags (in diesem Fall **mpipingpong**) mit dem Clusterbefehl **mpiexec**.

In diesem Beispiel wird davon ausgegangen, dass Sie Azure-Knoten in einer „Burst-auf-Azure“-Konfiguration hinzugefügt haben ([Szenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS)) in diesem Artikel). Wenn Sie HPC Pack auf einem Cluster von Azure-VMs bereitgestellt haben, müssen Sie die Befehlssyntax ändern, um eine andere Knotengruppe anzugeben, und zusätzliche Umgebungsvariablen festlegen, um Netzwerkdatenverkehr an das RDMA-Netzwerk weiterzuleiten.


So führen Sie "mpipingpong" auf dem Cluster aus


1. Öffnen Sie eine Eingabeaufforderung auf dem Hauptknoten oder auf einem ordnungsgemäß konfigurierten Clientcomputer.

2. Geben Sie zum Schätzen der Latenz zwischen Knotenpaaren in einer Azure-Burstbereitstellung mit vier Knoten den folgenden Befehl ein, um einen Auftrag zum Ausführen von "mpipingpong" mit einer geringen Paketgröße und einer großen Anzahl von Iterationen zu senden:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    Der Befehl gibt die ID des Auftrags zurück, der gesendet wird.

    Wenn Sie den HPC Pack-Cluster auf Azure-VMs bereitgestellt haben, geben Sie eine Knotengruppe an, die Computeknoten-VMs enthält, die in einem Clouddienst bereitgestellt wurden, und ändern Sie den Befehl **mpiexec** wie folgt:

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Geben Sie nach Abschluss des Auftrags zum Anzeigen der Ausgabe (in diesem Fall die Ausgabe von Aufgabe 1 des Auftrags) Folgendes ein:

    ```
    task view <JobID>.1
    ```

    &lt;*JobID*&gt; ist dabei die ID des Auftrags, der gesendet wurde.

    Die Ausgabe enthält Latenzergebnisse wie die folgenden.

    ![Pingpong-Latenz][pingpong1]

4. Geben Sie zum Schätzen des Durchsatzes zwischen Paaren von Azure-Burstknoten den folgenden Befehl ein, um einen Auftrag zum Ausführen von **mpipingpong** mit einer großen Paketgröße und einer kleinen Anzahl von Iterationen zu senden:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    Der Befehl gibt die ID des Auftrags zurück, der gesendet wird.

    Ändern Sie auf einem HPC Pack-Cluster, der auf Azure-VMs bereitgestellt wurde, den Befehl wie in Schritt 2 beschrieben.

5. Geben Sie nach Abschluss des Auftrags zum Anzeigen der Ausgabe (in diesem Fall die Ausgabe von Aufgabe 1 des Auftrags) Folgendes ein:

    ```
    task view <JobID>.1
    ```

  Die Ausgabe enthält Durchsatzergebnisse wie die folgenden.

  ![Pingpong-Durchsatz][pingpong2]


### Überlegungen zu MPI-Anwendungen


Im Folgenden finden Sie Überlegungen zum Ausführen von MPI-Anwendungen auf Azure-Instanzen. Einige gelten nur für Bereitstellungen von Azure-Knoten (Workerrolleninstanzen, die in einer "Burst-auf-Azure"-Konfiguration hinzugefügt wurden).

* Workerrolleninstanzen in einem Clouddienst werden in regelmäßigen Abständen ohne vorherige Ankündigung von Azure erneut bereitgestellt (beispielsweise zur Systemwartung oder beim Ausfall einer Instanz). Wenn eine Instanz erneut bereitgestellt wird, während sie einen MPI-Auftrag ausführt, verliert die Instanz alle Daten und nimmt wieder den Zustand ihrer erstmaligen Bereitstellung an, was einen Fehler des MPI-Auftrags verursachen kann. Je mehr Knoten Sie für einen einzelnen MPI-Auftrag verwenden, und je länger der Auftrag ausgeführt wird, umso wahrscheinlicher ist es, dass eine der Instanzen während der Ausführung eines Auftrags erneut bereitgestellt wird. Sie sollten dies auch berücksichtigen, wenn Sie einen einzelnen Knoten in der Bereitstellung als Dateiserver festlegen.


* Sie müssen keine A8- und A9-Instanzen verwenden, um MPI-Aufträge in Azure auszuführen. Die können beliebige Instanzgrößen nutzen, die von HPC Pack unterstützt werden. Die A8- und A9-Instanzen werden jedoch für die Ausführung von relativ umfangreichen MPI-Aufträge empfohlen, die von der Latenz und der Bandbreite des Netzwerks abhängen, das die Knoten miteinander verbindet. Wenn Sie andere Instanzen als A8 und A9 zum Ausführen von latenz- und bandbreitenabhängigen MPI-Aufträgen verwenden, empfehlen wir die Ausführung kleinerer Aufträge, bei denen eine einzelne Aufgabe nur auf wenigen Knoten ausgeführt wird.

* Für Anwendungen, die auf Azure-Instanzen bereitgestellt werden, gelten die Lizenzbedingungen der Anwendung. Informieren Sie sich bei Verwendung kommerzieller Anwendungen beim jeweiligen Hersteller über lizenzbedingte oder anderweitige Einschränkungen für die Ausführung in der Cloud. Nicht alle Hersteller bieten ein nutzungsbasiertes Lizenzmodell an.


* Azure-Instanzen benötigen ein erweitertes Setup für den Zugriff auf lokale Knoten, Freigaben und Lizenzserver. Beispiel: Um den Azure-Knoten den Zugriff auf einen lokalen Lizenzserver zu ermöglichen, können Sie in Azure ein virtuelles Site-to-Site-Netzwerk konfigurieren.


* Zum Ausführen von MPI-Anwendungen auf Azure-Instanzen registrieren Sie jede MPI-Anwendung bei der Windows-Firewall auf den Instanzen, indem Sie den Befehl **hpcfwutil** ausführen. Dadurch kann die MPI-Kommunikation über einen Port erfolgen, der von der Firewall dynamisch zugewiesen wird.

    >[AZURE.NOTE] Für Burst-auf-Azure-Bereitstellungen können Sie auch einen Befehl für eine Firewallausnahme konfigurieren, der automatisch auf allen neuen Azure-Knoten ausgeführt wird, die dem Cluster hinzugefügt werden. Nachdem Sie den Befehl **hpcfwutil** ausgeführt und sichergestellt haben, dass Ihre Anwendung funktioniert, fügen Sie den Befehl einem Startskript für die Azure-Knoten hinzu. Weitere Informationen finden Sie unter [Verwenden eines Startskripts für Azure-Knoten](https://technet.microsoft.com/library/jj899632.aspx).



* HPC Pack verwendet die Clusterumgebungsvariable CCP\_MPI\_NETMASK, um einen Bereich zulässiger Adressen für die MPI-Kommunikation anzugeben. Seit HPC Pack 2012 R2 wirkt sich die Clusterumgebungsvariable CCP\_MPI\_NETMASK nur auf die MPI-Kommunikation zwischen Clustercomputeknoten aus, die zu einer Domäne gehören (entweder lokal oder in Azure-VMs). Die Variable wird von Knoten ignoriert, die einer Burst-auf-Azure-Konfiguration hinzugefügt werden.


* MPI-Aufträge können nicht auf Azure-Instanzen ausgeführt werden, die in verschiedenen Clouddiensten bereitgestellt wurden (z. B. in Burst-auf-Azure-Bereitstellungen mit unterschiedlichen Knotenvorlagen oder auf Azure-VM-Computeknoten, die in mehreren Clouddiensten bereitgestellt wurden). Wenn Sie über mehrere Azure-Knotenbereitstellungen verfügen, die mit unterschiedlichen Knotenvorlagen gestartet werden, darf der MPI-Auftrag nur auf einem Satz von Azure-Knoten ausgeführt werden.


* Wenn Sie Azure-Knoten zum Cluster hinzufügen und online schalten, versucht der HPC-Auftragsplanungsdienst sofort, Aufträge auf den Knoten zu starten. Wenn nur ein Teil Ihrer Workload in Azure ausgeführt werden kann, stellen Sie sicher, dass Sie Auftragsvorlagen aktualisieren oder erstellen, um exakt zu definieren, welche Auftragstypen auf Azure ausgeführt werden können. Um beispielsweise sicherzustellen, dass mit einer Auftragsvorlage übermittelte Aufträge nur auf Azure-Knoten ausgeführt werden, fügen Sie der Auftragsvorlage die Knotengruppeneigenschaft hinzu, und wählen Sie "AzureNodes" als den erforderlichen Wert aus. Wenn Sie benutzerdefinierte Gruppen für die Azure-Knoten erstellen, verwenden Sie das HPC PowerShell-Cmdlet Add-HpcGroup.


## Nächste Schritte

* Weitere Informationen zum Ausführen von Linux MPI-Anwendungen, die auf das Azure RDMA-Netzwerk zugreifen, finden Sie unter [Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen](virtual-machines-linux-cluster-rdma.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=AcomDC_0128_2016-->