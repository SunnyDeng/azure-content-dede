Die folgenden Schritte zeigen das Vorgehen zum Erstellen einer Dropbox-App mithilfe der Website "Dropbox.com". Da die Website "Dropbox.com" ohne vorherige Ankündigung geändert werden kann, unterscheiden sich die tatsächlichen Benutzeroberflächenelemente möglicherweise von den hier gezeigten.

1. Wechseln Sie zum [Dropbox-Entwicklerportal](https://www.dropbox.com/developers/apps), klicken Sie auf **App Console** und anschließend auf **Create app**.

	![Erstellen einer Dropbox-App](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. Wählen Sie **Dropbox API app**, und konfigurieren Sie die weiteren Einstellungen.
 
	Die im nachstehenden Screenshot gezeigten Dateizugriffsoptionen ermöglichen es Ihnen, den Zugriff auf Ihr Dropbox-Konto mit einer einfachen HTTP-Get-Anforderung zu testen, wenn Sie über beliebige Dateien in Ihrem Konto verfügen.

	Der Name der Dropbox-API-App kann beliebig gewählt werden, solange er von der Dropbox-Website akzeptiert wird.

3. Klicken Sie auf **Create app**.

	![Erstellen einer Dropbox-App](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	Auf der nächsten Seite werden der App-Schlüssel und die geheimen App-Einstellungen gezeigt (in Azure als Client-ID und geheimer Clientschlüssel bezeichnet), die Sie zum Konfigurieren Ihres Azure-Dropbox-Connectors benötigen.

	Diese Seite enthält außerdem ein Feld, in dem Sie den Umleitungs-URI eingeben können. Diesen Wert rufen Sie im nächsten Abschnitt ab.

	![Erstellen einer Dropbox-App](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=July15_HO4-->