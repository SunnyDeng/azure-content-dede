<properties 
   pageTitle="Schritte für ein Failback von Azure zu VMware" 
   description="Dieser Artikel beschreibt, wie mithilfe von Azure Site Recovery und vContinuum ein Failback eines virtuellen Computers zu VMware durchgeführt werden kann." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/05/2015"
   ms.author="ruturajd@microsoft.com"/>

# Schritte für ein Failback von Azure zu VMware

Dieses Dokument informiert über die erforderlichen Schritte für ein Failback von Azure zu Ihrem VMware-Standort. Voraussetzung ist, dass Sie bereits die Schritte des Lernprogramms für [Schutz und Wiederherstellung von VMware zu Azure](site-recovery-vmware-to-azure.md) ausgeführt haben.

Nach einem erfolgreichen Failover zu Azure sind die virtuellen Computer auf der gleichnamigen Registerkarte verfügbar. Im Anschluss finden Sie die auszuführenden Schritte für ein Failback.

Wichtig: Bei einem Failback von Azure zu Ihrem VMware-Standort kann als Wiederherstellungsziel nur ein virtueller Computer verwendet werden. Dies gilt auch, wenn die ursprüngliche VMware-Quelle ein physischer Computer war: Nach einem Failover zu Azure und einem Failback zu VMware wird dieser in einen virtuellen Computer konvertiert.

## Übersicht

1.  Installieren des vContinuum-Servers (lokal)

    a. Konfigurieren des Servers mit einem Verweis auf den Konfigurationsserver

2.  Bereitstellen eines Prozessservers (PS) für Azure

3.  Installieren eines Masterziels (lokal)

4.  Schritte zur Rückkehr zum lokalen Schutz virtueller Computer nach einem Failover

    a. Überlegungen zur Konfiguration

5.  Überwachen der Rückkehr zum lokalen Schutz virtueller Computer

6.  Durchführen eines Failovers der virtuellen Computer zurück zur lokalen Bereitstellung

Im Anschluss finden Sie eine Übersicht über das Setup, das wir mit den weiter unten beschriebenen Schritten erreichen. Ein Teil des Setups wurde bereits während des Failovers durchgeführt.

-   Die blauen Linien stehen für die beim Failover verwendeten Verbindungen.

-   Die roten Linien stehen für die beim Failback verwendeten Verbindungen.

-   Bei den mit Pfeilen versehenen Linien handelt es sich um Internetverbindungen.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Installieren von vContinuum (lokal)

Die Setupdatei für vContinuum finden Sie [hier](http://go.microsoft.com/fwlink/?linkid=526305). Installieren Sie zusätzlich den [hier](http://go.microsoft.com/fwlink/?LinkID=533813) verfügbaren vContinuum-Patch.

1.  Führen Sie die Setupdatei aus, um die Installation von vContinuum zu starten. Klicken Sie auf **Weiter**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
2.  Geben Sie die IP-Adresse und den Port des CX-Servers an. Wählen Sie HTTPS aus.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

3.  Wechseln Sie zur CS-Bereitstellung auf Azure, um die CX-IP-Adresse zu ermitteln, und zeigen Sie das zugehörige Dashboard an.

	![](./media/site-recovery-failback-azure-to-vmware/image4.png)

4.  Wechseln Sie zur Registerkarte "Endpunkte" auf der VM-Seite, um den öffentlichen CX-Port zu ermitteln, und identifizieren Sie den öffentlichen Port für HTTPS-Endpunkte

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

5.  Geben Sie die Passphrase für den Konfigurationsserver (Configuration Server, CS) an. Die Passphrase haben Sie sich bei der CS-Registrierung notiert. Hierbei handelt es sich um die gleiche Passphrase, die Sie auch bei der Bereitstellung von Masterziel und Prozessserver verwendet haben. Sollten Sie die Passphrase vergessen haben, navigieren Sie in Azure zum CS. Dort finden Sie die Passphrase unter „C:\\Programme (x86)\\InMage Systems\\private\\connection.passphrase“.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

6.  Geben Sie den gewünschten Speicherort für den vContinuum-Server an, und starten Sie die Installation.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

7.  Sobald angezeigt wird, dass die Installation abgeschlossen ist, können Sie vContinuum starten. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Installieren des Prozessservers (PS) für Azure

Für Azure muss ein PS installiert werden, damit die virtuellen Computer in Azure die Daten wieder an das lokale Masterziel zurücksenden können. Der PS für Azure muss im gleichen Netzwerk bereitgestellt werden wie der Konfigurationsserver.

1.  Klicken Sie auf der Seite **Konfigurationsserver** in Azure auf die Option zum Hinzufügen eines neuen Prozessservers.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Geben Sie einen Prozessservernamen sowie einen Namen und ein Kennwort an, um eine Verbindung mit dem virtuellen Computer als Administrator herzustellen. Wählen Sie den Konfigurationsserver aus, bei dem Sie den Prozessserver registrieren möchten. Dies sollte der gleiche Server sein, den Sie für den Schutz und das Failover Ihrer virtuellen Computer verwenden. Geben Sie das Azure-Netzwerk an, in dem der Prozessserver bereitgestellt werden soll. Dabei sollte es sich um dasselbe Netzwerk wie beim Konfigurationsserver handeln. Geben Sie eine eindeutige IP-Adresse aus dem ausgewählten Subnetz an und starten Sie die Bereitstellung.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)


Für den Prozessserver wird ein Bereitstellungsauftrag ausgelöst.

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

Wenn der Prozessserver für Azure bereitgestellt wurde, können Sie sich mit den angegebenen Anmeldeinformationen bei dem Server anmelden. Verwenden Sie bei der Registrierung des PS die gleichen Schritte wie beim vorwärts gerichteten Schutz.

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

Während des Failbacks registrierte Server werden nicht in den VM-Eigenschaften angezeigt. Diese Server werden nur auf der Serverregisterkarte des Konfigurationsservers angezeigt, für den sie registriert wurden. Es kann etwa 10 bis 15 Minuten dauern, bis der PS unter dem Konfigurationsserver erscheint.

## Installieren eines Masterzielservers (lokal)

Abhängig von den virtuellen Quellcomputern muss lokal entweder ein Linux- oder ein Windows-basierter Masterzielserver installiert werden.

### Bereitstellen eines Windows-Masterziels (MT)

Ein Windows-MT ist bereits im vContinuum-Setup enthalten. Bei der Installation von vContinuum wird auf dem Computer auch ein MT bereitgestellt und beim Konfigurationsserver registriert.

1.  Erstellen Sie zunächst auf dem ESX-Host, auf dem Sie die virtuellen Computer von Azure wiederherstellen möchten, einen leeren lokalen Computer.

2.  Stellen Sie sicher, dass mit dem virtuellen Computer mindestens zwei Datenträger verknüpft sind: einer für das Betriebssystem und einer als Aufbewahrungslaufwerk.

3.  Installieren Sie das Betriebssystem.

4.  Installieren Sie vContinuum auf dem Server. Damit ist auch die Installation des MTs abgeschlossen.

### Bereitstellen eines Linux-MTs

1.  Erstellen Sie zunächst auf dem ESX-Host, auf dem Sie die virtuellen Computer von Azure wiederherstellen möchten, einen leeren lokalen Computer.

2.  Stellen Sie sicher, dass mit dem virtuellen Computer mindestens zwei Datenträger verknüpft sind: einer für das Betriebssystem und einer als Aufbewahrungslaufwerk.

3.  Installieren Sie das Linux-Betriebssystem. Bei dem System mit dem Linux-basierten Masterziel (Master Target, MT) darf für den Stamm- oder Aufbewahrungspeicherplatz nicht der LVM verwendet werden. Das Linux-MT ist standardmäßig so konfiguriert, dass LVM-Partitionen/Datenträger nicht erkannt werden.
4.  Folgende Partitionen können erstellt werden:

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Führen Sie nach der Installation des Betriebssystems die folgenden Schritte aus, bevor Sie mit der MT-Installation beginnen:


#### Schritte nach der Installation des Betriebssystems

Aktivieren Sie den Parameter „disk.EnableUUID = TRUE“, um SCSI-IDs für die einzelnen SCSI-Festplatten eines virtuellen Linux-Computers zu erhalten. Gehen Sie hierzu wie folgt vor:

1. Fahren Sie den virtuellen Computer herunter.
2. Klicken Sie im linken Bereich mit der rechten Maustaste auf den VM-Eintrag, und wählen Sie **Edit Settings**.
3. Klicken Sie auf die Registerkarte **Options**. Klicken Sie links unter „Advanced“ auf **General** und anschließend rechts auf **Configuration Parameters**. Wenn der Computer ausgeführt wird, ist die Option für die Konfigurationsparameter deaktiviert. Fahren Sie den Computer herunter, um die Registerkarte zu aktivieren.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Prüfen Sie, ob bereits eine Zeile mit **disk.EnableUUID** vorhanden ist. Falls die Zeile vorhanden und der Wert auf „False“ festgelegt ist, überschreiben Sie den Wert mit „True“. Die Groß- und Kleinschreibung spielt dabei keine Rolle. Falls die Zeile vorhanden und der Wert auf „True“ festgelegt ist, klicken Sie auf „Cancel“, und testen Sie nach dem Start des Gastbetriebssystems den SCSI-Befehl. Ist die Zeile nicht vorhanden, klicken Sie auf **Add Row**.
5. Fügen Sie in der Spalte „Name“ die Zeichenfolge „disk.EnableUUID“ ein. Legen Sie den Wert auf „TRUE“ fest. Fügen Sie die oben angegebenen Werte nicht mit Anführungszeichen ein.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Herunterladen und Installieren zusätzlicher Pakete

HINWEIS: Vergewissern Sie sich vor dem Herunterladen und Installieren zusätzlicher Pakete, dass das System über eine Internetverbindung verfügt.

# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Der obige Befehl lädt die folgenden 15 Pakete aus dem CentOS 6.6-Repository herunter und installiert sie:

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

HINWEIS: Wenn der Quellcomputer für das Stamm- oder Startgerät das Reiser- oder das XFS-Dateisystem verwendet, müssen vor dem Schutz die folgenden Pakete heruntergeladen und auf dem Linux-Masterziel installiert werden:

# cd /usr/local

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Anwenden benutzerdefinierter Konfigurationsänderungen

Schließen Sie zunächst die Schritte nach der Installation ab, bevor Sie benutzerdefinierte Konfigurationsänderungen anwenden.

Gehen Sie zum Anwenden benutzerdefinierter Konfigurationsänderungen wie folgt vor:

1. Kopieren Sie die Binärdatei für „RHEL 6-64 Unified Agent“ in das neu erstellte Betriebssystem.

2. Führen Sie für die Binärdatei den folgenden Untar-Befehl aus:

**tar -zxvf <Dateiname>**

3. Führen Sie den folgenden Befehl aus, um die erforderliche Berechtigung zu erteilen:

# **chmod 755 ./ApplyCustomChanges.sh**

4. Führen Sie den folgenden Befehl aus, um das Skript auszuführen:

**# ./ApplyCustomChanges.sh**

HINWEIS: Führen Sie das Skript nur einmal auf dem Server aus. Starten Sie den Server nach erfolgreicher Ausführung des obigen Skripts neu.

### Initiieren der MT-Installation

Laden Sie [hier](http://go.microsoft.com/fwlink/?LinkID=529757) die Installationsdatei für den Linux-Masterzielserver herunter.

Kopieren Sie das heruntergeladene Installationsprogramm für den Linux-Masterzielserver mithilfe eines beliebigen SFTP-Clienthilfsprogramms auf den virtuellen Linux-Computer für das Masterziel. Alternativ können Sie sich auch bei dem virtuellen Linux-Computer für das Masterziel anmelden und das Installationspaket mittels „wget“ unter dem angegebenen Link herunterladen.

Melden Sie sich bei dem virtuellen Computer für den Linux-Masterzielserver mit einem beliebigen SSH-Client an.

Wenn Sie über eine VPN-Verbindung mit dem Azure-Netzwerk verbunden sind, in dem Sie den Linux-Masterzielserver bereitgestellt haben, verwenden Sie die interne IP-Adresse für den Linux-Masterzielserver (zu finden auf dem Dashboard für virtuelle Computer) und den Port 22, um über Secure Shell eine Verbindung mit dem Linux-Masterzielserver herzustellen.

Wenn Sie über eine öffentliche Internetverbindung eine Verbindung mit dem Linux-Masterzielserver herstellen, verwenden Sie die öffentliche virtuelle IP-Adresse des Linux-Masterzielservers (zu finden auf der Dashboardseite für virtuelle Computer) und den für SSH erstellten öffentlichen Endpunkt, um sich bei dem Linux-Masterzielserver anzumelden.

Extrahieren Sie die Dateien aus dem gezippten tar-Archiv des Installationsprogramms für den Linux-Masterzielserver. Führen Sie hierzu

*tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64** in dem Verzeichnis aus, in das Sie das Installationsprogramm für den Linux-Masterzielserver kopiert haben.

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

Wenn Sie die Dateien des Installationsprogramms in ein anderes Verzeichnis extrahiert haben, geben Sie das Verzeichnis an, in das der Inhalt des tar-Archivs extrahiert wurde. Führen Sie an diesem Verzeichnispfad „sudo ./install.sh“ aus.

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

Wenn Sie zum Auswählen der primären Rolle des Agents aufgefordert werden, wählen Sie „2“ (Masterziel) aus.

Lassen Sie die anderen Optionen der interaktiven Installation unverändert.

Warten Sie, bis die Installation fortgesetzt wird und die Oberfläche für die Hostkonfiguration erscheint. Die Hostkonfiguration für den Linux-Masterzielserver wird über ein Befehlszeilenprogramm vorgenommen. Lassen Sie die Fenstergröße des SSH-Clienthilfsprogramms unverändert. Wählen Sie mithilfe der Pfeiltasten die Option „Global“ aus, und drücken Sie die EINGABETASTE.

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  Geben Sie die interne IP-Adresse des Konfigurationsservers (zu finden auf der Dashboardseite für virtuelle Computer) ein, und drücken Sie die EINGABETASTE.

2.  Geben Sie als Portnummer den Wert „22“ ein, und drücken Sie die EINGABETASTE.

3.  Lassen Sie die Option „Use https:“ unverändert. Drücken Sie erneut die EINGABETASTE.

4.  Geben Sie die für den Konfigurationsserver generierte Passphrase ein. Wenn Sie auf einem Windows-Computer einen PuTTY-Client für eine SSH-Verbindung mit dem virtuellen Computer für den Linux-Masterzielserver verwenden, können Sie den Inhalt durch Drücken von UMSCHALT+EINFG aus der Zwischenablage einfügen. Kopieren Sie die Passphrase durch Drücken von STRG+C in die lokale Zwischenablage, und fügen Sie sie durch Drücken von UMSCHALT+EINFG ein. Drücken Sie die EINGABETASTE.

5.  Navigieren Sie mit der NACH-RECHTS-TASTE zu „Quit“, und drücken Sie die EINGABETASTE.

Warten Sie, bis die Installation abgeschlossen ist.

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Falls der Linux-Masterzielserver noch nicht beim Konfigurationsserver registriert wurde, können Sie unter „/usr/local/ASR/Vx/bin/hostconfigcli“ das Hilfsprogramm für die Hostkonfiguration ausführen. (Hierzu müssen Sie zunächst Zugriffsberechtigungen für dieses Verzeichnis festlegen, indem Sie „chmod“ als Administrator ausführen.)


#### Überprüfen Sie die Registrierung des Masterzielservers beim Konfigurationsserver.

Rufen Sie in Azure Site Recovery über die Konfigurationsserverseite die Serverdetailseite auf, und vergewissern Sie sich, dass der Masterzielserver erfolgreich registriert wurde.

Hinweis: Nach dem Registrieren des MTs sehen Sie möglicherweise, dass dafür ein Konfigurationsfehler mit den möglichen Ursachen angezeigt wird. Der virtuelle Computer wird möglicherweise aus Azure gelöscht oder die Endpunkte sind nicht richtig konfiguriert. Dies liegt daran, dass die MT-Konfiguration von Azure-Endpunkten erkannt wird, wenn das MT in Azure bereitgestellt wird. Dies gilt allerdings nicht für lokale MT, sodass der Fehler dort ignoriert werden kann. Für das Failback sollten dadurch keine Probleme auftreten.


## Initiieren der Rückkehr zum lokalen Schutz der virtuellen Computer

Vor dem Failback der virtuellen Computer zurück zur lokalen Bereitstellung muss zuerst der lokale Schutz der virtuellen Computer wiederhergestellt werden. Gehen Sie zum Schutz der virtuellen Computer einer Anwendung wie folgt vor:

### Hinweis zum temporären Laufwerk

Beim Failover eines virtuellen Computers zu Azure wird ein temporäres Laufwerk für die Auslagerungsdatei hinzugefügt. Hierbei handelt es sich um ein zusätzliches Laufwerk, das von dem virtuellen Computer, für den das Failover ausgeführt wurde, in der Regel nicht benötigt wird, da dieser unter Umständen bereits über ein dediziertes Laufwerk für die Auslagerungsdatei verfügt.

Bevor Sie mit der Umkehr des Schutzes der virtuellen Computer beginnen, müssen Sie das Laufwerk offline schalten, damit es nicht geschützt wird.

Gehen Sie hierzu wie folgt vor:

1.  Öffnen Sie die Computerverwaltung (entweder über die Systemsteuerung oder im Explorer durch einen Rechtsklick auf „Computer“ und anschließendes Klicken auf „Verwalten“).

2.  Klicken Sie auf die Speicherverwaltung, um die Datenträger anzuzeigen, die online und mit dem Computer verbunden sind.

3.  Wählen Sie den temporären Datenträger aus, der dem Computer zugeordnet ist, und schalten Sie ihn offline.

4.  Wenn er erfolgreich offline geschaltet wurde, können Sie den Schutz des virtuellen Computers umkehren.

### Schutzplan für virtuelle Computer

Sehen Sie sich im Azure-Portal den Zustand des virtuellen Computers an, und vergewissern Sie sich, dass ein Failover durchgeführt wurde.

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

Hinweis: Während des Failovers von Azure zurück zum lokalen Schutz ähnelt der virtuelle Azure-Computer einem physischen virtuellen Computer. Das lokale Failoverziel ist ein virtueller Computer.

1.  Starten von vContinuum auf Ihrem Computer

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

2.  Wählen Sie unter **Choose Application** die Option **P2V** aus.

3.  Klicken Sie auf **New Protection**.

4.  Im daraufhin angezeigten Fenster können Sie mit der Rückkehr zum lokalen Schutz der virtuellen Computer beginnen.

    a. Wählen Sie den passenden Betriebssystemtyp für die virtuellen Computer aus, für die Sie ein Failback ausführen möchten, und rufen Sie Details ab.

    b. Ermitteln Sie in den Details zum primären Server die virtuellen Computer, die Sie schützen möchten.

    c. Die virtuellen Computer werden nicht mit ihrem Anzeigenamen aus vCenter oder Azure, sondern mit ihrem Computerhostnamen angezeigt.

    d. Die virtuellen Computer werden unter dem vCenter-Hostnamen aufgeführt, unter dem sie sich vor dem Failover befunden haben.

    e. Wenn Sie die virtuellen Computer ermittelt haben, die Sie schützen möchten, wählen Sie sie nacheinander aus.

5.  Wenn Sie einen zu schützenden virtuellen Computer auswählen, für den bereits ein Failover zu Azure stattgefunden hat, erscheint ein Popupfenster mit zwei Einträgen für den virtuellen Computer. Der Grund: Beim CS sind zwei Instanzen des virtuellen Computers registriert. Der Eintrag für den lokalen virtuellen Computer muss entfernt werden, damit der richtige virtuelle Computer geschützt werden kann. Bei den Einträgen handelt es sich jeweils um den Hostnamen des Computers. Zur Ermittlung des Eintrags für den richtigen virtuellen Azure-Computer können Sie sich bei dem virtuellen Azure-Computer anmelden und zu „C:\\Programme (x86)\\Microsoft Azure Site Recovery\\Application Data\\...“ navigieren. Ermitteln Sie in der Datei „drscout.conf“ die Host-ID. Behalten Sie im Dialogfeld von vContinuum den Eintrag, dessen Host-ID Sie auf dem virtuellen Computer gefunden haben. Löschen Sie alle anderen Einträge.

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

6.  Um den richtigen virtuellen Computer auszuwählen, können Sie auf seine IP-Adresse verweisen. Der lokale IP-Adressbereich ist der lokale virtuelle Computer.
7.  Klicken Sie auf **Entfernen**, um den Eintrag zu löschen.

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

8.  Wechseln Sie zu vCenter, und beenden Sie den virtuellen Computer in vCenter.
9.  Anschließend können Sie die virtuellen Computer auch lokal löschen.
10.  Geben Sie anschließend den lokalen Masterzielserver an, mit dem Sie die virtuellen Computer schützen möchten.
11.  Dazu stellen Sie eine Verbindung mit dem vCenter her, das als Failbackziel fungieren soll.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

12.  Wählen Sie auf der Grundlage des Hosts, auf dem Sie die virtuellen Computer wiederherstellen möchten, den Masterzielserver aus.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

13.  Geben Sie die Replikationsoption für die einzelnen virtuellen Computer an.

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

14.  Wählen Sie dazu den wiederherstellungsseitigen **Datenspeicher** aus. Hierbei handelt es sich um den Datenspeicher, in dem die virtuellen Computer wiederhergestellt werden.

Für jeden virtuellen Computer müssen folgende Optionen angegeben werden:

**Option** | **Empfohlener Wert**
---|---
Process Server IP | Wählen Sie den Prozessserver aus, den Sie für Azure bereitgestellt haben.
Retention size in MB| 
Retention value | 1
Days or Hours | Days
Consistency Interval | 1
Select target datastore | Der wiederherstellungsseitig verfügbare Datenspeicher. Dieser Datenspeicher muss über genügend Speicherplatz verfügen und auch für den ESX-Host verfügbar sein, auf dem Sie den virtuellen Computer realisieren möchten.
15.  Anschließend können Sie die Eigenschaften konfigurieren, mit denen der virtuellen Computer nach einem Failover zum lokalen Standort versehen werden soll. Folgende Eigenschaften können konfiguriert werden:

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


**Eigenschaft** | **Konfiguration**
---|---
Network Configuration|Konfigurieren Sie für jede erkannte Netzwerkkarte die Failback-IP-Adresse für den virtuellen Computer. Wählen Sie die Netzwerkkarte aus, und klicken Sie auf **Ändern**, um die IP-Adresse anzugeben.
Hardware Configuration|Sie können die CPU und den Arbeitsspeicher für den virtuellen Computer angeben. Diese Einstellung kann auf alle virtuellen Computer angewendet werden, die Sie schützen möchten.
Display Name|Die korrekten Werte für CPU und Arbeitsspeicher finden Sie unter der Rollengröße des virtuellen IAAS-Computers. Hier können Sie die Anzahl der Kerne und den zugewiesenen Arbeitsspeicher einsehen.
Display Name|Nach einem Failover zurück zum lokalen Betrieb können Sie die virtuellen Computer umbenennen, damit sie wie im vCenter-Bestand angezeigt werden. Beachten Sie, dass standardmäßig der Hostname des virtuellen Computers angezeigt wird. Den Namen des virtuellen Computers finden Sie in der Liste mit den virtuellen Computern der Schutzgruppe.
NAT Configuration|Diese Option wird im Anschluss näher erläutert.

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **PS-NAT-Einstellungsoptionen**
>
> Zum Schutz der virtuellen Computer müssen zwei Kommunikationskanäle eingerichtet werden:
>
> Der erste Kanal wird zwischen dem virtuellen Computer und dem Prozessserver eingerichtet. Über diesen Kanal werden die Daten des virtuellen Computers gesammelt und an den PS gesendet. Der PS sendet die Daten dann an das Masterziel. Wenn sich der Prozessserver und der zu schützende virtuelle Computer im gleichen Azure-VNET befinden, werden die NAT-Einstellungen nicht benötigt. Wenn sich der PS und der zu schützende virtuelle Computer in unterschiedlichen VNETs befinden, müssen Sie die NAT-Einstellungen für den PS angeben und die erste Option aktivieren.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> Die öffentliche IP-Adresse des Prozessservers finden Sie in der PS-Bereitstellung in Azure.
>
> Der zweite Kanal wird zwischen dem Prozessserver und dem Masterziel (Master Target, MT) eingerichtet. Die Verwendung der NAT-Option hängt davon ab, ob Sie zwischen MT und PS eine VPN-basierte Verbindung oder einen internetbasierten Schutz verwenden. Wenn der PS über ein VPN mit dem MT kommuniziert, wählen Sie die Option nicht aus. Wenn das Masterziel über das Internet mit dem Prozessserver kommunizieren muss, geben Sie die NAT-Einstellungen für den PS an.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> Die öffentliche IP-Adresse des Prozessservers finden Sie in der PS-Bereitstellung in Azure.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  Wenn Sie die lokalen virtuellen Computer nicht wie in Schritt 5d beschrieben gelöscht haben und der Datenspeicher, der als Ziel für das Failback fungiert (siehe Schritt 7a), immer noch die alten VMDK-Dateien enthält, müssen Sie zudem dafür sorgen, dass die virtuellen Failbackcomputer an einem anderen Ort erstellt werden. Hierzu können Sie in den erweiterten Einstellungen unter **Folder Name Settings** einen alternativen Ordner für die Wiederherstellung angeben.

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

Die anderen Optionen der erweiterten Einstellungen müssen nicht geändert werden. Stellen Sie sicher, dass Sie die Ordnernameneinstellungen auf alle Server anwenden.

2.  Danach können Sie mit der abschließenden Phase des Schutzes beginnen. In dieser Phase muss eine Bereitschaftsüberprüfung durchgeführt werden, um sicherzustellen, dass die virtuellen Computer für die Rückkehr zum lokalen Schutz bereit sind.

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


3.  Der Schutz beginnt.

    a. Der Status des Schutzes wird in vContinuum angezeigt.

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

Wenn Sie den exakten Status der Paare anzeigen möchten, können Sie auch auf den virtuellen Computer klicken und den Status im Abschnitt zur Volumereplikation verfolgen.

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Vorbereiten des Failbackplans

Sie können einen Failbackplan mit vContinuum vorbereiten, damit für die Anwendung jederzeit ein Failover zum lokalen Schutz durchgeführt werden kann. Diese Wiederherstellungspläne sind den ASR-Wiederherstellungsplänen sehr ähnlich.

1.  Starten Sie vContinuum, und wählen Sie die Option **Manage plans** aus.

2.  Wählen Sie die Unteroption **Recover** aus.

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

3.  Eine Liste mit allen Plänen zum Schutz der virtuellen Computer wird angezeigt. Diese Pläne können Sie auch für die Wiederherstellung verwenden.

4.  Wählen Sie den Schutzplan und die virtuellen Computer aus, die Sie wiederherstellen möchten.

    a. Für jeden virtuellen Computer werden weitere Details zum virtuellen Quellcomputer, zum Ziel-ESX-Server, auf dem der virtuelle Computer wiederhergestellt wird, und zum virtuellen Quelldatenträger angezeigt.

5.  Klicken Sie auf **Next**, um den Wiederherstellungs-Assistenten zu starten.

6.  Wählen Sie die virtuellen Computer aus, die Sie wiederherstellen möchten.

    a. Sehen Sie sich die Liste mit allen virtuellen Computern an, die wiederhergestellt werden können.

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


7.  Führen Sie die Bereitschaftsprüfung aus. Daraufhin wird geprüft, ob die richtigen Parameter konfiguriert sind, um die neueste Tag-Wiederherstellung des virtuellen Computers zu ermöglichen. Klicken Sie auf „Next“, wenn alle Überprüfungen erfolgreich waren. Falls nicht, sehen Sie sich das Protokoll an, und beheben Sie die Fehler.

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Vergewissern Sie sich im Konfigurationsschritt für den virtuellen Computer, dass die Wiederherstellungseinstellungen korrekt sind. Sollten die Einstellungen für den virtuellen Computer nicht den erforderlichen Einstellungen entsprechen, können Sie die Einstellungen ändern. Da wir diesen Schritt bereits während des Schutzes durchgeführt haben, können Sie ihn dieses Mal ignorieren.

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9.  Überprüfen Sie die Liste mit den virtuellen Computern, die wiederhergestellt werden sollen.

    a. Geben Sie eine Wiederherstellungsreihenfolge für die virtuellen Computer an.

Hinweis: Die virtuellen Computer sind mit dem Computerhostnamen angegeben. Unter Umständen lässt sich der Computerhostname nicht ohne Weiteres dem virtuellen Computer zuordnen. In Azure IAAS können Sie das Dashboard für virtuelle Computer aufrufen und dort den Hostnamen des virtuellen Computers einsehen, um die Namen zuzuordnen.

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10.  Geben Sie den Namen des Wiederherstellungsplans an, und wählen Sie in den Wiederherstellungsoptionen die Option für eine spätere Wiederherstellung aus.

    a. Falls Sie die Wiederherstellung sofort durchführen möchten, können Sie in den Wiederherstellungsoptionen die Einstellung für die sofortige Wiederherstellung auswählen.

    b. Es wird empfohlen, die Wiederherstellung später durchzuführen, da die erste Replikation für den Schutz unter Umständen noch nicht abgeschlossen ist.

    c. Klicken Sie abschließend auf die Schaltfläche **Recover**, um abhängig von den gewählten Wiederherstellungsoptionen entweder den Plan zu speichern oder die Wiederherstellung zu initiieren.

11.  Sie sehen den Wiederherstellungsstatus und erfahren, ob der Plan erfolgreich gespeichert wurde.

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

12.  Wenn Sie sich für die spätere Wiederherstellung entschieden haben, werden Sie darüber informiert, dass der Plan erstellt wurde und Sie die Wiederherstellung später durchführen können.

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Wiederherstellen virtueller Computer

Nachdem der Plan erstellt wurde, können Sie die virtuellen Computer wiederherstellen. Dazu müssen Sie sich zuvor vergewissern, dass die Synchronisierung der virtuellen Computer abgeschlossen ist.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Wird als Replikationsstatus „OK“ angezeigt, ist der Schutz abgeschlossen, und der RPO-Schwellenwert wurde erreicht. Zur Überprüfung der Integrität des Replikationspaars können Sie die Eigenschaften des virtuellen Computers aufrufen und sich die Integrität der Replikation ansehen.

**Deaktivieren Sie die virtuellen Azure-Computer, bevor Sie die Wiederherstellung initiieren. Dadurch wird ein Split Brain-Syndrom vermieden und sichergestellt, dass Ihren Endkunden eine Kopie der Anwendung zur Verfügung steht. Fahren Sie erst mit den folgenden Schritten fort, wenn Sie die virtuellen Azure-Computer erfolgreich deaktiviert haben.**

Starten Sie den gespeicherten Plan, um mit der lokalen Wiederherstellung der virtuellen Computer zu beginnen.

1.  Überwachen Sie die Pläne in vContinuum.

2.  Der Assistent führt die Pläne auf, die bis jetzt ausgeführt wurden.

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

3.  Klicken Sie auf den Wiederherstellungsknoten, und wählen Sie den Plan aus, den Sie wiederherstellen möchten.

    a. Sie werden darüber informiert, dass der Plan noch nicht gestartet wurde.

4.  Klicken Sie auf **Start**, um mit der Wiederherstellung zu beginnen.

5.  Sie können die Wiederherstellung der virtuellen Computer verfolgen.


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

6. Nachdem die virtuellen Computer eingeschaltet wurden, können Sie über Ihr vCenter eine Verbindung herstellen.

## Wiederherstellen des Schutzes in Azure nach dem Failback

Nach Abschluss des Failbacks empfiehlt es sich unter Umständen, die virtuellen Computer wieder zu schützen. Gehen Sie zum Konfigurieren des Schutzes wie folgt vor:

1.  Vergewissern Sie sich, dass die virtuellen Computer lokal ordnungsgemäß funktionieren und dass die Anwendung Ihre Endkunden erreicht.

2.  Wählen Sie die virtuellen Computer im Azure Site Recovery-Portal aus, und löschen Sie sie.

    a. Deaktivieren Sie den Schutz der virtuellen Computer. Dadurch wird sichergestellt, dass die virtuellen Computer nicht mehr geschützt sind.

3.  Navigieren Sie zu den virtuellen Azure IAAS-Computern, und löschen Sie die virtuellen Computer, für die ein Failover ausgeführt wurde.

4.  Löschen Sie die alten virtuellen Computer bei vSphere. (Hierbei handelt es sich um die virtuellen Computer, für die zuvor ein Failover zu Azure ausgeführt wurde.)

5.  Wählen Sie im ASR-Portal aus, dass die virtuellen Computer, für die kürzlich ein Failover ausgeführt wurde, geschützt werden sollen.

6.  Sobald die virtuellen Computer geschützt sind, können Sie sie einem Wiederherstellungsplan hinzufügen, sodass sie kontinuierlich geschützt sind.


 

<!---HONumber=Oct15_HO1-->