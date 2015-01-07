<properties writer="kathydav" editor="tysonn" manager="timlt" /> 

**Wichtig**: Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, geben Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers an. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/p/?LinkID=294063).


1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Verwaltungsportal][AzurePreviewPortal] an.

2. Klicken Sie im Verwaltungsportal unten links auf der Webseite auf **+Neu**, klicken Sie auf **Virtueller Computer** und dann auf **Aus Katalog**.

	![Create a New Virtual Machine][Image1]

3. Wählen Sie unter der Gruppe **SUSE** das Image eines virtuellen OpenSUSE-Computers aus, und klicken Sie dann unten rechts auf der Seite auf den Weiter-Pfeil.


4. Nehmen Sie auf der ersten Seite für die **Konfiguration des virtuellen Computers** die erforderlichen Einstellungen vor, oder überprüfen Sie diese:

	- Weisen Sie unter **Name des virtuellen Computers** einen Namen zu, z. B. "testlinuxvm".
	- Überprüfen Sie die **Ebene**, und wählen Sie eine **Größe** aus. Die Ebene bestimmt, zwischen welchen Größen Sie wählen können.
	- Geben Sie unter **Neuer Benutzername** einen Namen ein, z. B. "newuser", der der Sudoer-Listendatei hinzugefügt wird.
	- Wählen Sie aus, welche Art der **Authentifizierung** verwendet werden soll. Hinweise zu allgemeinen Kennwortrichtlinien finden Sie unter [Sichere Kennwörter](http://msdn.microsoft.com/de-de/library/ms161962.aspx).


5. Nehmen Sie auf der nächsten Seite für die **Konfiguration des virtuellen Computers** die erforderlichen Einstellungen vor, oder überprüfen Sie diese:
	- Verwenden Sie die Standardmethode **Einen neuen Cloud-Dienst erstellen**.
	- Geben Sie im Feld **DNS-Name** einen gültigen DNS-Namen ein, der als Teil der Adresse verwendet wird, z. B. "testlinuxvm".
	- Wählen Sie im Feld **Region/Affinitätsgruppe/Virtuelles Netzwerk** eine Region aus, in der dieses virtuelle Image gehostet wird.

6.	Klicken Sie zum Fertigstellen auf "Weiter", und warten Sie dann, bis Azure den virtuellen Computer vorbereitet und gestartet hat.

##Herstellen einer Verbindung mit dem virtuellen Computer
In Abhängigkeit von dem auf Ihrem Computer ausgeführten Betriebssystem verwenden Sie für die Verbindung mit dem virtuellen Computer SSH oder PuTTY:

- Verwenden Sie SSH, wenn Sie für die Verbindung zum virtuellen Computer Linux nutzen. Geben Sie an der Eingabeaufforderung Folgendes ein: 

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
	
	Type the user's password.

- Verwenden Sie PuTTY, wenn Sie für die Verbindung zum virtuellen Computer einen Windows-Computer nutzen. Sie können PuTTY von der [PuTTY-Downloadseite][PuTTYDownLoad] herunterladen. 

	Laden Sie **putty.exe** herunter, und speichern Sie die Datei in einem Verzeichnis auf dem Computer. Öffnen Sie eine Eingabeaufforderung, navigieren Sie zu dem betreffenden Verzeichnis, und führen Sie **putty.exe** aus.

	Geben Sie den Hostnamen ein, z. B. "testlinuxvm.cloudapp.net", und geben Sie dann für **Port** den Wert "22" ein.

	![PuTTY Screen][Image6]  

##Aktualisieren des virtuellen Computers (optional)
1. Nachdem Sie eine Verbindung mit dem virtuellen Computer hergestellt haben, haben Sie die Möglichkeit, Systemaktualisierungen und Patches zu installieren. Zum Ausführen der Aktualisierung geben Sie Folgendes ein:

	`$ sudo zypper update`

2. Wählen Sie **Software** und dann **Onlineupdate**, um die verfügbaren Aktualisierungen aufzulisten. Wählen Sie **Annehmen**, um die Installation zu starten und alle verfügbaren neuen Patches (außer den optionalen) anzuwenden. 

3. Wählen Sie nach Abschluss der Installation **Fertig stellen** aus.  Ihr System ist jetzt auf dem aktuellen Stand.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!--HONumber=35.1-->
