<properties
	pageTitle="Gewusst wie: Verwenden von VMAccess für virtuelle Linux-Computer"
	description="Verwenden des Azure-Vorschauportals oder der VMAccess-Erweiterung für Linux zum Zurücksetzen der Kennwörter und SSH-Schlüssel, um SSH-Konfigurationen zurückzusetzen und Linux-Benutzer zu löschen"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="kathydav"/>

# Gewusst wie: Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer #

Wenn Sie aufgrund eines vergessenen Kennworts, eines falschen SSH (Secure Shell)-Schlüssels oder eines Problems bei der SSH-Konfiguration keine Verbindung zu einem virtuellen Linux-Computer herstellen können, haben Sie die Möglichkeit, das Kennwort oder den SSH-Schlüssel zurückzusetzen oder die SSH-Konfiguration zu beheben (mithilfe der VMAccessforLinux-Erweiterung). Beachten Sie, dass sich dieser Artikel auf virtuelle Computer bezieht, die mit dem klassischen Bereitstellungsmodell erstellt wurden.

## Azure-Vorschauportal

Um die SSH-Konfiguration im [Azure-Vorschauportal](https://portal.azure.com) zurückzusetzen, klicken Sie auf **Durchsuchen** > **Virtuelle Computer** > *Ihr virtueller Linux-Computer* > **Remotezugriff zurücksetzen**. Beispiel:

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

Um den Namen und das Kennwort des Benutzerkontos mit sudo-Berechtigungen oder den öffentlichen SSH-Schlüssel im [Azure-Vorschauportal](https://portal.azure.com) zurückzusetzen, klicken Sie auf **Durchsuchen** > **Virtuelle Computer** > *Ihr virtueller Linux-Computer* > **Alle Einstellungen** > **Kennwortzurücksetzung**. Beispiel:

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## Azure-Befehlszeilenschnittstelle und PowerShell

Sie benötigen Folgendes:

- Microsoft Azure Linux Agent Version 2.0.5 oder höher. Die meisten Linux-Images im VM-Katalog enthalten Version 2.0.5. Sie können **waagent -version** ausführen, um zu ermitteln, welche Version installiert ist. Folgen Sie den Anweisungen im [Benutzerhandbuch für Azure Linux-Agent], um den Agent zu aktualisieren.
- Azure-Befehlszeilenschnittstelle (CLI). Informationen zum Installieren und Einrichten der Azure-CLI finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli.md).
- Azure PowerShell. Sie verwenden Befehle im Cmdlet Set-AzureVMExtension, um die Erweiterung VMAccessForLinux automatisch zu laden und zu konfigurieren. Ausführliche Informationen zum Einrichten von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell].
- Ein neues Kennwort oder neue SSH-Schlüssel, wenn Sie diese zurücksetzen möchten. Dies ist nicht erforderlich, wenn Sie die SSH-Konfiguration zurücksetzen möchten.

### Keine Installation erforderlich

Die Erweiterung "VMAccess" muss installiert sein, bevor Sie es verwenden können. Solange der Linux-Agent auf dem virtuellen Computer installiert ist, wird beim Ausführen eines Azure PowerShell-Befehls, der das Cmdlet **Set-AzureVMExtension** verwendet, automatisch die Erweiterung geladen.

## Verwenden der Azure-CLI

Mit der Azure-Befehlszeilenschnittstelle können Sie den Befehl **azure** in Ihrer Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) verwenden, um auf Befehle zuzugreifen. Informationen zur Nutzung der Erweiterung erhalten Sie durch Ausführen von **azure vm extension set –help**.

Mit der Azure-CLI können Sie die folgenden Aufgaben ausführen:

+ [Zurücksetzen des Kennworts](#pwresetcli)
+ [Zurücksetzen des SSH-Schlüssels](#sshkeyresetcli)
+ [Zurücksetzen des Kennworts und des SSH-Schlüssels](#resetbothcli)
+ [Erstellen eines neuen Benutzerkontos in sudo](#createnewsudocli)
+ [Zurücksetzen der SSH-Konfiguration](#sshconfigresetcli)
+ [Löschen eines Benutzers](#deletecli)
+ [Anzeigen des Status der VMAccess-Erweiterung](#statuscli)

### <a name="pwresetcli"></a>Zurücksetzen des Kennworts

Schritt 1: Erstellen Sie eine Datei namens PrivateConf.json mit den folgenden Inhalten, und ersetzen Sie dabei die Platzhalter-Werte.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01",
	}

Schritt 2: Führen Sie diesen Befehl aus, und ersetzen Sie dabei "vmname" durch den Namen des virtuellen Computers.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Zurücksetzen des SSH-Schlüssels

Schritt 1: Erstellen Sie eine Datei namens PrivateConf.json mit den folgenden Inhalten, und ersetzen Sie dabei die Platzhalter-Werte.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	}

Schritt 2: Führen Sie diesen Befehl aus, und ersetzen Sie dabei "vmname" durch den Namen des virtuellen Computers.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Zurücksetzen des Kennworts und des SSH-Schlüssels

Schritt 1: Erstellen Sie eine Datei namens PrivateConf.json mit den folgenden Inhalten, und ersetzen Sie dabei die Platzhalter-Werte.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword",
	}

Schritt 2: Führen Sie diesen Befehl aus, und ersetzen Sie dabei "vmname" durch den Namen des virtuellen Computers.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Erstellen eines neuen Benutzerkontos in sudo

Wenn Sie Ihren Benutzernamen vergessen haben, können Sie mit VMAccess einen neuen mit Berechtigung für sudo erstellen. Der vorhandene Benutzername und das Kennwort werden in diesem Fall nicht geändert.

Um einen neuen sudo-Benutzer mit Kennwortzugriff zu erstellen, verwenden Sie das Skript in [Zurücksetzen des Kennworts](#pwresetcli) und geben den neuen Benutzernamen an.

Um einen neuen sudo-Benutzer mit SSH-Schlüsselzugriff zu erstellen, verwenden Sie das Skript in [Zurücksetzen des SSH-Schlüssels](#sshkeyresetcli) und geben den neuen Benutzernamen an.

Sie können auch [Zurücksetzen des Kennworts und des SSH-Schlüssels](#resetbothcli) verwenden, um einen neuen Benutzer mit Kennwort- und SSH-Schlüsselzugriff zu erstellen.

### <a name="sshconfigresetcli"></a>Zurücksetzen der SSH-Konfiguration

Wenn sich die SSH-Konfiguration in einen unerwünschten Zustand befindet, verlieren Sie möglicherweise auch den Zugriff auf den virtuellen Computer. Mit der VMAccess-Erweiterung können Sie die Konfiguration auf den Standardzustand zurücksetzen. Hierzu legen Sie einfach nur den Schlüssel "reset\_ssh" auf "True" fest. Die Erweiterung startet den SSH-Server neu, öffnet den SSH-Port auf Ihrem virtuellen Computer und setzt die SSH-Konfiguration auf die Standardeinstellungen zurück. Das Benutzerkonto (Name, Kennwort oder SSH-Schlüssel) wird nicht geändert.

> [AZURE.NOTE]Die SSH-Konfigurationsdatei befindet sich unter "/etc/ssh/sshd\_config".

Schritt 1: Erstellen Sie eine Datei namens PrivateConf.json mit diesem Inhalt.

	{
	"reset_ssh":"True",
	}

Schritt 2: Führen Sie diesen Befehl aus, und ersetzen Sie dabei "vmname" durch den Namen des virtuellen Computers.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Löschen eines Benutzers

Wenn Sie ein Benutzerkonto löschen möchten, ohne sich direkt auf dem virtuellen Computer anzumelden, können Sie dieses Skript verwenden.

Schritt 1: Erstellen Sie eine Datei namens PrivateConf.json mit folgendem Inhalte, und ersetzen Sie dabei die Platzhalter-Werte.

	{
	"remove_user":"usernametoremove",
	}

Schritt 2: Führen Sie diesen Befehl aus, und ersetzen Sie dabei "vmname" durch den Namen des virtuellen Computers.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Anzeigen des Status der VMAccess-Erweiterung

Führen Sie zum Anzeigen des Status der VMAccess-Erweiterung diesen Befehl aus.

	azure vm extension get


## Verwenden von Azure PowerShell

Verwenden Sie das Cmdlet **Set-AzureVMExtension**, um die Änderungen vorzunehmen, die Sie mit VMAccess durchführen können. Beginnen Sie in allen Fällen mit der Verwendung des Clouddienstnamens und des Namens des virtuellen Computers, um das virtuelle Computerobjekt abzurufen und es in einer Variablen zu speichern.

Geben Sie die Namen des Clouddiensts und des virtuellen Computers ein, und führen Sie die folgenden Befehle in einer Azure PowerShell-Eingabeaufforderung auf Administratorebene aus. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Wenn Sie den Namen des Clouddiensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.


> [AZURE.NOTE]Die Befehlszeilen, die mit $ beginnen, legen PowerShell-Variablen fest, die später in PowerShell-Befehlen verwendet werden.

Wenn Sie die virtuelle Maschine mit dem Azure-Verwaltungsportal erstellt haben, führen Sie den folgenden zusätzlichen Befehl aus:

	$vm.GetInstance().ProvisionGuestAgent = $true

Dieser Befehl verhindert den Fehler "Provision Guest Agent muss für das VM-Objekt aktiviert sein, bevor Sie die IaaS VM Access-Erweiterung festlegen" bei der Ausführung des Befehls "Set-AzureVMExtension" in den folgenden Abschnitten.

Anschließend können Sie die folgenden Aufgaben ausführen:

+ [Zurücksetzen des Kennworts](#password)
+ [Zurücksetzen eines SSH-Schlüssels](#SSHkey)
+ [Zurücksetzen des Kennworts und des SSH-Schlüssels](#both)
+ [Zurücksetzen der SSH-Konfiguration](#config)
+ [Löschen eines Benutzers](#delete)
+ [Anzeigen des Status der VMAccess-Erweiterung](#status)

### <a name="password"></a>Zurücksetzen des Kennworts

Geben Sie den Namen des aktuellen Benutzers von Linux und das neue Kennwort ein, und führen Sie diese Befehle aus.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Wenn Sie das Kennwort oder den SSH-Schlüssel für ein vorhandenes Benutzerkonto zurücksetzen möchten, achten Sie darauf, dass Sie den genauen Benutzernamen eingeben. Wenn Sie einen anderen Namen eingeben, erstellt die Erweiterung "VMAccess" ein neues Benutzerkonto und weist das Kennwort diesem Konto zu.


### <a name="SSHKey"></a>Zurücksetzen eines SSH-Schlüssels

Geben Sie den Namen des aktuellen Benutzers von Linux und den Pfad zu dem Zertifikat ein, das die SSH-Schlüssel enthält, und führen Sie diese Befehle aus.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Zurücksetzen des Kennworts und des SSH-Schlüssels

Geben Sie den Namen des aktuellen Benutzers von Linux, das neue Kennwort und den Pfad zu dem Zertifikat ein, das die SSH-Schlüssel enthält, und führen Sie diese Befehle aus.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Zurücksetzen der SSH-Konfiguration

Fehler in der SSH-Konfiguration können verhindern, dass Sie auf den virtuellen Computer zugreifen können. Sie können dies beheben, indem Sie die SSH-Konfiguration auf den Standardzustand zurücksetzen. Dadurch werden alle neuen Zugriffsparameter in der Konfiguration entfernt, wie Benutzername, Kennwort und SSH-Schlüssel. Das Kennwort oder die SSH-Schlüssel des Benutzerkontos wird/werden jedoch nicht geändert. Die Erweiterung startet den SSH-Server neu, öffnet den SSH-Port auf Ihrem virtuellen Computer und setzt die SSH-Konfiguration auf die Standardeinstellungen zurück.

Führen Sie diese Befehle aus.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Die SSH-Konfigurationsdatei befindet sich unter /etc/ssh/sshd\_config.

### <a name="delete"></a>Löschen eines Benutzers

Geben Sie den Namen des Linux-Benutzers ein, der gelöscht werden soll, und führen Sie diese Befehle aus.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Anzeigen des Status der VMAccess-Erweiterung

Führen Sie zum Anzeigen des Status der VMAccess-Erweiterung diesen Befehl aus.

	$vm.GuestAgentStatus


## Zusätzliche Ressourcen

[Azure-VM-Erweiterungen und Features][]

[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH][]


<!--Link references-->
[Benutzerhandbuch für Azure Linux-Agent]: virtual-machines-linux-agent-user-guide.md
[Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell.md
[Azure-VM-Erweiterungen und Features]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=September15_HO1-->