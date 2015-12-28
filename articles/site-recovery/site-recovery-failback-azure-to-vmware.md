<properties 
   pageTitle="Failback von virtuellen VMware-Computern und physischen Servern aus Azure an VMware | Microsoft Azure" 
   description="Dieser Artikel beschreibt das Failback von virtuellen VMware-Computern, die in Azure mit Azure Site Recovery repliziert wurden." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="storage-backup-recovery" 
   ms.date="12/14/2015"
   ms.author="ruturajd@microsoft.com"/>

# Failback von virtuellen VMware-Computern und physischen Servern aus Azure an VMware mit Azure Site Recovery

## Übersicht

Dieses Dokument beschreibt, wie ein Failback von virtuellen VMware-Computern und physischen Windows- oder Linux-Servern aus Azure auf Ihren lokalen Standort durchgeführt wird.

Befolgen Sie zum Einrichten von Replikation und Failover für dieses Szenario die Anweisungen in [diesem Artikel](site-recovery-vmware-to-azure.md). Nach einem erfolgreichen Failover von virtuellen VMware-Computern oder physischen Servern an Azure mit Site Recovery sind die Computer auf der Registerkarte „virtuelle Azure-Computer“ verfügbar.

>[AZURE.NOTE]Sie können das Failback von virtuellen VMware-Computern und physischen Windows- oder Linux-Servern aus Azure an virtuelle VMware-Computer auf den lokalen, primären Standort durchführen. Wenn Sie das Failback auf einen physischen Computer durchführen, wird ein Failover an Azure den Computer in einen virtuellen Azure-Computer umwandeln, und das Failback an VMware in einen virtuellen VMware-Computer.

Dieses Diagramm veranschaulicht das Failover- und Failbackszenario. Die blauen Linien stehen für die beim Failover verwendeten Verbindungen. Die roten Linien stehen für die beim Failback verwendeten Verbindungen. Bei den mit Pfeilen versehenen Linien handelt es sich um Internetverbindungen.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Schritt 1: Installieren von vContinuum (lokal)

Sie müssen einen lokalen vContinuum-Server installieren und ihn auf den Konfigurationsserver zeigen lassen.

1.  [Laden Sie VContinuum herunter](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Laden Sie nach dem Download die aktualisierte [vContinuum-Version](http://go.microsoft.com/fwlink/?LinkID=533813) herunter.
3.  Führen Sie das Setup für die neueste Version aus, um vContinuum zu installieren. Klicken Sie auf der Seite **Willkommen** auf **Weiter**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Geben Sie auf der ersten Seite des Assistenten die IP-Adresse und den Port des CX-Servers an. Wählen Sie **HTTPS verwenden** aus.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Suchen Sie auf der Registerkarte **Dashboard** der Konfigurationsserver-VM in Azure die IP-Adresse des Konfigurationsservers. ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Suchen Sie auf der Registerkarte **Endpunkte** der Konfigurationsserver-VM in Azure den öffentlichen HTTPS-Port des Konfigurationsservers.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Geben Sie auf der Seite **Passphrasedetails für den Konfigurationsserver** die Passphrase an, die Sie notiert haben, als Sie den Konfigurationsserver registriert haben. Falls Sie die Passphrase vergessen haben, können Sie unter **C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase** auf der Konfigurationsserver-VM nachsehen.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Geben Sie auf der Seite **Zielspeicherort auswählen** an, wohin Sie den vContinuum-Server installieren möchten und klicken Sie auf **Installieren**.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Nach Abschluss der Installation können Sie vContinuum starten. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Schritt 2: Installieren eines Prozessservers in Azure 

Sie müssen einen Prozessserver in Azure installieren, damit die virtuellen Computer in Azure die Daten zurück an einem lokalen Masterzielserver senden können.

1.  Klicken Sie auf der Seite **Konfigurationsserver** in Azure auf die Option zum Hinzufügen eines neuen Prozessservers.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Geben Sie einen Prozessservernamen sowie einen Namen und ein Kennwort an, um eine Verbindung mit dem virtuellen Computer als Administrator herzustellen. Wählen Sie den Konfigurationsserver aus, bei dem Sie den Prozessserver registrieren möchten. Dies sollte der gleiche Server sein, den Sie für den Schutz und das Failover Ihrer virtuellen Computer verwenden.
3.  Geben Sie das Azure-Netzwerk an, in dem der Prozessserver bereitgestellt werden soll. Dabei sollte es sich um dasselbe Netzwerk wie beim Konfigurationsserver handeln. Geben Sie eine eindeutige IP-Adresse aus dem ausgewählten Subnetz an und starten Sie die Bereitstellung.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Es wird ein Auftrag ausgelöst, um den Prozessserver bereitzustellen.

	![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Nachdem der Prozessserver in Azure bereitgestellt wurde, können Sie sich mit den angegebenen Anmeldeinformationen bei dem Server anmelden. Registrieren Sie den Prozessserver auf die gleiche Weise, wie Sie den lokalen Prozessserver registriert haben.

	![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE]Während des Failbacks registrierte Server werden nicht in den VM-Eigenschaften in der Site Revocery angezeigt. Diese Server werden nur auf der Registerkarte **Server** des Konfigurationsservers angezeigt, für den sie registriert sind. Es dauert ungefähr 10 bis 15 Minuten, bis sie dort angezeigt werden.


## Schritt 3: Installieren eines lokalen Masterzielservers

Abhängig vom Betriebssystem Ihrer virtuellen Quellcomputer muss lokal entweder ein Linux- oder ein Windows-basierter Masterzielserver installiert werden.

### Bereitstellen eines Windows-Masterzielservers

Ein Windows-Masterzielserver ist bereits im vContinuum-Setup enthalten. Bei der Installation von vContinuum wird auf dem Computer auch ein Masterzielserver bereitgestellt und beim Konfigurationsserver registriert.

1.  Erstellen Sie zunächst auf dem ESX-Host, auf dem Sie die virtuellen Computer von Azure wiederherstellen möchten, einen leeren lokalen Computer.

2.  Stellen Sie sicher, dass mit dem virtuellen Computer mindestens zwei Datenträger verknüpft sind: einer für das Betriebssystem und einer als Aufbewahrungslaufwerk.

3.  Installieren Sie das Betriebssystem.

4.  Installieren Sie vContinuum auf dem Server. Dies schließt außerdem die Installation des Masterzielservers ab.

### Bereitstellen eines Linux-Masterzielservers

1.  Erstellen Sie zunächst auf dem ESX-Host, auf dem Sie die virtuellen Computer von Azure wiederherstellen möchten, einen leeren lokalen Computer.

2.  Stellen Sie sicher, dass mit dem virtuellen Computer mindestens zwei Datenträger verknüpft sind: einer für das Betriebssystem und einer als Aufbewahrungslaufwerk.

3.  Installieren Sie das Linux-Betriebssystem. Das Linux-basierte Masterzielsystem sollte für den Stamm- oder Aufbewahrungspeicherplatz nicht die LVM verwenden. Ein Linux-Masterzielserver ist standardmäßig so konfiguriert, dass LVM-Partitionen/Datenträger nicht erkannt werden.
4.  Folgende Partitionen können erstellt werden:

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Führen Sie nach der Installation des Betriebssystems die folgenden Schritte aus, bevor Sie mit der Masterziel-Installation beginnen:


#### Schritte nach der Installation des Betriebssystems

Aktivieren Sie den Parameter „disk.EnableUUID = TRUE“, um die SCSI-IDs für die einzelnen SCSI-Festplatten eines virtuellen Linux-Computers zu erhalten.

1. Fahren Sie den virtuellen Computer herunter.
2. Klicken Sie im linken Bereich mit der rechten Maustaste auf den VM-Eintrag und wählen Sie **Edit Settings**.
3. Klicken Sie auf die Registerkarte **Options**. Wählen Sie **Advanced > General iteml** > **Configuration Parameters**. Die Option **Configuration Parameters** ist nur verfügbar, wenn der Computer heruntergefahren ist.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Prüft, ob eine Zeile mit **disk.EnableUUID** vorhanden ist. Falls sie vorhanden und der Wert auf **False** festgelegt ist, legen Sie ihn auf **True** fest (ohne Beachtung der Groß-/Kleinschreibung). Falls die Zeile vorhanden und der Wert auf „True“ festgelegt ist, klicken Sie auf **Cancel** und testen Sie den SCSI-Befehl im Gastbetriebssystem, nachdem es hochgefahren wurde. Ist die Zeile nicht vorhanden, klicken Sie auf **Add Row**.
5. Fügen Sie in der Spalte **Name** die Zeichenfolge „disk.EnableUUID“ ein. Legen Sie den Wert auf „TRUE“ fest. Fügen Sie die oben angegebenen Werte nicht mit Anführungszeichen ein.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Herunterladen und Installieren zusätzlicher Pakete

HINWEIS: Vergewissern Sie sich vor dem Herunterladen und Installieren zusätzlicher Pakete, dass das System über eine Internetverbindung verfügt.

\#yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Mit diesem Befehl werden diese 15 Pakete aus dem CentOS 6.6-Repository heruntergeladen und installiert:

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

\# cd /usr/local

\# wget 
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget 
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm 
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget 
<http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Anwenden benutzerdefinierter Konfigurationsänderungen

Stellen Sie vor dem Übernehmen dieser Änderungen sicher, dass Sie den vorherigen Abschnitt abgeschlossen haben, und gehen Sie dann folgendermaßen vor:


1. Kopieren Sie die Binärdatei für „RHEL 6-64 Unified Agent“ in das neu erstellte Betriebssystem.

2. Führen Sie für die Binärdatei den folgenden Untar-Befehl aus:

**tar -zxvf <Dateiname>**
3. Führen Sie den folgenden Befehl aus, um Berechtigungen zu erteilen:

 # **chmod 755 ./ApplyCustomChanges.sh**

4. Führen Sie das folgende Skript aus:

 **# ./ApplyCustomChanges.sh**. Führen Sie das Skript nur einmal auf dem Server aus. Starten Sie den Server neu, nachdem das Skript ausgeführt wurde.



### Installieren des Linux-Servers


1. Zunächst müssen Sie die Installationsdatei [herunterladen](http://go.microsoft.com/fwlink/?LinkID=529757).
2. Kopieren Sie die Datei mithilfe eines beliebigen SFTP-Clienthilfsprogramms auf den virtuellen Linux-Computer für das Masterziel. Alternativ können Sie sich auch bei dem virtuellen Linux-Computer für das Masterziel anmelden und das Installationspaket mittels „wget“ unter dem angegebenen Link herunterladen.
3. Melden Sie sich bei dem virtuellen Computer für den Linux-Masterzielserver mit einem beliebigen SSH-Client an.
4. Wenn Sie über eine VPN-Verbindung mit dem Azure-Netzwerk verbunden sind, in dem Sie den Linux-Masterzielserver bereitgestellt haben, verwenden Sie die interne IP-Adresse für den Server, den Sie auf der Registerkarte **Dashboard** für den virtuellen Computer finden können, und den Port 22, um über Secure Shell eine Verbindung mit dem Linux-Masterzielserver herzustellen.
5. Wenn Sie über eine öffentliche Internetverbindung eine Verbindung mit dem Linux-Masterzielserver herstellen, verwenden Sie die öffentliche virtuelle IP-Adresse des Linux-Masterzielservers (zu finden auf der Registerkarte **Dashboard** für virtuelle Computer) und den für SSH erstellten öffentlichen Endpunkt, um sich bei dem Linux-Server anzumelden.
6. Extrahieren Sie die Dateien aus dem gezippten tar-Archiv des Installationsprogramms für den Linux-Masterzielserver, indem Sie den Befehl *“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*”* aus dem Verzeichnis ausführen, das die Datei des Installationsprogramms enthält.

	![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Wenn Sie die Dateien des Installationsprogramms in ein anderes Verzeichnis extrahiert haben, wechseln Sie zum Verzeichnis, in das der Inhalt des tar-Archivs extrahiert wurde. Führen Sie an diesem Verzeichnispfad „sudo ./install.sh“ aus.

	![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Wenn Sie zum Auswählen einer primären Rolle aufgefordert werden, wählen Sie **2 (Masterziel)**. Lassen Sie die anderen Optionen der interaktiven Installation unverändert.
9. Warten Sie, bis die Installation fortgesetzt wird und die Oberfläche für die Hostkonfiguration erscheint. Die Hostkonfiguration für den Linux-Masterzielserver wird über ein Befehlszeilenprogramm vorgenommen. Lassen Sie die Fenstergröße des SSH-Clienthilfsprogramms unverändert. Wählen Sie mithilfe der Pfeiltasten die Option **Global** aus und drücken Sie die EINGABETASTE.

	![](./media/site-recovery-failback-azure-to-vmware/image18.png)

	![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Geben Sie im Feld **IP** die interne IP-Adresse des Konfigurationsservers ein (diese finden Sie in derRegisterkarte **Dashboard** der Konfigurationsserver-VM) und drücken Sie die EINGABETASTE. Geben Sie unter **Port** den Wert **22** ein und drücken Sie die EINGABETASTE.
11.  Lassen Sie unter **HTTPS verwenden** die Option **Ja** ausgewählt und drücken Sie die EINGABETASTE.
12.  Geben Sie die für den Konfigurationsserver generierte Passphrase ein. Wenn Sie auf einem Windows-Computer einen PUTTY-Client für eine SSH-Verbindung mit dem virtuellen Linux-Computer verwenden, können Sie den Inhalt durch Drücken von UMSCHALT+EINFG aus der Zwischenablage einfügen. Kopieren Sie die Passphrase durch Drücken von STRG+C in die lokale Zwischenablage, und fügen Sie sie durch Drücken von UMSCHALT+EINFG ein. Drücken Sie die EINGABETASTE.
13.  Navigieren Sie mit der rechten Pfeiltaste zu „Quit“ und drücken Sie dann die EINGABETASTE. Warten Sie, bis die Installation abgeschlossen ist.

	![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Falls der Linux-Masterzielserver noch nicht beim Konfigurationsserver registriert wurde, können Sie unter „/usr/local/ASR/Vx/bin/hostconfigcli“ das Hilfsprogramm für die Hostkonfiguration ausführen. (Hierzu müssen Sie zunächst Zugriffsberechtigungen für dieses Verzeichnis festlegen, indem Sie „chmod“ als Administrator ausführen.)


#### Überprüfen Sie die Masterzielregistrierung

Sie können im Azure Site Recovery-Tresor unter **Konfigurationsserver** > **Serverdetails** überprüfen, ob der Masterzielserver erfolgreich am Konfigurationsserver registriert wurde.

>[AZURE.NOTE]Wenn Sie nach dem Registrieren des Masterzielservers den Konfigurationsfehler erhalten, dass der virtuelle Computer möglicherweise aus Azure gelöscht wurde oder dass die Endpunkte nicht richtig konfiguriert sind, hat dies folgenden Grund: Obwohl die Masterzielkonfiguration von den Azure-Endpunkten entdeckt wird, wenn das Masterziel in Azure bereitgestellt wird, gilt dies nicht für einen lokalen Masterzielserver. Das Failback wird dadurch nicht beeinträchtigt und Sie können diese Fehler ignorieren.



## Schritt 4: Schützen der virtuellen Computer auf dem lokalen Standort

Sie müssen die virtuellen Computer auf dem lokalen Standort schützen, bevor Sie ein Failback durchführen.

### Voraussetzungen

Beim Failover eines virtuellen Computers zu Azure wird ein temporäres Laufwerk für die Auslagerungsdatei hinzugefügt. Hierbei handelt es sich um ein zusätzliches Laufwerk, das von dem virtuellen Computer, für den das Failover ausgeführt wurde, in der Regel nicht benötigt wird, da dieser unter Umständen bereits über ein dediziertes Laufwerk für die Auslagerungsdatei verfügt.

Bevor Sie mit der Umkehr des Schutzes der virtuellen Computer beginnen, müssen Sie das Laufwerk offline schalten, damit es nicht geschützt wird.

Gehen Sie hierzu wie folgt vor:

1.  Öffnen Sie die Computerverwaltung und klicken Sie auf die Speicherverwaltung, um die Datenträger anzuzeigen, die online und mit dem Computer verbunden sind.
2.  Wählen Sie den temporären Datenträger aus, der dem Computer zugeordnet ist, und schalten Sie ihn offline. 

### Schützen der virtuellen Computer

1. Prüfen Sie im Azure-Portal den Zustand des virtuellen Computers und vergewissern Sie sich, dass ein Failover durchgeführt wurde.

	![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Starten Sie vContinuum auf Ihrem Computer.

	![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Klicken Sie auf **New Protection** und wählen Sie den Betriebssystemtyp aus.

4.  Wählen Sie im neuen Fenster, das sich öffnet, den **Betriebssystemtyp** aus und klicken dann auf **Get Details**, um die Details für den virtuellen Computer abzurufen, für den das Failback durchgeführt werden soll. Ermitteln Sie unter **Primary server details** die virtuellen Computer, die Sie schützen möchten. Virtuelle Computer werden unter dem vCenter-Hostnamen aufgeführt, auf dem sie sich vor dem Failover befanden.
5.  Wenn Sie einen zu schützenden virtuellen Computer auswählen, für den bereits ein Failover zu Azure stattgefunden hat, erscheint ein Popupfenster mit zwei Einträgen für den virtuellen Computer. Der Grund: Beim Konfigurationsserver sind zwei Instanzen des virtuellen Computers registriert. Der Eintrag für den lokalen virtuellen Computer muss entfernt werden, damit der richtige virtuelle Computer geschützt werden kann. Zur Ermittlung des Eintrags für den richtigen virtuellen Azure-Computer können Sie sich bei dem virtuellen Azure-Computer anmelden und zu „C:\\Programme (x86)\\Microsoft Azure Site Recovery\\Application Data\\...“ navigieren. Ermitteln Sie in der Datei „drscout.conf“ die Host-ID. Behalten Sie im Dialogfeld von vContinuum den Eintrag bei, dessen Host-ID Sie auf dem virtuellen Computer gefunden haben. Löschen Sie alle anderen Einträge. Um den richtigen virtuellen Computer auszuwählen, können Sie auf seine IP-Adresse verweisen. Der lokale IP-Adressbereich ist der lokale virtuelle Computer.

	![](./media/site-recovery-failback-azure-to-vmware/image22.png)

	![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Beenden Sie auf dem vCenter-Server den virtuellen Computer. Sie können die virtuellen Computer auch lokal löschen.
7. Geben Sie anschließend den lokalen Masterzielserver an, mit dem Sie die virtuellen Computer schützen möchten. Dazu stellen Sie eine Verbindung mit dem vCenter-Server her, der als Failbackziel fungieren soll.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Wählen Sie auf der Grundlage des Hosts, auf dem Sie die virtuellen Computer wiederherstellen möchten, den Masterzielserver aus.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Geben Sie die Replikationsoption für die einzelnen virtuellen Computer an. Wählen Sie dazu den wiederherstellungsseitigen Datenspeicher aus, in dem die virtuellen Computer wiederhergestellt werden. Die Tabelle fasst die verschiedenen Optionen zusammen, die Sie für jeden virtuellen Computer bereitstellen müssen.

	![](./media/site-recovery-failback-azure-to-vmware/image25.png)

	**Option** | **Empfohlener Wert**
	---|---
	IP-Adresse des Prozessservers | Wählen Sie den in Azure bereitgestellten Prozessserver.
	Retention size in MB| 
	Retention value | 1
	Days or Hours | Days
	Consistency Interval | 1
	Select target datastore | Der wiederherstellungsseitig verfügbare Datenspeicher. Dieser Datenspeicher muss über genügend Speicherplatz verfügen und auch für den ESX-Host verfügbar sein, auf dem Sie den virtuellen Computer wiederherstellen möchten.


10. Konfigurieren Sie die Eigenschaften, mit denen der virtuellen Computer nach einem Failover zum lokalen Standort versehen werden soll. Diese Eigenschaften werden in der folgenden Tabelle zusammengefasst.

	![](./media/site-recovery-failback-azure-to-vmware/image26.png)

	**Eigenschaft** | **Konfiguration**
	---|---
	Network Configuration| Wählen Sie jeden Netzwerkadapter aus, der erkannt wird, und klicken Sie auf **Ändern**, um die Failback-IP-Adresse für den virtuellen Computer zu konfigurieren. 
	Hardware Configuration| Geben Sie die CPU und den Arbeitsspeicher für den virtuellen Computer an. Einstellungen können auf alle virtuellen Computer angewendet werden, die Sie schützen möchten. Die korrekten Werte für CPU und Arbeitsspeicher finden Sie unter der Rollengröße des virtuellen IAAS-Computers. Hier können Sie die Anzahl der Kerne und den zugewiesenen Arbeitsspeicher einsehen.
	Anzeigename | Nach dem lokalen Failback können Sie die virtuellen Computer umbenennen, wenn sie im vCenter-Bestand angezeigt werden. Der Standardname ist der Hostname des virtuellen Computers. Den Namen des virtuellen Computers finden Sie in der Liste mit den virtuellen Computern der Schutzgruppe.
	NAT Configuration | Diese Option wird im Anschluss näher erläutert.

	![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### Konfigurieren von NAT-Einstellungen

1. Zum Schutz der virtuellen Computer müssen zwei Kommunikationskanäle eingerichtet werden: Der erste Kanal wird zwischen dem virtuellen Computer und dem Prozessserver eingerichtet. Dieser Kanal erfasst die Daten aus dem virtuellen Computer und sendet sie an einen Prozessserver, der sie anschließend an den Masterzielserver sendet. Wenn sich der Prozessserver und der zu schützende virtuelle Computer im gleichen virtuellen Azure-Netzwerk befinden, werden die NAT-Einstellungen nicht benötigt. Geben Sie andernfalls die NAT-Einstellungen an. Sehen Sie sich die öffentliche IP-Adresse des Prozessservers in Azure an. 

	![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Der zweite Kanal wird zwischen dem Prozessserver und dem Masterzielserver eingerichtet. Die Verwendung der NAT-Option hängt davon ab, ob Sie eine VPN-basierte Verbindung nutzen oder über das Internet kommunizieren. Wählen Sie keine NAT, wenn Sie eine VPN-basierte Verbindung verwenden (aber nur dann, wenn eine Internetverbindung besteht).

	![](./media/site-recovery-failback-azure-to-vmware/image29.png)

	![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Wenn Sie die lokalen virtuellen Computer nicht wie beschrieben gelöscht haben und der Datenspeicher, der als Ziel für das Failback fungiert, immer noch die alten VMDK-Dateien enthält, müssen Sie dafür sorgen, dass die virtuellen Failbackcomputer an einem anderen Ort erstellt werden. Wählen Sie hierzu die **erweiterten** Einstellungen aus und geben Sie unter **Folder Name Settings** einen alternativen Ordner für die Wiederherstellung an. Behalten Sie bei den anderen Optionen die Standardeinstellungen bei. Wenden Sie die Ordnernameneinstellungen auf alle Server an.

	![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Führen Sie eine Bereitschaftsüberprüfung durch, um sicherzustellen, dass die virtuellen Computer für die Rückkehr zum lokalen Schutz bereit sind.

	![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Warten Sie, bis die Überprüfung abgeschlossen ist. Wenn sie auf allen virtuellen Computern erfolgreich durchgeführt wurde, können Sie einen Namen für den Schutzplan angeben. Klicken Sie dann auf **Protect**, um zu beginnen.

	![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Sie können den Fortschritt in vContinuum überwachen.

	![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Nachdem der Schritt **Activating Protection Plan** abgeschlossen ist, können Sie den VM-Schutz im Site Recovery-Portal überwachen.

	![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Sie sehen den genauen Status, indem Sie auf den virtuellen Computer klicken und den Fortschritt überwachen.

	![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Vorbereiten des Failbackplans

Sie können einen Failbackplan mit vContinuum vorbereiten, damit für die Anwendung jederzeit ein Failback zum lokalen Standort durchgeführt werden kann. Diese Wiederherstellungspläne sind denen in Site Recovery sehr ähnlich.

1.  Starten Sie vContinuum und wählen Sie **Manage plans** > **Recover**. Sie können die Liste mit allen Plänen sehen, die für das Failover von virtuellen Computern verwendet wurden. Dieselben Pläne können Sie auch für die Wiederherstellung verwenden.

	![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Wählen Sie den Schutzplan und alle virtuellen Computer aus, die Sie darin wiederherstellen möchten. Wenn Sie jeden virtuellen Computer einzeln auswählen, sehen Sie weitere Details wie den Ziel-ESX-Server und den virtuellen Quelldatenträger. Klicken Sie auf **Weiter**, um den Assistenten zum Wiederherstellen zu starten, und wählen Sie die virtuellen Computer aus, die Sie wiederherstellen möchten.

	![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Sie können die Wiederherstellung mithilfe mehrerer Optionen durchführen. Wir empfehlen allerdings, die Option **Neuester Tag** zu verwenden und **Für alle virtuelle Computer übernehmen** auszuwählen, damit ganz sicher die neusten Daten aus dem virtuellen Computer verwendet werden.
4. Führen Sie die Bereitschaftsprüfung aus. Dadurch wird überprüft, ob die richtigen Parameter konfiguriert sind, um die VM-Wiederherstellung zu aktivieren. Klicken Sie auf **Weiter**, wenn alle Überprüfungen erfolgreich waren. Falls dies nicht der Fall ist, überprüfen Sie das Protokoll und beheben Sie die Fehler.

	![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Vergewissern Sie sich, dass unter **VM-Konfiguration** die Wiederherstellungseinstellungen korrekt sind. Bei Bedarf können Sie die VM-Einstellungen ändern.

	![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Überprüfen Sie die Liste mit den virtuellen Computern, die wiederhergestellt werden sollen, und geben Sie einen Wiederherstellungsauftrag an. Beachten Sie, dass virtuelle Computer mit dem Hostnamen des Computers aufgeführt sind. Unter Umständen lässt sich der Computerhostname nicht ohne Weiteres dem virtuellen Computer zuordnen. Um die Namen zuzuordnen, navigieren Sie in Azure zum **Dashboard** der virtuellen Computer und prüfen Sie den Hostnamen des virtuellen Computers.

	![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Geben Sie einen Namen für den Plan an und wählen Sie **spätere Wiederherstellung**. Die spätere Wiederherstellung wird empfohlen, da der anfängliche Schutz möglicherweise nicht vollständig abgeschlossen ist.
11. Klicken Sie auf **Wiederherstellen**, um den Plan zu speichern oder die Wiederherstellung auszulösen, wenn Sie die sofortige und nicht die spätere Wiederherstellung ausgewählt haben. Sie können den Wiederherstellungsstatus überprüfen, um festzustellen, ob der Plan gespeichert wurde.

	![](./media/site-recovery-failback-azure-to-vmware/image42.png)

	![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Wiederherstellen virtueller Computer

Nachdem der Plan erstellt wurde, können Sie die virtuellen Computer wiederherstellen. Dazu müssen Sie sich zuvor vergewissern, dass die Synchronisierung der virtuellen Computer abgeschlossen ist. Wird als Replikationsstatus „OK“ angezeigt, ist der Schutz abgeschlossen, und der RPO-Schwellenwert wurde erreicht. Sie können den Zustand in den Eigenschaften des virtuellen Computers überprüfen.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Deaktivieren Sie die virtuellen Azure-Computer, bevor Sie die Wiederherstellung initiieren. Dadurch wird ein Split-Brain-Syndrom vermieden und sichergestellt, dass Benutzer nur auf eine Kopie der Anwendung zugreifen.


1.  Starten Sie den gespeicherten Plan. Wählen Sie in vContinuum **Pläne überwachen**. Dadurch werden alle Pläne angezeigt, die ausgeführt wurden.

	![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Wählen Sie unter **Wiederherstellung** den Plan und klicken Sie auf **Starten**. Sie können die Wiederherstellung überwachen. Nach Aktivierung der virtuellen Computer können Sie über vCenter eine Verbindung dazu herstellen.

	![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## Wiederherstellen des Schutzes in Azure nach dem Failback

Nach Abschluss des Failbacks empfiehlt es sich unter Umständen, die virtuellen Computer wieder zu schützen. Gehen Sie hierzu wie folgt vor:

1.  Vergewissern Sie sich, dass die virtuellen Computer lokal ordnungsgemäß funktionieren und dass Anwendungen erreichbar sind.
2.  Wählen Sie die virtuellen Computer im Azure Site Recovery-Portal aus und löschen Sie sie. Deaktivieren Sie den Schutz der virtuellen Computer. Dadurch wird sichergestellt, dass die virtuellen Computer nicht mehr geschützt sind.
3.  Löschen Sie in Azure die virtuellen Azure-Computer, für die ein Failover ausgeführt wurde.
4.  Löschen Sie den alten virtuellen Computer bei vSphere. Hierbei handelt es sich um die virtuellen Computer, für die zuvor ein Failover zu Azure ausgeführt wurde.
5.  Schützen Sie im Site Recovery-Portal die virtuellen Computer, für die vor kurzem ein Failover ausgeführt wurde. Nachdem sie geschützt sind, können Sie diese zu einem Wiederherstellungsplan hinzufügen.
 
## Nächste Schritte

[Erfahren Sie mehr über](site-recovery-vmware-to-azure.md) die Replikation virtueller VMware-Computer zu Azure.

 

<!---HONumber=AcomDC_1217_2015-->