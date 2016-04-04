## Erstellen eines klassischen VNet über die Azure-Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um Ihre Azure-Ressourcen über die Eingabeaufforderung eines beliebigen Computers mit Windows, Linux oder OSX zu verwalten. Führen Sie zum Erstellen eines VNet mithilfe der Azure-Befehlszeilenschnittstelle die folgenden Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../articles/xplat-cli-install.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure network vnet create** aus, um ein VNet und ein Subnetz zu erstellen, wie unten dargestellt. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
	Erwartete Ausgabe:

			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**. Name des zu erstellenden VNet. In diesem Szenario *TestVNet*.
	- **-e (oder --address-space)**. VNet-Adressraum. In diesem Szenario *192.168.0.0*.
	- **-i (oder --cidr)**. Netzwerkmaske im CIDR-Format. In diesem Szenario *16*.
	- **-n (oder --subnet-name**). Name des ersten Subnetzes. In diesem Szenario *FrontEnd*.
	- **-p (oder --subnet-start-ip)**. IP-Startadresse für das Subnetz oder Subnetzadressraum. In diesem Szenario *192.168.1.0*.
	- **-r (oder --subnet-cidr)**. Netzwerkmaske im CIDR-Format für das Subnetz. In diesem Szenario *24*.
	- **-l (oder --location)**. Azure-Region, in der das VNet erstellt wird. In diesem Szenario *USA, Mitte*.

3. Führen Sie den Befehl **azure network vnet subnet create** aus, um ein Subnetz zu erstellen, wie unten dargestellt. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
	Hier ist die erwartete Ausgabe des obigen Befehls:

			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (oder --vnet-name)**. Name des VNet, in dem das Subnetz erstellt wird. In diesem Szenario *TestVNet*.
	- **-n (oder --name)**. Name des neuen Subnetzes. In diesem Szenario *BackEnd*.
	- **-a (oder --address-prefix)**. Subnetz-CIDR-Block. In diesem Szenario *192.168.2.0/24*.

4. Führen Sie den Befehl **azure network vnet show** aus, um die Eigenschaften des neuen VNet anzuzeigen, wie unten dargestellt.

			azure network vnet show

	Hier ist die erwartete Ausgabe des obigen Befehls:

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=AcomDC_0323_2016-->