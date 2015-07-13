**Hinweis**: In diesem Artikel wird die Erstellung eines virtuellen Computers beschrieben, der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie über den Hostnamen direkte Verbindungen mit den virtuellen Computern herstellen können oder damit Sie standortübergreifende Verbindungen einrichten können, verwenden Sie stattdessen die Methode **From Gallery**, und legen Sie das virtuelle Netzwerk fest, wenn Sie den virtuellen Computer erstellen. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Melden Sie sich mit Ihrem Azure-Konto beim Azure-Verwaltungsportal an.
2. Klicken Sie im Verwaltungsportal unten links auf der Webseite auf **+Neu**, klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.

	![Neuen virtuellen Computer erstellen][Image1]

3. Wählen Sie in **Platform Images** ein Image eines virtuellen CentOS-Computers aus, und klicken Sie anschließend unten rechts auf der Seite auf den Pfeil für die nächste Seite.
	
4. Geben Sie auf der Seite **Konfiguration des virtuellen Computers** die folgenden Informationen ein:
	- Geben Sie den **Namen des virtuellen Computers** ein, z. B. "testlinuxvm".
	- Geben Sie den **Namen des neuen Benutzers** ein, z. B. "newuser", der der Sudoer-Listendatei hinzugefügt wird.
	- Geben Sie in das Feld **Neues Kennwort** ein [sicheres Kennwort](http://msdn.microsoft.com/library/ms161962.aspx) ein.
	- Geben Sie das Kennwort in das Feld **Kennwort bestätigen** nochmals ein.
	- Wählen Sie in der Dropdownliste die geeignete **Größe** aus.

	Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.
	
5. Geben Sie auf der Seite **Virtual machine mode** die folgenden Informationen ein:
	- Wählen Sie **Standalone Virtual Machine**.
	- Geben Sie in das Feld **DNS-Name** eine gültigen DNS-Adresse ein, z. B. "testlinuxvm".
	- Wählen Sie im Feld **Speicherkonto** die Option **Use an automatically generated storage account**.
	- Wählen Sie im Feld **Region/Affinity Group/Virtual Network** eine Region aus, in der dieses virtuelle Image gehostet wird.

	Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.

6. Wählen Sie auf der Seite **Virtual machine options** im Feld **Availability Set** den Eintrag **(none)** aus.

	Klicken Sie auf das Häkchen, um fortzufahren.
	
7. Warten Sie, während Azure den virtuellen Computer vorbereitet.

##Konfigurieren von Endpunkten
Konfigurieren Sie nach der Erstellung des virtuellen Computers Endpunkte für Remoteverbindungen.

1. Klicken Sie im Verwaltungsportal auf **Virtuelle Computer**, klicken Sie auf den Namen des neuen virtuellen Computers und danach auf **Endpunkte**.

2. Klicken Sie unten auf der Seite auf **Endpunkt bearbeiten**. Bearbeiten Sie den SSH-Endpunkt, sodass sein **Öffentlicher Port** 22 lautet.

##Herstellen einer Verbindung mit dem virtuellen Computer
Wenn der virtuelle Computer bereitgestellt wurde und die Endpunkte konfiguriert sind, können Sie über SSH oder PuTTY eine Verbindung mit dem virtuellen Computer herstellen.

###Herstellen einer Verbindung über SSH
Wenn Sie Linux verwenden, stellen Sie über SSH eine Verbindung mit dem virtuellen Computer her. Geben Sie an der Eingabeaufforderung Folgendes ein:

	$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Geben Sie das Kennwort des Benutzers ein.

###Herstellen einer Verbindung über PuTTY
Wenn Sie einen Windows-Computer verwenden, stellen Sie über PuTTY eine Verbindung mit dem virtuellen Computer her. PuTTY kann von [PuTTY Download Page][PuTTYDownLoad] heruntergeladen werden.

1. Laden Sie **putty.exe** herunter, und speichern Sie die Daten in einem Verzeichnis auf dem Computer. Öffnen Sie eine Eingabeaufforderung, navigieren Sie zu dem betreffenden Verzeichnis, und führen Sie **putty.exe** aus.

2. Geben Sie "testlinuxvm.cloudapp.net" als **Hostnamen** und "22" für den **Port** ein. ![PuTTY-Bildschirm][Image6]

##Aktualisieren des virtuellen Computers (optional)
Sobald Sie eine Verbindung mit dem virtuellen Computer hergestellt haben, haben Sie die Möglichkeit, Aktualisierungen zu installieren. Führen Sie folgenden Befehl aus:

	$ sudo yum update

Geben Sie das Kennwort erneut ein. Warten Sie, während die Aktualisierungen installiert werden.


[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-centos-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-centos-vm-in-portal/putty.png

<!---HONumber=62-->