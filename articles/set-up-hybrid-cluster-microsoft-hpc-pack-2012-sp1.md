<properties 
	pageTitle="Einrichten eines Hybrid-Rechenclusters mit Microsoft HPC Pack" 
	description="Erfahren Sie mehr über die Verwendung von Microsoft HPC Pack und Azure für die Einrichtung eines kleinen HPC-Hybridclusters (High Performance Computing)." 
	services="cloud-services" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2015" 
	ms.author="danlep"/>


# Einrichten eines Hybrid-Rechenclusters mit Microsoft HPC Pack
Dieses Lernprogramm zeigt Ihnen, wie Sie mit Microsoft HPC Pack 2012 R2 und Azure ein kleines Hybrid-HPC-Cluster (High Performance Computing) einrichten. Das Cluster wird aus einem lokalen Hauptknoten (einem Computer mit dem Windows Server-Betriebssystem und HPC Pack) und einigen Serverknoten bestehen, die Sie bei Bedarf als Workerrollen-Instanzen in einem Azure-Cloud-Dienst bereitstellen. Dann können Sie auf dem Hybrid-Cluster Rechenaufträge (Compute Jobs) ausführen.
 
![Hybrid HPC cluster][Overview] 

Dieses Lernprogramm zeigt einen gelegentlich als Cloud-Bursting bezeichneten Ansatz: Wie mit skalierbaren, bei Bedarf verfügbaren Serverressourcen in Azure rechenintensive Anwendungen ausgeführt werden können.

In diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit Serverclustern oder HPC Pack haben. Es soll Ihnen lediglich helfen, ein Hybrid-Rechencluster für Demonstrationszwecke schnell bereitzustellen. Welche Überlegungen und Schritte zur Bereitstellung eines größeren Hybrid-Clusters mit HPC-Pack in einer Produktionsumgebung anzustellen bzw. auszuführen sind, ist in der [ausführlichen Anleitung](http://go.microsoft.com/fwlink/p/?LinkID=200493) beschrieben. Wenn Sie ein komplettes HPC Pack-Cluster in Azure einrichten möchten, finden Sie unter [Microsoft HPC Pack in Azure-VMs](http://go.microsoft.com/fwlink/p/?linkid=330375). 

>[AZURE.NOTE] Azure bietet für unterschiedliche Arbeitslasten Serverressourcen [unterschiedlicher Größen](http://go.microsoft.com/fwlink/p/?LinkId=389844) an. Die Instanzen A8 und A9 kombinieren beispielweise hohe Leistung und Zugang zu einem Anwendungsnetz mit niedrigen Latenzzeiten und hohem Durchsatz, was für bestimmte HPC-Anwendungen benötigt wird. Einzelheiten finden Sie unter [Informationen zu den rechenintensiven A8- und A9-Instanzen](http://go.microsoft.com/fwlink/p/?Linkid=328042). 

<h2 id="BKMK_Prereq">Voraussetzungen</h2>

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Erstellen eines Azure-Kontos](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/).

Darüber hinaus benötigen Sie für dieses Lernprogramm Folgendes:

* Einen lokalen Computer, auf dem eine Edition von Windows Server 2012 R2 oder Windows Server 2012 ausgeführt wird. Dieser Computer wird der Hauptknoten des HPC-Clusters sein. Wenn Sie Windows Server noch nicht verwenden, können Sie eine [Evaluierungsversion](http://technet.microsoft.com/evalcenter/dn205286.aspx) herunterladen und installieren.

	* Der Computer muss in eine Active Directory-Domäne eingebunden werden.

	* Vergewissern Sie sich, dass keine zusätzlicher Serverrollen oder Rollendienste installiert sind.

	* Damit das HPC Pack unterstützt werden kann, muss das Betriebssystem in einer der folgenden Sprachen installiert werden: Englisch, Japanisch oder Chinesisch (vereinfacht).

	* Stellen Sie sicher, dass wichtige und kritische Updates installiert wurden.
	
* Installationsdateien für HPC Pack 2012 R2. Diese stehen kostenlos zur Verfügung. Die neueste Version ist HPC Pack 2012 R2 Update 1. [Laden](http://go.microsoft.com/fwlink/p/?linkid=328024) Sie das komplette Installationspaket herunter, und kopieren Sie die Dateien auf den Hauptknoten-Computer oder auf einen Speicherort im Netzwerk. Wählen Sie Installationsdateien in derselben Sprache aus wie für die Installation von Windows Server.

* Ein Domänenkonto mit lokalen Administratorrechten auf dem Hauptknoten.

* TCP-Konnektivität an Port 443 vom Hauptknoten zu Azure.

<h2 id="BKMK_DeployHN">Installieren des HPC Pack auf dem Hauptknoten</h2>

Zuerst muss das Microsoft HPC Pack auf einem lokalen Computer mit Windows Server installiert werden, der als Hauptknoten im Cluster fungiert.

1. Melden Sie sich bei dem Hauptknoten mit einem Domänenkonto an, das über lokale Administratorrechte verfügt.

2. Starten Sie den Installations-Assistenten von HPC Pack, indem Sie die in den HPC Pack-Installationsdateien enthaltene Datei Setup.exe ausführen.

3. Klicken Sie im **HPC Pack 2012 R2 Setup**-Bildschirm auf **New installation or add new features to an existing installation**.

	![HPC Pack 2012 Setup][install_hpc1]

4. Klicken Sie auf der Seite **Microsoft Software User Agreement** auf **Next**.

5. Klicken Sie auf der Seite **Select Installation Type** auf **Create a new HPC cluster by creating a head node** und dann auf **Next**.

	![Select Installation Type][install_hpc2]

6. Der Installations-Assistent führt vor der Installation verschiedene Tests durch. Klicken Sie auf der Seite **Installation Rules** auf **Next**, wenn alle Tests erfolgreich durchgeführt worden sind. Lesen Sie andernfalls die bereitgestellten Informationen, und nehmen Sie eventuell notwendige Änderungen in Ihrer Umgebung vor. Führen Sie dann die Tests nochmals aus, oder starten Sie gegebenenfalls den Installations-Assistenten erneut. 

	![Installation Rules][install_hpc3]

7. Vergewissern Sie sich, dass auf der Seite **HPC DB Configuration** der **Hauptknoten** für alle HPC-Datenbanken ausgewählt ist, und klicken Sie dann auf **Next**.

	![DB Configuration][install_hpc4]

8. Akzeptieren Sie auf den verbleibenden Seiten des Assistenten die Standardeinstellungen. Klicken Sie auf der Seite **Install Required Components** auf **Install**.

	![Install][install_hpc6]

9. Wenn die Installation abgeschlossen ist, deaktivieren Sie **Start HPC Cluster Manager** und klicken dann auf **Finish**. (Sie werden HPC Cluster Manager in einem späteren Schritt starten, um die Konfiguration des Hauptknotens abzuschließen.)

	![Finish][install_hpc7]

<h2 id="BKMK_Prepare">Erstellen des Azure-Abonnements</h2>
Führen Sie im [Verwaltungsportal](https://manage.windowsazure.com) nachfolgend beschriebene Schritte für Ihr Azure-Abonnement aus. Diese sind erforderlich, damit Sie später Azure-Knoten von dem lokalen Hauptknoten aus bereitstellen können. 

- Laden Sie ein Verwaltungszertifikat hoch (wird für sichere Verbindungen zwischen dem Hauptknoten und den Azure-Diensten benötigt)
 
- Erstellen Sie einen Azure-Cloud-Dienst, in dem Azure-Knoten (Workerrollen-Instanzen) laufen werden

- Erstellen eines Azure-Speicherkontos
	
	>[AZURE.NOTE]Notieren Sie auch Ihre Azure-Abonnement-ID, die Sie später noch brauchen werden. Sie finden diese Kennung in den Azure-<a href="[https://account.windowsazure.com/Subscriptions">Kontoinformationen</a>.

<h3>Hochladen des Standard-Verwaltungszertifikats</h3>
HPC Pack installiert im Hauptknoten ein selbstsigniertes Zertifikat mit dem Namen "Default Microsoft HPC Azure Management", das Sie als ein Azure-Verwaltungszertifikat hochladen können. Dieses Zertifikat wird für Testzwecke und Machbarkeitsstudien bereitgestellt.

1. Melden Sie sich vom Hauptknoten-Computer aus im [Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie auf **Einstellungen** und dann auf **Verwaltungszertifikate**.

3. Klicken Sie in der Befehlsleiste auf **Upload**.

	![Certificate Settings][upload_cert1]

4. Suchen Sie im Hauptknoten nach der Datei C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Klicken Sie dann auf das **Häkchen**.

	![Upload Certificate][install_hpc10]

In der Liste der Verwaltungszertifikate wird **Default HPC Azure Management** angezeigt.

<h3>Erstellen eines Azure-Cloud-Dienstes</h3> 

>[AZURE.NOTE]Erstellen Sie den Cloud-Dienst und das Speicherkonto zur Optimierung der Leistung in derselben geografischen Region.

1. Klicken Sie im Verwaltungsportal in der Befehlsleiste auf **Neu**.

2. Klicken Sie auf **Server**, dann auf **Cloud-Dienst** und schließlich auf **Schnellerfassung**.

3. Geben Sie eine URL für den Cloud-Dienst ein, und klicken Sie dann auf **Cloud-Dienst erstellen**.

	![Create Service][createservice1]

<h3>Erstellen eines Azure-Speicherkontos</h3>

1. Klicken Sie im Verwaltungsportal in der Befehlsleiste auf **Neu**.

2. Klicken Sie auf **Datendienste**, dann auf **Speicher** und schließlich auf **Schnellerfassung**.

3. Geben Sie eine URL für das Konto ein, und klicken Sie dann auf **Speicherkonto erstellen**.

	![Create Storage][createstorage1]

<h2 id="BKMK_ConfigHN">Konfigurieren des Hauptknotens</h2>

Bevor Sie mit HPC Cluster Manager Azure-Knoten bereitstellen und Aufträge übermitteln können, müssen noch einige Schritte zur Konfiguration des Clusters ausgeführt werden. 

1. Starten Sie HPC Cluster Manager auf dem Hauptknoten. Wenn das Dialogfeld **Select Head Node** angezeigt wird, klicken Sie auf **Local Computer**. Die **Deployment To-do List** wird angezeigt.

2. Klicken Sie unter **Required deployment tasks** auf **Configure your network**.

	![Configure Network][config_hpc2]

3. Wählen Sie im Netzwerkkonfigurations-Assistent **All nodes only on an enterprise network** (Topology 5) aus.

	![Topology 5][config_hpc3] 

	>[AZURE.NOTE]Dies ist die einfachste Konfiguration für Demonstrationszwecke, da der Hauptknoten für die Verbindung zu Active Directory und zum Internet nur einen einzigen Netzwerkadapter braucht. Clusterkonfigurationen, die mehrere Netzwerke benötigen, werden in diesem Lernprogramm nicht behandelt. 
	 
4. Klicken Sie auf **Next**, um auf den verbleibenden Seiten des Assistenten die Standardwerte zu akzeptieren. Klicken Sie dann auf der Registerkarte **Review** auf **Configure**, um die Netzwerkkonfiguration abzuschließen.

5. Klicken Sie in der **Deployment To-do List** auf **Provide installation credentials**. 

6. Geben Sie im Dialogfeld **Installation Credentials** die Anmeldeinformationen für das Domänenkonto ein, das Sie für die Installation von HPC Pack verwendet haben. Klicken Sie dann auf **OK**.

	![Installation Credentials][config_hpc6]

	>[AZURE.NOTE]HPC Pack-Dienste verwenden Installationsanmeldeinformationen nur für die Bereitstellung lokaler Serverknoten.
	
7. Klicken Sie in der **Deployment To-do List** auf **Configure the naming of new nodes**. 

8. Akzeptieren Sie im Dialogfeld **Specify Node Naming Series** die Standardbenennungsreihe, und klicken Sie auf **OK**.

	![Node Naming][config_hpc8]

	>[AZURE.NOTE]Die Benennungsreihe erzeugt nur Namen für Serverknoten, die in die Domäne eingebunden sind. Azure-Knoten werden automatisch benannt.
	  
9. Klicken Sie in der **Deployment To-do List** auf **Create a node template**. Sie brauchen die Knotenvorlage, um Azure-Knoten zum Cluster hinzuzufügen.

10. Führen Sie im Create Node Template Wizard, folgende Schritte aus:

	a. Klicken Sie auf der Seite **Choose Node Template Type** auf **Azure node template** und dann auf **Next**.

	![Node Template][config_hpc10]

	b. Klicken Sie auf **Next**, um den Standardvorlagennamen zu akzeptieren.

	c. Geben Sie auf der Seite **Provide Subscription Information** Ihre Azure-Abonnement-ID ein (zu finden in den Azure-<a href="[https://account.windowsazure.com/Subscriptions">Kontoinformationen</a>). Klicken Sie dann neben **Verwaltungszertifikat** auf **Browse**, und wählen Sie **Default HPC Azure Management**. Klicken Sie dann auf **Next**.

	![Node Template][config_hpc12]

	d. Wählen Sie auf der Seite **Provide Service Information** den Cloud-Dienst und das Speicherkonto aus, die Sie in einem vorherigen Schritt erstellt haben. Klicken Sie dann auf **Weiter**.

	![Node Template][config_hpc13]

	e. Klicken Sie auf **Next**, um auf den verbleibenden Seiten des Assistenten die Standardwerte zu akzeptieren. Klicken Sie dann auf der Registerkarte **Review** auf **Create**, um die Knotenvorlage zu erstellen.

	>[AZURE.NOTE]Standardmäßig enthält die Azure-Knotenvorlage unter anderem Einstellungen, mit denen Sie Knoten manuell starten (bereitstellen) und anhalten können. Sie können auch einen Zeitplan für das automatische Starten und Stoppen der Azure-Knoten konfigurieren. 
	
<h2 id="#BKMK_worker">Hinzufügen von Azure-Knoten zum Cluster</h2>

Sie werden nun mit der Knotenvorlage Azure-Knoten zu dem Cluster hinzufügen. Wenn Knoten zu dem Cluster hinzugefügt werden, werden deren Konfigurationsinformationen gespeichert, so dass Sie sie jederzeit als Rolleninstanzen im Cloud-Dienst starten (bereitstellen) können. Azure-Knoten werden Ihrem Abonnement erst belastet, wenn die Rolleninstanzen im Cloud-Dienst ausgeführt werden.

In diesem Lernprogramm werden Sie zwei kleine Knoten hinzufügen.

1. Klicken Sie im HPC Cluster Manager unter **Node Management** im Bereich **Actions** auf **Add Node**. 

	![Add Node][add_node1]

2. Klicken Sie im Assistenten zum Hinzufügen von Knoten auf der Seite **Select Deployment Method** auf **Add Azure nodes** und dann auf **Next**.

	![Add Azure Node][add_node1_1]

3. Wählen Sie auf der Seite **Specify New Nodes** die zuvor erstellte Azure-Knotenvorlage (mit dem Standardnamen **Default AzureNode Template**) aus. Geben Sie dann **2** Knoten der Größe **Small** an, und klicken Sie auf **Next**.

	![Specify Nodes][add_node2]

	Einzelheiten zu den Größen virtueller Computer denen Sie in [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).

4. Klicken Sie auf der Seite **Completing the Add Node Wizard** auf **Finish**. 

	 Jetzt werden zwei Azure-Knoten namens **AzureCN-0001** und **AzureCN-0002** im HPC Cluster Manager angezeigt. Beide befinden sich im Status **Not-Deployed**.

	![Added Nodes][add_node3]

<h2 id="BKMK_Start">Starten der Azure-Knoten</h2>
Wenn Sie die Clusterressourcen in Azure nutzen wollen, veranlassen Sie den Start (die Bereitstellung) der Azure-Knoten mit HPC Cluster Manager und schalten sie online.

1.	Klicken Sie im HPC Cluster Manager unter **Node Management** auf einen oder beide Knoten, und klicken Sie dann im Bereich **Actions** auf **Start**. 

	![Start Nodes][add_node4]

2. Klicken Sie im Dialogfeld **Start Azure Nodes** auf **Start**.

	![Start Nodes][add_node5]
 
	Die Knoten wechseln in den Status **Provisioning**. Im Bereitstellungsprotokoll können Sie den Fortschritt der Bereitstellung verfolgen.

	![Provision Nodes][add_node6]

3. Nach einigen Minuten ist die Bereitstellung der Azure-Knoten beendet, und die Knoten befinden sich im Status **Offline**. In diesem Status werden die Rolleninstanzen zwar akzeptieren, aber Clusteraufträge noch nicht angenommen.

4. Sie können wie folgt prüfen, ob die Rolleninstanzen laufen: Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com) auf **Cloud-Dienste**, dann auf den Namen Ihres Cloud-Dienstes und abschließend auf **Instances**. 

	![Running Instances][view_instances1]

	Sie sehen, dass in dem Dienst zwei Workerrollen-Instanzen ausgeführt werden. HPC Pack stellt außerdem automatisch zwei **HpcProxy**-Instanzen (mittlerer Größe) für die Verarbeitung der Kommunikation zwischen dem Hauptknoten und Azure bereit.

5. Um die Azure-Knoten zum Ausführen von Clusteraufträgen online zu schalten, wählen Sie die Knoten aus, klicken Sie mit der rechten Maustaste, und klicken Sie dann auf **Bring Online**.

	![Offline Nodes][add_node7]
 
	HPC Cluster Manager zeigt an, dass sich die Knoten im Status **Online** befinden.

<h2 id="BKMK_RunCommand">Clusterweites Ausführen eines Befehls</h2>	
Mit dem HPC Pack-Befehl **clusrun** können Sie einen Befehl oder eine Anwendung auf einem oder mehreren Clusterknoten ausführen. Verwenden Sie **clusrun** als einfaches Beispiel zum Abrufen der IP-Konfiguration der Azure-Knoten.

1. Öffnen Sie auf dem Hauptnoten eine Eingabeaufforderung.

2. Geben Sie den folgenden Befehl ein:

	`clusrun /nodes:azurecn* ipconfig`

3. Eine Ausgabe ähnlich der folgenden wird angezeigt.

	![Clusrun][clusrun1]

<h2 id="BKMK_RunJob">Ausführen eines Testauftrags</h2>

Sie können einen Testauftrag übermitteln, der auf dem Hybrid-Cluster ausgeführt wird. Dieses Beispiel ist ein einfacher "Parameter-Sweep-Auftrag" (eine Art in sich paralleler Rechenvorgang), der durch die Ausführung von Unteraufgaben mit dem Befehl **set /a** eine Ganzzahl zu sich selbst hinzufügt. Alle Knoten des Clusters wirken an der Ausführung der Unteraufgaben für Ganzzahlen von 1 bis 100 mit. 

1. Klicken Sie im HPC Cluster Manager unter **Job Management** im Bereich **Actions** auf **New Parametric Sweep Job**.

	![New Job][test_job1]

2. Geben Sie im Dialogfeld **New Parametric Sweep Job** neben **Command Line** `set /a *+*` ein, indem Sie den in der Befehlsleiste angezeigten Standardbefehl überschreiben. Behalten Sie für die verbleibenden Einstellungen die Standardwerte bei, und klicken Sie dann auf **Submit**, um den Auftrag zu übermitteln.

	![Parametric Sweep][param_sweep1]

3. Wenn der Auftrag abgeschlossen ist, doppelklicken Sie auf den Auftrag **My Sweep Task**.

4. Klicken Sie auf **View Tasks** und dann auf eine Unteraufgabe, um die berechnete Ausgabe dieser Unteraufgabe anzuzeigen.

	![Task Results][view_job361]

5. Wenn Sie sehen möchten, welcher Knoten die Berechnung für diese Unteraufgabe durchgeführt hat, klicken Sie auf **Allocated Nodes**. (Möglicherweise zeigt Ihr Cluster einen anderen Knotennamen an.)

	![Task Results][view_job362]

<h2 id="BKMK_stop">Anhalten der Azure-Knoten</h2>

Nachdem Sie das Cluster ausprobiert haben, können Sie mit HPC Cluster Manager die Azure-Knoten stoppen, um unnötige Belastungen Ihres Kontos zu vermeiden. Dabei wird der Cloud-Dienst angehalten und die Azure-Rolleninstanzen werden entfernt. 

1. Wählen Sie in HPC Cluster Manager bei unterlegtem **Node Management** beide Azure-Knoten aus. Klicken Sie dann im Bereich **Actions** auf **Stop**. 

	![Stop Nodes][stop_node1]

2. Klicken Sie im Dialogfeld **Stop Azure Nodes** auf **Stop**.

	![Stop Nodes][stop_node2]

3. Die Knoten wechseln in den Status **Stopping**. Nach einigen Minuten zeigt HPC Cluster Manager an, dass die Knoten **Not-Deployed** sind.

	![Not Deployed Nodes][stop_node4]

4. Sie können sich wie folgt vergewissern, dass die Rolleninstanzen nicht mehr in Azure ausgeführt werden: Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com) auf **Cloud-Dienste**, dann auf den Namen Ihres Cloud-Dienstes und abschließend auf **Instances**. In der Produktionsumgebung werden keine Instanzen mehr bereitgestellt.

	![No Instances][view_instances2]

	Damit schließt dieses Lernprogramm.

<h2 id="">Zugehörige Ressourcen</h2>

* [HPC Pack 2012 R2 und HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
* [Zugriff auf Azure mit Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Microsoft HPC Pack in Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?linkid=330375)
* [Azure Big Compute: HPC und Batch](http://azure.microsoft.com/solutions/big-compute/)


[Übersicht]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
[Overview]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
[install_hpc1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
[install_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
[install_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
[install_hpc4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
[install_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
[install_hpc7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
[install_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
[upload_cert1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
[createstorage1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
[createservice1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
[config_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
[config_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
[config_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
[config_hpc8]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
[config_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
[config_hpc12]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
[config_hpc13]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
[add_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
[add_node1_1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
[add_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
[add_node3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
[add_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
[add_node5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
[add_node6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
[add_node7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
[view_instances1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
[clusrun1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
[test_job1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
[param_sweep1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
[view_job361]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
[view_job362]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
[stop_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
[stop_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
[stop_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
[view_instances2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png

<!--HONumber=47-->
