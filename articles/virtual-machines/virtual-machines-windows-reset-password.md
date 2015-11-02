<properties
	pageTitle="Zurücksetzen des Kennworts oder von Remotedesktop auf einen virtuellen Windows-Computer | Microsoft Azure"
	description="Setzen Sie das Administratorkennwort oder die Remotedesktopdienste auf einem mit dem Ressourcen-Manager-Bereitstellungsmodell erstellten virtuellen Windows-Computer zurück."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>

# Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für einen virtuelle Windows-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


Wenn Sie ein Kennwort vergessen haben oder ein Problem mit der Konfiguration des Remotedesktopdiensts vorliegt und Sie daher keine Verbindung mit einem virtuellen Windows-Computer herstellen können, verwenden Sie das Azure-Vorschauportal oder die VMAccess-Erweiterung zum Zurücksetzen des lokalen Administratorkennworts oder zum Zurücksetzen der Konfiguration des Remotedesktopdiensts.

## Vorschauportal

Um den Remotedesktop-Dienst im [Vorschauportal](https://portal.azure.com) zurückzusetzen, klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (Klassisch)** > *Ihr virtueller Windows-Computer* > **Remotezugriff zurücksetzen**. Die folgende Seite wird angezeigt.


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

Um den Namen und das Kennwort des lokalen Administratorkontos im [Vorschauportal](https://portal.azure.com) zurückzusetzen, klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (Klassisch)** > *Ihr virtueller Windows-Computer* > **Alle Einstellungen** > **Kennwortzurücksetzung**. Die folgende Seite wird angezeigt.

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)


## VMAccess-Erweiterung und PowerShell

Bevor Sie beginnen, benötigen Sie Folgendes:

- Das Azure PowerShell-Modul Version 0.8.5 oder höher. Sie können die installierte Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen. Anweisungen und einen Link zur neuesten Version finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409).
- Das neue Kennwort für das lokale Administratorkonto. Dies ist nicht erforderlich, wenn Sie die Konfiguration des Remotedesktopdiensts zurücksetzen möchten.
- Der VM-Agent.

Die Erweiterung "VMAccess" muss installiert sein, bevor Sie es verwenden können. Solange der VM-Agent auf dem virtuellen Computer installiert ist, wird die Erweiterung beim Ausführen eines Azure PowerShell-Befehls, der das Cmdlet **Set-AzureVMExtension** verwendet, automatisch geladen.

Überprüfen Sie zunächst, ob der VM-Agent bereits installiert ist. Fügen Sie den Clouddienstnamen und den Namen des virtuellen Computers hinzu, und führen Sie dann die folgenden Befehle an einer Azure PowerShell-Eingabeaufforderung mit Administratorrechten aus. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$csName = "<cloud service name>"
	$vmName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $csName -Name $vmName
	write-host $vm.VM.ProvisionGuestAgent

Wenn Sie den Namen des Clouddiensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.

Der Befehl **write-host** zeigt **True** an, wenn der VM-Agent installiert ist. Wenn **False** angezeigt wird, nutzen Sie die Anweisungen und den Link zum Download im Azure-Blogbeitrag VM [Agent and Extensions – Part 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) (in englischer Sprache).

Wenn Sie die virtuelle Maschine mit dem Azure-Portal erstellt haben, führen Sie den folgenden zusätzlichen Befehl aus:

	$vm.GetInstance().ProvisionGuestAgent = $true

Dieser Befehl verhindert den Fehler "Provision Guest Agent muss für das VM-Objekt aktiviert sein, bevor Sie die IaaS VM Access-Erweiterung festlegen" bei der Ausführung des Befehls **Set-AzureVMExtension** in den folgenden Abschnitten.

Nun können Sie diese Aufgaben ausführen:

- Setzen Sie das Kennwort eines lokalen Administratorkontos zurück.
- Setzen Sie die Konfiguration des Remotedesktopdiensts zurück.

## Zurücksetzen des Kennworts eines lokalen Administratorkontos

Fügen Sie den Namen des aktuellen lokalen Administratorkontos und das neue Kennwort hinzu, und führen Sie die folgenden Befehle aus.

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Wenn Sie einen anderen Namen als das aktuelle Konto eingeben, benennt die VMAccess-Erweiterung das lokale Administratorkonto um, weist das Kennwort diesem Konto zu und gibt einen Befehl zum Abmelden des Remotedesktops aus.
- Wenn das lokale Administratorkonto deaktiviert ist, wird es durch die VMAccess-Erweiterung aktiviert.

Diese Befehle setzen auch die Konfiguration des Remotedesktopdiensts zurück.

## Zurücksetzen der Konfiguration des Remotedesktopdiensts

Führen Sie den folgenden Befehl zum Zurücksetzen der Konfiguration des Remotedesktopdiensts aus.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Die VMAccess-Erweiterung führt diese beiden Befehle auf dem virtuellen Computer aus:

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Dieser Befehl aktiviert die integrierte Windows-Firewallgruppe, die den eingehenden Remotedesktop-Datenverkehr über TCP-Port 3389 zulässt.

- **Set-ItemProperty -Path 'HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Dieser Befehl legt den Registrierungswert "fDenyTSConnections" auf 0 fest und aktiviert damit Remotedesktopverbindungen.

Wenn das Problem mit dem Zugriff auf Remotedesktop dadurch nicht behoben werden kann, führen Sie das[ Azure IaaS-Diagnosepaket (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) aus.

1.	Klicken Sie im Diagnosepaket auf **Diagnosepaket Microsoft Azure IaaS (Windows)**, um eine neue Diagnosesitzung zu erstellen.
2.	Wählen Sie auf der Seite **Welches der folgenden Probleme tritt mit Ihrem virtuellen Azure-Computer auf?** das Problem **RDP-Verbindung mit einem virtuellen Azure-Computer (Neustart erforderlich)**.

Weitere Informationen finden Sie in der Knowledge Base im Artikel [Microsoft Azure IaaS (Windows)-Diagnosepaket](http://support.microsoft.com/kb/2976864).

Wenn Sie das Azure IaaS-Diagnosepaket (Windows) nicht ausführen konnten oder das Problem dadurch nicht behoben wurde, finden Sie weitere Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer unter Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Zusätzliche Ressourcen

[Azure-VM-Erweiterungen und Features](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md)

<!---HONumber=Oct15_HO4-->