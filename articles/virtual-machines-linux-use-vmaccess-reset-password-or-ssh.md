<properties 
	pageTitle="Gewusst wie: Verwenden von VMAccess für virtuelle Linux-Computer" 
	description="Gewusst wie: Verwenden der VMAccess-Erweiterung für Linux zum Zurücksetzen der Kennwörter und SSH-Schlüssel, zum Zurücksetzen der SSH-Konfigurationen und zum Löschen der Linux-Benutzer" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Gewusst wie: Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer #

Wenn Sie aufgrund eines vergessenen Kennworts, eines falschen SSH (Secure Shell)-Schlüssels oder eines Problems bei der SSH-Konfiguration keine Verbindung zu einem virtuellen Linux-Computer herstellen können, können Sie das Kennwort oder den SSH-Schlüssel zurücksetzen oder die SSH-Konfiguration beheben (mithilfe der VMAccessforLinux-Erweiterung). 

## Anforderungen

- Microsoft Azure Linux Agent Version 2.0.5 oder höher. Die meisten Linux-Images im VM-Katalog enthalten Version 2.0.5. Sie können  `waagent -version` ausführen, um zu ermitteln, welche Version installiert ist. Folgen Sie den Anweisungen im [Benutzerhandbuch für Azure Linux-Agent], um den Agent zu aktualisieren.
- Azure PowerShell. Sie verwenden Befehle im Cmdlet **Set-AzureVMExtension**, um die Erweiterung **VMAccessForLinux** automatisch zu laden und zu konfigurieren. Ausführliche Informationen zum Einrichten von Azure PowerShell finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell].
- Ein neues Kennwort oder neue SSH-Schlüssel, wenn Sie diese zurücksetzen möchten. Dies ist nicht erforderlich, wenn Sie die SSH-Konfiguration zurücksetzen möchten. 

## Keine Installation erforderlich

Die Erweiterung "VMAccess" muss installiert sein, bevor Sie es verwenden können. Solange der Linux-Agent auf dem virtuellen Computer installiert ist, wird die Erweiterung beim Ausführen eines Azure PowerShell-Befehls automatisch geladen, der das Cmdlet **Set-AzureVMExtension** verwendet. 

## Verwenden der Erweiterung zum Zurücksetzen eines Kennworts, SSH-Schlüssels oder der SSH-Konfiguration oder zum Löschen eines Benutzers

Verwenden Sie das Cmdlet **Set-AzureVMExtension**, um die Änderungen vorzunehmen, die Sie mit VMAccess durchführen können. Beginnen Sie in allen Fällen mit der Verwendung des Cloud-Dienstnamens und des Namens des virtuellen Computers, um das virtuelle Computerobjekt abzurufen und es in einer Variablen zu speichern. 

Geben Sie die Namen des Cloud-Diensts und des virtuellen Computers ein, und führen Sie die folgenden Befehle in einer Azure PowerShell-Eingabeaufforderung auf Administratorebene aus. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < und >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Wenn Sie den Namen des Cloud-Diensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.


> [AZURE.NOTE] Die Befehlszeilen, die mit $ beginnen, legen PowerShell-Variablen fest, die später in PowerShell-Befehlen verwendet werden.

Wenn Sie die virtuelle Maschine mit dem Azure-Verwaltungsportal erstellt haben, führen Sie den folgenden zusätzlichen Befehl aus:

	$vm.GetInstance().ProvisionGuestAgent = $true

Dieser Befehl verhindert den Fehler "Provision Guest Agent muss für das VM-Objekt aktiviert sein, bevor Sie die IaaS VM Access-Erweiterung festlegen" bei der Ausführung des Befehls "Set-AzureVMExtension" in den folgenden Abschnitten. 

Anschließend können Sie die folgenden Aufgaben ausführen:

+ [Kennwort zurücksetzen](#password)
+ [SSH-Schlüssel zurücksetzen](#SSHkey)
+ [Kennwort und SSH-Schlüssel zurücksetzen](#both)
+ [SSH-Konfiguration zurücksetzen](#config)
+ [Benutzer löschen](#delete)

### <a name="password"></a>Zurücksetzen des Kennworts

Geben Sie den Namen des aktuellen Benutzers von Linux und das neue Kennwort ein, und führen Sie diese Befehle aus.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Wenn Sie das Kennwort oder den SSH-Schlüssel für ein vorhandenes Benutzerkonto zurücksetzen möchten, achten Sie darauf, dass Sie den genauen Benutzernamen eingeben. Wenn Sie einen anderen Namen eingeben, erstellt die Erweiterung "VMAccess" ein neues Benutzerkonto und weist das Kennwort diesem Konto zu.


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

> [AZURE.NOTE] Die SSH-Konfigurationsdatei befindet sich unter /etc/ssh/sshd_config.

### <a name="delete"></a> Löschen eines Benutzers

Geben Sie den Namen des Linux-Benutzers ein, der gelöscht werden soll, und führen Sie diese Befehle aus.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## Zusätzliche Ressourcen

[Azure-VM - Erweiterungen und Features] []

[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH] []


<!--Link references-->
[Benutzerhandbuch für Azure Linux-Agent]: ../virtual-machines-linux-agent-user-guide
[Gewusst wie: Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell
[Azure-VM - Erweiterungen und Features]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
