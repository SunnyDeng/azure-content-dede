<properties 
	pageTitle="Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer" 
	description="Setzen Sie ein lokales Administratorkennwort oder den Remotedesktopdienst für virtuelle Windows-Computer mit dem Azure-Vorschauportal oder mithilfe von PowerShell-Befehlen rasch zurück." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="josephd"/>

# Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer

Wenn Sie ein Kennwort vergessen haben oder ein Problem mit der Konfiguration des Remotedesktopdiensts vorliegt und Sie daher keine Verbindung mit einem virtuellen Windows-Computer herstellen können, verwenden Sie das Azur-Vorschauportal oder die VMAccess-Erweiterung zum Zurücksetzen des lokalen Administratorkennworts oder zum Zurücksetzen der Konfiguration des Remotedesktopdiensts.

## Azure-Vorschauportal

Um den Remotedesktop-Dienst im [Azure-Vorschauportal](https://portal.azure.com) zurückzusetzen, klicken Sie auf **Durchsuchen** > **Virtual Maschines** > *Ihr virtueller Windows-Computer* > **Remotezugriff zurücksetzen**. Beispiel:


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

Um den Namen und das Kennwort des lokalen Administratorkontos im [Azure-Vorschauportal](https://portal.azure.com) zurückzusetzen, klicken Sie auf **Durchsuchen** > **Virtual Maschines** > *Ihr virtueller Windows-Computer* > **Alle Einstellungen** > **Kennwortzurücksetzung**. Beispiel:

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)

 
## VMAccess-Erweiterung und PowerShell

Bevor Sie beginnen, benötigen Sie Folgendes:

- Das Azure PowerShell-Modul Version 0.8.5 oder neuer. Sie können die installierte Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen. Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409). 
- Das neue Kennwort für das lokale Administratorkonto. Dies ist nicht erforderlich, wenn Sie die Konfiguration des Remotedesktopdiensts zurücksetzen möchten. 
- Der VM-Agent. 

Die Erweiterung "VMAccess" muss installiert sein, bevor Sie es verwenden können. Solange der VM-Agent auf dem virtuellen Computer installiert ist, wird die Erweiterung beim Ausführen eines Azure PowerShell-Befehls, der das Cmdlet **Set-AzureVMExtension** verwendet, automatisch geladen.
 
Überprüfen Sie zunächst, ob der VM-Agent bereits installiert ist. Füllen Sie den Cloud-Dienstnamen und den Namen des virtuellen Computers aus, und führen Sie dann die folgenden Befehle an einer Azure PowerShell-Eingabeaufforderung mit Administratorrechten aus. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Wenn Sie den Namen des Cloud-Diensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.

Der Befehl **write-host** zeigt **True** an, wenn der VM-Agent installiert ist. Wenn **False** angezeigt wird, nutzen Sie die Anweisungen und den Link zum Download im Azure-Blogbeitrag VM [Agent and Extensions – Part 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) (in englischer Sprache).

Wenn Sie die virtuelle Maschine mit dem Azure-Verwaltungsportal erstellt haben, führen Sie den folgenden zusätzlichen Befehl aus:

	$vm.GetInstance().ProvisionGuestAgent = $true

Dieser Befehl verhindert den Fehler "Provision Guest Agent muss für das VM-Objekt aktiviert sein, bevor Sie die IaaS VM Access-Erweiterung festlegen" bei der Ausführung des Befehls "Set-AzureVMExtension" in den folgenden Abschnitten.

Nun können Sie diese Aufgaben ausführen:

- Zurücksetzen des Kennworts eines lokalen Administratorkontos
- Zurücksetzen der Konfiguration des Remotedesktopdiensts

## Zurücksetzen des Kennworts eines lokalen Administratorkontos

Geben Sie den Namen des aktuellen lokalen Administratorkontos und das neue Kennwort ein, und führen Sie diese Befehle aus.

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Wenn Sie einen anderen Namen als das aktuelle Konto eingeben, benennt die VMAccess-Erweiterung das lokale Administratorkonto um, weist das Kennwort diesem Konto zu und gibt einen Befehl zum Abmelden des Remotedesktops aus.
- Wenn das lokale Administratorkonto deaktiviert ist, wird es durch die VMAccess-Erweiterung aktiviert.
 
Diese Befehle setzen auch die Konfiguration des Remotedesktopdiensts zurück.

## Zurücksetzen der Konfiguration des Remotedesktopdiensts

Führen Sie diesen Befehl zum Zurücksetzen der Konfiguration des Remotedesktopdiensts aus.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Die VMAccess-Erweiterung führt diese beiden Befehle auf dem virtuellen Computer aus:

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Dieser Befehl aktiviert die integrierte Windows-Firewallgruppe, die den eingehenden Remotedesktop-Datenverkehr über TCP-Port 3389 zulässt.

- **Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Dieser Befehl legt den Registrierungswert "fDenyTSConnections" auf 0 fest und aktiviert damit Remotedesktopverbindungen.

Wenn das Problem mit dem Zugriff auf Remotedesktop dadurch nicht behoben werden kann, führen Sie das[ Azure IaaS-Diagnosepaket (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) aus.

1.	Klicken Sie auf dieser Seite auf **Diagnosepaket Microsoft Azure IaaS (Windows)**, um eine neue Diagnosesitzung zu erstellen.
2.	Wählen Sie auf der Seite **Welches der folgenden Probleme tritt mit Ihrem virtuellen Azure-Computer auf?** das Problem R**DP-Verbindung mit einem virtuellen Azure-Computer (Neustart erforderlich)**. 

Weitere Informationen finden Sie in der Knowledge Base im Artikel [Microsoft Azure IaaS (Windows) und Diagnoseprogramme](http://support.microsoft.com/kb/2976864).

Wenn Sie das Azure IaaS-Diagnosepaket (Windows) nicht ausführen konnten oder das Problem dadurch nicht behoben wurde, finden Sie weitere Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer unter Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Zusätzliche Ressourcen

[Azure-VM-Erweiterungen und Features](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

<!---HONumber=58--> 