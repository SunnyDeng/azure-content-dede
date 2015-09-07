## Erstellen eines VNet im Azure-Portal.

Führen Sie zum Erstellen eines VNet basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

1. Navigieren Sie in einem Browser zu http://manage.windowsazure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **NEU** > **NETZWERKDIENSTE** > **VIRTUELLES NETZWERK** > **BENUTZERDEFINIERT ERSTELLEN**, wie in der folgenden Abbildung dargestellt.

	![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Geben Sie auf der Seite **Details zum virtuellen Netzwerk** den **NAMEN** des VNet ein, wählen Sie seinen **Speicherort** aus, und klicken Sie dann auf den Pfeil unten rechts auf der Seite auf, um mit Schritt 2 fortzufahren. Die folgende Abbildung zeigt die Einstellungen für dieses Szenario.

	![Seite "Details zu Virtual Network"](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Geben Sie auf der Seite **DNS-Server und VPN-Konnektivität** den Namen und die IP-Adresse für bis zu 9 DNS-Server an, die Sie verwenden möchten. Wenn Sie keinen DNS-Server angeben, verwendet Ihr VNet die interne Namensauflösung von Azure. In diesem Szenario werden keine DNS-Server konfiguriert.
5. Wenn Sie Ihrem VNet Punkt-zu-Site-VPN-Zugriff gewähren möchten, aktivieren Sie das Kontrollkästchen **Punkt-zu-Site-VPN konfigurieren**. Wenn Sie kein Punkt-zu-Site-VPN konfigurieren, können Sie es auch nach der Erstellung jederzeit zu Ihrem VNet hinzufügen. In diesem Szenario wird kein Punkt-zu-Site-VPN konfiguriert.
6. Wenn Sie Standort-zu-Standort-VPN-Konnektivität zwischen Ihrem VNet und einem anderen VNet oder Ihrem lokalen Netzwerk bereitstellen möchten, aktivieren Sie das Kontrollkästchen **Site-to-Site-VPN konfigurieren**, geben Sie an, ob Sie **ExpressRoute** verwenden möchten oder nicht, und geben Sie den Namen des Netzwerks an, mit dem eine Verbindung hergestellt werden soll. Wenn Sie kein Standort-zu-Standort-VPN konfigurieren, können Sie es auch nach der Erstellung jederzeit zu Ihrem VNet hinzufügen. In diesem Szenario wird kein Standort-zu-Site-VPN konfiguriert.
7. Klicken Sie auf den Pfeil unten rechts auf der Seite, um mit Schritt 3 fortzufahren. Auf der folgenden Abbildung sind die Einstellungen für dieses Szenario dargestellt.

	![Seite "DNS-Server und VPN-Konnektivität"](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Klicken Sie auf der Seite ** Adressräume des virtuellen Netzwerks** unter **START-IP** auf *10.0.0.0*, um den VNet-Adressraum zu ändern, und geben Sie dann die Start-IP des gewünschten Adressraums ein. Geben Sie in diesem Szenario *192.168.0.0* ein.
9. Wählen Sie unter **CIDR (ANZAHL ADRESSEN)** die Anzahl der Bits für die Subnetzmaske aus. Wählen in diesem Szenario *16 (65536)* aus.
10. Klicken Sie unter **SUBNETZE** auf *Subnet-1*, und benennen Sie das Subnetz bei Bedarf um. Benennen sie es in diesem Szenario in *FrontEnd* um.

	>[AZURE.NOTE]Wenn Sie das Textfeld "Name" für ein Subnetz verlassen, können Sie den Namen nicht mehr bearbeiten. Um dieses Problem zu beheben, müssen Sie das Subnetz durch Klicken auf die Schaltfläche "X" rechts davon entfernen und dann ein neues Subnetz hinzufügen, wie in Schritt 13 unten beschrieben.

11. Geben Sie unter **START-IP** für das erste Subnetz die IP-Startadresse für das Subnetz an. Geben Sie in diesem Szenario *192.168.1.0* ein.
12. Wählen Sie unter **CIDR (ANZAHL ADRESSEN)** die Anzahl der Bits für die Subnetzmaske des ersten Subnetzes aus. Wählen in diesem Szenario *24 (256)* aus.
13. Klicken Sie auf **Subnetz hinzufügen**, um bei Bedarf ein neues Subnetz hinzuzufügen. Fügen Sie in diesem Szenario ein Subnetz hinzu, und wiederholen Sie die Schritte 10 bis 12, um das VNet wie in der folgenden Abbildung dargestellt zu konfigurieren.

	![Seite "Adressräume von Virtual Network"](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite, um das VNet zu erstellen. Nach einigen Sekunden wird das VNet in der Liste der verfügbaren VNets angezeigt, wie in der folgenden Abbildung dargestellt.

	![Neues virtuelles Netzwerk](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)

<!---HONumber=August15_HO9-->