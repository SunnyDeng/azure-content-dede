<properties
 pageTitle="NAMD mit Microsoft HPC Pack auf virtuellen Linux-Computern | Microsoft Azure"
 description="Sie stellen einen Microsoft HPC Pack-Cluster in Azure bereit und führen eine NAMD-Simulation mit ";charmrun"; auf mehreren Linux-Computeknoten aus."
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
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/02/2015"
 ms.author="danlep"/>

# Ausführen von NAMD mit dem Microsoft HPC Pack auf Linux-Computeknoten in Azure

In diesem Artikel wird beschrieben, wie Sie einen Microsoft HPC Pack-Cluster in Azure bereitstellen und einen [NAMD](http://www.ks.uiuc.edu/Research/namd/)-Auftrag mit **charmrun** auf mehreren Linux-Computeknoten in einem virtuellen Clusternetzwerk ausführen, um die Struktur eines großen Biomolekularsystems zu berechnen und darzustellen.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.



NAMD (Nanoscale Molecular Dynamics Program) ist ein paralleles Molecular Dynamics-Paket für die Simulation großer Biomolekularsysteme mit Millionen von Atomen, z. B. Viren, Zellstrukturen und große Proteine. NAMD skaliert für normale Simulationen auf Hunderte Kerne und für die größten Simulationen auf mehr als 500.000 Kerne.

Microsoft HPC Pack bietet eine Vielzahl von umfangreichen HPC- und parallelen Anwendungen, einschließlich MPI-Anwendungen, auf Clustern mit virtuellen Microsoft Azure-Computern. Ab Microsoft HPC Pack 2012 R2 unterstützt HPC Pack auch die Ausführung von Linux-HPC-Anwendungen auf virtuellen Linux-Computeknoten, die in einem HPC Pack-Cluster bereitgestellt werden. Eine Einführung in die Verwendung von Linux-Computeknoten mit HPC Pack finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md).


## Voraussetzungen

* **HPC Pack-Cluster mit Linux-Computeknoten** – unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md) finden Sie die Voraussetzungen sowie die Schritte zum Bereitstellen eines HPC Pack-Clusters mit Linux Computeknoten in Azure mithilfe von Azure PowerShell-Skript und HPC Pack-Images im Azure Marketplace.

    Die folgende XML-Beispielkonfigurationsdatei können Sie mit dem Skript verwenden, um einen Azure-basierten HPC Pack-Cluster mit einem Hauptknoten mit Windows Server 2012 R2 und 4 großen (Größe "Large", A3) Computeknoten mit CentOS 6.6 bereitzustellen. Ersetzen Sie die Werte durch die entsprechenden Werte für Ihr Abonnement und Ihre Dienstnamen.

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>West US</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>Subnet-1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>Large</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>Large</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>    
```


* **NAMD-Software und Lernprogrammdateien** – Sie können die NAMD-Software für Linux von der [NAMD](http://www.ks.uiuc.edu/Research/namd/)-Website herunterladen. Dieser Artikel basiert auf NAMD, Version 2.10, und verwendet das Archiv [Linux-x86\_64 (64-Bit-Intel/AMD mit Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310), das Sie zum Ausführen von NAMD auf mehrere Linux-Computeknoten in einem Clusternetzwerk verwenden. Laden Sie auch die [NAMD-Lernprogrammdateien](http://www.ks.uiuc.edu/Training/Tutorials/#namd) herunter. Befolgen Sie die Anweisungen weiter unten in diesem Artikel, um das Archiv und die Lernprogrammbeispiele auf dem Hauptknoten des Clusters zu extrahieren.

* **VMD** (optional) – zum Anzeigen der Ergebnisse des NAMD-Auftrags laden Sie das Programm zur Molekularvisualisierung, [VMD](http://www.ks.uiuc.edu/Research/vmd/), auf einen Computer Ihrer Wahl herunter und installieren es. Die aktuelle Version ist 1.9.2. Informationen zu den ersten Schritten finden Sie auf der Downloadwebsite von VMD.


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

2.	Erstellen Sie die Datei "C:\\cred.xml", und kopieren Sie die RSA-Schlüsseldaten in diese Datei. Ein Beispiel für diese Datei finden Sie im Anhang am Ende dieses Artikels.

    ```
    <ExtendedData>
      <PrivateKey>Copy the contents of private key here</PrivateKey>
      <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.	Öffnen Sie ein Befehlsfenster, und geben Sie den folgenden Befehl ein, um die Anmeldeinformationen für das Konto "hpclab\\hpcuser" festzulegen. Verwenden Sie den **extendeddata**-Parameter, um den Namen der für die Schlüsseldaten erstellten Datei "C:\\cred.xml" zu übergeben.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Dieser Befehl wird ohne Ausgabe abgeschlossen. Speichern Sie die Datei "cred.xml" nach dem Festlegen der Anmeldeinformationen für die zum Ausführen der Aufträge erforderlichen Benutzerkonten an einem sicheren Ort, oder löschen Sie sie.

5.	Wenn Sie das RSA-Schlüsselpaar auf einem der Linux-Knoten generiert haben, sollten Sie die Schlüssel nach der Verwendung löschen. HPC Pack richtet kein gegenseitiges Vertrauen ein, wenn eine vorhandene Datei "id\_rsa" oder "id\_rsa.pub" gefunden wird.

>[AZURE.IMPORTANT]Es wird nicht empfohlen, Linux-Aufträge als Clusteradministrator auf einem freigegebenen Cluster auszuführen, da ein von einem Administrator übermittelter Auftrag auf den Linux-Knoten unter dem root-Konto ausgeführt wird. Ein Auftrag, der von einem Benutzer ohne Administratorrechte übermittelt wurde, wird unter einem lokalen Linux-Benutzerkonto ausgeführt, das den gleichen Namen wie der Auftragsbenutzer hat. HPC Pack richtet dann das gegenseitige Vertrauen für diesen Linux-Benutzer auf allen Knoten ein, die dem Auftrag zugewiesen sind. Der Linux-Benutzer kann vor der Ausführung des Auftrags manuell auf den Linux-Knoten eingerichtet werden, oder HPC Pack erstellt den Benutzer automatisch, wenn der Auftrag übermittelt wird. Wenn der Benutzer von HPC Pack erstellt wurde, löscht HPC Pack ihn nach Abschluss des Auftrags wieder. Die Schlüssel werden nach Auftragsabschluss auf den Knoten gelöscht, um Sicherheitsrisiken zu verringern.

## Einrichten einer Dateifreigabe für Linux-Knoten

Richten Sie nun eine SMB-Standardfreigabe für einen Ordner auf dem Hauptknoten ein, und stellen Sie den freigegebenen Ordner für alle Linux-Knoten bereit, damit die Linux-Knoten auf NAMD-Dateien mit einem gemeinsamen Pfad zugreifen können. Weitere Informationen zu Dateifreigabeoptionen und den zugehörigen Schritten finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](virtual-machines-linux-cluster-hpcpack.md). (Wir empfehlen in diesem Artikel die Bereitstellung eines freigegebenen Ordners auf dem Hauptknoten, da Linux-Knoten mit CentOS 6.6 derzeit den Azure-Dateidienst nicht unterstützen, der ähnliche Funktionen bietet. Weitere Informationen zum Bereitstellen einer Azure-Dateifreigabe finden Sie unter [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx) (Beibehalten von Verbindungen zu Microsoft Azure-Dateien, in englischer Sprache).)

1.	Erstellen Sie auf dem Hauptknoten einen Ordner, und geben Sie ihn für alle Benutzer mit Lese-/Schreibberechtigungen frei. In diesem Beispiel ist "\\\CentOS66HN\\Namd" der Name des Ordners, wobei "CentOS66HN" der Hostname des Hauptknotens ist.

2. Extrahieren Sie die NAMD-Dateien in dem Ordner. Verwenden Sie dazu eine Windows-Version von **tar** oder ein anderes Windows-Dienstprogramm, das TAR-Archive verarbeiten kann. Extrahieren Sie das NAMD-TAR-Archiv nach "\\\CentOS66HN\\Namd\\namd2" und die Dateien des Lernprogramms nach "\\\CentOS66HN\\Namd\\namd2\\namdsample".

2.	Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie die folgenden Befehle aus, um den freigegebenen Ordner bereitzustellen.

    ```
    PS > clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Der erste Befehl erstellt einen Ordner namens "/namd2" auf allen Knoten in der Gruppe "LinuxNodes". Der zweite Befehl stellt den freigegebenen Ordner "//CentOS66HN/Namd/namd2" im Ordner bereit. Die Bits "dir\_mode" und "file\_mode" werden dabei auf 777 festgelegt. Die Parameter *username* und *password* im Befehl sollten mit den Anmeldeinformationen eines Benutzers auf dem Hauptknoten übereinstimmen.

>[AZURE.NOTE]Das Symbol "`" im zweiten Befehl ist ein Escapesymbol für PowerShell. "`," bedeutet, dass "," (das Komma) Teil des Befehls ist.


## Vorbereiten der Ausführung eines NAMD-Auftrags

 Der NAMD-Auftrag benötigt eine *nodelist*-Datei, um **charmrun** die Anzahl der Knoten, die beim Starten von NAMD-Prozessen verwendet werden, mitzuteilen. Schreiben Sie ein Bash-Skript, das die Datei mit der Knotenliste generiert und **charmrun** mit dieser nodelist-Datei ausführt. Sie können dann einen NAMD-Auftrag in HPC-Cluster-Manager übermitteln, der dieses Skript aufruft.

### Umgebungsvariablen und nodelist-Datei
Die Informationen zu Knoten und Kernen befinden sich in der Umgebungsvariable "$CCP\_NODES\_CORES", die automatisch beim Aktivieren des Auftrags automatisch vom HPC Pack-Hauptknoten festgelegt wird. Das Format für die Variable "$CCP\_NODES\_CORES" lautet wie folgt:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

Hier werden die Gesamtzahl der Knoten, die Knotennamen und die Anzahl der Kerne auf jedem Knoten aufgelistet, die dem Auftrag zugeordnet sind. Wenn der Auftrag z. B. 10 Kerne zum Ausführen benötigt, ist der Wert von "$CCP\_NODES\_CORES" etwa wie folgt:

```
3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
```

Im Folgenden finden die Informationen in der nodelist-Datei, die vom Skript generiert wird:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Beispiel:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```
### Bash-Skript zum Erstellen einer nodelist-Datei

Erstellen Sie mit einem Text-Editor Ihrer Wahl das folgende Bash-Skript im Ordner mit den NAMD-Programmdateien, und benennen Sie es mit "hpccharmrun.sh". Ein vollständiges Beispiel für diese Datei finden Sie im Anhang dieses Artikels. Dieses Bash-Skript führt Folgendes aus.

>[AZURE.TIP]Speichern Sie das Skript als Textdatei mit Linux-Zeilenenden (nur LF, nicht CR-LF). Dadurch wird sichergestellt, dass es auf den Linux-Knoten ordnungsgemäß ausgeführt wird.

1.	Definieren Sie einige Variablen.

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.	Rufen Sie Knoteninformationen aus den Umgebungsvariablen ab. $NODESCORES speichert eine Liste von Teilungswörtern aus $CCP\_NODES\_CORES. $COUNT ist die Größe von $NODESCORES.

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.	Wenn die Variable $CCP\_NODES\_CORES nicht festgelegt wurde, starten Sie **charmrun** einfach direkt. (Dies sollte nur auftreten, wenn Sie dieses Skript direkt auf den Linux-Knoten ausführen.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
    	# CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
    	#echo ${CHARMRUN} $*
    	${CHARMRUN} $*
    ```

4.	Sie können auch eine nodelist-Datei für **charmrun** erstellen.

    ```
    else
    	# Create the nodelist file
    	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    	# Write the head line
    	echo "group main" > ${NODELIST_PATH}

    	# Get every node name and number of cores and write into the nodelist file
    	I=1
    	while [ ${I} -lt ${COUNT} ]
    	do
    		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
    		let "I=${I}+2"
    	done
```
5.	Führen Sie **charmrun** mit der nodelist-Datei aus, erfassen Sie den Rückgabestatus, und entfernen Sie abschließend die nodelist-Datei.

    ${CCP\_NUMCPUS} ist eine andere Umgebungsvariable, die vom HPC Pack-Hauptknoten festgelegt wird. Sie speichert die Gesamtanzahl der Kerne, die diesem Auftrag zugeordnet sind. Wir verwenden diese Anzahl für die Angabe der Anzahl von Prozessen für "charmrun".

    ```
	# Run charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
    fi

    ```
6.	Beenden Sie den Vorgang mit dem Rückgabenstatus von **charmrun**.

    ```
    exit ${RTNSTS}
    ```

## Übermitteln eines NAMD-Auftrags

Jetzt können Sie einen NAMD-Auftrag in HPC-Cluster-Manager übermitteln.

1.	Stellen Sie eine Verbindung mit dem Hauptknoten des Clusters her, und starten Sie HPC-Cluster-Manager.

2.  Vergewissern Sie sich in **Node Management**, dass die Linux-Computeknoten den Status **Online** aufweisen. Ist dies nicht der Fall, wählen Sie sie aus, und klicken Sie auf **Online schalten**.

2.  Klicken Sie in **Job Management** auf **Neuer Auftrag**.

3.	Geben Sie einen Namen für den Auftrag ein, z. B. *hpccharmrun*.

    ![Neuer HPC-Auftrag][namd_job]

4.	Wählen Sie auf der Seite **Auftragsdetails** unter **Job Resources** den Ressourcentyp **Knoten** aus, und legen Sie **Minimum** auf "3" fest. In diesem Beispiel führen wir den Auftrag auf 3 Linux-Knoten aus, von denen jeder über 4 Kerne verfügt.

    ![Auftragsressourcen][job_resources]

5.	Fügen Sie auf der Seite **Task Details and I/O Redirection** einen neuen Task für den Auftrag aus, und legen Sie die folgenden Werte fest.

    * **Befehlszeile** – `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    * **Arbeitsverzeichnis** – /namd2

    * **Minimum** – 3

    ![Taskdetails][task_details]

    >[AZURE.NOTE]Sie legen das Arbeitsverzeichnis hier fest, da **charmrun** versucht, auf jedem Knoten zum gleichen Arbeitsverzeichnis zu navigieren. Wenn das Arbeitsverzeichnis nicht festgelegt ist, startet HPC Pack den Befehl in einem zufällig benannten Ordner, der auf einem der Linux-Knoten erstellt wurde. Dadurch kann der folgende Fehler auf den anderen Knoten auftreten: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Um dies zu vermeiden, geben Sie einen Ordnerpfad an, auf den von allen Knoten als Arbeitsverzeichnis zugegriffen werden kann.

5.	Klicken Sie auf **Senden**, um diesen Auftrag auszuführen.

    Standardmäßig sendet HPC Pack den Auftrag unter dem Konto des aktuell angemeldeten Benutzers. Sie werden nach dem Klicken auf **Senden** möglicherweise in einem Dialogfeld aufgefordert, den Benutzernamen und das Kennwort einzugeben.

    ![Anmeldeinformationen][creds]

    Unter bestimmten Umständen speichert HPC Pack von Ihnen zuvor eingegebene Benutzerinformationen, sodass dieses Dialogfeld nicht angezeigt wird. Damit HPC Pack es erneut anzeigt, geben Sie den folgenden Befehl in ein Eingabeaufforderungsfenster ein und senden dann den Auftrag.

    ```
    hpccred delcreds
    ```

6.	Der Auftrag nimmt mehrere Minuten in Anspruch.

7.	Sie finden das Auftragsprotokoll in "\<headnodeName>\\Namd\\namd2\\namd2\_hpccharmrun.log" und die Ausgabedateien in "\<headnode>\\Namd\\namd2\\namdsample\\1-2-sphere".

8.	Starten Sie gegebenenfalls VMD, um die Auftragsergebnisse anzuzeigen. Die Schritte zur Visualisierung der NAMD-Ausgabedateien (in diesem Fall ein Molekül des Proteins Ubiquitin in einer Wasserkugel) gehen über den Rahmen dieses Artikels hinaus. Weitere Einzelheiten finden Sie im [NAMD-Lernprogramm](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf).

    ![Auftragsergebnisse][vmd_view]

## Anhang

### Beispielskript für "hpccharmrun.sh"

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
	#echo ${CHARMRUN} $*
	${CHARMRUN} $*
else
	# Create the nodelist file
	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

	# Write the head line
	echo "group main" > ${NODELIST_PATH}

	# Get every node name & cores and write into the nodelist file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 
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




<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png

<!---HONumber=Oct15_HO3-->