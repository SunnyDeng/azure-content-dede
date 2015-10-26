## Erstellen eines VNet mithilfe einer Netzwerkkonfigurationsdatei im Azure-Portal

Azure verwendet eine XML-Datei, um alle für ein Abonnement verfügbaren VNets zu definieren. Sie können diese Datei herunterladen und bearbeiten, um vorhandene VNets zu ändern oder zu löschen und neue zu erstellen. In diesem Dokument erfahren Sie, wie Sie die Datei, die auch als Netzwerkkonfigurationsdatei oder netcgf-Datei bezeichnet wird, herunterladen und bearbeiten können, um ein neues VNet zu erstellen. Unter [Konfigurationsschema für virtuelle Azure-Netzwerke](https://msdn.microsoft.com/library/azure/jj157100.aspx) finden Sie weitere Informationen zur Netzwerkkonfigurationsdatei.

Führen Sie zum Erstellen eines VNet unter Verwendung einer netcfg-Datei im Azure-Portal die folgenden Schritte aus.

1. Navigieren Sie in einem Browser zu http://manage.windowsazure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Blättern Sie in der Liste der Dienste nach unten, und klicken Sie auf **Netzwerke**, wie nachfolgend dargestellt.

	![Virtuelle Azure-Netzwerke](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Klicken Sie unten auf der Seite auf die Schaltfläche **EXPORTIEREN**, wie unten dargestellt.

	![Schaltfläche "Exportieren"](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Wählen Sie auf der Seite **Netzwerkkonfiguration exportieren** das Abonnement aus, aus dem Sie die Konfiguration des virtuellen Netzwerks exportieren möchten, und klicken Sie dann auf das Häkchen in der unteren linken Ecke der Seite.
5. Folgen Sie den Anweisungen des Browsers zum Speichern der Datei **NetworkConfig.xml**. Notieren Sie sich den Speicherort der Datei.
6. Öffnen Sie die in Schritt 5 oben gespeicherte Datei mit einem beliebigen XML- oder Text-Editor, und suchen Sie nach dem **<VirtualNetworkSites>**-Element. Wenn Sie bereits Netzwerke erstellt haben, wird für jedes Netzwerk ein eigenes **<VirtualNetworkSite>**-Element angezeigt.
7. Um das in diesem Szenario beschriebene virtuelle Netzwerk zu erstellen, fügen Sie den folgenden XML-Code direkt unter dem **<VirtualNetworkSites>**-Element hinzu:

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

8.  Speichern Sie die Netzwerkkonfigurationsdatei.
9.  Klicken Sie im Azure-Portal in der unteren linken Ecke der Seite auf **NEU**, klicken Sie dann auf **NETZWEKDIENSTE**, dann auf **VIRTUELLES NETZWERK**, und klicken Sie dann auf **KONFIGURATION IMPORTIEREN**, wie in der folgenden Abbildung dargestellt.

	![Konfiguration importieren](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Klicken Sie auf der Seite **Netzwerkkonfigurationsdatei importieren** auf **DATEI SUCHEN**. Navigieren Sie zu dem Ordner, in dem Sie die Datei in Schritt 8 oben gespeichert haben, wählen Sie die Datei aus, und klicken Sie dann auf **Öffnen**. Das Webseite sollte ähnlich wie die folgende Abbildung aussehen. Klicken Sie in der unteren rechten Ecke der Seite auf den Pfeil, um mit dem nächsten Schritt fortzufahren.

	![Seite "Netzwerkkonfiguration importieren"](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Beachten Sie auf der Seite **Netzwerk erstellen** den Eintrag für das neue VNet, wie in der folgenden Abbildung dargestellt.

	![Seite "Netzwerk erstellen"](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite, um das VNet zu erstellen. Nach einigen Sekunden wird das VNet in der Liste der verfügbaren VNets angezeigt, wie in der folgenden Abbildung dargestellt.

	![Neues virtuelles Netzwerk](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=Oct15_HO3-->