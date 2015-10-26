<properties 
   pageTitle="Herstellen einer Verbindung zwischen klassischen VNets und ARM-VNets in Azure - Lösungshandbuch"
   description="Weitere Informationen zum Erstellen einer VPN-Verbindung zwischen klassischen VNets und neuen VNets"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/18/2015"
   ms.author="telmos" />

# Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets

Azure verfügt zurzeit über zwei Verwaltungsmodi: Azure Service Manager (als "klassisch" bezeichnet) und Azure Resource Manager (ARM). Wenn Sie Azure seit einiger Zeit verwenden, verfügen Sie wahrscheinlich über virtuelle Azure-Computer und Instanzrollen, die in einem klassischen VNet ausgeführt werden. Und Ihre neueren virtuellen Computer und Rolleninstanzen werden ggf. in einem in ARM erstellten VNet ausgeführt.

Unter diesen Umständen sollten Sie sicherstellen, dass die neue Infrastruktur mit Ihren klassischen Ressourcen kommunizieren kann. Sie können zu diesem Zweck eine VPN-Verbindung zwischen den beiden VNets erstellen. Die Abbildung unten zeigt eine Beispielumgebung mit zwei VNets (klassisches und ARM-VNet) mit einer sicheren Tunnelverbindung zwischen den VNets.

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]Dieses Dokument führt Sie zu Testzwecken schrittweise durch eine End-to-End-Lösung. Wenn Sie Ihre VNets bereits eingerichtet haben und mit VPN-Gateways und Site-to-Site-Verbindungen in Azure vertraut sind, lesen Sie [Konfigurieren einer S2S-VPN-Verbindung zwischen einem ARM-VNet und einem klassischen VNet](../virtual-networks-arm-asm-s2s-howto.md).

Sie führen die folgenden Aktionen aus, um dieses Szenario zu testen:

1. [Erstellen einer klassischen VNet-Umgebung](#Create-a-classic-VNet-environment).
2. [Erstellen einer neuen VNet-Umgebung](#Create-a-new-VNet-environment).
3. [Verbinden der beiden VNets](#Connect-the-two-VNets).

Sie führen die oben genannten Schritte zuerst mithilfe der klassischen Azure-Verwaltungstools (z. B. klassisches Portal, Netzwerkkonfigurationsdateien und Azure Service Manager-PowerShell-Cmdlets) aus. Später fahren Sie mit den neuen Verwaltungstools (z. B. dem neuen Azure-Portal, ARM-Vorlagen und ARM-PowerShell-Cmdlets) fort.

>[AZURE.IMPORTANT]Damit die VNets miteinander verbunden werden können, dürfen keine Konflikte der CIDR-Blöcke vorliegen. Stellen Sie sicher, dass jedes VNet über einen eindeutigen CIDR-Block verfügt!

## Erstellen einer klassischen VNet-Umgebung

Sie können eine vorhandenes klassisches VNet zum Herstellen einer Verbindung mit einem neuen ARM-VNet verwenden. In diesem Beispiel erfahren Sie, wie zu Testzwecken ein neues klassisches VNet mit zwei Subnetzen, ein Gateway sowie ein virtueller Computer erstellt werden.

### Schritt 1: Erstellen eines klassischen VNet

Befolgen Sie die Anweisungen unten, um ein neues VNet zu erstellen, das Abbildung 1 oben entspricht.

1. Fügen Sie in einer PowerShell-Konsole Ihr Azure-Konto hinzu, indem Sie den folgenden Befehl ausführen.

		Add-AzureAccount

2. Befolgen Sie die Anweisungen im Anmeldedialogfeld, um sich mit Ihrem Azure-Konto anzumelden.

3. Stellen Sie sicher, dass Sie Azure Service Management-PowerShell-Cmdlets verwenden, indem Sie den folgenden Befehl ausführen.

		Switch-AzureMode AzureServiceManagement

4. Laden Sie die Azure-Netzwerkkonfigurationsdatei herunter, indem Sie den folgenden Befehl ausführen.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. Öffnen Sie die Datei, die Sie soeben heruntergeladen haben, und fügen Sie eine lokale Netzwerksite mit dem Namen*vnet02* hinzu, die *10.2.0.0/16* als ein Adresspräfix und eine beliebige gültige öffentliche IP-Adresse als VPN-Gatewayadresse verwendet. Fügen Sie zu diesem Zweck das Element **LocalNetworkSite** dem Element **LocalNetworkSites** in Ihrer Konfigurationsdatei hinzu. Der Dateicodeausschnitt unten zeigt ein Beispielelement **LocalNetworksSites**.

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. Fügen Sie im Element **VirtualNetworkSites** ein neues virtuelles Netzwerk mit dem Adresspräfix *10.1.0.0/16* und zwei Subnetzen gemäß der Szenarioabbildung oben hinzu. Der Dateicodeausschnitt unten zeigt ein Beispielelement **VirtualNetworkSites**.
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. Speichern Sie die Datei, und laden Sie dann Sie dann in Azure hoch, indem Sie den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie den Dateipfad wie für Ihre Umgebung erforderlich ändern.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### Schritt 2: Erstellen eines virtuellen Computers im klassischen VNet

Befolgen Sie die Anweisungen unten, um einen virtuellen Computer im klassischen VNet mithilfe von Azure Service Manager-PowerShell-Cmdlets zu erstellen.

1. Rufen Sie ein VM-Image aus Azure ab. Der folgende PowerShell-Befehl ruft das aktuellste Windows Server 2012 R2-Image ab, das verfügbar ist.

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. Erstellen Sie ein Speicherkonto zum Speichern der virtuellen Festplatte (VHD) für den virtuellen Computer, indem Sie den folgenden Befehl ausführen.

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  Erstellen Sie den virtuellen Computer, indem Sie die folgenden Befehle ausführen. Stellen Sie sicher, dass Sie die Werte für den Benutzernamen und das Kennwort ersetzen.

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  Verbinden Sie den virtuellen Computer mit *Subnet1*, indem Sie die folgenden Befehle ausführen.

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. Erstellen Sie einen neuen Clouddienst zum Hosten des virtuellen Computers, indem Sie den folgenden Befehl ausführen.

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### Schritt 3: Erstellen eines VPN-Gateways für das klassische VNet 

Befolgen Sie die Anweisungen unten, um das VPN-Gateway für "vnet01" mithilfe des klassischen Azure-Portals zu erstellen.

1. Öffnen Sie das klassische Azure-Portal unter https://manage.windowsazure.com Falls erforderlich, geben Sie Ihre Anmeldeinformationen an.
2. Führen Sie einen Bildlauf nach unten in der Liste **ALLE ELEMENTE** aus, und klicken Sie dann auf**NETZWERKE**.
3. Klicken Sie in der Liste der virtuellen Netzwerke auf **vnet01**, und klicken Sie dann auf**KONFIGURIEREN**.
4. Aktivieren Sie unter **Site-to-Site-Konnektivität** das Kontrollkästchen **Eine Verbindung mit dem lokalen Netzwerk herstellen**.
5. Wählen Sie in der Liste **LOKALES NETZWERK** die Option **vnet02** aus, klicken Sie auf **SPEICHERN**, und klicken Sie dann auf **JA**.
6. Klicken Sie auf**DASHBOARD**, und beachten Sie die Meldung, dass noch kein Gateway erstellt wurde. Abbildung 2 unten zeigt dies.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. Klicken Sie auf **GATEWAY ERSTELLEN** wie in Abbildung 3 unten gezeigt, um ein VPN-Gateway für "vnet01" zu erstellen.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. Klicken Sie in der Liste der Gatewaytypen auf **DYNAMISCH**, und klicken Sie dann auf **JA**. Beachten Sie, dass sich die Dashboardanzeige ändert. Das Gateway wird in Gelb angezeigt, während es erstellt wird.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]Dieser Vorgang kann einige Minuten dauern.

9. Notieren Sie sich die öffentliche IP-Adresse für das Gateway wie unten gezeigt, nachdem es erstellt wurde. Sie benötigen diese Adresse, um später ein lokales Netzwerks für das ARM-VNet zu erstellen.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## Erstellen einer neuen VNet-Umgebung

Da das klassische VNet nun aktiv ist und mit einem virtuellen Computer und einem Gateway ausgeführt wird, wird es Zeit, das ARM-VNet zu erstellen.

### Schritt 1: Erstellen eines neuen VNet in ARM

Befolgen Sie die Anweisungen unten, um das ARM-VNet mit zwei Subnetzen und einem lokalen Netzwerk für das klassische VNet mithilfe einer ARM-Vorlage zu erstellen.

1. Laden Sie die Dateien "azuredeploy.json" und "azuredeploy-parameters.json" von [Git-Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s) herunter.
2. Öffnen Sie die "Datei azuredeploy.json" in Visual Studio, und beachten Sie, dass durch die Vorlage vier Ressourcen erstellt werden: 

	- **Lokales Gateway**: Diese Ressource stellt das Gateway dar, das für das VNet erstellt wird, mit dem eine Verbindung hergestellt werden soll. In diesem Szenario handelt es sich um das Gateway für "vnet01".
	- **Virtuelles Netzwerk**: Diese Ressource stellt ein ARM-VNet dar, das erstellt werden soll. In diesem Szenario ist dies "vnet02".
	- **Öffentliche IP-Adresse des Gateways**: Diese Ressource stellt die öffentliche IP-Adresse für das Gateway dar, das für das ARM-VNet erstellt werden soll. 
	- **Gateway**: Diese Ressource stellt das Gateway dar, das für das ARM-VNet ("vnet02") erstellt werden soll.

3. Beachten Sie die in dieser Datei verwendeten Parameter. Die meisten von ihnen verfügen über einen Standardwert. Sie können diese Werte entsprechend Ihren Anforderungen ändern.

4. Öffnen Sie die Datei "azuredeploy-parameters.json" in Visual Studio. Diese Datei enthält die Werte, die für die Parameter in der Vorlagendatei verwendet werden sollen. Bearbeiten Sie die folgenden Werte, wenn erforderlich.

	- **subscriptionId**: Bearbeiten Sie Ihre Abonnement-ID, und fügen Sie sie ein. Wenn Sie Ihre Abonnement-ID nicht kennen, führen Sie das PowerShell-Cmdlet **Get-AzureSubscription** zum Abrufen Ihrer ID aus.
	- **Standort**: Geben Sie den Azure-Standort an, an dem das VNet erstellt wird. In diesem Szenario ist dies **USA, Mitte**.
	- **virtualNetworkName**: Dies ist der Name des zu erstellenden ARM-VNets. In diesem Szenario ist dies **vnet02**.
	- **localGatewayName**: Dies ist das lokale Netzwerk, mit dem Sie aus Ihrem neuen ARM-VNet eine Verbindung herstellen möchten. In diesem Szenario handelt es sich um **vnet01**.
	- **localGatewayIpAddress**: Dies ist die öffentliche IP-Adresse für das Gateway, das für das Netzwerk erstellt wird, mit dem Sie eine Verbindung herstellen möchten. In diesem Fall ist dies die IP-Adresse, die Sie sich in Schritt 9 oben notiert haben, als Sie das VPN-Gateway für **vnet01** erstellt haben.
	- **localGatewayAddressPrefix**: Dies ist der CIDR-Block für das lokale Netzwerk, mit dem Ihr VNet eine Verbindung herstellt. In diesem Szenario ist das VNet, mit dem Sie eine Verbindung herstellen, **vnet01**, und der CIDR-Block ist **10.1.0.0/16**.
	- **gatewayPublicIPName**: Dies ist der Name des IP-Objekts, das für die öffentliche IP-Adresse des Gateways erstellt wird, das für das ARM-VNet erstellt wird.
	- **gatewayName**: Dies ist der Name des Gatewayobjekts, das für das ARM-VNet erstellt wird.
	- **addressPrefix**: Dies ist der CIDR-Block für das ARM-VNet. In diesem Szenario ist dies **10.2.0.0/16**.
	- **subnet1Prefix**: Dies ist der CIDR-Block für ein Subnetz im ARM-VNet. In diesem Szenario ist dies **10.2.0.0/24**.
	- **gatewaySubnetPrefix**: Dies ist der CIDR-Block für das Gatewaysubnetz im ARM-VNet. In diesem Szenario ist dies **10.2.200.0/29**.
	- **connectionName**: Dies ist der Name des zu erstellenden Verbindungsobjekts.
	- **sharedKey**: Dies ist der gemeinsam verwendete IPSec-Schlüssel für die Verbindung. In diesem Szenario handelt es sich um **abc123**.

5. Führen Sie zum Erstellen des ARM-VNet und der zugehörigen Objekte in einer neuen Ressourcengruppe mit dem Namen **RG1** den folgenden PowerShell-Befehl aus. Stellen Sie sicher, dass Sie den Pfad für die Vorlage und die Parameterdatei ändern.

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]Dieser Vorgang kann einige Minuten dauern.

7. Navigieren Sie in Ihrem Browser zu https://portal.azure.com/, und geben Sie Ihre Anmeldeinformationen ein, sofern erforderlich.
8. Klicken Sie wie unten gezeigt auf die Ressourcengruppenkachel **RG1** im Azure-Portal.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. Beachten Sie die Ressourcen, die mithilfe der ARM-Vorlage der Gruppe hinzugefügt werden.

### Schritt 2: Erstellen eines neuen virtuellen Computers in ARM

Befolgen Sie die Anweisungen unten, um im Azure-Portal einen virtuellen Computer im neuen VNet zu erstellen.

1. Klicken Sie im Portal auf die Schaltfläche **NEU** und dann auf **Compute**, und klicken Sie dann auf **Windows Server 2012 R2 Datacenter**.
2. Wählen Sie im unteren der rechten Bereich **Computestapel auswählen** aus, wählen Sie **Ressourcen-Manager-Stapel verwenden** zum Erstellen des virtuellen Computers in ARM (wie unten gezeigt) aus, und klicken Sie dann auf**Erstellen**.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. Geben Sie auf dem Blatt **Grundlagen** den Namen des virtuellen Computers, den Benutzernamen, das Kennwort, das Abonnement, die Ressourcengruppe und den Standort für den virtuellen Computer ein, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Einstellungen für dieses Szenario.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. Wählen auf dem Blatt **Größe auswählen** eine Größe aus, und klicken Sie dann auf **Auswählen**. Wählen Sie für dieses Szenario **D2** aus.
5. Klicken Sie auf dem Blatt **Einstellungen** auf **Virtuelles Netzwerk**, und klicken Sie dann auf **vnet02**.
6. Klicken Sie auf **Subnetz**, klicken Sie auf **Subnet1**, und klicken Sie dann auf**OK**. Das Blatt **Zusammenfassung** sollte ähnlich wie die folgende Abbildung aussehen.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. Klicken Sie auf **OK**, und klicken Sie dann auf **Erstellen**, um den virtuellen Computer zu erstellen. Es wird eine neue Kachel im Portal angezeigt, die zeigt, dass der virtuelle Computer bereitgestellt wird – wie unten dargestellt.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]Dieser Vorgang kann einige Minuten dauern. Sie können nun mit dem nächsten Teil dieses Dokuments fortfahren.

## Verbinden der beiden VNets

Da Sie nun über zwei VNets verfügen, mit denen virtuelle Computer verbunden sind, können Sie die VNets über das zuvor eingerichtete Gateway verbinden und die Verbindung testen.

### Schritt 1: Konfigurieren des Gateways für das klassische VNet

Sie müssen das klassische VNet für die Verwendung der IP-Adresse des Gateways konfigurieren, das für das ARM-VNet ("vnet02") erstellt wurde und dann eine Verbindung aus jedem VNet einrichten. Befolgen Sie zu diesem Zweck die Anweisungen unten.

1. Führen Sie zum Abrufen der IP-Adresse für das Gateway im ARM-VNet den folgenden Befehl aus, und beachten Sie die Ausgabe. Notieren Sie sich die Adresse. Sie benötigen diese, um die Einstellungen des lokalen Netzwerks für das klassische VNet später zu ändern.

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. Stellen Sie sicher, dass Sie die Azure Service Management-API für Ihre PowerShell-Befehle verwenden, indem Sie den folgenden Befehl ausführen.

		Switch-AzureMode AzureServiceManagement

3. Laden Sie Ihre Azure-Netzwerkkonfigurationsdatei herunter, indem Sie den folgenden Befehl ausführen.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. Öffnen Sie die Datei, die Sie soeben heruntergeladen haben, und bearbeiten Sie das Element **LocalNetworkSite** für **vnet02**, um die IP-Adresse des Gateways für das neue VNet hinzuzufügen, die Sie in Schritt 1 weiter oben abgerufen haben. Das Element sollte ähnlich wie das folgende Beispiel aussehen.

	      <LocalNetworkSite name="vnet03">
	        <AddressSpace>
	          <AddressPrefix>10.3.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. Speichern Sie die Datei, und führen Sie dann den folgenden Befehl aus, um das klassische VNet zu konfigurieren. Stellen Sie sicher, dass Sie den Pfad so ändern, dass er auf die Datei verweist, die Sie in Schritt 4 oben gespeichert haben.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. Legen Sie den gemeinsam verwendeten IPSec-Schlüssel für das klassische VNet-Gateway fest, indem Sie den folgenden Befehl ausführen. Sie sollte eine Ausgabe erhalten, die der unten abgebildeten ähnlich ist. Stellen Sie sicher, dass Sie die VNet-Namen ändern, wenn Sie eigene, bereits vorhandenen VNets verwenden.

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### Schritt 2: Konfigurieren des Gateways für das ARM-VNet

Nachdem Sie nun das klassische VNet-Gateway konfiguriert haben, muss die Verbindung eingerichtet werden. Befolgen Sie zu diesem Zweck die Anweisungen unten.

1. Führen Sie in einer PowerShell-Konsole den folgenden Befehl aus, um in den ARM-Modus zu wechseln. 

		Switch-AzureMode AzureResourceManager

2. Erstellen Sie die Verbindung zwischen den Gateways, indem Sie die folgenden Befehle ausführen.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. Öffnen Sie das Azure-Portal unter https://manage.windowsazure.com, und geben Sie Ihre Anmeldeinformationen ein, wenn erforderlich.
4. Führen Sie unter **ALLE ELEMENTE** einen Bildlauf nach unten aus, und klicken Sie auf **NETZWERKE**. Klicken Sie auf **vnet01**, und klicken Sie dann auf **DASHBOARD**. Beachten Sie, dass die Verbindung zwischen **vnet01** und **vnet02** nun eingerichtet ist, wie unten gezeigt.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. Obwohl Sie das klassische VNet und seine Verbindung über das klassische Portal verwalten können, wird empfohlen, das neue Azure-Portal zu verwenden. Navigieren Sie zum Öffnen des neuen Portals zu https://portal.azure.com.
6. Klicken Sie im neuen Portal auf **ALLE DURCHSUCHEN**, klicken Sie auf **Virtuelle Netzwerke (Klassisch)**, und klicken Sie dann auf **vnet01**. Beachten Sie den unten gezeigten Bereich **VPN-Verbindungen**.

	![VNet-Dashboard](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### Schritt 3: Testen der Konnektivität

Da die beiden VNets nun verbunden sind, müssen Sie die Konnektivität testen, indem Sie Ping von einem virtuellen Computer für einen anderen ausführen. Sie müssen die Firewalleinstellungen in einem der virtuellen Computer so ändern, dass ICMP zulässig ist, und dann für diesen virtuellen Computer Ping vom anderen virtuellen Computer aus ausführen. Befolgen Sie zu diesem Zweck die Anweisungen unten.

1. Klicken Sie im Azure-Portal auf **ALLE DURCHSUCHEN**, klicken Sie auf**Virtuelle Computer**, und klicken Sie dann auf **VM02**.
2. Klicken Sie auf dem Blatt **VM02** auf **Verbinden**. Wenn erforderlich, klicken Sie auf Ihrem Browsersicherheitsbanner auf **Öffnen**, um die RDP-Datei zu öffnen.
3. Klicken Sie im Dialogfeld **Remotedesktopverbindung** auf **Verbinden**.
4. Geben Sie im Dialogfeld **Windows-Sicherheit** Ihren Benutzernamen und das Kennwort für den virtuellen Computer ein. 
5. Klicken Sie im Dialogfeld **Remotedesktopverbindung** auf **Ja**.
6. Sobald eine Verbindung mit dem virtuellen Computer hergestellt wurde, klicken Sie unter **Server-Manager** auf **Tools**, und klicken Sie dann auf **Windows-Firewall mit erweiterter Sicherheit**.
7. Klicken Sie im Fenster **Windows-Firewall mit erweiterter Sicherheit** auf **Eingehende Regeln**.
8. Klicken Sie in Liste **Eingehende Regeln** mit der rechten Maustaste auf **Datei- und Druckerfreigabe (Echoanforderung - ICMPv4 eingehend)**, und klicken Sie dann auf **Regel aktivieren**.
9. Klicken Sie auf der Taskleiste auf die Schaltfläche **Windows PowerShell**, und führen Sie dann den folgenden Befehl aus.

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. Notieren Sie sich die IP-Adresse für den virtuellen Computer. In diesem Szenario ist dies **10.2.0.101**. Sie werden für diese Adresse Ping vom anderen virtuellen Computer aus ausführen, um die Konnektivität zwischen den VNets zu testen.
11. Klicken Sie im Azure-Portal im linken Bereich auf **Virtuelle Computer (Klassisch)**, klicken Sie auf **VM01**, und klicken Sie dann auf **Verbinden**. Wenn erforderlich, klicken Sie auf Ihrem Browsersicherheitsbanner auf **Öffnen**, um die RDP-Datei zu öffnen.
12. Klicken Sie im Dialogfeld **Remotedesktopverbindung** auf **Verbinden**.
13. Geben Sie im Dialogfeld **Windows-Sicherheit** Ihren Benutzernamen und das Kennwort für den virtuellen Computer ein. 
14. Klicken Sie im Dialogfeld **Remotedesktopverbindung** auf **Ja**.
15. Klicken Sie auf der Taskleiste für den virtuellen Remotecomputer auf die Schaltfläche **Windows PowerShell**, und führen Sie dann den folgenden Befehl aus.

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## Nächste Schritte

- Weitere Informationen zum [Netzwerkressourcenanbieter (Network Resource Provider, NRP) für ARM](../resource-groups-networking.md).
- Anzeigen der allgemeinen Richtlinien zum [Erstellen einer S2S-VPN-Verbindung zwischen einem klassischen VNet und einem ARM-VNet](../virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=Oct15_HO3-->