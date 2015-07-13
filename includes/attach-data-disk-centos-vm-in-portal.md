1. Klicken Sie im Azure-[Verwaltungsportal](http://manage.windowsazure.com) auf **Virtuelle Computer**, und wählen anschließend den gerade von Ihnen erstellten virtuellen Computer aus (**testlinuxvm**).

2. Klicken Sie in der Befehlsleiste auf **Einbinden** und anschließend auf **Leeren Datenträger einbinden**.

	Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.


3. Der **Name des virtuellen Computers**, der **Speicherort** und der **Dateiname** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein.

	![Leeren Datenträger einbinden][Image2]

	**Hinweis:** Alle Datenträger werden aus einer VHD-Datei im Azure-Speicher erstellt. Sie können einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

4. Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

5. Klicken Sie auf den Namen des virtuellen Computers, um das Dashboard anzuzeigen; so können Sie überprüfen, ob der Datenträger erfolgreich an den virtuellen Computer angefügt wurde. Der von Ihnen angeschlossene Datenträger wird in der Tabelle **Datenträger** aufgeführt.

	Wenn Sie einen Datenträger anfügen, kann er erst verwendet werden, nachdem Sie sich angemeldet und die Einrichtung abgeschlossen haben.

##Herstellen einer Verbindung mit dem virtuellen Computer mithilfe von SSH oder PuTTY und Abschließen der Einrichtung
Melden Sie sich beim virtuellen Computer an, um die Einrichtung des Datenträgers abzuschließen und ihn zum Speichern von Daten zu nutzen.

1. Stellen Sie nach der Bereitstellung des virtuellen Computers eine Verbindung mithilfe von SSH oder PuTTY her, und melden Sie sich (wie in den obigen Schritten beschrieben) als **newuser** an.	


2. Geben Sie in das SSH- oder PuTTY-Fenster folgenden Befehl ein, und geben Sie dann das Kontokennwort ein:

	`$ sudo grep SCSI /var/log/messages`

	Die ID des letzten hinzugefügten Datenträgers in den angezeigten Meldungen (in diesem Beispiel **sdc**).

	![GREP][Image4]


3. Geben Sie in das SSH- oder PuTTY-Fenster folgenden Befehl ein, um den Datenträger **/dev/sdc** zu partitionieren:

	`$ sudo fdisk /dev/sdc`


4. Geben Sie **n** ein, um eine neue Partition zu erstellen.

	![FDISK][Image5]


5. Geben Sie **p** ein, um die Partition als primäre Partition festzulegen, geben Sie **1** ein, um die Partition als erste Partition festzulegen, und drücken Sie dann die Eingabetaste, um den Standardwert (1) für den Zylinder zu übernehmen.

	![FDISK][Image6]


6. Geben Sie **p** ein, um die Details zum Datenträger anzuzeigen, der gerade partitioniert wird.

	![FDISK][Image7]


7. Geben Sie **w** ein, um die Einstellungen für den Datenträger zu schreiben.

	![FDISK][Image8]


8. Formatieren Sie den neuen Datenträger mit dem Befehl **mkfs**:

	`$ sudo mkfs -t ext4 /dev/sdc1`

9. Als Nächstes muss ein Verzeichnis zum Einbinden des neuen Dateisystems zur Verfügung stehen. Geben Sie z. B. den folgenden Befehl ein, um ein Verzeichnis zum Anfügen des Laufwerks zu erstellen, und geben Sie anschließend das Kontokennwort ein:

	`sudo mkdir /datadrive`


10. Geben Sie den folgenden Befehl ein, um das Laufwerk einzubinden:

	`sudo mount /dev/sdc1 /datadrive`

	Der Datenträger kann nun als **/datadrive** verwendet werden.


11. Fügen Sie das neue Laufwerk zu /etc/fstab hinzu:

	Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es zur Datei /etc/fstab hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique IDentifier) in /etc/fstab zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (d. h. auf /dev/sdc1) zu verweisen. Sie können das Hilfsprogramm **blkid** verwenden, um den UUID des neuen Laufwerks herauszufinden:
	
		`sudo -i blkid`

	Die Ausgabe sieht etwa wie folgt aus:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]blkid erfordert nicht in allen Fällen einen sudo-Zugang, ist aber auf manchen Distributionen leichter mit `sudo -i` auszuführen, wenn /sbin oder /usr/sbin sich nicht in Ihrem `$PATH` befinden.

	**Achtung:** Eine fehlerhafte Bearbeitung der Datei /etc/fstab kann dazu führen, dass das System sich nicht mehr starten lässt. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der Distribution Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

	Geben Sie in einem Texteditor die Informationen zum neuen Dateisystem am Ende der Datei /etc/fstab ein. In diesem Beispiel verwenden wir den UUID-Wert für das neue Gerät **/dev/sdc1**, das in den vorhergehenden Schritten erstellt wurde, und den Bereitstellungspunkt **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	Wenn zusätzliche Datenlaufwerke oder Partitionen erstellt werden, müssen Sie diese ebenfalls einzeln in "/etc/fstab" einfügen.

	Sie können nun testen, ob das Dateisystem korrekt eingebunden wurde, indem Sie die Einbindung aufheben und wiederherstellen. Verwenden Sie dafür den zuvor erstellten Beispielbereitstellungspunkt `/datadrive`:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Wenn der zweite Befehl zu einem Fehler führt, prüfen Sie die Datei /etc/fstab auf korrekte Syntax.


	>[AZURE.NOTE]Wenn Sie später einen Datenträger entfernen, ohne "fstab" zu bearbeiteten, kann der Start des virtuellen Computers fehlschlagen. Für den Fall, dass dieses Problem häufiger auftritt, bieten die meisten Distributionen die fstab-Optionen `nofail` und/oder `nobootwait`. Diese erlauben einen Systemstart auch dann, wenn der Datenträger nicht vorhanden ist. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Verteilung.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=62-->