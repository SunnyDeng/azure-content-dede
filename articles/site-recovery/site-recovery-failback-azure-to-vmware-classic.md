<properties 
   pageTitle="Failback von virtuellen VMware-Computern und physischen Servern zum lokalen Standort | Microsoft Azure"
   description="Hier erfahren Sie, wie nach einem Failover von VMware-VMs und physischen Servern zu Azure ein Failback zum lokalen Standort ausgeführt wird." 
   services="site-recovery" 
   documentationCenter="" 
   authors="rayne-wiselman" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="01/11/2015"
   ms.author="raynew"/>

# Failback von virtuellen VMware-Computern und physischen Servern zum lokalen Standort

> [AZURE.SELECTOR]
- [Enhanced](site-recovery-failback-azure-to-vmware-classic.md)
- [Legacy](site-recovery-failback-azure-to-vmware-classic-legacy.md)


In diesem Artikel wird beschrieben, wie Sie für virtuelle Azure-Computer ein Failback zum lokalen Standort durchführen. Befolgen Sie die Anweisungen in diesem Artikel, wenn Sie bereit sind, Ihre virtuellen VMware-Computer oder Ihre physischen Windows-/Linux-Server nach einem Failover vom lokalen Standort zu Azure (beschrieben in diesem [Tutorial](site-recovery-vmware-to-azure-classic.md)) wieder per Failback an den lokalen Standort zurückzuführen.



## Übersicht

Dieses Diagramm zeigt Aufbau und Ablauf des Failbacks in diesem Szenario.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Das Failback funktioniert wie folgt:

- Nach einem Failover zu Azure erfolgt die Rückführung zum lokalen Standort per Failback in mehreren Phasen:
	- **Phase 1**: Sie schützen die virtuellen Azure-Computer erneut. Dadurch beginnt deren Replikation zurück auf die virtuellen VMware-Computer am lokalen Standort. Beim erneuten Aktivieren des Schutzes wird der virtuelle Computer (Virtual Machine, VM) in eine Failback-Schutzgruppe verschoben, die beim ursprünglichen Erstellen der Failover-Schutzgruppe automatisch erstellt wurde. Wenn Sie Ihre Failover-Schutzgruppe einem Wiederherstellungsplan hinzugefügt haben, wurde auch die Failback-Schutzgruppe automatisch diesem Plan hinzugefügt. Beim Ausführen des erneuten Schutzes legen Sie die Planung für das Failback fest.
	- **Phase 2**: Nach der Replikation Ihrer virtuellen Azure-Computer zum lokalen Standort führen Sie ein Failover aus, um das Failback von Azure durchzuführen.
	- **Phase 3**: Nachdem Ihre Daten per Failback zurückgeführt wurden, schützen Sie die lokalen virtuellen Computer, zu denen Sie das Failback durchgeführt haben, erneut. Dadurch beginnt deren Replikation zu Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### Failback zum ursprünglichen oder zu einem anderen Standort

Wenn Sie ein Failover für einen virtuellen VMware-Computer durchgeführt haben, können Sie das Failback zur gleichen Quell-VM ausführen, sofern sie lokal noch vorhanden ist. In diesem Szenario werden beim Failback nur die geänderten Daten (Deltaänderungen) zurückgeführt. Beachten Sie Folgendes:

- Wenn Sie ein Failover für physische Server durchgeführt haben, erfolgt das Failback immer zu einem neuen virtuellen VMware-Computer.
- Wenn Sie ein Failback zum ursprünglichen virtuellen Computer durchführen, ist Folgendes erforderlich:
	- Wenn der virtuelle Computer von einem vCenter-Server verwaltet wird, muss der ESX-Host des Masterziels Zugriff auf den Datenspeicher des virtuellen Computers haben.
	- Wenn sich der virtuelle Computer auf einem ESX-Host befindet, aber nicht von vCenter verwaltet wird, muss sich die Festplatte des virtuellen Computers in einem Datenspeicher befinden, auf den der Host des Masterziels Zugriff hat.
	- Wenn sich Ihr virtueller Computer auf einem ESX-Host befindet und nicht vCenter verwendet, müssen Sie für den ESX-Host des Masterziels eine Ermittlung ausführen, bevor Sie den erneuten Schutz ausführen. Dasselbe gilt auch bei einem Failback für physische Server.
	- Als weitere Option können Sie den lokalen virtuellen Computer (sofern er noch vorhanden ist) vor dem Ausführen des Failbacks löschen. Beim Failback wird dann ein neuer virtueller Computer auf dem gleichen Host erstellt, der als Masterziel-ESX-Host fungiert.
	
- Wenn Sie das Failback zu einem anderen Speicherort durchführen, werden die Daten in dem gleichen Datenspeicher und in dem gleichen ESX-Host wiederhergestellt, der vom lokalen Masterzielserver verwendet wird.


## Voraussetzungen

- Sie benötigen eine VMware-Umgebung, um ein Failback für virtuelle VMware-Computer und physische Server durchführen zu können. Ein Failback auf einen physischen Server wird nicht unterstützt.
- Ein Failback ist nur möglich, wenn Sie beim ursprünglichen Einrichten des Schutzes ein Azure-Netzwerk erstellt haben. Für das Failback wird eine VPN-Verbindung oder eine ExpressRoute-Verbindung von dem Azure-Netzwerk, in dem sich die virtuellen Azure-Computer befinden, mit ihrem lokalen Standort benötigt.
- Wenn die virtuellen Computer, für die Sie das Failback ausführen möchten, von einem vCenter-Server verwaltet werden, müssen Sie sicherstellen, dass auf den vCenter-Servern die erforderlichen Berechtigungen für die Ermittlung von virtuellen Computern (VMs) vorhanden sind. [Weitere Informationen](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
- Wenn auf einem virtuellen Computer Momentaufnahmen vorhanden sind, tritt beim Ausführen des erneuten Schutzes ein Fehler auf. Sie können die Momentaufnahmen oder die Datenträger löschen. 
- Vor dem Ausführen des Failbacks müssen Sie einige Komponenten erstellen.
	- **Erstellen Sie einen Prozessserver in Azure**. Dies ist ein virtueller Azure-Computer, den Sie erstellen und während des Failbacks fortlaufend ausführen müssen. Sie können diesen virtuellen Computer nach Abschluss des Failbacks löschen.
	- **Erstellen Sie einen Masterzielserver**: Der Masterzielserver sendet und empfängt die Failbackdaten. Auf dem von Ihnen lokal erstellten Verwaltungsserver ist standardmäßig ein Masterzielserver installiert. Je nach Datenverkehrsvolumen beim Failback müssen Sie jedoch u. U. einen separaten Masterzielserver für das Failback erstellen.
	- Wenn Sie einen zusätzlichen Masterzielserver erstellen möchten, der unter Linux ausgeführt wird, müssen Sie vor dem Installieren des Masterzielservers die Linux-VM einrichten. Eine Beschreibung hierzu finden Sie weiter unten.

## Einrichten des Prozessservers in Azure

Sie müssen einen Prozessserver in Azure installieren, damit die virtuellen Azure-Computer die Daten zurück an einem lokalen Masterzielserver senden können.

1.  Wählen Sie im Site Recovery-Portal unter **Konfigurationsserver** die Option zum Hinzufügen eines neuen Prozessservers aus.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Geben Sie einen Prozessservernamen sowie einen Namen und ein Kennwort an, um eine Verbindung mit dem virtuellen Azure-Computer als Administrator herzustellen. Wählen Sie in **Konfigurationsserver** den lokalen Verwaltungsserver aus, und geben Sie das Azure-Netzwerk an, in dem der Prozessserver bereitgestellt werden soll. Dies muss das Netzwerk sein, in dem sich die virtuellen Azure-Computer befinden. Geben Sie eine eindeutige IP-Adresse aus dem ausgewählten Subnetz an, und starten Sie die Bereitstellung.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

	Es wird ein Auftrag zum Bereitstellen des Prozessservers ausgelöst.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

	Nachdem der Prozessserver in Azure bereitgestellt wurde, können Sie sich mit den angegebenen Anmeldeinformationen dort anmelden. Bei der ersten Anmeldung wird ein Prozessserver-Dialogfeld angezeigt. Geben Sie die IP-Adresse des lokalen Verwaltungsservers und die zugehörige Passphrase ein. Belassen Sie die Standardeinstellung für den Port bei „443“. Sie können auch den Standardport 9443 für die Datenreplikation beibehalten, sofern Sie diese Einstellung beim Einrichten des lokalen Verwaltungsservers nicht bewusst geändert haben.

	>[AZURE.NOTE] Der Server wird nicht unter **VM-Eigenschaften** angezeigt. Er wird nur auf der Registerkarte **Server** des Verwaltungsservers angezeigt, bei dem er registriert wurde. Es kann etwa 10–15 Minuten dauern, bis der Prozessserver angezeigt wird.


## Einrichten des lokalen Masterzielservers

Der Masterzielserver empfängt die Failbackdaten. Auf dem lokalen Verwaltungsserver wird automatisch ein Masterzielserver installiert. Wenn Sie jedoch große Datenmengen per Failback zurückführen, müssen Sie möglicherweise einen zusätzlichen Masterzielserver einrichten. Gehen Sie hierzu wie folgt vor:

>[AZURE.NOTE] Wenn Sie einen Masterzielserver unter Linux installieren möchten, befolgen Sie die Anweisungen im nächsten Abschnitt.

1. Wenn Sie den Masterzielserver unter Windows installieren, öffnen Sie auf dem virtuellen Computer, auf dem Sie den Masterzielserver installieren, die Seite „Schnellstart“. Laden Sie anschließend die Installationsdatei für den Azure Site Recovery-Assistenten für einheitliches Setup herunter.
2. Führen Sie das Setup aus, und wählen Sie in **Vorbereitung** die Option **Weitere Prozessserver zum horizontalen Skalieren der Bereitstellung hinzufügen** aus.
3. Beenden Sie den Assistenten genauso wie beim [Einrichten des Verwaltungsservers](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Geben Sie auf der Seite **Konfigurationsserverdetails** die IP-Adresse dieses Masterzielservers sowie eine Passphrase zum Zugreifen auf den virtuellen Computer an.

### Einrichten eines virtuellen Linux-Computers als Masterzielserver
Um den Verwaltungsserver einzurichten, auf dem der Masterzielserver als virtueller Linux-Computer ausgeführt wird, müssen Sie das Minimalbetriebssystem CentOS 6.6 installieren, die SCSI-IDs für jede SCSI-Festplatte abrufen, einige zusätzliche Pakete installieren und einige benutzerdefinierte Änderungen vornehmen.

#### Installieren von CentOS 6.6

1.	Installieren Sie das Minimalbetriebssystem CentOS 6.6 auf der Verwaltungsserver-VM. Behalten Sie das ISO-Image im DVD-Laufwerk, und starten Sie das System. Überspringen Sie den Medientest, wählen Sie als Sprache US-Englisch aus, wählen Sie **Basic Storage Devices** aus, vergewissern Sie sich, dass auf der Festplatte keine wichtigen Daten vorhanden sind, und klicken Sie auf **Yes**, wodurch alle Daten gelöscht werden. Geben Sie den Hostnamen des Verwaltungsservers ein, und wählen Sie die Netzwerkkarte des Servers aus. Wählen Sie im Dialog **Editing System** die Option **Connect automatically** aus, und fügen Sie eine statische IP-Adresse, ein Netzwerk und DNS-Einstellungen hinzu. Geben Sie eine Zeitzone und ein Stammkennwort zum Zugriff auf den Verwaltungsserver ein. 
2.	Wenn Sie nach dem gewünschten Installationstyp gefragt werden, wählen Sie für die Partition **Create Custom Layout** aus. Klicken Sie anschließend auf **Next**, wählen Sie **Free** aus, und klicken Sie auf „Create“. Erstellen Sie die Partitionen **/**, **/var/crash** und **/home** mit **FS Type:** **ext4**. Erstellen Sie die Swappartition als **FS Type: swap**.
3.	Wenn bereits vorhandene Geräte gefunden werden, wird eine Warnung angezeigt. Klicken Sie auf **Format**, um das Laufwerk mit den Partitionseinstellungen zu formatieren. Klicken Sie auf **Write change to disk**, um die Partitionsänderungen zu übernehmen.
4.	Wählen Sie **Install boot loader** > **Next**, um das Startladeprogramm in der Stammpartition zu installieren.
5.	Klicken Sie nach Abschluss der Installation auf **Reboot**.


#### Abrufen der SCSI-IDs

1. Rufen Sie nach der Installation die SCSI-IDs für alle SCSI-Festplatten im virtuellen Computer ab. Fahren Sie zu diesem Zweck die Verwaltungsserver-VM herunter, klicken Sie in VMware in den VM-Eigenschaften mit der rechten Maustaste auf den VM-Eintrag, und klicken Sie dann auf **Einstellungen bearbeiten** > **Optionen**.
2. Wählen Sie **Erweitert** > **Allgemeines Element**, und klicken Sie auf **Konfigurationsparameter**. Diese Option ist deaktiviert, wenn der virtuelle Computer ausgeführt wird. Um sie zu aktivieren, muss der virtuelle Computer heruntergefahren werden.
3. Wenn die Zeile **disk.EnableUUID** vorhanden ist, stellen Sie sicher, dass der Wert auf **True** festgelegt ist (die Groß-/Kleinschreibung wird beachtet). Wenn dies der Fall ist, können Sie den Vorgang abbrechen und den SCSI-Befehl in einem Gastbetriebssystem testen, nachdem es hochgefahren wurde. 
4.	Wenn die Zeile nicht vorhanden ist, klicken Sie auf **Zeile hinzufügen**, und fügen Sie sie mit dem Wert **True** hinzu. Verwenden Sie keine doppelten Anführungszeichen.

#### Installieren von zusätzlichen Paketen

Sie müssen einige zusätzliche Pakete herunterladen und installieren.

1.	Stellen Sie sicher, dass der Masterzielserver mit dem Internet verbunden ist.
2.	Führen Sie den folgenden Befehl aus, um 15 Pakete aus dem CentOS-Repository herunterzuladen und zu installieren: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.	Wenn Linux auf den zu schützenden Quellcomputern mit dem Dateisystem Reiser oder XFS für das Stamm- oder Startgerät ausgeführt wird, müssen Sie die diese zusätzlichen Pakete wie folgt herunterladen und installieren:

	- # cd /usr/local
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
	- # rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
	- # wget [http://mirror.centos.org/centos/6.6/os/x86\_64/Packages/xfsprogs-3.1.1-16.el6.x86\_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
	- # rpm –ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Anwenden von benutzerdefinierten Änderungen

Nachdem Sie die nach der Installation nötigen Schritte ausgeführt und die Pakete installiert haben, führen Sie folgende benutzerdefinierte Änderungen aus:

1.	Kopieren Sie die Binärdatei „RHEL 6-64 Unified Agent“ auf den virtuellen Computer. Führen Sie für die Binärdatei den folgenden Untar-Befehl aus: **tar –zxvf <file name>**
2.	Führen Sie den folgenden Befehl aus, um Berechtigungen zu erteilen: **# chmod 755 ./ApplyCustomChanges.sh**
3.	Führen Sie das folgende Skript aus: **# ./ApplyCustomChanges.sh**. Das Skript darf nur einmal ausgeführt werden. Starten Sie den Server neu, nachdem das Skript erfolgreich ausgeführt wurde.


## Ausführen des Failbacks

### Erneutes Schützen der virtuellen Azure-Computer

1.	Wählen Sie im Site Recovery-Portal auf der Registerkarte **Computer** den virtuellen Computer aus, für den das Failover durchgeführt wurde, und klicken Sie auf **Erneut schützen**.
2.	Wählen Sie bei **Masterzielserver** und **Prozessserver** den lokalen Masterzielserver und den Azure-VM-Prozessserver aus.
3.	Wählen Sie das Konto aus, das Sie für die Verbindung mit dem virtuellen Computer konfiguriert haben.
4.	Wählen Sie die Failbackversion der Schutzgruppe aus. Beispiel: Wenn der virtuelle Computer in der Schutzgruppe „PG\_1“ geschützt ist, müssen Sie „PG1\_Failback“ auswählen.
5.	Wenn die Wiederherstellung an einem anderen Speicherort erfolgen soll, wählen Sie das Aufbewahrungslaufwerk und den Datenspeicher aus, wie sie für den Masterzielserver konfiguriert wurden. Beim Ausführen des Failbacks zum lokalen Standort verwenden die virtuellen VMware-Computer im Failback-Schutzplan den gleichen Datenspeicher wie der Masterzielserver. Wenn Sie den virtuellen Azure-Replikatcomputer auf dem gleichen lokalen virtuellen Computer wiederherstellen möchten, muss sich der lokale virtuelle Computer bereits im selben Datenspeicher befinden wie der Masterzielserver. Wenn lokal kein virtueller Computer vorhanden ist, wird er beim Ausführen des erneuten Schutzes erstellt.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.	Nachdem Sie auf **OK** geklickt haben, um den erneuten Schutz zu starten, beginnt ein Auftrag mit der Replikation des virtuellen Computers von Azure zum lokalen Standort. Sie können den Fortschritt auf der Registerkarte **Aufträge** nachverfolgen.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### Ausführen eines Failovers zum lokalen Standort

Nach dem Ausführen des erneuten Schutzes wird der virtuelle Computer zur Failbackversion seiner Schutzgruppe verschoben und automatisch dem Wiederherstellungsplan hinzugefügt, den Sie für das Failover zu Azure genutzt haben (sofern ein solcher vorhanden ist).

1.	Wählen Sie auf der Seite **Wiederherstellungspläne** den Wiederherstellungsplan aus, der die Failbackgruppe enthält, und klicken Sie auf **Failover** > **Ungeplantes Failover**.
2.	Überprüfen Sie unter **Failover bestätigen** die Failoverrichtung (zu Azure), und wählen Sie den Wiederherstellungspunkt aus, der für das Failover verwendet werden soll (den aktuellsten). Wenn Sie beim Konfigurieren der Replikationseigenschaften die Option für **Multi-VM** aktiviert haben, können Sie zum letzten anwendungs- oder absturzkonsistenten Wiederherstellungspunkt wiederherstellen. Klicken Sie auf das Häkchen, um das Failover zu starten.
3.	Während des Failovers fährt Site Recovery die virtuellen Azure-Computer herunter. Vergewissern Sie sich, dass das Failback wie erwartet abgeschlossen wurde, und überprüfen Sie anschließend, ob die virtuellen Azure-Computer wie erwartet heruntergefahren wurden.

### Erneutes Schützen des lokalen Standorts

Nach Abschluss des Failbacks befinden sich Ihre Daten wieder am lokalen Standort, aber sie sind nicht geschützt. Führen Sie daher folgende Schritte aus, um die Replikation zu Azure erneut zu starten:

1.	Wählen Sie im Site Recovery-Portal auf der Registerkarte **Computer** die virtuellen Computer aus, für die das Failback durchgeführt wurde, und klicken Sie auf **Erneut schützen**. 
2.	Vergewissern Sie sich, dass die Replikation zu Azure erwartungsgemäß funktioniert. Wenn dies der Fall ist, können Sie die (derzeit nicht ausgeführten) virtuellen Azure-Computer löschen, für die das Failback ausgeführt wurde.


## Ausführen eines Failbacks mit ExpressRoute

Ein Failback kann über eine VPN-Verbindung oder über Azure ExpressRoute ausgeführt werden. Wenn Sie ExpressRoute verwenden möchten, beachten Sie Folgendes:

- ExpressRoute muss in dem virtuellen Azure-Netzwerk eingerichtet werden, zu dem das Failover der Quellcomputer durchgeführt wird und in dem sich die virtuellen Azure-Computer nach dem Failover befinden.
- Die Daten werden zu einem Azure-Speicherkonto auf einem öffentlichen Endpunkt repliziert. Sie müssen ein öffentliches Peering in ExpressRoute einrichten, bei dem das Zieldatencenter für die Site Recovery-Replikation ExpressRoute verwendet.

<!---HONumber=AcomDC_0224_2016-->