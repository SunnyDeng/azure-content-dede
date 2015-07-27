<properties
	pageTitle="Verwalten von Registrierung und Schutz" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer auf lokalen Servern zu Azure oder einem sekundären Datencenter. In diesem Artikel erfahren Sie, wie Sie die Registrierung von Servern im Site Recovery-Tresor aufheben und den Schutz für virtuelle Computer und physische Server deaktivieren." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/08/2015" 
	ms.author="raynew"/>

# Verwalten von Registrierung und Schutz

In diesem Artikel erfahren Sie, wie Sie die Registrierung von Servern im Site Recovery-Tresor aufheben und wie Sie den Site Recovery-Schutz für virtuelle Computer deaktivieren. Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Aufheben der Registrierung eines VMM-Servers

Wenn Sie die Registrierung eines VMM-Servers in einem Tresor aufheben möchten, müssen Sie den Server im Azure Site Recovery-Portal auf der Registerkarte **Server** löschen. Beachten Sie Folgendes:

-  **Verbundener VMM-Server**: Es empfiehlt sich, die Registrierung des VMM-Servers aufzuheben, während dieser mit Azure verbunden ist. Dadurch wird sichergestellt, dass Einstellungen auf dem lokalen VMM-Server sowie die zugeordneten VMM-Server (VMM-Server mit Clouds, die Clouds auf dem zu löschenden Server zugeordnet sind) ordnungsgemäß bereinigt werden. Nicht verbundene Server sollten nur im Falle eines dauerhaften Konnektivitätsproblems entfernt werden.
- **Unverbundener VMM-Server**: Wenn der VMM-Server beim Löschen nicht verbunden ist, müssen Sie manuell ein Bereinigungsskript ausführen. Dieses Skript finden Sie im [Microsoft-Katalog](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439). Notieren Sie sich für die manuelle Bereinigung die VMM-ID des Servers.
- **VMM-Server im Cluster**: Führen Sie folgende Schritte aus, wenn Sie die Registrierung eines in einem Cluster bereitgestellten VMM-Servers aufheben müssen:

	- Ist der Server verbunden, löschen Sie den verbundenen VMM-Server auf der Registerkarte **Server**. Melden Sie sich zum Deinstallieren des Anbieters bei jedem Clusterknoten an, und deinstallieren Sie ihn über die Systemsteuerung. Führen Sie für alle passiven Knoten des Clusters das im vorherigen Abschnitt erwähnte Bereinigungsskript aus, um die Registrierungseinträge zu löschen.
	- Ist der Server nicht verbunden, müssen Sie das Bereinigungsskript auf allen Clusterknoten ausführen.

### Aufheben der Registrierung eines nicht verbundenen VMM-Servers

Führen Sie auf dem zu entfernenden VMM-Server die folgenden Schritte aus:

1. Heben Sie die Registrierung des VMM-Servers im Azure-Portal auf.
2. Laden Sie auf dem VMM-Server das Bereinigungsskript herunter.
3. Öffnen Sie PowerShell mit der Option „Als Administrator ausführen“, um die Ausführungsrichtlinie für den Standardbereich (LocalMachine) zu ändern.
4. Folgen Sie den Anweisungen des Skripts. 

Gehen Sie auf VMM-Servern mit Clouds, die mit Clouds auf dem zu entfernenden Server gekoppelt sind, wie folgt vor:

1. Führen Sie das Bereinigungsskript sowie die Schritte 2 bis 4 aus.
2. Geben Sie die VMM-ID für den VMM-Server an, dessen Registrierung aufgehoben wurde. 
3. Dieses Skript entfernt die Registrierungsinformationen für den VMM-Server sowie die Informationen zur Cloud-Kopplung.


## Aufheben der Registrierung eines Hyper-V-Servers an einem Hyper-V-Standort

Wenn Azure Site Recovery zum Schutz virtueller Computer auf einem Hyper-V-Server an einem Hyper-V-Standort (ohne VMM-Server) bereitgestellt wurde, können Sie die Registrierung eines Hyper-V-Servers im Tresor wie folgt aufheben:

1. Deaktivieren Sie den Schutz für virtuelle Computer auf dem Hyper-V-Server.
2. Wählen Sie im Azure Site Recovery-Portal auf der Registerkarte **Server** den Server aus, und klicken Sie auf „Löschen“. Der Server muss zu diesem Zeitpunkt nicht mit Azure verbunden sein.
3. Führen Sie das folgende Skript aus, um die Einstellungen auf dem Server zu bereinigen und die Registrierung im Tresor aufzuheben: 

	    pushd .
	    try
	    {
		     $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
		     $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
		     $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
		     $isAdmin=$principal.IsInRole($administrators)
		     if (!$isAdmin)
		     {
		        "Please run the script as an administrator in elevated mode."
		        $choice = Read-Host
		        return;       
		     }
	
		    $error.Clear()    
			"This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
			$choice =  Read-Host
		
			if (!($choice -eq 'Y' -or $choice -eq 'y'))
			{
			"Stopping cleanup."
			return;
			}
		
			$serviceName = "dra"
			$service = Get-Service -Name $serviceName
			if ($service.Status -eq "Running")
		    {
				"Stopping the Azure Site Recovery service..."
				net stop $serviceName
			}
		
		    $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
			$registrationPath = $asrHivePath + '\Registration'
			$proxySettingsPath = $asrHivePath + '\ProxySettings'
		    $draIdvalue = 'DraID'
	    
		    if (Test-Path $asrHivePath)
		    {
		        if (Test-Path $registrationPath)
		        {
		            "Removing registration related registry keys."	
			        Remove-Item -Recurse -Path $registrationPath
		        }
	
		        if (Test-Path $proxySettingsPath)
	        {
			        "Removing proxy settings"
			        Remove-Item -Recurse -Path $proxySettingsPath
		        }
	
		        $regNode = Get-ItemProperty -Path $asrHivePath
		        if($regNode.DraID -ne $null)
		        {            
		            "Removing DraId"
		            Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
		        }
			    "Registry keys removed."
		    }
	
		    # First retrive all the certificates to be deleted
			$ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
			# Open a cert store object
			$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
			$store.Open('ReadWrite')
			# Delete the certs
			"Removing all related certificates"
		    foreach ($cert in $ASRcerts)
		    {
			    $store.Remove($cert)
		    }
	    }catch
	    {	
		    [system.exception]
		    Write-Host "Error occured" -ForegroundColor "Red"
		    $error[0] 
		    Write-Host "FAILED" -ForegroundColor "Red"
	    }
	    popd


## Beenden des Schutzes für einen virtuellen Hyper-V-Computer

Wenn Sie einen virtuellen Hyper-V-Computer nicht mehr schützen möchten, müssen Sie den Schutz dafür entfernen. Je nachdem, wie Sie den Schutz aufheben, müssen Sie möglicherweise die Sicherheitseinstellungen auf dem Computer manuell bereinigen.

### Schutz entfernen

1. Wählen Sie in den Cloudeigenschaften auf der Registerkarte **Virtuelle Computer** den virtuellen Computer aus, und klicken Sie auf **Entfernen**.
2. Auf der Seite **Entfernen des virtuellen Computers bestätigen** stehen mehrere Optionen zur Verfügung:

	- **Schutz deaktivieren**: Wenn Sie diese Option aktivieren und speichern, wird der virtuelle Computer nicht mehr durch Site Recovery geschützt. Die Sicherheitseinstellungen für den virtuellen Computer werden automatisch bereinigt.
	- **Aus dem Tresor entfernen**: Wenn Sie diese Option auswählen, wird der virtuelle Computer nur aus dem Site Recovery-Tresor entfernt. Lokale Sicherheitseinstellungen für den virtuellen Computer sind nicht betroffen. Sie müssen die Einstellungen manuell bereinigen, um die Schutzeinstellungen und den virtuellen Computer aus dem Azure-Abonnement zu entfernen sowie die Schutzeinstellungen, die Sie zum manuellen Bereinigen (wie unten beschrieben) benötigt werden.

Wenn Sie den virtuellen Computer und dessen Festplatten löschen, werden sie am Zielspeicherort entfernt.

### Manuelles Bereinigen der Schutzeinstellungen (zwischen VMM-Standorten)

Bei Verwendung der Option **Verwaltung des virtuellen Computers beenden** müssen die Einstellungen manuell bereinigt werden:

1. Führen Sie über die VMM-Konsole auf dem primären Server das folgende Skript aus, um die Einstellungen für den primären virtuellen Computer zu bereinigen. Klicken Sie in der VMM-Konsole auf die Schaltfläche „PowerShell“, um die VMM-PowerShell-Konsole zu öffnen. Ersetzen Sie „SQLVM1“ durch den Namen des virtuellen Computers.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Führen Sie auf dem sekundären VMM-Server das folgende Skript aus, um die Einstellungen für den sekundären virtuellen Computer zu bereinigen:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. Aktualisieren Sie anschließend auf dem sekundären VMM-Server die virtuellen Computer auf dem Hyper-V-Hostserver, damit der sekundäre virtuelle Computer in der VMM-Konsole neu erkannt wird.
4. Mit den obigen Schritten werden die Replikationseinstellungen nur für den VMM-Server gelöscht. Wenn Sie die Replikation des virtuellen Computers für den virtuellen Computer entfernen möchten, müssen Sie die folgenden Schritte für den primären sowie für den sekundären virtuellen Computer ausführen. Führen Sie zum Entfernen der Replikation das folgende Skript aus, und ersetzen Sie dabei "SQLVM1" durch den Namen des virtuellen Computers.

	    Remove-VMReplication –VMName “SQLVM1”


### Manuelles Bereinigen der Schutzeinstellungen (zwischen lokalen VMM-Standorten und Azure)

1. Führen Sie auf dem VMM-Quellserver das folgende Skript aus, um die Einstellungen für den primären virtuellen Computer zu bereinigen:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Mit den obigen Schritten werden die Replikationseinstellungen nur für den VMM-Server gelöscht. Nachdem Sie die Replikation vom VMM-Server entfernt haben, müssen Sie mithilfe des folgenden Skripts die Replikation für den virtuellen Computer entfernen, der auf dem Hyper-V-Hostserver ausgeführt wird. Ersetzen Sie „SQLVM1“ durch den Namen des virtuellen Computers und „host01.contoso.com“ durch den Namen des Hyper-V-Hostservers.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### Manuelles Bereinigen der Schutzeinstellungen (zwischen Hyper-V-Standorten und Azure)

1. Verwenden Sie auf dem Hyper-V-Hostquellserver das folgende Skript, um die Replikation für den virtuellen Computer zu entfernen. Ersetzen Sie „SQLVM1“ durch den Namen des virtuellen Computers.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## Beenden Sie den Schutz für eine virtuelle VMware-Maschine oder für einen physischen Server

Wenn Sie den Schutz für eine virtuelle VMware-Maschine oder einen physischen Server beenden möchten, müssen Sie den Schutz dafür entfernen. Je nachdem, wie Sie den Schutz aufheben, müssen Sie möglicherweise die Sicherheitseinstellungen auf dem Computer manuell bereinigen.

### Schutz entfernen

1. Wählen Sie in den Cloudeigenschaften auf der Registerkarte **Virtuelle Computer** den virtuellen Computer aus, und klicken Sie auf **Entfernen**.
2. Wählen Sie in **Virtuellen Computer entfernen** eine der folgenden Optionen:

	- **Schutz deaktivieren (verwendet für die Wiederherstellung eines Drilldowns und der Änderung des Volumes)** – Sie werden diese Optione nur dann sehen und aktivieren können, wenn Sie:
		- **Ändern der Volumegröße des virtuellen Computers**– Wenn Sie die Größe eines Volumes ändern, wechselt der virtuelle Computer in einen kritischen Zustand. Wählen Sie diese Option, falls dies der Fall ist. Der Schutz wird unter Beibehaltung der Wiederherstellungspunkte in Azure deaktiviert. Wenn Sie den Schutz für den Computer erneut aktivieren, werden die Daten für das angepasste Volume an Azure übertragen werden.
		- **Ausführen eines Failovers**– Nachdem Sie Ihre Umgebung durch Ausführen eines Failovers von lokalen virtuellen VMware-Maschinen oder physischen Servern nach Azure getestet haben, wählen Sie diese Option, um Ihre lokalen virtuellen Computer wieder zu schützen. Mit dieser Option werden die einzelnen virtuellen Computer deaktiviert, sodass Sie den Schutz für diese wieder aktivieren müssen. Beachten Sie Folgendes:
			- Das Deaktivieren des virtuellen Computers mit dieser Einstellung wirkt sich nicht auf den replizierten virtuellen Computer in Azure aus.
			- Sie dürfen den Mobilitätsdienst nicht von der virtuellen Maschine deinstallieren.
	
	- **Schutz deaktivieren**: Wenn Sie diese Option aktivieren und speichern, wird der Computer nicht mehr durch Site Recovery geschützt. Die Sicherheitseinstellungen für den Computer werden automatisch bereinigt.
	- **Aus dem Tresor entfernen**: Wenn Sie diese Option auswählen, wird der Computer nur aus dem Site Recovery-Tresor entfernt. Lokale Sicherheitseinstellungen für den Computer sind nicht betroffen. Um die Einstellungen vom Computer und den virtuellen Computer aus dem Azure-Abonnement zu entfernen, müssen Sie die Einstellungen bereinigen, indem Sie den Mobilitätsdienst deinstallieren. ![Optionen entfernen](./media/site-recovery-manage-registration-and-protection/RegistrationProtection_RemoveVM.png)

<!---HONumber=July15_HO2-->