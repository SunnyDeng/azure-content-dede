<properties 
   pageTitle="Reservierte IP"
   description="Informationen zu reservierten IPs, VIP, ILPIP und deren Verwaltung"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Reservierte IP – Übersicht
In Azure fallen IP-Adressen in zwei Kategorien: dynamisch und reserviert. Öffentliche IP-Adressen, die von Azure verwaltet werden, sind standardmäßig dynamische IP-Adressen. Daher kann sich die IP-Adresse, die für einen bestimmten Cloud-Dienst (VIP) oder zum direkten Zugreifen auf einen virtuellen Computer oder eine Rolleninstanz (ILPIP) genutzt wird, von Zeit zu Zeit ändern, wenn Ressourcen heruntergefahren oder neu zugeordnet werden.

Sie können eine IP-Adresse reservieren, um zu verhindern, dass sie sich ändert. Reservierte IPs können nur als VIP verwendet werden. So wird sichergestellt, dass die IP-Adresse für den Cloud-Dienst sich auch dann nicht ändert, wenn Ressourcen heruntergefahren oder neu zugeordnet werden. Außerdem können Sie vorhandene dynamische IP-Adressen, die als VIP verwendet werden, in eine reservierte IP-Adresse konvertieren.

>[AZURE.WARNING]Sie können einer VIP nur eine reservierte IP-Adresse zuweisen.

## Wann benötige ich eine reservierte IP-Adresse?
- **Sie möchten sicherstellen, dass die IP-Adresse in Ihrem Abonnement reserviert ist**. Wenn Sie eine IP-Adresse reservieren möchten, die Ihr Abonnement unter keinen Umständen verlässt, sollten Sie eine reservierte öffentliche IP-Adresse verwenden.  
- **Sie möchten, dass die IP-Adresse Ihrem Cloud-Dienst auch im beendeten oder neu zugeordneten Zustand (VMs) zugewiesen bleibt**. Auf Ihren Dienst soll mit einer IP-Adresse zugegriffen werden, die sich auch dann nicht ändert, wenn virtuelle Computer im Cloud-Dienst beendet oder neu zugeordnet werden.
- **Sie möchten sicherstellen, dass für ausgehenden Datenverkehr von Azure eine vorhersagbare IP-Adresse verwendet wird**. Unter Umständen haben Sie Ihre lokale Firewall so konfiguriert, dass nur Datenverkehr von bestimmten IP-Adressen zugelassen wird. Bei Reservierung einer IP-Adresse kennen Sie die IP-Quelladresse und müssen Ihre Firewallregeln bei einer IP-Änderung nicht aktualisieren.

## Häufig gestellte Fragen
1. Kann ich eine reservierte IP-Adresse für alle Azure-Dienste verwenden?  
  - Reservierte IPs können nur für virtuelle Computer und Cloud-Dienst-Instanzenrollen verwendet werden.
1. Wie viele reservierte IP-Adressen können verwendet werden?  
  - Derzeit können mit allen Azure-Abonnements 20 reservierte IPs verwendet werden. Sie können aber auch zusätzliche reservierte IP-Adressen anfordern. Weitere Informationen finden Sie unter [Abonnement- und Diensteinschränkungen](../azure-subscription-service-limits/).
1. Fällt für reservierte IPs eine Gebühr an? 
  - Preisinformationen finden Sie unter [Preise für reservierte IP-Adressen](http://go.microsoft.com/fwlink/?LinkID=398482).
1. Wie viele reservierte IP-Adressen können einem Cloud-Dienst zugewiesen werden? 
  - Pro Endgerät in einem Cloud-Dienst ist nur eine IP-Adresse zulässig. Dabei spielt es keine Rolle, ob die IP-Adresse reserviert ist oder nicht. Das Reservieren einer IP-Adresse berechtigt Sie nicht dazu, einem Cloud-Dienst weitere IP-Adressen hinzuzufügen.
1. Wie wird eine IP-Adresse reserviert? 
  - Sie können PowerShell oder die [Azure-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn722420.aspx) nutzen, um eine reservierte IP für eine bestimmte Region anzufordern. Azure reserviert eine IP-Adresse aus dieser Region und ordnet sie Ihrem Abonnement zu. Sie können die reservierte IP-Adresse dann in dieser Region verwenden. Das Reservieren einer IP-Adresse ist nicht möglich, indem Sie das Verwaltungsportal verwenden.
1. Ist die Verwendung mit auf Affinitätsgruppen basierenden VNets möglich? 
  - Reservierte IP-Adressen werden nur in regionalen VNets unterstützt. Für VNets, die Affinitätsgruppen zugeordnet sind, besteht keine Unterstützung. Weitere Informationen zum Zuordnen eines VNets zu einer Region oder Affinitätsgruppe finden Sie unter [Informationen zu regionalen VNets und Affinitätsgruppen](https://msdn.microsoft.com/library/azure/jj156085.aspx). 

## Verwalten von reservierten VIPs

Bevor Sie reservierte IPs verwenden können, müssen Sie diese Ihrem Abonnement hinzufügen. Führen Sie den folgenden PowerShell-Befehl aus, um eine reservierte IP aus dem Pool mit den öffentlichen IP-Adressen zu erstellen, die für den Standort *Central US* verfügbar sind:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location “Central US”

Beachten Sie dabei, dass Sie nicht angeben können, welche IP-Adresse reserviert wird. Führen Sie den folgenden PowerShell-Befehl aus, und notieren Sie sich die Werte für *ReservedIPName* und *Address*, um anzuzeigen, welche IP-Adressen in Ihrem Abonnement reserviert sind:

	Get-AzureReservedIP

	ReservedIPName       : MyReservedIP
	Address              : 23.101.114.211
	Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
	Label                : 
	Location             : Central US
	State                : Created
	InUse                : False
	ServiceName          : 
	DeploymentName       : 
	OperationDescription : Get-AzureReservedIP
	OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
	OperationStatus      : Succeeded

Nachdem eine IP-Adresse reserviert wurde, bleibt sie Ihrem Abonnement so lange zugeordnet, bis Sie sie löschen. Führen Sie den folgenden PowerShell-Befehl aus, um die obige reservierte IP zu löschen:

	Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## Gewusst wie: Zuordnen einer reservierten IP zu einem neuen Cloud-Dienst
Mit dem unten angegebenen Skript wird eine neue reservierte IP erstellt, die dann einem neuen Cloud-Dienst mit dem Namen *TestService* zugeordnet wird.

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location “Central US”
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE]Wenn Sie eine reservierte IP für die Verwendung mit einem Cloud-Dienst erstellen, müssen Sie auf den virtuellen Computer für die eingehende Kommunikation weiterhin mit *VIP:&lt;Portnummer>* verweisen. Das Reservieren einer IP-Adresse bedeutet nicht, dass Sie eine direkte Verbindung mit dem virtuellen Computer herstellen können. Die reservierte IP wird dem Cloud-Dienst zugewiesen, für den der virtuelle Computer bereitgestellt wurde. Wenn Sie direkt per IP eine Verbindung mit dem virtuellen Computer herstellen möchten, müssen Sie eine öffentliche IP auf Instanzebene konfigurieren. Eine öffentliche IP auf Instanzebene ist eine Art von öffentlicher IP (als ILPIP bezeichnet), die Ihrem virtuellen Computer direkt zugewiesen ist. Sie kann nicht reserviert werden. Weitere Informationen finden Sie unter [Öffentliche IP auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip).

## Gewusst wie: Entfernen einer reservierten IP aus einer aktiven Bereitstellung
Führen Sie den folgenden PowerShell-Befehl aus, um die reservierte IP, die dem oben per Skript erstellten neuen Dienst hinzugefügt wurde, zu entfernen:

	Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE]Wenn Sie eine reservierte IP aus einer aktiven Bereitstellung entfernen, wird die Reservierung nicht aus Ihrem Abonnement entfernt. Die IP-Adresse wird lediglich frei und kann von einer anderen Ressource in Ihrem Abonnement verwendet werden.

## Gewusst wie: Zuordnen einer reservierten IP zu einer aktiven Bereitstellung
Mit dem unten angegebenen Skript wird ein neuer Cloud-Dienst mit dem Namen *TestService2* und dem neuen virtuellen Computer*TestVM2* erstellt. Anschließend wird die vorhandene reservierte IP mit dem Namen *MyReservedIP* dem Cloud-Dienst zugeordnet.

	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService2 -Location "Central US"
	Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## Gewusst wie: Zuordnen einer reservierten IP zu einem Cloud-Dienst mit einer Dienstkonfigurationsdatei
Sie können eine reservierte IP einem Cloud-Dienst auch zuordnen, indem Sie eine Dienstkonfigurationsdatei (CSCFG) verwenden. Im folgenden XML-Beispielcode wird veranschaulicht, wie Sie einen Cloud-Dienst für die Verwendung einer reservierten VIP mit dem Namen *MyReservedIP* konfigurieren:
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
	  <NetworkConfiguration>
	    <AddressAssignments>
	      <ReservedIPs>
	       <ReservedIP name="MyReservedIP"/>
	      </ReservedIPs>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Nächste Schritte

[Reservierte private IP-Adresse](../virtual-networks-reserved-private-ip)

[Öffentliche IP auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip)

[Reservierte IP-REST-APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)

<!---HONumber=July15_HO2-->