
#### So fügen Sie ein Speicherkonto zur StorSimple 8000-Serie mit Update 1.0 hinzu

1. Wählen Sie auf der StorSimple-Manager-Dienst-Startseite Ihren Dienst aus, und doppelklicken Sie dann darauf. Damit gelangen Sie auf die Seite **Schnellstart**. Wählen Sie die Seite **Konfigurieren** aus.

2. Klicken Sie auf **Speicherkonto hinzufügen/bearbeiten**.

3. Klicken Sie im Dialogfeld **Speicherkonto hinzufügen/bearbeiten** auf **Neues hinzufügen**:

4. Wählen Sie im Feld **Anbieter** den entsprechenden Clouddienstanbieter aus. Die unterstützten Anbieter sind: Azure, Amazon S3, Amazon S3 mit RRS, HP und OpenStack. Geben Sie die Anmeldeinformationen und den Speicherort des Speicherkontos Ihres Clouddienstanbieters an. Die Felder für die Anmeldeinformationen unterscheiden sich in Abhängigkeit vom angegebenen Clouddienstanbieter.
  - Wenn Sie Azure als Clouddienstanbieter ausgewählt haben, geben Sie den **Namen** und den primären **Zugriffsschlüssel** für Ihr Microsoft Azure-Speicherkonto an. Der Speicherort wird für ein Azure-Konto automatisch angegeben.

        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)

 - Wenn Sie Amazon S3 oder Amazon-S3 mit RRS ausgewählt haben, geben Sie einen aussagekräftigen **Namen für das Speicherkonto**, den **Zugriffsschlüssel** und den **geheimen Schlüssel** an. Bei Amazon S3 und Amazon S3 mit RRS werden die folgenden Speicherorte unterstützt:

		- US Standard
		- US West (Oregon)
		- US West (Northern California)
		- EU (Ireland)
		- Asia Pacific (Singapore)
		- Asia Pacific (Sydney)
		- Asia Pacific (Tokyo)
		- South America (Sao Paulo)

        ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
	  		
 - Wenn Sie HP als Clouddienstanbieter ausgewählt haben, geben Sie einen aussagekräftigen **Namen für das Speicherkonto**, die **Mandanten-ID**, den **Benutzernamen** und das **Kennwort** an. Bei HP werden die folgenden Speicherorte unterstützt:

		- US East
		- US West
	  
        ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
	  		
 - Wenn Sie **OpenStack** als Clouddienstanbieter ausgewählt haben, geben Sie einen **Hostnamen**, den **Zugriffsschlüssel** und den **geheimen Schlüssel** an.

        > [AZURE.NOTE] For all the cloud service providers, excluding Azure, a friendly name is allowed. You can use different friendly names and create more than one storage account with the same set of credentials.

        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)

5. Aktivieren Sie **SSL-Modus aktivieren**, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem Gerät und der Cloud zu erstellen. Deaktivieren Sie das Kontrollkästchen **SSL-Modus aktivieren** nur, wenn der Betrieb in einer privaten Cloud stattfindet.

      \>[AZURE.NOTE]Bei Verwendung von HP als Anbieter wird immer SSL aktiviert.
  		
6. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Sie werden benachrichtigt, nachdem das Speicherkonto erfolgreich erstellt wurde.

7. Das neu erstellte Speicherkonto wird auf der Seite **Konfigurieren** unter **Speicherkonten** angezeigt. Klicken Sie auf **Speichern**, um das neue Speicherkonto zu speichern. Klicken Sie auf **OK**, wenn Sie zur Bestätigung aufgefordert werden.

<!---HONumber=July15_HO5-->