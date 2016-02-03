<properties 
   pageTitle="Konfigurieren einer Punkt-zu-Standort-VPN-Gatewayverbindung mit Azure Virtual Network | Microsoft Azure"
   description="Stellen Sie eine sichere Verbindung mit Ihrem Azure Virtual Network durch Erstellen einer Punkt-zu-Standort-VPN-Verbindung her. Diese Konfiguration ist hilfreich, wenn eine standortübergreifende Verbindung ohne Verwendung eines VPN-Geräts von einem Remotestandort aus erforderlich ist. Sie kann zudem mit Hybridnetzwerk-Konfigurationen verwendet werden. Dieser Artikel enthält PowerShell-Anweisungen für VNets, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="cherylmc" />

# Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem virtuellen Netzwerk mithilfe von PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Classic](vpn-gateway-point-to-site-create.md)

Mit einer Punkt-zu-Standort-Konfiguration können Sie von einem Clientcomputer eine einzelne sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine VPN-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten. Eine Punkt-zu-Standort-Verbindung ist eine hervorragende Lösung, wenn Sie von einem Remotestandort, z. B. von zu Hause oder in einer Konferenz, eine Verbindung mit Ihrem VNet herstellen möchten. Dieses Methode eignet sich auch, wenn Sie nur wenige Clients besitzen, die mit einem virtuellen Netzwerk verbunden werden müssen. Damit Punkt-zu-Standort-Verbindungen funktionieren, ist kein VPN-Gerät und keine öffentliche IP-Adresse erforderlich. Weitere Informationen zu Punkt-zu-Standort-Verbindungen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) und [Informationen zu standortübergreifenden Verbindungen](vpn-gateway-cross-premises-options.md).

Dieser Artikel bezieht sich auf VNets und VPN-Gateways, die mithilfe des **Azure-Ressourcen-Manager**-Bereitstellungsmodells erstellt wurden. Wenn Sie eine Punkt-zu-Standort-Verbindung für ein VNet konfigurieren möchten, das über die Dienstverwaltung erstellt wurde (auch als klassisches Bereitstellungsmodell bezeichnet), lesen Sie den Artikel [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem VNet](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Informationen zu dieser Konfiguration.

In diesem Szenario erstellen Sie ein virtuelles Netzwerk mit einer Punkt-zu-Standort-Verbindung. Eine Punkt-zu-Standort-Verbindung besteht aus folgenden Elementen: VNet mit einem Gateway, einer hochgeladenen CER-Stammzertifikatdatei, einem Clientzertifikat und der VPN-Konfiguration auf der Clientseite.

Für diese Konfiguration verwenden wir die folgenden Werte:

- Name: **TestVNet**, mit den Adressbereichen **192.168.0.0/16** und **10.254.0.0/16**. Beachten Sie, dass Sie für ein VNet mehrere Adressräume verwenden können.
- Subnetzname: **FrontEnd**, verwendet **192.168.1.0/24**
- Subnetzname: **BackEnd**, verwendet **10.254.1.0/24**
- Subnetzname: **GatewaySubnet**, verwendet **192.168.200.0/24**. Der Subnetzname *GatewaySubnet* ist erforderlich, damit das Gateway funktioniert. 
- VPN-Clientadresspool: **172.16.201.0/24**. VPN-Clients, die sich über diese Punkt-zu-Standort-Verbindung mit dem VNet verbinden, erhalten eine IP-Adresse aus diesem Pool.
- Abonnement: Überprüfen Sie, ob Sie das richtige Abonnement verwenden, falls Sie mehrere besitzen.
- Ressourcengruppe: **TestRG**
- Standort: **USA, Osten**
- DNS-Server: **IP-Adresse** des DNS-Servers, der für die Namensauflösung verwendet werden soll.
- GW-Name: **GW**
- Name der öffentlichen IP: **GWIP**
- VpnType: **RouteBased**


## Vorbereitungen

Stellen Sie sicher, dass Sie ein Azure-Abonnement besitzen und die für diese Konfiguration erforderlichen Azure PowerShell-Cmdlets installiert haben (1.0.2 oder höher). Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.
	
**Informationen zum Installieren von PowerShell-Cmdlet-Modulen**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Konfigurieren einer Punkt-zu-Standort-Verbindung für Azure

1. Melden Sie sich in der PowerShell-Konsole bei Ihrem Azure-Konto an. Sie werden von diesem Cmdlet zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert. Nach dem Anmelden werden Ihre Kontoeinstellungen heruntergeladen, damit sie Azure PowerShell zur Verfügung stehen.

		Login-AzureRmAccount 

2. Rufen Sie eine Liste Ihrer Azure-Abonnements ab.

		Get-AzureRmSubscription

3. Geben Sie das Abonnement an, das Sie verwenden möchten.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. In dieser Konfiguration werden die folgenden PowerShell-Variablen mit den Werten deklariert, die Sie verwenden möchten. Die deklarierten Werte werden in den Beispielskripts verwendet. Deklarieren Sie die gewünschten Werte. Verwenden Sie das unten gezeigte Beispiel, und ersetzen Sie die Werte nach Bedarf durch Ihre eigenen.

		$VNetName  = "TestVNet"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. Erstellen Sie eine neue Ressourcengruppe.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Erstellen Sie die Subnetzkonfigurationen für das virtuelle Netzwerk, und benennen Sie sie mit *FrontEnd*, *BackEnd* und *GatewaySubnet*. Beachten Sie, dass diese Präfixe im oben deklarierten VNet-Adressraum enthalten sein müssen.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Erstellen des virtuellen Netzwerks Beachten Sie, dass der angegebene DNS-Server dazu in der Lage sein muss, die Namen für die Ressourcen aufzulösen, mit denen Sie eine Verbindung herstellen möchten. In diesem Beispiel haben wir eine öffentliche IP-Adresse verwendet. Sie können jedoch Ihre eigenen Werte hier einfügen.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Geben Sie die Variablen für das virtuelle Netzwerk an, das Sie gerade erstellt haben.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Fordern Sie eine dynamisch zugewiesene öffentliche IP-Adresse an. Diese IP-Adresse ist erforderlich, damit das Gateway ordnungsgemäß funktioniert. Später werden Sie das Gateway mit der Gateway-IP-Konfiguration verbinden.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Laden Sie eine CER-Stammzertifikatdatei in Azure hoch. Sie können ein Stammzertifikat aus Ihrer Unternehmenszertifikatumgebung oder ein selbstsigniertes Stammzertifikat verwenden. Sie können bis zu 20 Stammzertifikate hochladen. Eine Anleitung zum Erstellen eines selbstsignierten Stammzertifikats mithilfe von *MakeCert* finden Sie unter [Arbeiten mit selbstsignierten Stammzertifikaten für Punkt-zu-Standort-Konfigurationen](vpn-gateway-certificates-point-to-site.md). Beachten Sie, dass die CER-Datei nicht den privaten Schlüssel des Stammzertifikats enthalten sollte.
	
	Unten ist ein Beispiel aufgeführt. Die Herausforderung beim Hochladen der Daten für das öffentliche Zertifikat besteht darin, die gesamte Zeichenfolge ohne Leerzeichen zu kopieren und einzufügen. Andernfalls funktioniert der Upload nicht. Für diesen Schritt müssen Sie Ihre eigene CER-Zertifikatdatei verwenden. Versuchen Sie nicht, das unten gezeigte Beispiel zu kopieren und einzufügen.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Der GatewayType muss „Vpn“ sein, und der „VpnType“ muss „RouteBased“ lauten.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Clientkonfiguration

Für jeden Client, der eine Punkt-zu-Standort-Verbindung mit Azure herstellt, gelten zwei Voraussetzungen: Der VPN-Client muss für die Verbindungsherstellung konfiguriert sein, und auf dem Client muss ein Clientzertifikat installiert sein. Konfigurationspakete für VPN-Clients stehen für Windows-Clients zur Verfügung. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Laden Sie das Konfigurationspaket für VPN-Clients herunter. Verwenden Sie in diesem Schritt das folgende Beispiel zum Herunterladen des Clientkonfigurationspakets.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	Das PowerShell-Cmdlet gibt einen URL-Link zurück. Kopieren Sie den zurückgegebenen Link, und fügen Sie ihn in einen Webbrowser ein, um das Paket auf Ihren Computer herunterzuladen. Im Folgenden sehen Sie ein Beispiel für die zurückgegebene URL.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Generieren und installieren Sie die Clientzertifikate (*.pfx), die aus dem Stammzertifikat auf den Clientcomputern erstellt werden. Sie können eine beliebige Installationsmethode verwenden, mit der Sie vertraut sind. Wenn Sie ein selbstsigniertes Stammzertifikat verwenden und mit der entsprechenden Vorgehensweise nicht vertraut sind, lesen Sie die Informationen unter [Arbeiten mit selbstsignierten Stammzertifikaten für Punkt-zu-Standort-Konfigurationen](vpn-gateway-certificates-point-to-site.md).

3. Um eine Verbindung mit Ihrem VNet herzustellen, navigieren Sie auf dem Clientcomputer zu „VPN-Verbindungen“, und suchen Sie die VPN-Verbindung, die Sie gerade erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Klicken Sie auf **Verbinden**. Möglicherweise wird eine Popupmeldung angezeigt, die sich auf die Verwendung des Zertifikats bezieht. Klicken Sie in diesem Fall auf **Weiter**, um erhöhte Rechte zu verwenden.

4. Klicken Sie auf der Statusseite **Verbindung** auf **Verbinden**, um die Verbindung herzustellen. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, vergewissern Sie sich, dass das angezeigte Clientzertifikat dem Zertifikat entspricht, die Sie zum Herstellen der Verbindung verwenden möchten. Wenn dies nicht der Fall ist, verwenden Sie den Dropdownpfeil, um das richtige Zertifikat auszuwählen, und klicken Sie dann auf **OK**.

5. Die Verbindung sollte jetzt eingerichtet werden. Mit dem nachstehenden Verfahren können Sie Ihre Verbindung überprüfen.

## Überprüfen der Verbindung

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all* aus.

2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die erhaltene IP-Adresse eine der Adressen aus dem Clientadresspool des Punkt-zu-Standort-VPN ist, den Sie in Ihrer Konfiguration angegeben haben. Das Ergebnis sollte etwa wie folgt aussehen:
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## So können Sie ein Stammzertifikat hinzufügen oder entfernen

Zertifikate werden zur Authentifizierung von VPN-Clients für Punkt-zu-Standort-VPNs verwendet. Die folgenden Schritte führen Sie durch das Hinzufügen und Entfernen von Stammzertifikaten.

### Hinzufügen eines Stammzertifikats

Sie können bis zu 20 Stammzertifikate in Azure hinzufügen. Gehen Sie folgendermaßen vor, um ein Stammzertifikat hinzuzufügen.

1. Erstellen Sie das neue Stammzertifikat, und bereiten Sie es für den Upload vor.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Laden Sie das neue Stammzertifikat hoch. Beachten Sie, dass Sie jeweils nur ein Stammzertifikat hinzufügen können.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. Mit dem folgenden Cmdlet können Sie überprüfen, ob das neue Zertifikat ordnungsgemäß hinzugefügt wurde:

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

### Entfernen eines Stammzertifikats

Sie können ein Stammzertifikat aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können aus diesem Stammzertifikat generierte Clientzertifikate erst wieder über Punkt-zu-Standortverbindungen mit Azure verbunden werden, wenn ein Clientzertifikat installiert wird, das aus einem gültigen Stammzertifikat in Azure erstellt wurde.

1. Entfernen Sie ein Stammzertifikat.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. Überprüfen Sie mit dem folgenden Cmdlet, ob das Zertifikat erfolgreich entfernt wurde.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## Verwalten der Liste gesperrter Clientzertifikate

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Wenn Sie ein Stammzertifikat aus Azure entfernen, wird der Zugriff für alle vom gesperrten Stammzertifikat generierten/signierten Clientzertifikate widerrufen. Beim Sperren eines Clientzertifikats hingegen können Sie den Zugriff für ein bestimmtes Zertifikat widerrufen. Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

### Sperren eines Clientzertifikats

1. Rufen Sie den Fingerabdruck des zu sperrenden Clientzertifikats ab.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Fügen Sie den Fingerabdruck zur Liste der gesperrten Fingerabdrücke hinzu.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Überprüfen Sie, ob der Fingerabdruck zur Zertifikatsperrliste hinzugefügt wurde. Sie müssen die Fingerabdrücke nacheinander hinzufügen.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Reaktivieren eines Clientzertifikats

Sie können ein Clientzertifikat reaktivieren, indem Sie den Fingerabdruck aus der Liste der gesperrten Clientzertifikate entfernen.

1.  Entfernen Sie den Fingerabdruck aus der Liste der gesperrten Clientzertifikate.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Überprüfen Sie, ob der Fingerabdruck aus der Sperrliste entfernt wurde.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Nächste Schritte

Sie können Ihrem virtuellen Netzwerk einen virtuellen Computer hinzufügen. Für diese Schritte finden Sie Informationen unter [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0121_2016-->