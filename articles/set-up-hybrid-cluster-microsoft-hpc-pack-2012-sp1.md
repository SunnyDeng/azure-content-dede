<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="dongill" editor="mattshel" />

Einrichten eines Hybrid-Clusters mit Microsoft HPC Pack
=======================================================

Dieses Lernprogramm zeigt Ihnen, wie Sie mit Microsoft HPC Pack 2012 R2 und Azure ein kleines Hybrid-HPC-Cluster (High Performance Computing) einrichten. Das Cluster wird aus einem lokalen Hauptknoten (einem Computer mit dem Windows Server-Betriebssystem und HPC Pack) und einigen Serverknoten bestehen, die Sie bei Bedarf als Workerrollen-Instanzen in einem Azure-Clouddienst bereitstellen. Dann können Sie auf dem Hybrid-Cluster Rechenaufträge (Compute Jobs) ausführen.

![Hybrid-HPC-Cluster](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png)

Dieses Lernprogramm zeigt einen gelegentlich als Cloud-Bursting bezeichneten Ansatz: Wie mit skalierbaren, bei Bedarf verfügbaren Serverressourcen in Azure rechenintensive Anwendungen ausgeführt werden können.

In diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit Serverclustern oder HPC Pack haben. Es soll Ihnen lediglich helfen, ein Hybrid-Cluster für Demonstrationszwecke schnell bereitzustellen. Welche Überlegungen und Schritte zur Bereitstellung eines größeren Hybrid-Clusters mit HPC-Pack in einer Produktionsumgebung anzustellen bzw. auszuführen sind, ist in der [ausführlichen Anleitung](http://go.microsoft.com/fwlink/p/?LinkID=200493) beschrieben.

> [WACOM.NOTE] Azure bietet für unterschiedliche Arbeitsplasten Serverressourcen [unterschiedlicher Größen](http://go.microsoft.com/fwlink/p/?LinkId=389844) an. Die Instanzen A8 und A9 kombinieren beispielweise hohe Leistung und Zugang zu einem Anwendungsnetz mit niedrigen Latenzzeiten und hohem Durchsatz, was für bestimmte HPC-Anwendungen benötigt wird. Weitere Informationen finden Sie unter [Rechenintensive A8- und A9-Instanzen: Schnellstart mit HPC Pack](http://go.microsoft.com/fwlink/p/?Linkid=328042).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

-   [Voraussetzungen](#BKMK_Prereq)
-   [Installieren des HPC Pack auf dem Hauptknoten](#BKMK_DeployHN)
-   [Erstellen des Azure-Abonnements](#BKMK_Prpare)
-   [Konfigurieren des Hauptknotens](#BKMK_ConfigHN)
-   [Hinzufügen von Azure-Knoten zum Cluster](#BKMK_worker)
-   [Starten der Azure-Knoten](#BKMK_start)
-   [Clusterweites Ausführen eines Befehls](#BKMK_RunCommand)
-   [Ausführen eines Testauftrags](#BKMK_RunJob)
-   [Anhalten der Azure-Knoten](#BKMK_stop)

Voraussetzungen
---------------

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenloses Azure-Testkonto](http://www.windowsazure.com/de-de/pricing/free-trial/).

Darüber hinaus benötigen Sie für dieses Lernprogramm Folgendes:

-   Einen lokalen Computer, auf dem eine Edition von Windows Server 2012 R2 oder Windows Server 2012 ausgeführt wird. Dieser Computer fungiert als der Hauptknoten des HPC-Clusters. Wenn Sie Windows Server noch nicht verwenden, können Sie eine [Evaluierungsversion](http://technet.microsoft.com/evalcenter/dn205286.aspx) herunterladen und installieren.

    -   Der Computer muss in eine Active Directory-Domäne eingebunden werden.

    -   Vergewissern Sie sich, dass keine zusätzlicher Serverrollen oder Rollendienste installiert sind.

    -   Damit das HPC Pack unterstützt werden kann, muss das Betriebssystem in einer der folgenden Sprachen installiert werden: Englisch, Japanisch oder Chinesisch (vereinfacht).

    -   Stellen Sie sicher, dass wichtige und kritische Updates installiert wurden.

-   Installationsdateien für HPC Pack 2012 R2. Diese stehen kostenlos zur Verfügung. Das komplette Installationspaket müssen Sie [herunterladen](http://go.microsoft.com/fwlink/p/?linkid=389557) und die Dateien auf den Hauptknoten-Computer oder auf einen Speicherort im Netzwerk kopieren. Wählen Sie Installationsdateien in derselben Sprache aus wie für die Installation von Windows Server.

-   Ein Domänenkonto mit lokalen Administratorrechten auf dem Hauptknoten.

-   TCP-Konnektivität an Port 443 vom Hauptknoten zu Azure.

Installieren des HPC Pack auf dem Hauptknoten
---------------------------------------------

Zuerst muss das Microsoft HPC Pack auf einem lokalen Computer installiert werden, der als Hauptknoten im Cluster fungiert.

1.  Melden Sie sich bei dem Hauptknoten mit einem Domänenkonto an, das über lokale Administratorrechte verfügt.

2.  Starten Sie den Installations-Assistenten von HPC Pack, indem Sie die in den HPC Pack-Installationsdateien enthaltene Datei Setup.exe ausführen.

3.  Klicken Sie im **HPC Pack 2012 R2 Setup**-Bildschirm auf **New installation or add new features to an existing installation**.

    ![HPC Pack 2012 Setup](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png)

4.  Klicken Sie auf der **Seite mit dem Vertrag für Benutzer von Microsoft-Software** auf **Next**.

5.  Klicken Sie auf der Seite **Select Installation Type** auf **Create a new HPC cluster by creating a head node** und dann auf **Next**.

    ![Installationstyp auswählen](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png)

6.  Der Installations-Assistent führt vor der Installation verschiedene Tests durch. Klicken Sie auf **Next** auf der Seite **Installation Rules**, wenn alle Tests erfolgreich durchgeführt worden sind. Lesen Sie andernfalls die bereitgestellten Informationen, und nehmen Sie eventuell notwendige Änderungen in Ihrer Umgebung vor. Führen Sie dann die Tests nochmals aus, oder starten Sie gegebenenfalls den Installations-Assistenten erneut.

    ![Installationsregeln](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png)

7.  Vergewissern Sie sich, dass auf der Seite **HPC DB Configuration** der **Hauptknoten** für alle HPC-Datenbanken ausgewählt ist, und klicken Sie dann auf **Next**.

    ![DB-Konfiguration](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png)

8.  Akzeptieren Sie auf den verbleibenden Seiten des Assistenten die Standardeinstellungen. Klicken Sie auf der Seite **Install Required Components** auf **Install**.

    ![Installation](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png)

9.  Wenn die Installation abgeschlossen ist, deaktivieren Sie **Start HPC Cluster Manager** und klicken dann auf **Finish**. (Sie werden HPC Cluster Manager in einem späteren Schritt starten, um die Konfiguration des Hauptknotens abzuschließen.)

    ![Fertig stellen](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png)

Erstellen des Azure-Abonnements
-------------------------------

Führen Sie im [Verwaltungsportal](https://manage.windowsazure.com) nachfolgend beschriebene Schritte für Ihr Azure-Abonnement aus. Diese sind erforderlich, damit Sie später Azure-Knoten von dem lokalen Hauptknoten aus bereitstellen können.

-   Laden Sie ein Verwaltungszertifikat hoch (wird für sichere Verbindungen zwischen dem Hauptknoten und den Azure-Diensten benötigt)

-   Erstellen Sie einen Azure-Clouddienst, in dem Azure-Knoten (Workerrollen-Instanzen) laufen werden

-   Erstellen Sie ein Azure-Speicherkonto

    > [WACOM.NOTE]Notieren Sie auch Ihre Azure-Abonnement-ID, die Sie später noch brauchen werden. Sie finden diese Kennung in den Azure-[Kontoinformationen]([https://account.windowsazure.com/Subscriptions).

### Hochladen des Standard-Verwaltungszertifikats

HPC Pack installiert im Hauptknoten ein selbstsigniertes Zertifikat mit dem Namen Default Microsoft HPC Azure Management, das Sie als ein Azure-Verwaltungszertifikat hochladen können. Dieses Zertifikat wird für Testzwecke und Machbarkeitsstudien bereitgestellt.

1.  Melden Sie sich vom Hauptknoten-Computer aus im [Verwaltungsportal](https://manage.windowsazure.com) an.

2.  Klicken Sie auf **Settings** und dann auf **Verwaltungszertifikate**.

3.  Klicken Sie in der Befehlsleiste auf **Upload**.

    ![Zertifikateinstellungen](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png)

4.  Suchen Sie im Hauptknoten die Datei C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer. Klicken Sie dann auf das **Kontrollkästchen**.

    ![Hochladen des Zertifikats](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png)

In der Liste der Verwaltungszertifikate sehen Sie **Default HPC Azure Management**.

### Erstellen eines Azure-Clouddienstes

> [WACOM.NOTE]Erstellen Sie den Clouddienst und das Speicherkonto zur Optimierung der Leistung in derselben geografischen Region bzw. Affinitätsgruppe (sofern konfiguriert).

1.  Klicken Sie im Verwaltungsportal in der Befehlsleiste auf **New**.

2.  Klicken Sie auf **Server**, dann auf **Cloud-Dienst** und abschließend auf **Schnellerfassung**.

3.  Geben Sie eine URL für den Clouddienst ein, und klicken Sie dann auf **Cloud-Dienst erstellen**.

    ![Erstellen des Dienstes](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png)

### Erstellen eines Azure-Speicherkontos

1.  Klicken Sie im Verwaltungsportal in der Befehlsleiste auf **New**.

2.  Klicken Sie auf **Datendienste**, dann auf **Speicher** und abschließend auf **Schnellerfassung**.

3.  Geben Sie eine URL für das Konto ein, und klicken Sie dann auf **Speicherkonto erstellen**.

    ![Erstellen des Speicherkontos](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png)

Konfigurieren des Hauptknotens
------------------------------

Bevor Sie mit HPC Cluster Manager Azure-Knoten bereitstellen und Aufträge übermitteln können, müssen noch einige Schritte zur Konfiguration des Clusters ausgeführt werden.

1.  Starten Sie HPC Cluster Manager auf dem Hauptknoten. Wenn das Dialogfeld **Select Head Node** angezeigt wird, klicken Sie auf **Local Computer**. Die **Deployment To-do List** wird angezeigt.

2.  Klicken Sie unter **Required deployment tasks** auf **Configure your network**.

    ![Netzwerkkonfiguration](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png)

3.  Wählen Sie im Network Configuration Wizard **All nodes only on an enterprise network** (Topology 5) aus.

    ![Topologie 5](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png)

    > [WACOM.NOTE]Dies ist die einfachste Form der Konfiguration für Demonstrationszwecke, da der Hauptknoten für die Verbindung zum Active Directory und zum Internet nur einen einzelnen Netzwerkadapter braucht. Clusterkonfigurationen, die mehrere Netzwerke benötigen, werden in diesem Lernprogramm nicht behandelt.

4.  Klicken Sie auf **Next**, und akzeptieren Sie auf den verbleibenden Seiten des Assistenten die Standardwerte. Klicken Sie in der Registerkarte **Review** auf **Configure**, um die Netzwerkkonfiguration abzuschließen.

5.  Klicken Sie in der **Deployment To-do List** auf **Provide installation credentials**.

6.  Geben Sie in das Dialogfeld **Installation Credentials** die Anmeldedaten des Domänenkontos ein, das Sie für die Installation von HPC Pack verwendet haben. Klicken Sie dann auf **OK**.

    ![Installations-Anmeldeinformationen](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png)

    > [WACOM.NOTE]HPC Pack-Dienste verwenden Installations-Anmeldeinformationen nur für die Bereitstellung lokaler Serverknoten.

7.  Klicken Sie in der **Deployment To-do List** auf **Configure the naming of new nodes**.

8.  Akzeptieren Sie im Dialogfeld **Specify Node Naming Series** die Standard-Knotenbenennungsreihe, und klicken Sie auf **OK**.

    ![Knotenbenennung](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png)

    > [WACOM.NOTE]Die Benennungsreihe erzeugt nur Namen für Serverknoten, die in die Domäne eingebunden sind. Azure-Knoten werden automatisch benannt.

9.  Klicken Sie in der **Deployment To-do List** auf **Create a node template**. Sie brauchen die Knotenvorlage, um Azure-Knoten zum Cluster hinzuzufügen.

10. Führen Sie im Create Node Template Wizard, folgende Schritte aus:

    a. Klicken Sie auf der Seite **Choose Node Template Type** auf **Azure node template** und dann auf **Next**.

    ![Knotenvorlage](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png)

    b. Klicken Sie auf **Next**, und akzeptieren Sie damit den Standard-Vorlagennamen.

    c. Geben Sie auf der Seite **Provide Subscription Information** Ihre Azure-Abonnement-ID ein (zu finden in den Azure [Kontoinformationen]([https://account.windowsazure.com/Subscriptions)). Klicken Sie dann neben **Verwaltungszertifikat** auf **Browse**, und wählen Sie **Default HPC Azure Management.** Klicken Sie dann auf **Next**.

    ![Knotenvorlage](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png)

    d. Wählen Sie auf der Seite **Provide Service Information** den Clouddienst und das Speicherkontos aus, das Sie in einem früheren Schritt erstellt haben. Klicken Sie dann auf **Next**.

    ![Knotenvorlage](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png)

    e. Klicken Sie auf **Next**, und akzeptieren Sie auf den verbleibenden Seiten des Assistenten die Standardwerte. Klicken Sie dann in der Registerkarte **Review** auf **Create**, um die Knotenvorlage anzulegen.

    > [WACOM.NOTE]Standardmäßig enthält die Azure-Knotenvorlage unter anderem Einstellungen, mit denen Sie Knoten manuell starten (bereitstellen) und stoppen können. Sie können auch einen Zeitplan für das automatische Starten und Stoppen der Azure-Knoten konfigurieren.

Hinzufügen von Azure-Knoten zum Cluster
---------------------------------------

Sie werden nun mit der Knotenvorlage Azure-Knoten zu dem Cluster hinzufügen. Wenn Knoten zu dem Cluster hinzugefügt werden, werden deren Konfigurationsinformationen gespeichert, so dass Sie sie jederzeit als Rolleninstanzen im Clouddienst starten (bereitstellen) können. Azure-Knoten werden Ihrem Abonnement erst belastet, wenn die Rolleninstanzen im Clouddienst ausgeführt werden.

In diesem Lernprogramm werden Sie zwei kleine Knoten hinzufügen.

1.  Klicken Sie im HPC Cluster Manager bei unterlegtem **Node Management** im Bereich **Actions** auf **Add Node**.

    ![Hinzufügen von Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png)

2.  Klicken Sie im Add Node Wizard auf der Seite **Select Deployment Method** auf **Add Azure nodes** und dann auf **Next**.

    ![Hinzufügen eines Azure-Knotens](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png)

3.  Wählen Sie auf der Seite **Specify New Nodes** die Azure-Knotenvorlage aus, die Sie zuvor angelegt haben (Standardname **Default AzureNode Template**). Spezifizieren Sie dann **2** Knoten der Größe **Small**, und klicken Sie anschließend auf **Next**.

    ![Spezifikation von Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png)

    Einzelheiten zu den Größen virtueller Computer denen Sie in [Größen virtueller Computer und Cloud-Dienste für Windows Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).

4.  Klicken Sie auf der Seite **Completing the Add Node Wizard** auf **Fertig stellen**.

    Daraufhin werden zwei Azure-Knoten mit der Bezeichnung **AzureCN-0001** und **AzureCN-0002** in HPC Cluster Manager angezeigt. In beiden Fällen lautet der Status **Not-Deployed**.

    ![Hinzugefügte Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png)

Starten der Azure-Knoten
------------------------

Wenn Sie die Clusterressourcen in Azure nutzen wollen, veranlassen Sie den Start (die Bereitstellung) der Azure-Knoten mit HPC Cluster Manager und schalten sie online.

1.  Klicken Sie in HPC Cluster Manager bei unterlegtem **Node Management**, auf einen oder beide Knoten und dann im Bereich **Actions** auf **Start**.

    ![Starten von Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png)

2.  Klicken Sie im Dialogfeld **Start Azure Nodes** auf **Start**.

    ![Starten von Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png)

    Die Knoten wechseln in den Status **Provisioning**. Im Bereitstellungsprotokoll können Sie den Fortschritt der Bereitstellung verfolgen.

    ![Bereitstellung von Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png)

3.  Nach einigen Minuten ist die Bereitstellung der Azure-Knoten beendet, und die Knoten befinden sich im Status **Offline**. In diesem Status werden die Rolleninstanzen zwar akzeptieren, aber Clusteraufträge noch nicht angenommen.

4.  Sie können wie folgt prüfen, ob die Rolleninstanzen laufen: Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com) auf **Cloud-Dienste**, dann auf den Namen Ihres Clouddienstes und abschließend auf **Instances**.

    ![Ausführen von Instanzen](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png)

    Sie sehen, dass in dem Dienst zwei Workerrollen-Instanzen ausgeführt werden. HPC Pack stellt außerdem automatisch zwei **HpcProxy**-Instanzen (mittlere Größe) bereit, die für die Kommunikation zwischen dem Hauptknoten und Azure zuständig sind.

5.  Um nun die Azure-Knoten online zu schalten und Clusteraufträge ausführen zu können, wählen Sie die Knoten aus, klicken mit der rechten Maustaste und klicken dann auf **Bring online**.

    ![Offline geschaltete Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png)

    HPC Cluster Manager zeigt an, dass die Knoten **Online** sind.

Clusterweites Ausführen eines Befehls
-------------------------------------

Mit dem HPC Pack-Befehl **clusrun** können Sie einen Befehl oder eine Anwendung auf einem oder mehreren Clusterknoten ausführen. Rufen Sie hier in einem einfachen Beispiel mit **clusrun** die IP-Konfiguration der Azure-Knoten ab.

1.  Öffnen Sie auf dem Hauptnoten eine Eingabeaufforderung.

2.  Geben Sie folgenden Befehl ein:

    `clusrun /nodes:azurecn* ipconfig`

3.  Die Ausgabe wird ähnlich wie unten gezeigt aussehen.

    ![Clusrun](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png)

Ausführen eines Testauftrags
----------------------------

Sie können einen Testauftrag übermitteln, der auf dem Hybrid-Cluster ausgeführt wird. Dieses Beispiel ist ein einfacher "Parameter-Sweep"-Auftrag (eine Art in sich paralleler Rechenvorgang), der durch die Ausführung von Unteraufgaben mit dem Befehl **set /a** eine Ganzzahl zu sich selbst hinzufügt. Alle Knoten des Clusters wirken an der Ausführung der Unteraufgaben für Ganzzahlen von 1 bis 100 mit.

1.  Klicken Sie in HPC Cluster Manager bei unterlegtem **Job Management** im Bereich **Actions** auf **New Parametric Sweep Job**.

    ![Neuer Auftrag](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png)

2.  Geben Sie im Dialogfeld **New Parametric Sweep Job** neben **Command Line** `set /a *+*` ein (indem Sie den in der Befehlsleiste angezeigten Standardbefehl überschreiben). Belassen Sie bei den übrigen Einstellungen die Standardwerte, und klicken Sie dann auf **Submit**, um den Auftrag zu übermitteln.

    ![Parameter-Sweep](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png)

3.  Wenn der Auftrag ausgeführt worden ist, doppelklicken Sie auf dem Auftrag **My Sweep Task**.

4.  Klicken Sie auf **View Tasks** und dann auf eine Unteraufgabe, so dass die berechnete Ausgabe dieser Unteraufgabe angezeigt wird.

    ![Ergebnisse einer Aufgabe](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png)

5.  Wenn Sie wissen möchten, welcher Knoten die Berechnung für diese Unteraufgabe ausgeführt hat, klicken Sie auf **Allocated Nodes**. (Möglicherweise zeigt Ihr Cluster einen anderen Knotennamen an.)

    ![Ergebnisse einer Aufgabe](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png)

Anhalten der Azure-Knoten
-------------------------

Nachdem Sie das Cluster ausprobiert haben, können Sie mit HPC Cluster Manager die Azure-Knoten stoppen, um unnötige Belastungen Ihres Kontos zu vermeiden. Dabei wird der Clouddienst angehalten und die Azure-Rolleninstanzen werden entfernt.

1.  Wählen Sie in HPC Cluster Manager bei unterlegtem **Node Management** beide Azure-Knoten aus. Klicken Sie dann im Bereich **Actions** auf **Stop**.

    ![Knoten anhalten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png)

2.  Klicken Sie im Dialogfeld **Stop Azure Nodes** auf **Stop**.

    ![Knoten anhalten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png)

3.  Die Knoten wechseln in den Status **Stopping**. Nach einigen Minuten zeigt HPC Cluster Manager an, dass die Knoten **Not-Deployed** sind.

    ![Nicht bereitgestellte Knoten](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png)

4.  Sie können sich wie folgt vergewissern, dass die Rolleninstanzen nicht mehr in Azure ausgeführt werden: Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com) auf **Cloud-Dienste**, dann auf den Namen Ihres Clouddienstes und abschließend auf **Instances**. In der Produktionsumgebung werden keine Instanzen mehr bereitgestellt.

    ![Keine Instanzen](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png)

    Damit schließt dieses Lernprogramm.

Zugehörige Ressourcen
---------------------

-   [HPC Pack 2012 R2 und HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
-   [Zugriff auf Azure mit Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
-   [Bereitstellen von Anwendungen auf Azure-Knoten](http://go.microsoft.com/fwlink/p/?LinkId=325317)
-   [Microsoft HPC Pack in einer Azure Virtual Machine](http://go.microsoft.com/fwlink/p/?linkid=330375)

