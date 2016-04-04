<properties
	pageTitle="Zurücksetzen des Kennworts oder von Remotedesktop auf einen virtuellen Windows-Computer | Microsoft Azure"
	description="Setzen Sie das Administratorkennwort oder die Remotedesktopdienste auf einem mit dem Ressourcen-Manager-Bereitstellungsmodell erstellten virtuellen Windows-Computer zurück."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="dkshir"/>

# Zurücksetzen des Remotedesktopdiensts oder seines Anmeldekennworts in einer Windows-basierten Azure-VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


Wenn Sie ein Kennwort vergessen haben, oder ein Problem mit der Konfiguration des Remotedesktopdiensts vorliegt, und Sie daher keine Verbindung mit einem virtuellen Windows-Computer herstellen können, erfahren Sie in diesem Artikel, wie Sie das lokale Administratorkennwort oder die Konfiguration des Remotedesktopdiensts zurücksetzen können.

Abhängig vom Bereitstellungsmodell des virtuellen Computers können Sie entweder das Portal oder die Erweiterung „VMAccess“ in Azure PowerShell verwenden. Wenn Sie Azure PowerShell verwenden, stellen Sie sicher, dass das neueste Azure PowerShell-Modul auf Ihrem Arbeitscomputer installiert ist, und Sie bei Ihrem Azure-Abonnement angemeldet sind. Ausführliche Schrittbeschreibungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).


> [AZURE.TIP] Sie können die von Ihnen installierte Azure PowerShell-Version mit dem Befehl `Get-Module azure | format-table version` prüfen.


## Windows-VMs im klassischen Bereitstellungsmodell

### Azure-Portal

Für die mit dem klassischen Bereitstellungsmodell erstellten virtuellen Computer können Sie das [Azure-Portal](https://portal.azure.com) zum Zurücksetzen des Remotedesktopdiensts verwenden. Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > *Ihr virtueller Windows-Computer* > **Remotezugriff zurücksetzen...**. Die folgende Seite wird angezeigt.


![](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Sie können auch versuchen, den Namen und das Kennwort des lokalen Administratorkontos zurückzusetzen. Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > *Ihr virtueller Windows-Computer* > **Alle Einstellungen** > **Kennwort zurücksetzen**. Die folgende Seite wird angezeigt.

![](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Klicken Sie nach Eingabe des neuen Benutzernamens und Kennworts auf **Speichern**.

### VMAccess-Erweiterung und PowerShell

Stellen Sie sicher, dass der VM-Agent auf dem virtuellen Computer installiert ist. Die Erweiterung „VMAccess“ muss nicht installiert sein, bevor Sie ihn benutzen können, solange der VM-Agent verfügbar ist. Stellen Sie mit folgendem Befehl sicher, dass der VM-Agent bereits installiert ist. Ersetzen Sie „myCloudService“ und „myVM“ durch den Namen Ihres Clouddiensts bzw. Ihrer VM. Sie finden diese durch Ausführen von `Get-AzureVM` ohne Parameter.

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Der Befehl **write-host** zeigt **True** an, wenn der VM-Agent installiert ist. Wenn **False** angezeigt wird, nutzen Sie die Anweisungen und den Link zum Download im Azure-Blogbeitrag VM [Agent and Extensions – Part 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) (in englischer Sprache).

Wenn Sie den virtuellen Computer mit dem Portal erstellt haben, überprüfen Sie, ob `$vm.GetInstance().ProvisionGuestAgent` **True** zurückgibt. Legen Sie den Wert andernfalls mit folgendem Befehl fest:

	$vm.GetInstance().ProvisionGuestAgent = $true

Dieser Befehl verhindert den Fehler "Provision Guest Agent muss für das VM-Objekt aktiviert sein, bevor Sie die IaaS VM Access-Erweiterung festlegen" bei der Ausführung des Befehls **Set-AzureVMExtension** in den folgenden Abschnitten.

#### **Zurücksetzen des Kennworts eines lokalen Administratorkontos**

Erstellen Sie Anmeldeinformationen mit dem Namen des aktuellen lokalen Administratorkontos und einem neuen Kennwort, und führen Sie dann `Set-AzureVMAccessExtension` wie folgt aus.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Wenn Sie einen anderen Namen als das aktuelle Konto eingeben, benennt die VMAccess-Erweiterung das lokale Administratorkonto um, weist das Kennwort diesem Konto zu und gibt einen Befehl zum Abmelden des Remotedesktops aus. Wenn das lokale Administratorkonto deaktiviert ist, wird es durch die VMAccess-Erweiterung aktiviert.

Diese Befehle setzen auch die Konfiguration des Remotedesktopdiensts zurück.

#### **Zurücksetzen der Konfiguration des Remotedesktopdiensts**

Führen Sie den folgenden Befehl zum Zurücksetzen der Konfiguration des Remotedesktopdiensts aus.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Die VMAccess-Erweiterung führt diese beiden Befehle auf dem virtuellen Computer aus:

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Dieser Befehl aktiviert die integrierte Windows-Firewallgruppe, die den eingehenden Remotedesktop-Datenverkehr über TCP-Port 3389 zulässt.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Dieser Befehl legt den Registrierungswert "fDenyTSConnections" auf 0 fest und aktiviert damit Remotedesktopverbindungen.


## Windows-VMs im Resource Manager-Bereitstellungsmodell

Das Azure-Portal unterstützt derzeit nicht das Zurücksetzen der Remotezugriffs- oder Anmeldeinformationen für virtuelle Computer, die mit dem Resource Manager erstellt wurden.


### VMAccess-Erweiterung und PowerShell

Stellen Sie sicher, dass Azure PowerShell 1.0 oder höher installiert ist, und Sie sich bei Ihrem Konto mithilfe des `Login-AzureRmAccount`-Cmdlets angemeldet haben.

#### **Zurücksetzen des Kennworts eines lokalen Administratorkontos**

Sie können das Administratorkennwort und/oder den Benutzernamen mithilfe des PowerShell-Befehls [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) zurücksetzen.

Erstellen Sie Ihre lokalen Administratorkonto-Anmeldeinformationen mithilfe des folgenden Befehls:

	$cred=Get-Credential

Wenn Sie einen anderen Namen als den des aktuellen Kontos eingeben, benennt der Befehl der VMAccess-Erweiterung unten das lokale Administratorkonto um, weist das Kennwort diesem Konto zu und gibt einen Befehl zum Abmelden des Remotedesktops aus. Wenn das lokale Administratorkonto deaktiviert ist, wird es durch die VMAccess-Erweiterung aktiviert.
	
Verwenden Sie die VMAccess-Erweiterung, um die neuen Anmeldeinformationen wie folgt festlegen:

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password


Ersetzen Sie `myRG`, `myVM`, `myVMAccess` und den Speicherort durch für Ihr Setup relevante Werte.


#### **Zurücksetzen der Konfiguration des Remotedesktopdiensts**

Sie können den Remotezugriff auf Ihre VM zurücksetzen, indem Sie entweder [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) oder „Set-AzureRmVMAccessExtension“ wie folgt verwenden. Ersetzen Sie `myRG`, `myVM`, `myVMAccess` und den Speicherort durch Ihre eigenen Werte.

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus

ODER<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus

	
> [AZURE.TIP] Sowohl `Set-AzureRmVMAccessExtension` als auch `Set-AzureRmVMExtension` fügt einen neu benannten VM-Zugriffs-Agent auf dem virtuellen Computer hinzu. Zu jedem Zeitpunkt kann eine VM nur einen einzelnen VM-Zugriffs-Agent haben. Um die Eigenschaften des VM-Zugriffs-Agents nacheinander einzustellen, entfernen Sie den zuvor festgelegten Zugriffs-Agent entweder mit `Remove-AzureRmVMAccessExtension` oder `Remove-AzureRmVMExtension`. Ab Azure PowerShell Version 1.2.2 können Sie diesen Schritt vermeiden, wenn Sie `Set-AzureRmVMExtension` mit einer `-ForceRerun`-Option verwenden. Verwenden Sie unbedingt den gleichen Namen für den VM-Zugriffs-Agent, den Sie mit dem vorherigen Befehl mit der `-ForceRerun`-Option festgelegt haben.


Wenn Sie weiterhin keinen Remotezugriff auf den virtuellen Computer ausführen können, finden Sie weitere Schritte unter [Troubleshoot Remote Desktop connections to a Windows-based Azure virtual machine](virtual-machines-windows-troubleshoot-rdp-connection.md) (Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Computer in Azure).


## Zusätzliche Ressourcen

[Azure-VM-Erweiterungen und Features](virtual-machines-windows-extensions-features.md)

[Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0323_2016-->