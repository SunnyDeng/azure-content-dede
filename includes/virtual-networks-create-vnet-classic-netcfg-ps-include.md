## Erstellen eines VNet mithilfe einer Netzwerkkonfigurationsdatei von PowerShell

Azure verwendet eine XML-Datei, um alle für ein Abonnement verfügbaren VNets zu definieren. Sie können diese Datei herunterladen und bearbeiten, um vorhandene VNets zu ändern oder zu löschen und neue zu erstellen. In diesem Dokument erfahren Sie, wie Sie die Datei, die auch als Netzwerkkonfigurationsdatei oder netcgf-Datei bezeichnet wird, herunterladen und bearbeiten können, um ein neues VNet zu erstellen. Unter [Konfigurationsschema für virtuelle Azure-Netzwerke](https://msdn.microsoft.com/library/azure/jj157100.aspx) finden Sie weitere Informationen zur Netzwerkkonfigurationsdatei.

Führen Sie zum Erstellen eines VNet unter Verwendung einer netcfg-Datei mithilfe von PowerShell die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Verwenden Sie in der Azure-PowerShell-Konsole das Cmdlet **Get-AzureVnetConfig**, um die Netzwerkkonfigurationsdatei herunterzuladen, indem Sie den folgenden Befehl ausführen. 

		Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

	Hier ist die erwartete Ausgabe des obigen Befehls:

		XMLConfiguration                                                                                                     
		----------------                                                                                                     
		<?xml version="1.0" encoding="utf-8"?>...  

3. Öffnen Sie die in Schritt 2 oben gespeicherte Datei mit einem beliebigen XML- oder Text-Editor, und suchen Sie nach dem **<VirtualNetworkSites>**-Element. Wenn Sie bereits Netzwerke erstellt haben, wird für jedes Netzwerk ein eigenes **<VirtualNetworkSite>**-Element angezeigt.
4. Um das in diesem Szenario beschriebene virtuelle Netzwerk zu erstellen, fügen Sie den folgenden XML-Code direkt unter dem **<VirtualNetworkSites>**-Element hinzu:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

9.  Speichern Sie die Netzwerkkonfigurationsdatei.
10. Verwenden Sie in der Azure PowerShell-Konsole das Cmdlet **Set-AzureVnetConfig**, um die Netzwerkkonfigurationsdatei hochzuladen, indem Sie den folgenden Befehl ausführen. Beachten Sie die Ausgabe unter dem Befehl. Unter **OperationStatus** sollte **Succeeded** angezeigt werden. Ist dies nicht der Fall, überprüfen Sie die XML-Datei auf Fehler.

		Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

	Hier ist die erwartete Ausgabe des obigen Befehls:

		OperationDescription OperationId                          OperationStatus
		-------------------- -----------                          ---------------
		Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
	
11. Verwenden Sie in der Azure PowerShell-Konsole das Cmdlet **Get-AzureVnetSite**, um zu überprüfen, ob das neue Netzwerk hinzugefügt wurde, indem Sie den folgenden Befehl ausführen.

		Get-AzureVNetSite -VNetName TestVNet

	Hier ist die erwartete Ausgabe des obigen Befehls:

		AddressSpacePrefixes : {192.168.0.0/16}
		Location             : Central US
		AffinityGroup        : 
		DnsServers           : {}
		GatewayProfile       : 
		GatewaySites         : 
		Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
		InUse                : False
		Label                : 
		Name                 : TestVNet
		State                : Created
		Subnets              : {FrontEnd, BackEnd}
		OperationDescription : Get-AzureVNetSite
		OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
		OperationStatus      : Succeeded

<!---HONumber=Sept15_HO2-->