<properties 
   pageTitle="Verschieben eines virtuellen Computers oder einer Rolleninstanz in ein anderes Subnetz"
   description="Hier erfahren Sie, wie virtuelle Computer und Rolleninstanzen in ein anderes Subnetz verschoben werden."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Verschieben eines virtuellen Computers oder einer Rolleninstanz in ein anderes Subnetz

Sie können PowerShell verwenden, um Ihre virtuellen Computer im selben virtuellen Netzwerk (VNet) zwischen Subnetzen zu verschieben. Rolleninstanzen können anstatt mit PowerShell mithilfe der CSCFG-Datei verschoben werden.

Gründe für das Verschieben virtueller Computer in ein anderes Subnetz Die Subnetzmigration ist sinnvoll, wenn das ältere Subnetz zu klein ist und aufgrund von vorhandenen virtuellen Computer in diesem Subnetz nicht erweitert werden kann. In diesem Fall können Sie ein neues, größeres Subnetz erstellen und die virtuellen Computer zum neuen Subnetz migrieren. Nach Abschluss der Migration können Sie das alte leere Subnetz löschen.

## Verschieben eines virtuellen Computers in ein anderes Subnetz

Führen Sie zum Verschieben eines virtuellen Computers das PowerShell-Cmdlet „Set-AzureSubnet“ aus. Verwenden Sie dabei das unten angegebene Beispiel als Vorlage. Im folgenden Beispiel verschieben wir „TestVM“ vom aktuellen Subnetz in Subnetz-2. Bearbeiten Sie das Beispiel Ihrer Umgebung entsprechend. Hinweis: Jedes Mal, wenn Sie das Update-AzureVM-Cmdlet als Teil einer Prozedur ausführen, wird der virtuelle Computer im Rahmen des Aktualisierungsprozesses neu gestartet.

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

Wenn Sie eine statische DIP für Ihren virtuellen Computer angegeben haben, müssen Sie diese Einstellung deaktivieren, bevor Sie den virtuellen Computer in ein neues Subnetz verschieben können. Verwenden Sie in diesem Fall Folgendes:

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## So verschieben Sie eine Rolleninstanz in ein anderes Subnetz

Bearbeiten Sie zum Verschieben einer Rolleninstanz die CSCFG-Datei. Im folgenden Beispiel verschieben wir „Role0“ im virtuellen Netzwerk *VNETName* vom aktuellen Subnetz in *Subnetz-2*. Da die Rolleninstanz bereits bereitgestellt wurde, ändern Sie einfach den Subnetznamen in „Subnetz-2“. Bearbeiten Sie das Beispiel Ihrer Umgebung entsprechend.

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=AcomDC_1217_2015-->