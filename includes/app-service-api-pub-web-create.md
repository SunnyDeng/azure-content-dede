1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie auf **Veröffentlichen**. 

	![Menüoption zur Projektveröffentlichung](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. Klicken Sie auf die Registerkarte **Profil**, und klicken Sie auf **Microsoft Azure API-Apps (Vorschau)**.

	![Dialogfeld zur Webveröffentlichung](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. Klicken Sie auf **Neu**, um eine neue API-App im Azure-Abonnement bereitzustellen.

	![Dialogfeld zur Auswahl vorhandener API-Dienste](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. Geben Sie im Dialogfeld **API-App erstellen** Folgendes ein:

	- Geben Sie unter **API App Name** den Namen ein, den Sie für dieses Lernprogramm verwenden möchten. 
	- Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten.
	- Wählen Sie unter **App Service-Plan** einen vorhandenen App Service-Plan aus, oder wählen Sie **Neuen App Service-Plan erstellen**, und geben Sie den Namen eines neuen Plans ein. 
	- Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neue Ressourcengruppe erstellen**, und geben Sie einen Namen ein. 
	- Wählen Sie unter **Zugriffsebene** die Option **Für alle Benutzer verfügbar** aus. Sie können den Zugriff später über das Azure-Vorschauportal einschränken.
	- Wählen Sie unter **Region** eine Region in Ihrer Nähe aus.  

	![Dialogfeld zum Konfigurieren von Microsoft Azure Web-Apps](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. Klicken Sie auf **OK**, um die API-App in Ihrem Abonnement zu erstellen.

	Da dieser Vorgang einige Minuten in Anspruch nehmen kann, zeigt Visual Studio ein Bestätigungsdialogfeld an.

	![Bestätigungsmeldung zum Start der Erstellung von API-Diensten](./media/app-service-api-pub-web-create/25-api-provisioning-started-v3.png)

6. Klicken Sie im Bestätigungsdialogfeld auf **OK**.
 
	Im Rahmen des Bereitstellungsprozesses werden die Ressourcengruppe und die API-App in Ihrem Azure-Abonnement erstellt. Visual Studio zeigt den Fortschritt im Fenster **Azure App Service-Aktivität** an.

	![Statusbenachrichtigung über das Fenster "Azure App Service-Aktivität"](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=August15_HO6-->