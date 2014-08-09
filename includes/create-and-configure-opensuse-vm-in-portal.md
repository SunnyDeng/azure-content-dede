<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Wichtig**: Wenn Ihr virtueller Computer ein virtuelles Netzwerk
verwenden soll, geben Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers an. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke][1].

1.  Melden Sie sich mit Ihrem Azure-Konto am
    [Azure-Verwaltungsportal][2] an.

2.  Klicken Sie im Verwaltungsportal unten links auf der Webseite auf
    **+Neu**, klicken Sie auf **Virtueller Computer** und dann auf
    **From Gallery**.
    
    ![Neuen virtuellen Computer
    erstellen](./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png)

3.  Wählen Sie in **Platform Images** ein Image eines virtuellen
    OpenSUSE-Computers aus, und klicken Sie anschließend unten rechts
    auf der Seite auf den Pfeil für die nächste Seite.

4.  Geben Sie auf der Seite **Konfiguration des virtuellen Computers**
    die folgenden Informationen ein:
    
    * Geben Sie den **Namen des virtuellen Computers** ein, z. B.
      "testlinuxvm".
    * Geben Sie den **Namen des neuen Benutzers** ein, z. B. "newuser",
      der der Sudoer-Listendatei hinzugefügt wird.
    * Geben Sie in das Feld **Neues Kennwort** ein [sicheres
      Kennwort][3] ein.
    * Geben Sie das Kennwort in das Feld **Kennwort bestätigen**
      nochmals ein.
    * Wählen Sie in der Dropdownliste die geeignete **Größe** aus.
    
    Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um
    fortzufahren.

5.  Geben Sie auf der Seite **Virtual machine mode** die folgenden
    Informationen ein:
    * Wählen Sie **Standalone Virtual Machine**.
    * Geben Sie in das Feld **DNS-Name** eine gültigen DNS-Adresse ein,
      z. B. "testlinuxvm".
    * Wählen Sie im Feld **Region/Affinity Group/Virtual Network** eine
      Region aus, in der dieses virtuelle Image gehostet wird.
    
    Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um
    fortzufahren.

6.  Wählen Sie auf der Seite **Virtual machine options** im Feld
    **Availability Set** den Eintrag **(none)** aus. Klicken Sie auf das
    Häkchen, um fortzufahren.

7.  Warten Sie, während Azure den virtuellen Computer vorbereitet.

## Konfigurieren von Endpunkten

Nach der Erstellung des virtuellen Computers müssen Sie Endpunkte für Remoteverbindungen konfigurieren.

1.  Klicken Sie im Verwaltungsportal auf **Virtueller Computer** und
    anschließend auf den Namen Ihres neuen virtuellen Computers sowie
    auf **Endpunkte**.

2.  Klicken Sie unten auf der Seite auf **Endpunkt bearbeiten**.
    Bearbeiten Sie den SSH-Endpunkt, sodass sein **Öffentlicher Port**
    22 lautet.

## Herstellen einer Verbindung mit dem virtuellen Computer

Wenn der virtuelle Computer bereitgestellt wurde und die Endpunkte konfiguriert sind, können Sie über SSH oder PuTTY eine Verbindung mit dem virtuellen Computer herstellen.

### Herstellen einer Verbindung über SSH

Wenn Sie einen Windows-Computer verwenden, stellen Sie über SSH eine Verbindung mit dem virtuellen Computer her. Geben Sie an der Eingabeaufforderung Folgendes ein:

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Geben Sie das Kennwort des Benutzers ein.

### Herstellen einer Verbindung über PuTTY

Wenn Sie einen Windows-Computer verwenden, stellen Sie über PuTTY eine Verbindung mit dem virtuellen Computer her. PuTTY kann von [PuTTY Download Page][4] heruntergeladen werden.

1.  Laden Sie **putty.exe** herunter, und speichern Sie die Daten in
    einem Verzeichnis auf dem Computer. Öffnen Sie eine
    Eingabeaufforderung, navigieren Sie zu dem betreffenden Verzeichnis,
    und führen Sie **putty.exe** aus.

2.  Geben Sie "testlinuxvm.cloudapp.net" als **Hostnamen** und "22" für
    den **Port** ein.
    ![PuTTY-Bildschirm](./media/create-and-configure-opensuse-vm-in-portal/putty.png)

## Aktualisieren des virtuellen Computers (optional)

1.  Sobald Sie eine Verbindung mit dem virtuellen Computer hergestellt
    haben, haben Sie die Möglichkeit, Systemaktualisierungen und Patches
    zu installieren. Führen Sie folgenden Befehl aus:
    
    `$ sudo zypper update`

2.  Wählen Sie **Software** und dann **Onlineupdate**. Eine Liste von
    Aktualisierungen wird angezeigt. Wählen Sie **Annehmen**, um die
    Installation zu starten und alle neuen Patches (außer den
    optionalen) anzuwenden, die derzeit für Ihr System verfügbar sind.

3.  Wählen Sie nach Abschluss der Installation **Fertig stellen** aus.
    Ihr System ist jetzt auf dem aktuellen Stand.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/de-de/library/ms161962.aspx
[4]: http://www.puttyssh.org/download.html