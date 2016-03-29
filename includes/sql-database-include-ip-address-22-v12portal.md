
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) unter http://portal.azure.com/ an.

2. Klicken Sie im linken Banner auf **Durchsuchen**. Das Blatt **Durchsuchen** wird angezeigt.

3. Scrollen Sie und klicken Sie auf **SQL-Server**. Das Blatt **SQL-Server** wird angezeigt.

	![Suchen Sie Ihren Azure SQL-Datenbankserver im Portal][b21-FindServerInPortal]

4. Klicken Sie auf die Zeile für den Server. Ein Blatt für den Server wird angezeigt.

5. Klicken Sie auf dem Serverblatt auf **Einstellungen**. Das Blatt **Einstellungen** wird angezeigt.

6. Klicken Sie auf **Firewall**. Das Blatt **Firewalleinstellungen** wird angezeigt.

	![Klicken Sie auf "Einstellungen > Firewall"][b31-SettingsFirewallNavig]

7. Klicken Sie auf **Client-IP-Adresse hinzufügen**, um die IP-Adresse Ihres Clientcomputers hinzuzufügen. Das Portal wird einen Namen für Ihre Regel erstellen. Alternativ können Sie einen Namen eingeben und die hohen und niedrigen IP-Adresswerte für den Bereich angeben, den Sie aktivieren möchten. Es kann praktisch sein, wenn der niedrige Wert auf **.0** endet und der hohe auf **.255**.

	![Fügen Sie einen erlaubten IP-Adressbereich hinzu][b41-AddRange]

8. Klicken Sie auf **Speichern**.



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_0316_2016-->