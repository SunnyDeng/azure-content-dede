<properties writer="kathydav" editor="tysonn" manager="timlt" />

1. Melden Sie sich beim [klassischen Azure-Portal](http://manage.windowsazure.com) an. Testen Sie das [kostenlose Testkonto](https://azure.microsoft.com/pricing/free-trial/), wenn Sie noch kein Abonnement haben.

2. Klicken Sie in der Befehlsleiste am unteren Ende des Fensters auf **Neu**.

3. Unter **Compute** klicken Sie auf **Virtueller Computer** und anschließend auf **From Gallery**.

	![Neuen virtuellen Computer erstellen][Image1]

4. Wählen Sie unter der Gruppe **SUSE** das Image eines virtuellen OpenSUSE-Computers aus, und klicken Sie auf den Pfeil, um den Vorgang fortzusetzen.

5. Gehen Sie auf der ersten Seite für die **Konfiguration des virtuellen Computers** wie folgt vor:

	- Weisen Sie unter **Name des virtuellen Computers** einen Namen zu, z. B. "testlinuxvm". Der Name muss zwischen 3 und 15 Zeichen enthalten, er darf nur aus Buchstaben, Zahlen und Bindestrichen bestehen und muss mit einem Buchstaben beginnen und mit einem Buchstaben oder einer Zahl enden.

	- Überprüfen Sie den **Tarif**, und wählen Sie eine **Größe** aus. Die Ebene bestimmt, zwischen welchen Größen Sie wählen können. Die Größe wirkt sich auf seine Betriebskosten sowie auf die Konfigurationsoptionen, wie die Anzahl der Datenträger, die Sie anschließen können, aus. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](../articles/virtual-machines-size-specs.md).
	- Geben Sie einen **neuen Benutzernamen** ein, oder übernehmen Sie den Standardnamen **azureuser**. Dieser Name wird der Sudoer-Listendatei hinzugefügt.
	- Wählen Sie aus, welche Art der **Authentifizierung** verwendet werden soll. Hinweise zu allgemeinen Kennwortrichtlinien finden Sie unter [Sichere Kennwörter](http://msdn.microsoft.com/library/ms161962.aspx).

6. Gehen Sie auf der Seite für die **Konfiguration des virtuellen Computers** wie folgt vor:

	- Verwenden Sie die Standardeinstellung **Einen neuen Clouddienst erstellen**.
	- Geben Sie im Feld **DNS-Name** einen gültigen DNS-Namen ein, der als Teil der Adresse verwendet wird, z. B. "testlinuxvm".
	- Wählen Sie im Feld **Region/Affinity Group/Virtual Network** eine Region aus, in der dieses virtuelle Image gehostet wird.
	- Behalten Sie unter **Endpunkte** den SSH-Endpunkt bei. Sie können nun weitere Endpunkte hinzufügen oder nach dem Erstellen des virtuellen Computers vorhandene Endpunkte hinzufügen, ändern oder löschen.

	>[AZURE.NOTE] Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, **müssen** Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Sie können einen virtuellen Computer nicht einem virtuellen Netzwerk hinzufügen, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick](virtual-networks-overview.md).

7.	Behalten Sie auf der letzten Seite für die **Konfiguration des virtuellen Computers** die Standardeinstellungen bei, und klicken Sie zum Fertigstellen auf das Häkchen.

Der neue virtuelle Computer wird im Portal unter **Virtuelle Computer** aufgelistet. Der virtuelle Computer wird eingerichtet, dabei wird der Status **(Bereitstellung)** angezeigt. Wenn als Status **Wird ausgeführt** gemeldet wird, können Sie mit dem nächsten Schritt fortfahren.

##Herstellen einer Verbindung mit dem virtuellen Computer

In Abhängigkeit von dem Betriebssystem auf dem Computer, über den Sie eine Verbindung herstellen, verwenden Sie für die Verbindung mit dem virtuellen Computer SSH oder PuTTY:

- Verwenden Sie auf einem Computer mit Linux SSH. Geben Sie an der Eingabeaufforderung Folgendes ein:

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

	Geben Sie das Kennwort des Benutzers ein.

- Verwenden Sie auf einem Computer mit Windows PuTTY. Ist PuTTY nicht installiert, laden Sie die Anwendung von der [PuTTY-Downloadseite][PuTTYDownload] herunter.

	Speichern Sie **putty.exe** in einem Verzeichnis auf dem Computer. Öffnen Sie eine Eingabeaufforderung, navigieren Sie zu dem betreffenden Verzeichnis, und führen Sie **putty.exe** aus.

	Geben Sie den Hostnamen ein, z. B. "testlinuxvm.cloudapp.net", und geben Sie dann für **Port** den Wert 22 ein.

	![PuTTY-Bildschirm][Image6]

##Aktualisieren des virtuellen Computers (optional)

1. Nachdem Sie eine Verbindung mit dem virtuellen Computer hergestellt haben, haben Sie die Möglichkeit, Systemaktualisierungen und Patches zu installieren. Zum Ausführen der Aktualisierung geben Sie Folgendes ein:

	`$ sudo zypper update`

2. Wählen Sie **Software** und dann **Onlineupdate**, um die verfügbaren Updates aufzulisten. Wählen Sie **Annehmen**, um die Installation zu starten und alle verfügbaren neuen Patches (außer den optionalen) anzuwenden.

3. Wählen Sie nach Abschluss der Installation **Fertig stellen** aus. Ihr System ist jetzt auf dem aktuellen Stand.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=AcomDC_0128_2016-->