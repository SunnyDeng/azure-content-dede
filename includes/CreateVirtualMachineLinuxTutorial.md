<!---
Don't use this file. It's deprecated and will be removed. Instead use, virtual-machines-Linux-tutorial-log-on-attach-disk.md
-->

## <a id="logon"></a>Anmelden bei einem virtuellen Computer nach dessen Erstellung ##

Sie können den SSH-Client verwenden, um die Einstellungen des virtuellen Computers und der Anwendungen zu verwalten, die auf dem Computer ausgeführt werden. Hierzu müssen Sie einen SSH-Client auf dem Computer installieren, über den Sie auf den virtuellen Computer zugreifen möchten. Es gibt eine Vielzahl an SSH-Client-Programmen, die Sie verwenden können. Hier einige mögliche Programme:

- Wenn Sie einen Computer mit einem Windows-Betriebssystem verwenden, können Sie beispielsweise einen SSH-Client wie PuTTY verwenden. Weitere Informationen erhalten Sie unter [PuTTY Download](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) \(in englischer Sprache\).
- Wenn Sie einen Computer mit einem Linux-Betriebssystem verwenden, können Sie beispielsweise einen SSH-Client wie OpenSSH verwenden. Weitere Informationen finden Sie unter [OpenSSH](http://www.openssh.org/).

Dieses Tutorial zeigt Ihnen, wie Sie mithilfe des PuTTY-Programms auf den virtuellen Computer zugreifen.

1. Navigieren Sie im Verwaltungsportal zum **Hostnamen** und den **Portinformationen**. Sie finden die erforderlichen Informationen im Dashboard des virtuellen Computers. Klicken Sie in der **Schnelleinsicht** des Dashboards auf den Namen des virtuellen Computers, und navigieren Sie zu den **SSH-Details**.

	![Rufen Sie die SSH-Details auf.](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2. Öffnen Sie das PuTTY-Programm.

3. Geben Sie den **Hostnamen** und die **Portinformationen** aus dem Dashboard ein, und klicken Sie dann auf **Öffnen**.

	![Geben Sie den Hostnamen und die Portinformationen ein.](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4. Melden Sie sich mit dem Konto "NeuerBenutzer1" an, das Sie beim Erstellen des virtuellen Computers angegeben haben.

	![Melden Sie sich bei dem neuen virtuellen Computer an.](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)

	Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.


## <a id="attachdisk"> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer ##

Möglicherweise muss Ihre Anwendung Daten speichern. Schließen Sie hierzu einen Datenträger an den virtuellen Computer an, den Sie zuvor erstellt haben. Am einfachsten ist es, einen leeren Datenträge an den virtuellen Computer anzuschließen.

**Hinweis: Datenträger und Ressourcendatenträger**  
Datenträger befinden sich im Azure-Speicher und können zur dauerhaften Speicherung von Dateien und Anwendungsdaten verwendet werden.

An jeden erstellen virtuellen Computer ist auch ein temporärer lokaler *Ressourcendatenträger* angeschlossen. Da die Daten auf einem Ressourcendatenträger bei Neustarts verloren gehen können, werden Ressourcendatenträger oft von Anwendungen und Prozessen zur vorübergehenden \(temporären\) Datenspeicherung auf dem virtuellen Computer verwendet. Außerdem dienen sie zur Speicherung von Auslagerungsdateien für das Betriebssystem.

Auf Linux wird der Ressourcendatenträger in der Regel vom Azure Linux Agent verwaltet und automatisch in **/mnt/resource** \(oder **/mnt** auf Ubuntu-Abbildern\) eingebunden. Beachten Sie, dass der Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Andererseits kann der Datenträger unter Linux vom Kernel den Namen `/dev/sdc` erhalten und die Benutzer müssen diese Ressource partitionieren, formatieren und bereitstellen. Weitere Informationen erhalten Sie im [Azure Linux Agent User Guide](http://www.windowsazure.com/manage/linux/how-to-guides/linux-agent-guide/) \(Benutzerhandbuch für Azure Linux, in englischer Sprache\).



1. Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM1** aus, den Sie zuvor erstellt haben.

3. Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf **Leeren Datenträger anschließen**.
	
	Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.

	![Definieren Sie die Datenträgerdetails.](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4. Der **Name des virtuellen Computers**, der **Speicherort** und der **Dateiname** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein.

	**Hinweis:** Alle Datenträger werden aus einer VHD-Datei im Azure-Speicher erstellt. Sie können einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch automatisch erzeugt.

5. Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

6. Sie können überprüfen, ob der Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde, indem Sie sich das Dashboard anzeigen lassen. Klicken Sie auf den Namen des virtuellen Computers, um das Dashboard anzuzeigen.

	Die Anzahl der Datenträger für den virtuellen Computer beträgt jetzt 2, und der Datenträger, den Sie angeschlossen haben, wird in der Tabelle **Disks** aufgeführt.

	![Datenträger erfolgreich angeschlossen](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)


Der gerade von Ihnen an den virtuellen Computer angeschlossene Datenträger ist nach dem Hinzufügen offline und nicht initialisiert. Sie müssen sich beim Computer anmelden und den Datenträger initialisieren, damit Sie diesen zum Speichern von Daten verwenden können.

1. Stellen Sie eine Verbindung zum virtuellen Computer her, indem Sie die oben unter **Anmelden bei einem virtuellen Computer nach dessen Erstellung** aufgeführten Schritte durchführen.


2. Geben Sie in das SSH-Fenster folgenden Befehl ein, und geben Sie dann das Kontokennwort ein:

	`sudo grep SCSI /var/log/messages`

	In den angezeigten Meldungen können Sie den Bezeichner des letzten hinzugefügten Datenträgers finden.

	![Bezeichnen Sie den Datenträger.](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)


3. Geben Sie im SSH-Fenster den folgen Befehl ein, um ein neues Gerät zu erstellen, und geben Sie anschließend das Kennwort für das Konto ein:

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]In diesem Beispiel müssen Sie eventuell auf manchen Distributionen `sudo -i` verwenden, wenn /sbin oder /usr/sbin sich nicht in Ihrem `$PATH` befinden.


4. Geben Sie **n** ein, um eine neue Partition zu erstellen.

	![Neues Gerät erstellen](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)


5. Geben Sie **p** ein, um die Partition zur primären zu machen, geben Sie **1** ein, um sie zur ersten Partition zu machen, und drücken Sie die Eingabetaste, um den Standardwert für den Zylinder zu übernehmen.

	![Partition erstellen](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)


6. Geben Sie **p** ein, um die Details zum Datenträger anzuzeigen, der gerade partitioniert wird.

	![Informationen zum Datenträger auflisten](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)


7. Geben Sie **w** ein, um die Einstellungen für den Datenträger zu schreiben.

	![Datenträgeränderungen schreiben](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)


8. Sie müssen das Dateisystem auf der neuen Partition erstellen. Geben Sie z. B. den folgenden Befehl ein, um das Dateisystem zu erstellen, und geben Sie anschließend das Kontokennwort ein:

	`sudo mkfs -t ext4 /dev/sdc1`

	![Dateisystem erstellen](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)

	>[AZURE.NOTE]Bitte beachten Sie, dass auf SUSE Linux Enterprise 11-Systemen nur schreibgeschützter Zugriff für ext4-Dateisysteme möglich ist. Für diese Systeme wird empfohlen, das neue Dateisystem als ext3 statt als ext4 zu formatieren.


9. Als Nächstes muss ein Verzeichnis zum Einbinden des neuen Dateisystems zur Verfügung stehen. Geben Sie z. B. den folgenden Befehl ein, um ein Verzeichnis zum Anfügen des Laufwerks zu erstellen, und geben Sie anschließend das Kontokennwort ein:

	`sudo mkdir /datadrive`


10. Geben Sie den folgenden Befehl ein, um das Laufwerk einzubinden:

	`sudo mount /dev/sdc1 /datadrive`

	Der Datenträger kann nun als **/datadrive** verwendet werden.


11. Fügen Sie das neue Laufwerk zu /etc/fstab hinzu:

	Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es zur Datei /etc/fstab hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID \(Universally Unique IDentifier\) in /etc/fstab zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen \(d. h. auf /dev/sdc1\) zu verweisen. Sie können das Hilfsprogramm **blkid** verwenden, um den UUID des neuen Laufwerks herauszufinden:
	
		`sudo -i blkid`

	Die Ausgabe sieht etwa wie folgt aus:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]blkid erfordert nicht in allen Fällen einen sudo-Zugang, ist aber auf manchen Distributionen leichter mit `sudo -i` auszuführen, wenn /sbin oder /usr/sbin sich nicht in Ihrem `$PATH` befinden.

	**Achtung:** Eine fehlerhafte Bearbeitung der Datei /etc/fstab kann dazu führen, dass das System sich nicht mehr starten lässt. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der Distribution Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

	Geben Sie in einem Texteditor die Informationen zum neuen Dateisystem am Ende der Datei /etc/fstab ein. In diesem Beispiel verwenden wir den UUID-Wert für das neue Gerät **/dev/sdc1**, das in den vorhergehenden Schritten erstellt wurde, und den Bereitstellungspunkt **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	In auf SUSE Linux basierenden Systemen müssen Sie eventuell ein etwas unterschiedliches Format verwenden:

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	Wenn zusätzliche Datenlaufwerke oder Partitionen erstellt werden, müssen Sie diese ebenfalls einzeln in /etc/fstab einfügen.

	Sie können nun testen, ob das Dateisystem korrekt eingebunden wurde, indem Sie die Einbindung aufheben und wiederherstellen. Verwenden Sie dafür den zuvor erstellten Beispielbereitstellungspunkt `/datadrive`:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Wenn der zweite Befehl zu einem Fehler führt, prüfen Sie die Datei /etc/fstab auf korrekte Syntax.


	>[AZURE.NOTE] Wenn Sie später einen Datenträger entfernen, ohne fstab zu bearbeiteten, kann der Start des virtuellen Computers fehlschlagen. Für den Fall, dass dieses Problem häufiger auftritt, bieten die meisten Distributionen die fstab-Optionen `nofail` und/oder `nobootwait`, die einen Systemstart auch dann erlauben, wenn der Datenträger nicht vorhanden ist. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution.



<!--HONumber=52-->
