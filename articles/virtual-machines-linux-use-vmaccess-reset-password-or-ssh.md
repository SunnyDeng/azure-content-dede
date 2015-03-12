<properties 
	pageTitle="Verwenden von VMAccess für virtuelle Linux-Computer" 
	description="So verwenden Sie die VMAccess-Erweiterung für Linux zum Zurücksetzen von Kennwörtern, SSH-Schlüsseln und SSH-Konfigurationen" 
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
	ms.date="10/30/2014" 
	ms.author="kathydav"/>

#Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer#

Wenn Sie aufgrund eines vergessenen Kennworts oder SSH-Schlüssels oder eines Problems bei der SSH-Konfiguration keine Verbindung zu einem virtuellen Linux-Computer herstellen können, können Sie das Kennwort, den SSH-Schlüssel oder die SSH-Konfiguration mithilfe der VMAccessforLinux-Erweiterung zurücksetzen. 


##Anforderungen

- Microsoft Azure Linux Agent Version 2.0.5 oder höher. Die meisten Linux-Images im VM-Katalog enthalten Version 2.0.5. Sie können  `waagent -version` ausführen, um zu ermitteln, welche Version installiert ist. Folgen Sie den Anweisungen im [Benutzerhandbuch für Azure Linux-Agent], um den Agent zu aktualisieren.

- Das Azure PowerShell-Modul. Das Modul enthält das **Set-AzureVMExtension**-Cmdlet, mit dem Sie Befehle für die Verwendung der **VMAccessForLinux**-Erweiterung ausführen. Ausführliche Informationen zum Einrichten des Moduls finden Sie unter [Installieren und Konfigurieren von Azure PowerShell].

- Ein neues Kennwort oder neue SSH-Schlüssel, wenn Sie diese zurücksetzen möchten. Diese sind nicht erforderlich, wenn Sie die SSH-Konfigurationsprobleme beheben möchten. 

##Keine Installation erforderlich

VMAccess muss vor der Verwendung nicht installiert werden. Solange der Linux-Agent und das Azure-Modul installiert sind, wird die Erweiterung beim Ausführen eines Befehls, der das **Set AzureVMExtension**-Cmdlet aufruft, automatisch geladen. 

##Verwenden der Erweiterung zum Zurücksetzen eines Kennworts, SSH-Schlüssels oder der Konfiguration oder zum Hinzufügen eines Benutzers

Verwenden Sie das **Set-AzureVMExtension**-Cmdlet, um die Änderungen vorzunehmen, die Sie mit VMAccess durchführen können. Beginnen Sie in allen Fällen mit der Verwendung des Cloud-Dienstnamens und des Namens des virtuellen Computers, um das virtuelle Computerobjekt abzurufen und es in einer Variablen zu speichern.   

Öffnen Sie Azure PowerShell, und geben Sie Folgendes an der Eingabeaufforderung ein. Achten Sie darauf, dass Sie die Platzhalter "MyServiceName" und "MyVMName" durch die tatsächlichen Namen ersetzen:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

Anschließend können Sie die folgenden Aufgaben ausführen:

+ [Kennwort zurücksetzen](#password)
+ [SSH-Schlüssel zurücksetzen](#SSHkey)
+ [Kennwort und SSH-Schlüssel zurücksetzen](#both)
+ [SSH-Konfiguration zurücksetzen](#config)

### <a name="password"></a>Zurücksetzen des Kennworts
Geben Sie den Benutzernamen und das Kennwort ein und speichern Sie sie in Variablen. Erstellen Sie dann eine einzelne Variable zur Speicherung der Werte, damit die nächsten Befehle diese verwenden können.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

Speichern Sie Name, Herausgeber und Versionsnummer in Variablen: 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

Führen Sie mit allen erforderlichen, in Variablen gespeicherten Werten den folgenden Befehl aus:

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Wenn Sie das Kennwort oder den SSH-Schlüssel für ein vorhandenes Benutzerkonto zurücksetzen möchten, achten Sie darauf, dass Sie den genauen Benutzernamen eingeben. Wenn Sie einen anderen Namen eingeben, erstellt die VMAccess-Erweiterung ein neues Benutzerkonto und weist das Kennwort diesem Konto zu.

### <a name="SSHkey"></a>Zurücksetzen des SSH-Schlüssels

Geben Sie den Benutzernamen und den Pfad Ihres neuen öffentlichen SSH-Schlüssels ein und speichern Sie sie in Variablen:

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

Führen Sie die folgenden Befehle aus:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>Zurücksetzen des Kennworts und des SSH-Schlüssels

Geben Sie für den aktuellen Benutzer ein neues Kennwort und den Pfad des neuen Zertifikats mit dem öffentlichen SSH-Schlüssel ein und speichern Sie sie in Variablen: 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

Führen Sie die folgenden Befehle aus:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>Zurücksetzen der SSH-Konfiguration

Fehler in der SSH-Konfiguration können den Zugriff auf den virtuellen Computer verhindern. Sie können dies beheben, indem Sie die Konfiguration auf die Standardeinstellungen zurücksetzen. Dadurch werden alle neuen Zugriffsparameter in der Konfiguration (Benutzername, Kennwort oder SSH-Schlüssel) entfernt. Das Kennwort oder die SSH-Schlüssel des Benutzerkontos werden hierdurch nicht geändert. Die Erweiterung startet den SSH-Server neu, öffnet den SSH-Port auf Ihrem virtuellen Computer und setzt die SSH-Konfiguration auf die Standardeinstellungen zurück.  

Setzen Sie das Flag, das angibt, dass Sie die Konfiguration zurücksetzen möchten, und speichern Sie es in einer Variablen: 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

Führen Sie die folgenden Befehle aus:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Die SSH-Konfigurationsdatei befindet sich unter "/etc/ssh/sshd_config".

## Problembehandlung

Bei Verwendung des **Set-AzureVMExtension**-Cmdlets erhalten Sie möglicherweise diesen Fehler: "Bereitstellen Gast-Agent muss vor dem Festlegen der IaaS VM Access-Erweiterung auf dem VM-Objekt aktiviert werden". 

Dies kann geschehen, wenn Sie das Verwaltungsportal für die Erstellung des virtuellen Linux-Computers verwenden, weil der Wert der Gast-Agent-Eigenschaft möglicherweise nicht auf "True" festgelegt wurde. Um dieses Problem zu beheben, führen Sie die folgenden Befehle aus:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#Zusätzliche Ressourcen
[Azure-VM-Erweiterungen und Features] []

[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH] []


<!--Link references-->
[Benutzerhandbuch für Azure Linux-Agent]: ../virtual-machines-linux-agent-user-guide
[Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell
[Azure-VM-Erweiterungen und Features]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx



<!--HONumber=42-->
