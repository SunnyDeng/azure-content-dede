## Erstellen eines klassischen VNET im Azure-Portal.

Führen Sie zum Erstellen eines klassischen VNet basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **NEU** > **Netzwerk** > **Virtuelles Netzwerk**. Beachten Sie, dass in der Liste **Bereitstellungsmodell auswählen** bereits **Klassisch** angezeigt wird. Klicken Sie anschließend auf **Erstellen**, wie in der folgenden Abbildung dargestellt.

	![Erstellen eines VNET im Azure-Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Geben Sie auf dem Blatt **Virtuelles Netzwerk** den **Namen** des VNet ein, und klicken Sie dann auf **Adressraum**. Konfigurieren Sie Ihre Adressraumeinstellungen für das VNet und sein erstes Subnetz, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Einstellungen des CIDR-Blocks für das aktuelle Szenario.

	![Blatt "Adressraum"](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Klicken Sie auf **Ressourcengruppe**, und wählen Sie eine Ressourcengruppe aus, zu der Sie das VNet hinzufügen möchten, oder klicken Sie auf **Neue Ressourcengruppe erstellen**, um das VNet zu einer neuen Ressourcengruppe hinzuzufügen. Die folgende Abbildung zeigt die Ressourcengruppeneinstellungen für eine neue Ressourcengruppe mit dem Namen **TestRG**. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md/#resource-groups).

	![Blatt "Ressourcengruppe erstellen"](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Ändern Sie ggf. die **Abonnement**- und **Speicherort**-Einstellungen für das VNet.

6. Wenn das VNet nicht als Kachel im **Startmenü** angezeigt werden soll, deaktivieren Sie **An Startmenü anheften**.

7. Klicken Sie auf **Erstellen** und achten Sie auf die Kachel namens **Virtuelles Netzwerk erstellen**, wie in der folgenden Abbildung dargestellt.

	![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Warten Sie, bis das VNet erstellt wurde. Wenn die Kachel unten angezeigt wird, klicken Sie darauf, um weitere Subnetze hinzuzufügen.

	![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Daraufhin sollte die **Konfiguration** für das VNet angezeigt werden, wie unten dargestellt.

	![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Klicken Sie auf **Subnetze** > **Hinzufügen**, geben Sie einen **Namen** ein, legen Sie einen **Adressbereich (CIDR-Block)** für Ihr Subnetz fest, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Einstellungen für das aktuelle Szenario.

	![Erstellen eines VNET im Azure-Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

<!---HONumber=AcomDC_0211_2016-->