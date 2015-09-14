<properties
 pageTitle="Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen | Microsoft Azure"
	description="Erfahren Sie, wie Sie einen Linux-Cluster mit A8- oder A9-VMs erstellen, um RDMA zum Ausführen von MPI-Anwendungen zu verwenden."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>
<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/17/2015"
	ms.author="danlep"/>

# Einrichten eines Linux RDMA-Clusters zum Ausführen von MPI-Anwendungen

In diesem Artikel wird beschrieben, wie Sie einen Linux RDMA-Cluster mit [virtuellen Computern der Größe A8 und A9](virtual-machines-a8-a9-a10-a11-specs.md) in Azure einrichten, um parallele MPI-Anwendungen (Message Passing Interface) auszuführen. Wenn Sie Linux-basierte A8- und A9-VMs zum Ausführen einer unterstützten MPI-Implementierung konfigurieren, erfolgt die Kommunikation der MPI-Anwendungen in Azure effizient über ein auf RDMA-Technologie (Remote Direct Memory Access) basierenden Netzwerk mit niedriger Latenz und hohem Durchsatz.

>[AZURE.NOTE]Azure Linux RDMA wird derzeit mit Intel MPI Library 5.0 unter SUSE Linux Enterprise Server 12 (SLES 12) unterstützt.
>
> Azure bietet zudem rechenintensive A10- und A11-Instanzen mit Verarbeitungskapazitäten ähnlich denjenigen von A8- und A9-Instanzen, aber ohne eine Verbindung zu einem RDMA-Back-End-Netzwerk. Wenn Sie MPI-Workloads in Azure ausführen möchten, erzielen Sie in der Regel mit den A8- und A9-Instanzen eine optimale Leistung.


## Optionen für die Bereitstellung von Linux-Clustern

Mit den folgenden Methoden können Sie einen Linux RDMA-Cluster mit oder ohne eine Auftragsplanung erstellen.

* **HPC Pack** – Sie können einen Microsoft HPC Pack-Cluster in Azure erstellen und Computeknoten hinzufügen, auf denen unterstützte Linux-Distributionen ausgeführt werden (Unterstützung ab HPC Pack 2012 R2 Update 2). Einige Linux-Knoten können für den Zugriff auf das RDMA-Netzwerk konfiguriert werden. Informationen zu den ersten Schritten finden Sie in der [Dokumentation zu HPC Pack](http://go.microsoft.com/fwlink/?LinkId=617894).

* **Azure-CLI-Skripts** – Sie können die [Azure-Befehlszeilenschnittstelle](../xplat-cli.md) (CLI) für Mac, Linux und Windows zum Erstellen eigener Skripts für die Bereitstellung eines virtuellen Netzwerks und der übrigen Komponenten verwenden, die zum Erstellen eines Linux-Clusters erforderlich sind. Über die CLI im asm-Modus (Azure Service Management) werden die Clusterknoten seriell bereitgestellt, daher kann die Bereitstellung vieler Computeknoten einige Minuten in Anspruch nehmen. Ein Beispiel finden Sie in den Schritten im weiteren Verlauf dieses Artikels.

* **Azure-Ressourcen-Manager-Vorlagen** – Durch Erstellen einer einfachen Azure-Ressourcen-Manager-JSON-Vorlagendatei und Ausführen der Azure-CLI-Befehle im arm-Modus oder unter Verwendung des Azure-Vorschauportals können Sie mehrere A8- und A9-Linux-VMs bereitstellen sowie virtuelle Netzwerke, statische IP-Adressen, DNS-Einstellungen und andere Ressourcen definieren, um einen Computecluster einzurichten, der das RDMA-Netzwerk nutzen und MPI-Workloads ausführen kann. Sie können [eine eigene Vorlage erstellen](../resource-group-authoring-templates.md) oder auf der [Seite "Azure Schnellstart Vorlagen"](https://azure.microsoft.com/documentation/templates/) nach Vorlagen von Microsoft oder der Community suchen, um die gewünschte Lösung bereitzustellen. Ressourcen-Manager-Vorlagen stellen in der Regel die schnellste und zuverlässigste Möglichkeit zum Bereitstellen eines Linux-Clusters dar.

## Bereitstellung in der Azure-Dienstverwaltung mit Azure-CLI-Skripts

Die folgenden Schritte helfen Ihnen beim Bereitstellen einer SLES-12-VM, beim Installieren von Intel MPI Library und anderen Anpassungen, beim Erstellen eines benutzerdefinierten VM-Images und beim anschließenden Erstellen des Skripts für die Bereitstellung eines Clusters aus A8- oder A9-VMs unter Verwendung der Azure-Befehlszeilenschnittstelle.

### Voraussetzungen

* **Clientcomputer** – Sie benötigen einen Mac-, Linux- oder Windows-basierten Clientcomputer für die Kommunikation mit Azure.

* **Azure-Abonnement** – Wenn Sie über kein Konto verfügen, können Sie in wenigen Minuten ein kostenloses Testkonto einrichten. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

* **Kernnutzungskontingent** – Sie müssen möglicherweise das Kernnutzungskontingent erhöhen, um einen Cluster mit A8- oder A9-VMs bereitzustellen. Beispielsweise benötigen Sie mindestens 128 Kerne, wenn Sie, wie in diesem Artikel dargestellt, acht A9-VMs bereitstellen möchten. Um ein Kontingent zu erhöhen, können Sie [eine gebührenfreie Onlinekundensupport-Anforderung öffnen](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

* **Azure-CLI** – [Installieren](../xplat-cli-install.md) Sie die Azure-CLI und [konfigurieren](../xplat-cli-connect.md) Sie sie zur Verwendung Ihres Azure-Abonnements auf dem Clientcomputer.


### Bereitstellen einer SLES 12-VM

Führen Sie nach der Anmeldung bei Azure mit der Azure-Befehlszeilenschnittstelle den Befehl `azure config list` aus, um sicherzustellen, dass die Befehlszeilenschnittstelle sich im asm-Modus (Azure Service Management) befindet. Wenn dies nicht der Fall ist, aktivieren Sie den Modus durch Ausführen dieses Befehls:

```
azure config mode asm
```

Geben Sie Folgendes ein, um alle Abonnements aufzulisten, zu deren Verwendung Sie berechtigt sind:

```
azure account list
```

Das aktuelle aktive Abonnement erkennen Sie daran, dass es für `Current` den Wert `true` aufweist. Ist dies nicht das Abonnement, das Sie zum Erstellen des Clusters verwenden möchten, legen Sie die entsprechenden Abonnementnummer als aktives Abonnement fest:

```he
azure account set <subscription-number>
```

Um die öffentlich verfügbaren SLES 12 HPC-Images in Azure anzuzeigen, führen Sie, wenn Ihre Shellumgebung **grep** unterstützt, einen Befehl aus, der etwa folgendermaßen aussieht:

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]SLES 12 HPC-Images sind mit den erforderlichen Linux RDMA-Treibern für Azure vorkonfiguriert.

Stellen Sie nun einen virtuellen Computer der Größe A9 mit einem verfügbaren SLES 12 HPC-Image bereit, indem Sie einen Befehl ausführen, der etwa folgendermaßen aussieht:

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 10004 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

Hierbei gilt:

* Die Größe (in diesem Beispiel A9) kann A8 oder A9 sein.

* Die externe SSH-Portnummer (in diesem Beispiel 10004) ist eine beliebige gültige Portnummer. Die interne SSH-Portnummer wird auf 22 festgelegt.

* Ein neuer Clouddienst wird in der durch den Standort festgelegten Azure-Region erstellt. Geben Sie einen Standort, z. B. "West US" ein, an dem die A8- und A9-Instanzen verfügbar sind.

* Der Imagename kann derzeit `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (kostenlos) oder `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` für SUSE Priority Support (gebührenpflichtig)

### Anpassen des virtuellen Computers

Nach Abschluss der VM-Bereitstellung stellen Sie mithilfe der externen IP-Adresse des virtuellen Computers (oder des DNS-Namens) und der externen Portnummer, die Sie konfiguriert haben, eine SSH-Verbindung zum virtuellen Computer her, und nehmen Sie die Anpassungen vor. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird](virtual-machines-linux-how-to-log-on.md).

>[AZURE.NOTE]Microsoft Azure bietet keinen Stammzugriff auf Linux-VMs. Wenn Sie als Benutzer angemeldet sind, können Sie `sudo –s` verwenden, um Administratorzugriff zu erhalten.

**Updates** – Sie können mithilfe von **zypper** und mit den NFS-Dienstprogrammen Updates installieren.

>[AZURE.IMPORTANT]Zurzeit empfiehlt es sich nicht, Kernel-Updates zu installieren, da dies zu Problemen mit den Linux RDMA-Treibern führen kann.

**Intel MPI** – Laden Sie die Intel MPI Library 5.0-Laufzeit von der ["Intel.com"-Website](https://software.intel.com/de-DE/intel-mpi-library/) herunter, und installieren Sie sie. Nach der Registrierung bei Intel folgen Sie dem Link in der Bestätigungs-E-Mail auf die entsprechende Webseite, und kopieren Sie den Downloadlink der TGZ-Datei für die entsprechende Version von Intel MPI.

Führen Sie zum Installieren von Intel MPI auf dem virtuellen Computer Befehle aus, die etwa folgendermaßen aussehen:

```

$ wget <download link for your registration>
$ tar xvzf <tar-file>
$ cd <mpi-directory>
$ sudo ./install.sh
```

**Sperren von Speicher** – Zum Sperren des für RDMA verfügbaren Arbeitsspeichers mit MPI-Codes müssen Sie die folgenden Einstellungen in der Datei "/etc/security/limits.conf" hinzufügen oder ändern:

```
<User or group name> hard    memlock <memory required for your application in KB>
<User or group name> soft    memlock <memory required for your application in KB>
```

>[AZURE.NOTE]Zu Testzwecken können Sie "memlock" auch auf "unbegrenzt" festlegen. Beispiel: `<User or group name>    hard    memlock unlimited`.


**SSH-Schlüssel** – Richten Sie für den Benutzernamen und das Kennwort, mit dem Sie diesen virtuellen Computer erstellt haben, eine Vertrauensstellung zwischen allen Computeknoten im Cluster her. Verwenden Sie den folgenden Befehl, um SSH-Schlüssel zu erstellen:

```
$ ssh-keygen
```

Speichern Sie den öffentlichen Schlüssel an einem Standardspeicherort, und merken Sie sich die eingegebene Passphrase.

```
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Bearbeiten oder erstellen Sie im "~/.ssh"-Verzeichnis die Datei "ssh\_config". Geben Sie den IP-Adressbereich des privaten Netzwerks an, das Sie in Azure verwenden:

```
host 10.32.0.*
StrictHostKeyChecking no
```

Alternativ können Sie die VPN-IP-Adresse der einzelnen virtuellen Computer in Ihrem Cluster wie folgt aufführen:

```
host 10.32.0.1
 StrictHostKeyChecking no
host 10.32.0.2
 StrictHostKeyChecking no
host 10.32.0.3
 StrictHostKeyChecking no
```

>[AZURE.NOTE]Das Konfigurieren von `StrictHostKeyChecking no` kann ein potenzielles Sicherheitsrisiko darstellen, wenn eine bestimmte IP-Adresse oder ein bestimmter Adressbereich nicht wie oben dargestellt angegeben wird.

**Anwendungen** – Kopieren Sie alle benötigten Anwendungen auf diesen virtuellen Computer, oder führen Sie andere Anpassungen aus, bevor Sie das Image erfassen.

### Erfassen des Images

Zum Erfassen des Images führen Sie zuerst den folgenden Befehl auf der Linux-VM aus:

```
$ sudo waagent -deprovision
```

Führen Sie dann auf Ihrem Clientcomputer die folgenden Azure-CLI-Befehle zum Erfassen des Images aus. Weitere Informationen finden Sie unter [Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden](virtual-machines-linux-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Nachdem Sie diese Befehle ausgeführt haben, wird das VM-Image für Ihre Verwendung erfasst, und der virtuelle Computer wird gelöscht. Jetzt ist Ihr benutzerdefiniertes Image zur Bereitstellung eines Clusters fertig.

### Bereitstellen eines Clusters mit dem Image

Ändern Sie das folgende Skript mit den entsprechenden Werten für Ihre Umgebung, und führen Sie es auf Ihrem Clientcomputer aus. Da die virtuellen Computer mit der ASM-Bereitstellungsmethode seriell bereitgestellt werden, dauert es einige Minuten, bis die im Skript vorgeschlagenen acht A9-VMs bereitgestellt wurden.

```
### Script to create a compute cluster without a scheduler in a VNet in Azure
### Create a custom private network in Azure
### Replace 10.32.0.0 with your virtual network address space
### Replace <network-name> with your network identifier
### Select a region where A8 and A9 VMs are available, such as West US
### See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

### Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
### Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

### Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

### Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

### In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

### Save this script and run it at the CLI prompt to provision your cluster
```

## Konfigurieren und Ausführen von Intel MPI

Zum Ausführen von MPI-Anwendungen auf Azure Linux RDMA müssen Sie bestimmte, für Intel MPI spezifische Umgebungsvariablen konfigurieren. Hier finden Sie ein Beispielskript zum Konfigurieren der Variablen und Ausführen einer Anwendung.

```
#!/bin/bash -x
source /opt/intel/impi_latest/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Das Format der Hostdatei sieht wie folgt aus. Fügen Sie eine Zeile für jeden Knoten im Cluster hinzu. Geben Sie private IP-Adressen aus dem zuvor definierten VNet an, keine DNS-Namen.

```
private ip address1:16 [e.g. 10.32.0.1:16]
private ip address2:16
...
```

## Verifizieren eines Basisclusters mit zwei Knoten nach der Installation von Intel MPI

Sie können die folgenden Intel MPI-Befehle ausführen, um die Clusterkonfiguration unter Verwendung der Pingpongbenchmark zu überprüfen.

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>, <host2> -ppn 1 -n 2 -env I_MPI_FABRICS dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
```

In einem funktionierenden Cluster mit zwei Knoten sollte eine Ausgabe ähnlich der folgenden angezeigt werden:

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```

## Überlegungen zur Netzwerktopologie

* Für Linux-VMs ist Eth1 für RDMA-Netzwerkverkehr reserviert. Ändern Sie keine Eth1-Einstellungen oder anderen Informationen in der Konfigurationsdatei, die sich auf dieses Netzwerk beziehen.

* In Azure wird IP over InfiniBand (IB) nicht unterstützt. Nur RDMA over IB wird unterstützt.

* Auf Linux-VMs ist Eth0 für den regulären Azure-Netzwerkverkehr reserviert.

## Nächste Schritte

* Versuchen Sie, Ihre Linux-MPI-Anwendungen im Linux-Cluster bereitzustellen und auszuführen.

* Anleitungen zu Intel MPI finden Sie in der [Dokumentation zu Intel MPI Library](https://software.intel.com/de-DE/articles/intel-mpi-library-documentation/).

<!---HONumber=September15_HO1-->