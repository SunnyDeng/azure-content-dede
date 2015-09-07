## Erstellen von VNets mithilfe der Azure-Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um Ihre Azure-Ressourcen über die Eingabeaufforderung eines beliebigen Computers mit Windows, Linux oder OSX zu verwalten. Führen Sie zum Erstellen eines VNet mithilfe der Azure-Befehlszeilenschnittstelle die folgenden Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

		info:    New mode is arm

3. Führen Sie bei Bedarf **azure group create** aus, um eine neue Ressourcengruppe zu erstellen, wie unten dargestellt. Beachten Sie die Ausgabe des Befehls. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md/#resource-groups).

		azure group create -n TestRG -l centralus

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (oder --name)**. Name für die neue Ressourcengruppe. In diesem Szenario *TestRG*.
	- **-l (oder --location)**. Azure-Region, in der die neue Ressourcengruppe erstellt wird. In diesem Szenario *centralus*.

4. Führen Sie den Befehl **azure network vnet create** aus, um ein VNet und ein Subnetz zu erstellen, wie unten dargestellt. Beachten Sie die Ausgabe des CLI-Befehls. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.
5. 
		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g (oder --resource-group)**. Name der Ressourcengruppe, in der das neue VNet erstellt wird. In diesem Szenario *TestRG*.
	- **-n (oder --name)**. Name des zu erstellenden VNet. In diesem Szenario *TestVNet*.
	- **-a (oder --address-prefixes)**. Liste der für den VNet-Adressraum verwendeten CIDR-Blöcke. In diesem Szenario *192.168.0.0/16*.
	- **-l (oder --location)**. Azure-Region, in der das VNet erstellt wird. In diesem Szenario *centralus*.

5. Führen Sie den Befehl **azure network vnet subnet create** aus, um ein Subnetz zu erstellen, wie unten dargestellt. Beachten Sie die Ausgabe des Befehls. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-t (oder --vnet-name**. Name des VNet, in dem das Subnetz erstellt wird. In diesem Szenario *TestVNet*.
	- **-n (oder --name)**. Name des neuen Subnetzes. In diesem Szenario *FrontEnd*.
	- **-a (oder --address-prefix)**. Subnetz-CIDR-Block. In diesem Szenario *192.168.1.0/24*.

6. Wiederholen Sie bei Bedarf Schritt 5, um weitere Subnetze zu erstellen. Führen Sie für dieses Szenario den folgenden Befehl aus, um das *BackEnd*-Subnetz zu erstellen.

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Führen Sie den Befehl **azure network vnet show** aus, um die Eigenschaften des neuen VNet anzuzeigen, wie unten dargestellt.

		azure network vnet show -g TestRG -n TestVNet

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=August15_HO9-->