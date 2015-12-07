<properties
 pageTitle="Ausführen von OpenFOAM mit HPC Pack auf virtuellen Linux-Computern | Microsoft Azure"
 description="Sie erfahren, wie Sie einen Microsoft HPC Pack-Cluster unter Azure bereitstellen und einen OpenFOAM-Auftrag auf mehreren Linux-Computeknoten in einem RDMA-Netzwerk ausführen."
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
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="11/22/2015"
 ms.author="danlep"/>

# Ausführen von OpenFOAM mit Microsoft HPC Pack auf einem Linux-RDMA-Cluster in Azure

In diesem Artikel wird veranschaulicht, wie Sie einen Microsoft HPC Pack-Cluster unter Azure bereitstellen und einen [OpenFOAM](http://openfoam.com/)-Auftrag mit Intel MPI auf mehreren Linux-Computeknoten ausführen, die über das Azure Remote Direct Memory Access (RDMA)-Netzwerk verbunden sind.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.

OpenFOAM (Open Field Operation and Manipulation) ist ein frei verfügbares Open Source-Computational Fluid Dynamics (CFD)-Softwarepaket, das sowohl in kommerziellen als auch in akademischen Organisationen häufig in den Bereichen Maschinenbau und Wissenschaft eingesetzt wird. Es enthält Tools für das „Meshing“, insbesondere snappyHexMesh, eine parallelisierte Meshing-Anwendung für komplexe CAD-Geometrien, und für die Vor- und Nachbearbeitung. Fast alle Prozesse werden gleichzeitig ausgeführt, damit Benutzer in den Genuss aller Vorteile der verwendeten Computerhardware kommen.

Microsoft HPC Pack bietet eine Vielzahl von umfangreichen HPC- und parallelen Anwendungen, einschließlich MPI-Anwendungen, auf Clustern mit virtuellen Microsoft Azure-Computern. Ab Microsoft HPC Pack 2012 R2 Update 2 unterstützt HPC Pack auch die Ausführung von Linux-HPC-Anwendungen auf virtuellen Linux-Computeknoten, die in einem HPC Pack-Cluster bereitgestellt werden. Eine Einführung in die Verwendung von Linux-Computeknoten mit HPC Pack finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md).

>[AZURE.NOTE]In diesem Artikel wird vorausgesetzt, dass Sie sich bereits etwas mit der Verwaltung von Linux-Systemen und der Ausführung von MPI-Workloads in Linux-HPC-Clustern auskennen.

## Voraussetzungen

*   **HPC Pack-Cluster mit Linux-Computeknoten** – unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md) finden Sie die Voraussetzungen sowie die Schritte zum Bereitstellen eines HPC Pack-Clusters mit Linux Computeknoten in Azure mithilfe von Azure PowerShell-Skript und HPC Pack-Images im Azure Marketplace. Weitere Aspekte der Verwendung von rechenintensiven A8-Instanzen zum Zugreifen auf das Azure RDMA-Netzwerk finden Sie unter [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](virtual-machines-a8-a9-a10-a11-specs.md).

    Die folgende XML-Beispielkonfigurationsdatei können Sie mit dem Skript verwenden, um einen Azure-basierten HPC Pack-Cluster bereitzustellen, der aus einem Hauptknoten der Größe A8 mit Windows Server 2012 R2 und zwei Computeknoten der Größe A8 mit SUSE Linux Enterprise Server 12 besteht. Ersetzen Sie die Werte durch die entsprechenden Werte für Ihr Abonnement und Ihre Dienstnamen.

    >[AZURE.NOTE]Derzeit werden Linux-RDMA-Netzwerke in Azure nur für virtuelle Computer unterstützt, die mit dem RDMA-fähigen SUSE Linux Enterprise Server 12-Image im Azure Marketplace erstellt wurden (b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-sles-12-hpc-v20150708).

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>allvhdsje</StorageAccount>
      </Subscription>
      <Location>Japan East</Location>  
      <VNet>
        <VNetName>suse12rdmavnet</VNetName>
        <SubnetName>SUSE12RDMACluster</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>SUSE12RDMA-HN</VMName>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <NodeCount>2</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```

    **Weitere wichtige Informationen**

    *   Stellen Sie alle Linux-Computeknoten unter einem Clouddienst bereit, um die RDMA-Netzwerkverbindung zwischen den Knoten zu verwenden.

    *   Falls Sie nach der Bereitstellung der Linux-Knoten eine Verbindung per SSH herstellen müssen, um zusätzliche Verwaltungsaufgaben durchzuführen, finden Sie die SSH-Verbindungsdetails für die einzelnen virtuellen Linux-Computer im Azure-Portal.
        
*   **Intel MPI**: Zum Ausführen von OpenFOAM auf Linux-Computeknoten in Azure benötigen Sie die Intel MPI Library 5-Laufzeit von der [Intel.com-Website](https://software.intel.com/de-DE/intel-mpi-library/). In einem späteren Schritt installieren Sie Intel MPI auf Ihren Linux-Computeknoten. Folgen Sie als Vorbereitung dazu nach der Registrierung bei Intel dem Link in der Bestätigungs-E-Mail auf die entsprechende Webseite, und kopieren Sie den Downloadlink der TGZ-Datei für die entsprechende Version von Intel MPI. Dieser Artikel basiert auf Intel MPI-Version 5.0.3.048.

*   **OpenFOAM Source Pack**: Laden Sie die OpenFOAM Source Pack-Software für Linux auf der [Website der OpenFOAM Foundation](http://www.openfoam.org/download/source.php) herunter. Dieser Artikel basiert auf Source Pack-Version 2.3.1, die als „OpenFOAM-2.3.1.tgz“ zum Download verfügbar ist. Befolgen Sie die Anleitung weiter unten in diesem Artikel, um OpenFOAM auf den Linux-Computeknoten zu entpacken und zu kompilieren.

*   **EnSight** (optional): Um die Ergebnisse Ihrer OpenFOAM-Simulation anzuzeigen, müssen Sie die Windows-Version der [EnSight](https://www.ceisoftware.com/download/)-Visualisierung und das Analyseprogramm auf den Hauptknoten des HPC Pack-Clusters herunterladen und installieren. Die Informationen zu Lizenzierung und Download finden Sie auf der EnSight-Website.


## Einrichten der gegenseitigen Vertrauensstellung zwischen Computeknoten

Für das Ausführen eines knotenübergreifenden Auftrags auf mehreren Linux-Knoten müssen die Knoten sich gegenseitig vertrauen (per **rsh** oder **ssh**). Wenn Sie den HPC Pack-Cluster mit dem IaaS-Bereitstellungsskript für Microsoft HPC Pack erstellen, richtet das Skript automatisch dauerhafte gegenseitige Vertrauensstellungen für das angegebene Administratorkonto ein. Für Benutzer ohne Administratorrechte, die Sie in der Domäne des Clusters erstellen, müssen Sie temporäre gegenseitige Vertrauensstellungen zwischen den Knoten einrichten, wenn diesen ein Auftrag zugewiesen wird, und diese Beziehung nach Abschluss des Auftrags wieder entfernen. Wenn Sie dies für alle Benutzer durchführen möchten, geben Sie ein RSA-Schlüsselpaar für den Cluster an, den HPC Pack zum Einrichten der Vertrauensstellung verwendet.

### Generieren eines RSA-Schlüsselpaars

Das Generieren eines RSA-Schlüsselpaars, das einen öffentlichen Schlüssel und einen privaten Schlüssel enthält, ist mit dem Linux-Befehl **ssh-keygen** sehr einfach.

1.	Melden Sie sich auf einem Linux-Computer an.

2.	Führen Sie den folgenden Befehl aus:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]Drücken Sie die EINGABETASTE, um die Standardeinstellungen zu verwenden, bis der Befehl abgeschlossen ist. Geben Sie hier keine Passphrase ein. Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, drücken Sie einfach die EINGABETASTE.

    ![Generieren eines RSA-Schlüsselpaars][keygen]

3.	Wechseln Sie in das Verzeichnis "~/.ssh". Der private Schlüssel wird in "id\_rsa" gespeichert und der öffentliche Schlüssel in "id\_rsa.pub".

    ![Private und öffentliche Schlüssel][keys]

### Hinzufügen des Schlüsselpaars zum HPC Pack-Cluster
1.	Stellen Sie mit dem HPC Pack-Administratorkonto (das Administratorkonto, das Sie mit dem Bereitstellungsskript eingerichtet haben) eine Remotedesktopverbindung mit dem Hauptknoten her.

2. Verwenden Sie Windows Server-Standardverfahren zum Erstellen eines Domänenbenutzerkontos in der Active Directory-Domäne des Clusters. Verwenden Sie z. B. das Tool Active Directory-Benutzer und -Computer auf dem Hauptknoten. In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie einen Domänenbenutzer mit dem Namen "hpclab\\hpcuser" erstellen.

3.	Erstellen Sie die Datei "C:\\cred.xml", und kopieren Sie die RSA-Schlüsseldaten in diese Datei. Ein Beispiel für diese Datei finden Sie im Anhang am Ende dieses Artikels.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.	Öffnen Sie eine Eingabeaufforderung, und geben Sie den folgenden Befehl ein, um die Anmeldeinformationen für das Konto „hpclab\\hpcuser“ festzulegen. Verwenden Sie den **extendeddata**-Parameter, um den Namen der für die Schlüsseldaten erstellten Datei "C:\\cred.xml" zu übergeben.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Dieser Befehl wird ohne Ausgabe abgeschlossen. Speichern Sie die Datei "cred.xml" nach dem Festlegen der Anmeldeinformationen für die zum Ausführen der Aufträge erforderlichen Benutzerkonten an einem sicheren Ort, oder löschen Sie sie.

5.	Wenn Sie das RSA-Schlüsselpaar auf einem der Linux-Knoten generiert haben, sollten Sie die Schlüssel nach der Verwendung löschen. HPC Pack richtet kein gegenseitiges Vertrauen ein, wenn eine vorhandene Datei "id\_rsa" oder "id\_rsa.pub" gefunden wird.

>[AZURE.IMPORTANT]Es wird nicht empfohlen, Linux-Aufträge als Clusteradministrator auf einem freigegebenen Cluster auszuführen, da ein von einem Administrator übermittelter Auftrag auf den Linux-Knoten unter dem root-Konto ausgeführt wird. Ein Auftrag, der von einem Benutzer ohne Administratorrechte übermittelt wurde, wird unter einem lokalen Linux-Benutzerkonto ausgeführt, das den gleichen Namen wie der Auftragsbenutzer hat. HPC Pack richtet dann das gegenseitige Vertrauen für diesen Linux-Benutzer auf allen Knoten ein, die dem Auftrag zugewiesen sind. Der Linux-Benutzer kann vor der Ausführung des Auftrags manuell auf den Linux-Knoten eingerichtet werden, oder HPC Pack erstellt den Benutzer automatisch, wenn der Auftrag übermittelt wird. Wenn der Benutzer von HPC Pack erstellt wurde, löscht HPC Pack ihn nach Abschluss des Auftrags wieder. Die Schlüssel werden nach Auftragsabschluss auf den Knoten gelöscht, um Sicherheitsrisiken zu verringern.

## Einrichten einer Dateifreigabe für Linux-Knoten

Richten Sie nun eine SMB-Standardfreigabe für einen Ordner auf dem Hauptknoten ein, und stellen Sie den freigegebenen Ordner für alle Linux-Knoten bereit, damit die Linux-Knoten auf Anwendungsdateien mit einem gemeinsamen Pfad zugreifen können. Wenn Sie möchten, können Sie eine andere Dateifreigabeoption verwenden, z. B. eine Azure Files-Freigabe (für viele Szenarien empfohlen) oder eine NFS-Freigabe. Dateifreigabeinformationen und die zugehörigen ausführlichen Schritte finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md).

1.	Erstellen Sie auf dem Hauptknoten einen Ordner, und geben Sie ihn für alle Benutzer mit Lese-/Schreibberechtigungen frei. Beispielsweise können Sie „C:\\OpenFOAM“ auf dem Hauptknoten als „\\\SUSE12RDMA-HN\\OpenFOAM“ freigeben. Hierbei ist *SUSE12RDMA-HN* der Hostname des Hauptknotens.

2.	Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie die folgenden Befehle aus, um den freigegebenen Ordner bereitzustellen.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>’`,dir_mode=0777`,file_mode=0777
    ```

Der erste Befehl erstellt einen Ordner namens „/openfoam“ auf allen Knoten in der Gruppe „LinuxNodes“. Der zweite Befehl stellt den freigegebenen Ordner „//SUSE12RDMA-HN/OpenFOAM“ auf den Linux-Knoten bereit, wobei die Bits „dir\_mode“ und „file\_mode“ auf 777 festgelegt sind. Die Parameter *username* und *password* im Befehl sollten mit den Anmeldeinformationen eines Benutzers auf dem Hauptknoten übereinstimmen.

>[AZURE.NOTE]Das Symbol "`" im zweiten Befehl ist ein Escapesymbol für PowerShell. "`," bedeutet, dass "," (das Komma) Teil des Befehls ist.

## Installieren von MPI und OpenFOAM

Zum Ausführen von OpenFOAM als MPI-Auftrag im RDMA-Netzwerk müssen Sie OpenFOAM mit den Intel MPI-Bibliotheken kompilieren.

Sie führen zuerst mehrere **clusrun**-Befehle aus, um Intel MPI-Bibliotheken und OpenFOAM auf allen Linux-Knoten zu installieren. Verwenden Sie die bereits konfigurierte Hauptknotenfreigabe, um die Installationsdateien auf die Linux-Knoten zu verteilen.

>[AZURE.IMPORTANT]Diese Installations- und Kompilierschritte sind Beispiele, und es sind gewisse Kenntnisse im Bereich der Verwaltung von Linux-Systemen erforderlich. Vor allem muss sichergestellt werden, dass die abhängigen Compiler und Bibliotheken richtig installiert werden. Unter Umständen müssen Sie bestimmte Umgebungsvariablen oder andere Einstellungen ändern, die für Ihre Versionen von Intel MPI und OpenFOAM benötigt werden. Weitere Informationen finden Sie unter [Intel MPI Library für Linux – Installationsleitfaden](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html) und [OpenFOAM Source Pack-Installation](http://www.openfoam.org/download/source.php).


### Installieren von Intel MPI

Speichern Sie das heruntergeladene Installationspaket für Intel MPI (in diesem Beispiel „l\_mpi\_p\_5.0.3.048.tgz“) unter „C:\\OpenFoam“ auf dem Hauptknoten, damit die Linux-Knoten auf diese Datei über „/openfoam“ zugreifen können. Führen Sie anschließend **clusrun** aus, um die Intel MPI Library auf allen Linux-Knoten zu installieren.

1.  Mit den folgenden Befehlen wird das Installationspaket kopiert und in „/opt/intel“ auf jedem Knoten extrahiert.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Verwenden Sie für die unbeaufsichtigte Installation von Intel MPI Library die Datei „silent.cfg“. Ein Beispiel für diese Datei finden Sie im Anhang am Ende dieses Artikels. Legen Sie diese Datei im freigegebenen Ordner „/openfoam“ ab. Weitere Informationen zur Datei „silent.cfg“ finden Sie unter [Intel MPI Library für Linux – Installationsleitfaden (Unbeaufsichtigte Installation)](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html#silentinstall).

    >[AZURE.TIP]Stellen Sie sicher, dass Sie die Datei „silent.cfg“ als Textdatei mit Linux-Zeilenenden (nur LF, nicht CR-LF) speichern. Dadurch wird sichergestellt, dass es auf den Linux-Knoten ordnungsgemäß ausgeführt wird.

3.  Installieren Sie die Intel MPI Library im unbeaufsichtigten Modus.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### Konfigurieren von MPI

Zu Testzwecken sollten Sie „/etc/security/limits.conf“ auf den Linux-Knoten die folgenden Zeilen hinzufügen:

```
*               hard    memlock         unlimited
*               soft    memlock         unlimited
```

Hierzu können Sie die Datei „limits.conf“ unter „C:\\OpenFoam“ erstellen (Textdatei mit Linux-Zeilenenden speichern) und den folgenden Befehl ausführen, um sie auf die Linux-Knoten zu kopieren:

```
clusrun /nodegroup:LinuxNodes cp /openfoam/limits.conf /etc/security
```

Starten Sie die Linux-Knoten neu, nachdem Sie „limits.confile“ aktualisiert haben. Stellen Sie nach dem Neustart sicher, dass der freigegebene Ordner als „/openfoam“ bereitgestellt wurde.

### Kompilieren und Installieren von OpenFOAM

Speichern Sie das heruntergeladene Installationspaket für das OpenFOAM Source Pack (in diesem Beispiel „OpenFOAM-2.3.1.tgz“) unter „C:\\OpenFoam“ auf dem Hauptknoten, damit die Linux-Knoten auf diese Datei über „/openfoam“ zugreifen können. Führen Sie anschließend **clusrun** aus, um OpenFOAM auf allen Linux-Knoten zu kompilieren.


1.  Erstellen Sie den Ordner „/opt/OpenFOAM“ auf jedem Linux-Knoten, kopieren Sie das Quellpaket in diesen Ordner, und extrahieren Sie ihn dort.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Legen Sie zum Kompilieren von OpenFOAM mit der Intel MPI Library zuerst einige Umgebungsvariablen für Intel MPI und OpenFOAM fest. Verwenden Sie hierfür ein Bash-Skript mit dem Namen „settings.sh“. Ein Beispiel für diese Datei finden Sie im Anhang am Ende dieses Artikels. Fügen Sie diese Datei (mit Linux-Zeilenenden gespeichert) in den Ordner „/openfoam“ ein. Diese Datei enthält auch die Einstellungen für die MPI- und OpenFOAM-Laufzeiten, die Sie später zum Ausführen eines OpenFOAM-Auftrags verwenden.

3. Installieren Sie die abhängigen Pakete, die zum Kompilieren von OpenFOAM benötigt werden. Je nach Linux-Distribution müssen Sie hierfür zuerst mehrere Repositorys hinzufügen. Die Repositorys und Pakete sind im Anhang am Ende dieses Artikels aufgeführt. Es wird empfohlen, per SSH-Verbindung auf jeden Linux-Knoten zuzugreifen, um die Befehle auszuführen und zu prüfen, ob die Ausführung funktioniert.

4.  Führen Sie den folgenden Befehl aus, um OpenFOAM zu kompilieren. Es dauert eine gewisse Zeit, bis der Kompilierungsprozess abgeschlossen ist. Hierbei wird ein große Menge von Protokollinformationen für die Standardausgabe generiert, sodass Sie die Option **/interleaved** verwenden sollten, um die Ausgabe überlappend anzuzeigen.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```

>[AZURE.NOTE]Das „`“-Symbol im Befehl ist ein Escapesymbol für PowerShell. “`&” bedeutet, dass das „&“ ein Teil des Befehls ist.

## Vorbereiten der Ausführung eines OpenFOAM-Auftrags

Als Nächstes bereiten Sie sich auf das Ausführen eines MPI-Auftrags mit dem Namen „sloshingTank3D“ auf zwei Linux-Knoten vor. Dies ist eines der OpenFoam-Beispiele. In diesem Beispiel ist „/opt/openfoam231“ der Installationspfad von OpenFOAM auf den Linux-Knoten.

### Einrichten der Laufzeitumgebung

Führen Sie den folgenden Befehl in einem Windows PowerShell-Fenster auf dem Hauptknoten aus, um die Laufzeitumgebungen für MPI und OpenFOAM auf allen Linux-Knoten einzurichten.

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### Vorbereiten der Beispieldaten

Verwenden Sie die bereits konfigurierte Hauptknotenfreigabe, um Dateien für die Linux-Knoten freizugeben (Bereitstellung als „/openfoam“).

1.  Stellen Sie eine SSH-Verbindung mit einem der Linux-Computeknoten her.

2.  Führen Sie den folgenden Befehl aus, um die OpenFOAM-Laufzeitumgebung einzurichten, falls Sie dies noch nicht getan haben.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Kopieren Sie das sloshingTank3D-Beispiel in den freigegebenen Ordner, und navigieren Sie dorthin.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Wenn Sie die Standardparameter dieses Beispiels verwenden, kann die Ausführung mindestens zehn Minuten dauern. Es kann also ratsam sein, einige Parameter zu ändern, um die Ausführung zu beschleunigen. Eine einfache Option ist das Ändern der Zeitschrittvariablen „deltaT“ und „writeInterval“ in der Datei „system/controlDict“. Darin werden alle Eingabedaten gespeichert, die sich auf die Zeitkontrolle und das Lesen und Schreiben von Lösungsdaten beziehen. Beispielsweise können Sie den Wert für „deltaT“ von 0,05 in 0,5 und den Wert für „writeInterval“ von 0,05 in 0,5 ändern.

    ![Schrittvariablen ändern][step_variables]

5.  Geben Sie die gewünschten Werte für die Variablen in der Datei „system/decomposeParDict“ an. In diesem Beispiel werden zwei Linux-Knoten mit jeweils acht Kernen verwendet. Legen Sie also „numberOfSubdomains“ auf 16 und „n“ von „hierarchicalCoeffs“ auf (1 1 16) fest, damit OpenFOAM parallel mit 16 Prozessen ausgeführt wird. Weitere Informationen zur parallelen Ausführung von OpenFOAM finden Sie unter [OpenFOAM-Benutzerhandbuch: 3.4 Paralleles Ausführen von Anwendungen](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Prozesse zerlegen][decompose]

6.  Führen Sie die folgenden Befehle aus dem Verzeichnis „sloshingTank3D“ aus, um die Beispieldaten vorzubereiten.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  Auf dem Hauptknoten sollten Sie verfolgen können, dass die Beispieldatendateien in „C:\\OpenFoam\\sloshingTank3D“ kopiert werden. („C:\\OpenFoam“ ist der freigegebene Ordner auf dem Hauptknoten.)

    ![Datendateien auf dem Hauptknoten][data_files]

### Hostdatei für mpirun

In diesem Schritt erstellen Sie eine Hostdatei (eine Liste mit Computeknoten), die vom Befehl **mpirun** verwendet wird.

1.	Erstellen Sie auf einem der Linux-Knoten eine neue Datei mit dem Namen „hostfile“ unter „/openfoam“, damit die Datei auf allen Linux-Knoten unter „/openfoam/hostfile“ erreichbar ist.

2.	Schreiben Sie die Namen Ihrer Linux-Knoten in diese Datei. In diesem Beispiel sieht die Datei wie folgt aus:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Sie können diese Datei auch unter „C:\\OpenFoam\\hostfile“ auf dem Hauptknoten erstellen. Speichern Sie sie in diesem Fall als Textdatei mit Linux-Zeilenenden (nur LF, nicht CR-LF). Dadurch wird sichergestellt, dass es auf den Linux-Knoten ordnungsgemäß ausgeführt wird.

    **Bash-Skript-Wrapper**

    Wenn Sie über viele Linux-Knoten verfügen und Ihr Auftrag nur auf einigen ausgeführt werden soll, ist die Verwendung einer festen Hostdatei nicht zu empfehlen. Sie wissen nämlich nicht, welche Knoten dem Auftrag zugeordnet werden. Schreiben Sie in diesem Fall einen Bash-Skript-Wrapper für **mpirun**, damit die Hostdatei automatisch erstellt wird. Ein Beispiel für einen Bash-Skript-Wrapper mit dem Namen „hpcimpirun.sh“ finden Sie im Anhang am Ende dieses Artikels. Speichern Sie ihn als „/openfoam/hpcimpirun.sh“. Dieses Beispielskript bewirkt Folgendes:

    1.	Richtet die Umgebungsvariablen für **mpirun** und einige weitere Befehlsparameter ein, um den MPI-Auftrag über das RDMA-Netzwerk auszuführen. In diesem Fall wird damit Folgendes festgelegt:

        *	I\_MPI\_FABRICS=shm:dapl
        *	I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0
        *	I\_MPI\_DYNAMIC\_CONNECTION=0

    2.	Erstellt eine Hostdatei gemäß der Umgebungsvariable $CCP\_NODES\_CORES, die vom HPC-Hauptknoten festgelegt wird, wenn der Auftrag aktiviert wird.

        Für das Format von $CCP\_NODES\_CORES wird das folgende Muster verwendet:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        Hierbei gilt:

        * `<Number of nodes>`: Anzahl der Knoten, die dem Auftrag zugeordnet sind.  
        
        * `<Name of node_n_...>`: Name der einzelnen Knoten, die dem Auftrag zugeordnet sind.
        
        * `<Cores of node_n_...>`: Anzahl der Kerne auf dem Knoten, die dem Auftrag zugeordnet sind.

        Wenn der Auftrag für die Ausführung beispielsweise zwei Kerne benötigt, lautet „$CCP\_NODES\_CORES“ etwa wie folgt:
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.	Ruft den Befehl **mpirun** auf und fügt zwei Parameter an die Befehlszeile an.

        * `--hostfile <hostfilepath>: <hostfilepath>`: Der Pfad der Hostdatei, die mit dem Skript erstellt wird.

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`: Eine Umgebungsvariable, die vom HPC Pack-Hauptknoten festgelegt wird, auf dem die Anzahl aller Kerne gespeichert ist, die dem Auftrag zugeordnet sind. In diesem Fall wird die Anzahl der Prozesse für **mpirun** angegeben.


## Übermitteln eines OpenFOAM-Auftrags

Sie können einen Auftrag jetzt in HPC Cluster Manager übermitteln. Sie müssen das Skript „hpcimpirun.sh“ in den Befehlszeilen für einige Aufgaben des Auftrags übergeben.

1. Stellen Sie eine Verbindung mit dem Hauptknoten des Clusters her, und starten Sie HPC-Cluster-Manager.

2. Vergewissern Sie sich unter **Ressourcenverwaltung**, dass die Linux-Computeknoten den Status **Online** aufweisen. Ist dies nicht der Fall, wählen Sie sie aus, und klicken Sie auf **Online schalten**.

3.  Klicken Sie in **Job Management** auf **Neuer Auftrag**.

4.  Geben Sie einen Namen für den Auftrag ein, z. B. _sloshingTank3D_.

    ![Auftragsdetails][job_details]

5.	Wählen Sie in den **Auftragsressourcen** den Ressourcentyp „Knoten“ aus, und legen Sie den Minimalwert auf „2“ fest. Der Auftrag wird auf zwei Linux-Knoten ausgeführt, die in diesem Beispiel jeweils über acht Kerne verfügen.

    ![Auftragsressourcen][job_resources]

6.	Fügen Sie dem Auftrag vier Aufgaben hinzu, indem Sie die folgenden Befehlszeilen und Einstellungen für die Aufgaben verwenden.

    >[AZURE.NOTE]Mit der Ausführung von `source /openfoam/settings.sh` werden die OpenFOAM- und MPI-Laufzeitumgebungen eingerichtet. Bei jeder folgenden Aufgabe wird also vor dem OpenFOAM-Befehl die Laufzeitumgebung aufgerufen.

    *   **Aufgabe 1**: Ausführen von **decomposePar**, um Datendateien für die parallele Ausführung von **interDyMFoam** zu generieren.
    
        *   Zuweisung von einem Knoten zur Aufgabe

        *   **Befehlszeile** – `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Arbeitsverzeichnis** – /openfoam/sloshingTank3D

        ![Aufgabe 1 Details][task_details1]

    *   **Aufgabe 2**: Paralleles Ausführen von **interDyMFoam**, um das Beispiel zu berechnen.

        *   Zuweisung von zwei Knoten zur Aufgabe

        *   **Befehlszeile** – `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Arbeitsverzeichnis** – /openfoam/sloshingTank3D

        ![Aufgabe 2 Details][task_details2]

    *   **Aufgabe 3**: Ausführen von **reconstructPar**, um die Sätze mit den Zeitverzeichnissen für jedes processor\_N\_ directory-Element zu einem zentralen Satz mit Zeitverzeichnissen zusammenzuführen.

        *   Zuweisung von einem Knoten zur Aufgabe

        *   **Befehlszeile** – `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Arbeitsverzeichnis** – /openfoam/sloshingTank3D

        ![Aufgabe 3 Details][task_details3]

    *   **Aufgabe 4**: Paralleles Ausführen von **foamToEnsight**, um die OpenFOAM-Ergebnisdateien in das EnSight-Format zu kopieren und die EnSight-Dateien in ein Verzeichnis mit dem Namen Ensight im case-Verzeichnis abzulegen.

        *   Zuweisung von zwei Knoten zur Aufgabe

        *   **Befehlszeile** – `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Arbeitsverzeichnis** – /openfoam/sloshingTank3D

        ![Aufgabe 4 Details][task_details4]

6.	Fügen Sie diesen Aufgaben in aufsteigender Aufgabenreihenfolge Abhängigkeiten hinzu.

    ![Abhängigkeiten von Aufgaben][task_dependencies]

7.	Klicken Sie auf **Senden**, um diesen Auftrag auszuführen.

    Standardmäßig sendet HPC Pack den Auftrag unter dem Konto des aktuell angemeldeten Benutzers. Nach dem Klicken auf **Senden** wird unter Umständen ein Dialogfeld mit der Aufforderung angezeigt, den Benutzernamen und das Kennwort einzugeben.

    ![Anmeldeinformationen][creds]

    Unter bestimmten Umständen speichert HPC Pack von Ihnen zuvor eingegebene Benutzerinformationen, sodass dieses Dialogfeld nicht angezeigt wird. Damit HPC Pack es erneut anzeigt, geben Sie den folgenden Befehl in ein Eingabeaufforderungsfenster ein und senden dann den Auftrag.

    ```
    hpccred delcreds
    ```

8.	Die Ausführung des Auftrags kann zwischen zehn Minuten und mehreren Stunden dauern. Dies richtet sich nach den Parametern, die Sie für das Beispiel festgelegt haben. In der Heat Map sehen Sie, dass der Auftrag auf zwei Linux-Knoten ausgeführt wird.

    ![Heat Map][heat_map]

    Auf jedem Knoten werden acht Prozesse gestartet.

    ![Linux-Prozesse][linux_processes]

9.  Wenn der Auftrag abgeschlossen ist, finden Sie die Ergebnisse des Auftrags in den Ordnern unter „C:\\OpenFoam\\sloshingTank3D“ und die Protokolldateien unter „C:\\OpenFoam“.


## Anzeigen der Ergebnisse in EnSight

Verwenden Sie bei Bedarf [EnSight](https://www.ceisoftware.com/), um die Ergebnisse des OpenFOAM-Auftrags zu visualisieren und zu analysieren. Weitere Informationen zur Visualisierung und zu Animationen in EnSight finden Sie in dieser [Videoanleitung](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Nachdem Sie EnSight auf dem Knoten installiert haben, können Sie die Anwendung starten.

2.  Öffnen Sie „C:\\OpenFoam\\sloshingTank3D\\EnSight\\sloshingTank3D.case“.

    Es wird ein Tank angezeigt.

    ![Tank in EnSight][tank]

3.	Erstellen Sie eine **Isosurface** aus **internalMesh**, und wählen Sie dann die Variable **alpha\_water** aus.

    ![Isofläche erstellen][isosurface]

4.	Legen Sie die Farbe für das Element **Isosurface\_part** fest, das Sie im vorherigen Schritt erstellt haben. Legen Sie sie beispielsweise auf Wasserblau fest.

    ![Farbe einer Isofläche ändern][isosurface_color]

5.  Erstellen Sie ein **Iso-volume** aus **walls**, indem Sie im Bereich **Parts** die Option **walls** wählen und in der Symbolleiste auf die Schaltfläche **Isosurfaces** klicken.

6.	Wählen Sie im Dialogfeld unter **Type** die Option **Isovolume**, und legen Sie den Minimalwert für **Isovolume range** auf 0,5 fest. Klicken Sie auf **Create with selected parts**, um das isovolume-Element zu erstellen.

7.	Legen Sie die Farbe für das Element **Iso\_volume\_part** fest, das Sie im vorherigen Schritt erstellt haben. Legen Sie sie beispielsweise auf ein dunkles Wasserblau fest.

8.	Legen Sie die Farbe für **walls** fest. Legen Sie sie beispielsweise auf transparentes Weiß fest.

9. Klicken Sie nun auf die **Wiedergabeschaltfläche**, um die Ergebnisse der Simulation anzuzeigen.

    ![Ergebnis für Tank][tank_result]


## Anhang


### Beispieldatei "cred.xml"

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### Beispieldatei „silent.cfg“

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no

```

### Beispielskript „settings.sh“

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```

### Beispielbefehle zum Hinzufügen von Repositorys und abhängigen Paketen auf Linux-Knoten

```
sudo zypper ar ftp://ftp.muug.mb.ca/mirror/opensuse/factory-snapshot/repo/oss/ update1

sudo zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ update2

sudo zypper ar ftp://ftp.pbone.net/mirror/ftp.opensuse.org/factory-snapshot/repo/oss/ update3

sudo zypper ar ftp://mirror.switch.ch/pool/4/mirror/opensuse/opensuse/distribution/13.2/repo/oss/ update4

sudo zypper ar ftp://bo.mirror.garr.it/pub/1/opensuse/distribution/13.2/repo/oss/ update6

sudo zypper ar ftp://ftp.pbone.net/mirror/ftp.opensuse.org/distribution/13.2/repo/oss/ update7

sudo zypper ar ftp://ftp.icm.edu.pl/vol/rzm5/linux-opensuse/distribution/13.2/repo/oss/ update8

sudo zypper install -t pattern devel_C_C++

sudo zypper install cmake boost-devel gnuplot mpfr-devel openmpi-devel glu-devel  
```

###Beispielskript „hpcimpirun.sh“

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
	${MPIRUN} $*
else
	# Create the hostfile file
	NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

	# Get every node name and write into the hostfile file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the mpirun with hostfile arg
	${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details1.png
[task_details2]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details2.png
[task_details3]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details3.png
[task_details4]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details4.png
[task_dependencies]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/linux_processes.png

<!---HONumber=AcomDC_1125_2015-->