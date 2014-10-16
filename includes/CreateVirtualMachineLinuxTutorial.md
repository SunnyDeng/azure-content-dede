# Erstellen eines virtuellen Linux-Computers

Das Erstellen eines virtuellen Linux-Computers ist einfach, wenn Sie die Bildergalerie im Azure Verwaltungsportal verwenden. In diesem Leitfaden wird davon ausgegangen, dass Sie bisher keine Erfahrung mit der Verwendung von Azure haben. Sie können einen virtuellen Linux-Computer in der Cloud erstellen, darauf zugreifen und ihn anpassen.

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][].

Sie erhalten Informationen zu folgenden Themen:

-   [Informationen zu virtuellen Computern in Azure][]
-   [Erstellen eines virtuellen Computers][]
-   [Anmelden bei einem virtuellen Computer nach dessen Erstellung][]
-   [Anschließen eines Datenträgers an den neuen virtuellen Computer][]

**Hinweis**: In diesem Tutorial wird ein virtueller Computer erstellt, der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, müssen Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke][].

## <span id="virtualmachine"></span> </a>Informationen zu virtuellen Computern in Azure

Ein virtueller Computer in Azure ist ein Server in der Cloud, den Sie steuern und verwalten können. Nachdem Sie einen virtuellen Computer in Azure erstellt haben, können Sie ihn jederzeit löschen und neu erstellen, und Sie können auf den virtuellen Computer wie auf einen Server in Ihrem Büro zugreifen. Zum Erstellen eines virtuellen Computers werden Dateien für virtuelle Festplatten (Virtual Hard Disk File, VHD) verwendet. Die folgenden Typen von VHDs werden verwendet:

-   **Abbild** – Eine VHD, die als Vorlage zum Erstellen eines neuen virtuellen Computers verwendet wird. Ein Abbild ist eine Vorlage, da es nicht über spezifische Einstellungen verfügt wie ein ausgeführter virtueller Computer. Beispiele für diese Einstellungen sind Computername und Benutzerkontoeinstellungen. Wenn Sie einen virtuellen Computer mithilfe eines Abbilds erstellen, wird automatisch eine Betriebssystemfestplatte für den neuen virtuellen Computer erstellt.
-   **Festplatte** – Eine Festplatte ist eine VHD, die Sie als ausgeführte Version eines Betriebssystems starten und einbinden können. Sobald ein Abbild bereitgestellt wurde, wird es zu einer Festplatte. Es wird immer eine Festplatte erstellt, wenn Sie ein Abbild zum Erstellen eines virtuellen Computers verwenden. Jede VHD, die an virtualisierte Hardware angeschlossen ist und die im Rahmen eines Service ausgeführt wird, ist eine Festplatte.

Folgende Optionen sind verfügbar, um virtuelle Computer mithilfe von Abbildern zu erstellen:

-   Erstellen Sie einen virtuellen Computer unter Verwendung eines Abbilds, das in der Bildergalerie des Azure Verwaltungsportals bereitgestellt wurde.
-   Erstellen Sie eine VHD-Datei, die ein Abbild zu Azure enthält, und laden Sie diese hoch. Erstellen Sie dann einen virtuellen Computer mithilfe des Abbilds. Weitere Informationen zum Erstellen und Hochladen eines benutzerdefinierten Abbilds finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte mit Linux-Betriebssystem][].

Jeder virtuelle Computer befindet sich in einem Clouddienst, entweder alleine oder zusammen mit anderen virtuellen Computern. Sie können virtuelle Computer im selben Clouddienst platzieren, damit die virtuellen Computer miteinander kommunizieren können, und um einen Lastenausgleich zwischen den virtuellen Computern zu ermöglichen sowie hohe Verfügbarkeit der Computer sicherzustellen. Weitere Informationen zu Clouddiensten und virtuellen Computern finden Sie im Abschnitt über Ausführungsmodelle unter [Einführung in Azure][].

## <span id="custommachine"></span> </a>Erstellen eines virtuellen Computers

Sie verwenden die Methode **Aus Galerie**, um einen benutzerdefinierten virtuellen Computer im Verwaltungsportal zu erstellen. Diese Methode bietet Ihnen bei der Erstellung mehr Optionen für die Konfiguration des virtuellen Computers, zum Beispiel verbundene Ressourcen, DNS-Name und Netzwerkkonnektivität bei Bedarf.

1.  Melden Sie sich im Azure [Verwaltungsportal][] an.
    Klicken Sie in der Befehlsleiste auf **Neu**.

2.  Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.

3.  Wählen Sie unter **Abbild auswählen** ein Abbild aus einer der Listen aus. (Die verfügbaren Abbilder können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.) Klicken Sie auf den Pfeil, um fortzufahren.

4.  Wenn mehrere Versionen des Images verfügbar sind, wählen Sie unter **Version Release Date** die Version aus, die Sie verwenden möchten.

5.  Geben Sie unter **Virtual Machine Name** den Namen ein, den Sie verwenden möchten. Geben Sie für diesen virtuellen Computer **MyTestVM1** ein.

6.  Wählen Sie unter **Größe** die Größe für den virtuellen Computer aus. Die Größe, die Sie auswählen, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung erforderlich sind. Wählen Sie für diesen virtuellen Computer die kleinste verfügbare Größe.

7.  Geben Sie unter **Neuer Benutzername** den Namen des Kontos ein, das Sie zur Administration des virtuellen Computers verwenden. Sie können nicht "root" für den Benutzernamen verwenden. Geben Sie für diesen virtuellen Computer **NeuerBenutzer1** ein.

8.  Wählen Sie unter **Authentication** **Kennwort angeben**. Geben Sie dann die erforderlichen Informationen ein, und klicken Sie auf den Pfeil, um fortzufahren.

9.  Sie können virtuelle Computer zusammen im Clouddienst platzieren. Aber in diesem Tutorial erstellen Sie nur einen einzigen virtuellen Computer. Wählen Sie hierzu **Neuen Clouddienst erstellen** aus.

10. Geben Sie unter **DNS-Name des Clouddiensts** einen Namen ein, der zwischen drei und 24 Kleinbuchstaben und Ziffern enthält. Dieser Name wird zu einem Teil des URI, mit dessen Hilfe der virtuelle Computer über den Clouddienst kontaktiert wird. Geben Sie für diesen virtuellen Computer **MeinService1** ein.

11. Wählen Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** den Standort des virtuellen Computers aus.

12. Sie können ein Speicherkonto auswählen, in dem die VHD-Datei gespeichert wird. In diesem Tutorial verwenden wir die Standardeinstellung **Automatisch generiertes Speicherkonto verwenden**.

13. Wählen Sie außerdem unter **Verfügbarkeitsgruppe** für dieses Tutorial die Standardeinstellung **Keine**. Klicken Sie auf das Kontrollkästchen, um den virtuellen Computer zu erstellen, und klicken Sie dann auf den Pfeil, um fortzufahren.

14. Entscheiden sie unter **VM Agent**, ob der VM Agent installiert werden soll. Dieser Agent stellt die Umgebung bereit, in der Sie Erweiterungen installieren können, die Ihnen bei der Interaktion mit dem virtuellen Computer helfen können. Nähere Informationen finden Sie unter [Verwalten von Erweiterungen][].

15. Prüfen Sie unter **Endpunkte** den automatisch erstellten Endpunkt, um Secure Shell-Verbindungen (SSH) zu dem virtuellen Computer zuzulassen. (Endpunkte ermöglichen Ressourcen im Internet oder anderen virtuellen Netzwerken die Kommunikation mit einem virtuellen Computer.) Sie können jetzt weitere Endpunkte hinzufügen oder diese später erstellen. Anweisungen dazu, wie Sie sie später erstellen, finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer][].

Nachdem der virtuelle Computer und der Clouddienst erstellt wurden, wird der neue virtuelle Computer im Verwaltungsportal unter **Virtuelle Computer** und der Clouddienst unter **Cloud Services** aufgeführt. Sowohl der virtuelle Computer als auch der Clouddienst werden automatisch gestartet.

## <span id="logon"></span> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

Sie können den SSH-Client verwenden, um die Einstellungen des virtuellen Computers und der Anwendungen zu verwalten, die auf dem Computer ausgeführt werden. Hierzu müssen Sie einen SSH-Client auf dem Computer installieren, über den Sie auf den virtuellen Computer zugreifen möchten. Es gibt eine Vielzahl an SSH-Client-Programmen, die Sie verwenden können. Hier einige mögliche Programme:

-   Wenn Sie einen Computer mit einem Windows-Betriebssystem verwenden, können Sie beispielsweise einen SSH-Client wie PuTTY verwenden. Weitere Informationen erhalten Sie unter [PuTTY Download][] (in englischer Sprache).
-   Wenn Sie einen Computer mit einem Linux-Betriebssystem verwenden, können Sie beispielsweise einen SSH-Client wie OpenSSH verwenden. Weitere Informationen finden Sie unter [OpenSSH][].

Dieses Tutorial zeigt Ihnen, wie Sie mithilfe des PuTTY-Programms auf den virtuellen Computer zugreifen.

1.  Navigieren Sie im Verwaltungsportal zum **Hostnamen** und den **Portinformationen**. Sie finden die erforderlichen Informationen im Dashboard des virtuellen Computers. Klicken Sie in der **Schnelleinsicht** des Dashboards auf den Namen des virtuellen Computers, und navigieren Sie zu den **SSH-Details**.

    ![Rufen Sie die SSH-Details auf.][]

2.  Öffnen Sie das PuTTY-Programm.

3.  Geben Sie den **Hostnamen** und die **Portinformationen** aus dem Dashboard ein, und klicken Sie dann auf **Öffnen**.

    ![Geben Sie den Hostnamen und die Portinformationen ein.][]

4.  Melden Sie sich mit dem Konto "NeuerBenutzer1" an, das Sie beim Erstellen des virtuellen Computers angegeben haben.

    ![Melden Sie sich bei dem neuen virtuellen Computer an.][]

    Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

## <span id="attachdisk"></span> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer

Möglicherweise muss Ihre Anwendung Daten speichern. Schließen Sie hierzu einen Datenträger an den virtuellen Computer an, den Sie zuvor erstellt haben. Am einfachsten ist es, einen leeren Datenträge an den virtuellen Computer anzuschließen.

**Hinweis: Datenträger im Vergleich zu Ressourcendatenträgern**
 Datenträger auf Azure Storage können zum dauerhaften Speichern von Dateien und Anwendungsdaten verwendet werden.

An jeden erstellen virtuellen Computer ist auch ein temporärer lokaler *Ressourcendatenträger* angeschlossen. Da die Daten auf einem Ressourcendatenträger bei Neustarts verloren gehen können, werden Ressourcendatenträger oft von Anwendungen und Prozessen zur vorübergehenden (temporären) Datenspeicherung auf dem virtuellen Computer verwendet. Außerdem dienen sie zur Speicherung von Auslagerungsdateien für das Betriebssystem.

Auf Linux wird der Ressourcendatenträger in der Regel vom Azure Linux Agent verwaltet und automatisch in **/mnt/resource** (oder **/mnt** auf Ubuntu-Abbildern) eingebunden. Beachten Sie, dass der Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Andererseits kann der Datenträger unter Linux vom Kernel den Namen `/dev/sdc` erhalten, und die Benutzer müssen diese Ressource partitionieren, formatieren und bereitstellen. Weitere Informationen erhalten Sie im [Azure Linux Agent User Guide][] (Benutzerhandbuch für Azure Linux, in englischer Sprache).

1.  Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM1** aus, den Sie zuvor erstellt haben.

3.  Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf **Leeren Datenträger anschließen**.

    Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.

    ![Definieren Sie die Datenträgerdetails.][]

4.  Der **Name des virtuellen Computers**, der **Speicherort** und der **Dateiname** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein.

    **Hinweis:** Alle Datenträger werden aus einer VHD-Datei im Azure-Speicher erstellt. Sie können einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch automatisch erzeugt.

5.  Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

6.  Sie können überprüfen, ob der Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde, indem Sie sich das Dashboard anzeigen lassen. Klicken Sie auf den Namen des virtuellen Computers, um das Dashboard anzuzeigen.

    Die Anzahl der Datenträger für den virtuellen Computer beträgt jetzt 2, und der Datenträger, den Sie angeschlossen haben, wird in der Tabelle **Disks** aufgeführt.

    ![Datenträger erfolgreich angeschlossen][]

Der gerade von Ihnen an den virtuellen Computer angeschlossene Datenträger ist nach dem Hinzufügen offline und nicht initialisiert. Sie müssen sich beim Computer anmelden und den Datenträger initialisieren, damit Sie diesen zum Speichern von Daten verwenden können.

1.  Stellen Sie eine Verbindung zum virtuellen Computer her, indem Sie die oben unter **Anmelden bei einem virtuellen Computer nach dessen Erstellung** aufgeführten Schritte durchführen.

2.  Geben Sie in das SSH-Fenster folgenden Befehl ein, und geben Sie dann das Kontokennwort ein:

    `sudo grep SCSI /var/log/messages`

    In den angezeigten Meldungen können Sie den Bezeichner des letzten hinzugefügten Datenträgers finden.

    ![Bezeichnen Sie den Datenträger.][]

3.  Geben Sie im SSH-Fenster den folgen Befehl ein, um ein neues Gerät zu erstellen, und geben Sie anschließend das Kennwort für das Konto ein:

    `sudo fdisk /dev/sdc`

    > [WACOM.NOTE] In diesem Beispiel müssen Sie eventuell auf manchen Distributionen `sudo -i` verwenden, wenn /sbin oder /usr/sbin sich nicht in Ihrem `$PATH` befinden.

4.  Geben Sie **n** ein, um eine neue Partition zu erstellen.

    ![Neues Gerät erstellen][]

5.  Geben Sie **p** ein, um die Partition zur primären zu machen, geben Sie **1** ein, um sie zur ersten Partition zu machen, und drücken Sie die Eingabetaste, um den Standardwert für den Zylinder zu übernehmen.

    ![Partition erstellen][]

6.  Geben Sie **p** ein, um die Details zum Datenträger anzuzeigen, der gerade partitioniert wird.

    ![Informationen zum Datenträger auflisten][]

7.  Geben Sie **w** ein, um die Einstellungen für den Datenträger zu schreiben.

    ![Datenträgeränderungen schreiben][]

8.  Sie müssen das Dateisystem auf der neuen Partition erstellen. Geben Sie z. B. den folgenden Befehl ein, um das Dateisystem zu erstellen, und geben Sie anschließend das Kontokennwort ein:

    `sudo mkfs -t ext4 /dev/sdc1`

    ![Dateisystem erstellen][]

    > [WACOM.NOTE] Bitte beachten Sie, dass auf SUSE Linux Enterprise 11-Systemen nur schreibgeschützter Zugriff für ext4-Dateisysteme möglich ist. Für diese Systeme wird empfohlen, das neue Dateisystem als ext3 statt als ext4 zu formatieren.

9.  Als Nächstes muss ein Verzeichnis zum Einbinden des neuen Dateisystems zur Verfügung stehen. Geben Sie z. B. den folgenden Befehl ein, um ein Verzeichnis zum Anfügen des Laufwerks zu erstellen, und geben Sie anschließend das Kontokennwort ein:

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

    > [WACOM.NOTE] blkid erfordert nicht in allen Fällen einen sudo-Zugang, ist aber auf manchen Distributions leichter mit `sudo -i` auszuführen, wenn /sbin oder /usr/sbin sich nicht in Ihrem `$PATH` befinden.

    **Vorsicht:** Eine fehlerhafte Bearbeitung der Datei /etc/fstab kann dazu führen, dass das System sich nicht mehr starten lässt. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der Distribution Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

    Geben Sie in einem Texteditor die Informationen zum neuen Dateisystem am Ende der Datei /etc/fstab ein. In diesem Beispiel verwenden wir den UUID-Wert für das neue Gerät **/dev/sdc1**, das in den vorhergehenden Schritten erstellt wurde, und den Bereitstellungspunkt **/datadrive**:

        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

    In auf SUSE Linux basierenden Systemen müssen Sie eventuell ein etwas unterschiedliches Format verwenden:

        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

    Wenn zusätzliche Datenlaufwerke oder Partitionen erstellt werden, müssen Sie diese ebenfalls einzeln in /etc/fstab einfügen.

    Sie können nun testen, ob das Dateisystem korrekt eingebunden wurde, indem Sie die Einbindung aufheben und wiederherstellen. Verwenden Sie dafür den zuvor erstellten Beispielbereitstellungspunkt `/datadrive`:

        `sudo umount /datadrive`
        `sudo mount /datadrive`

    Wenn der zweite Befehl zu einem Fehler führt, prüfen Sie die Datei /etc/fstab auf korrekte Syntax.

    > [WACOM.NOTE] Wenn Sie später einen Datenträger entfernen, ohne fstab zu bearbeiteten, kann der Start des virtuellen Computers fehlschlagen. Für den Fall, dass dieses Problem häufiger auftritt, bieten die meisten Distributionen die fstab-Optionen `nofail` und/oder `nobootwait`, die einen Systemstart auch dann erlauben, wenn der Datenträger nicht vorhanden ist. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution.

## Nächste Schritte

Weitere Informationen zu Linux auf Azure erhalten Sie in den folgenden Artikeln:

-   [Einführung in Linux auf Azure][]

-   [Verwenden des Azure-Befehlszeilentools für Mac und Linux][]

  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [Informationen zu virtuellen Computern in Azure]: #virtualmachine
  [Erstellen eines virtuellen Computers]: #custommachine
  [Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
  [Anschließen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk
  [Überblick über virtuelle Azure-Netzwerke]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Erstellen und Hochladen einer virtuellen Festplatte mit Linux-Betriebssystem]: /en-us/manage/linux/common-tasks/upload-a-vhd/
  [Einführung in Azure]: http://go.microsoft.com/fwlink/p/?LinkId=311926
  [Verwaltungsportal]: http://manage.windowsazure.com
  [Verwalten von Erweiterungen]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Einrichten von Endpunkten für einen virtuellen Computer]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-set-up-endpoints/
  [PuTTY Download]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Rufen Sie die SSH-Details auf.]: ./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png
  [Geben Sie den Hostnamen und die Portinformationen ein.]: ./media/CreateVirtualMachineLinuxTutorial/putty.png
  [Melden Sie sich bei dem neuen virtuellen Computer an.]: ./media/CreateVirtualMachineLinuxTutorial/sshlogin.png
  [Azure Linux Agent User Guide]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/
  [Definieren Sie die Datenträgerdetails.]: ./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png
  [Datenträger erfolgreich angeschlossen]: ./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png
  [Bezeichnen Sie den Datenträger.]: ./media/CreateVirtualMachineLinuxTutorial/diskmessages.png
  [Neues Gerät erstellen]: ./media/CreateVirtualMachineLinuxTutorial/diskpartition.png
  [Partition erstellen]: ./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png
  [Informationen zum Datenträger auflisten]: ./media/CreateVirtualMachineLinuxTutorial/diskinfo.png
  [Datenträgeränderungen schreiben]: ./media/CreateVirtualMachineLinuxTutorial/diskwrite.png
  [Dateisystem erstellen]: ./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png
  [Einführung in Linux auf Azure]: http://www.windowsazure.com/de-de/documentation/articles/introduction-linux/
  [Verwenden des Azure-Befehlszeilentools für Mac und Linux]: http://www.windowsazure.com/de-de/documentation/articles/xplat-cli/
