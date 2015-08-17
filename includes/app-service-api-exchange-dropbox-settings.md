4. Wechseln Sie in einem anderen Browserfenster oder in einer anderen Browserregisterkarte zum [Azure-Vorschauportal](https://portal.azure.com).

3. Wechseln Sie zum Blatt **API-App** für Ihren Dropbox-Connector. (Wenn Sie noch das Blatt **Ressourcengruppe** anzeigen, klicken Sie einfach auf den Dropbox-Connector im Diagramm.)

4. Klicken Sie auf **Einstellungen**, und klicken Sie dann auf dem Blatt **Einstellungen** auf **Authentifizierung**.

	![Klicken Sie auf "Einstellungen"](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![Klicken Sie auf "Authentifizierung"](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. Geben Sie auf dem Blatt "Authentifizierung" die Client-ID und den geheimen Clientschlüssel von der Dropbox-Website ein, und klicken Sie dann auf **Speichern**.

	![Geben Sie Einstellungen ein, und klicken Sie auf "Speichern"](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. Kopieren Sie den **Umleitungs-URI** (graues Feld oberhalb von Client-ID und dem geheimen Clientschlüssel), und fügen Sie den Wert auf der Seite ein, die Sie im vorherigen Schritt geöffnet gelassen haben.

	Der Umleitungs-URI folgt diesem Muster:

		[gatewayurl]/api/consent/redirect/[connectorname]

	Zum Beispiel:

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Abrufen des Umleitungs-URI](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Erstellen einer Dropbox-App](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=August15_HO6-->