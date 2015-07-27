7. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das API-App-Projekt, und wählen Sie **Veröffentlichen** aus, um das Dialogfeld für die Veröffentlichung zu öffnen. Das Veröffentlichungsprofil, das Sie zuvor erstellt haben, sollte vorausgewählt sein. 

9. Klicken Sie auf **Veröffentlichen**, um den Bereitstellungsprozess zu beginnen.

	![Bereitstellen der API-App](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	Das Fenster **Azure App Service-Aktivität** zeigt den Fortschritt der Bereitstellung an.

	![Statusbenachrichtigung im Fenster "Azure App Service-Aktivität"](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	Während dieses Bereitstellungsprozesses versucht Visual Studio automatisch, das *Gateway* neu zu starten. Das Gateway ist eine Web-App, die Verwaltungsfunktionen für alle API-Apps in einer Ressourcengruppe verarbeitet, und muss neu gestartet werden, um Änderungen in der API-Definition oder der Datei *apiapp.json* einer API-App zu erkennen.
 
	Wenn Sie eine API-App mithilfe einer anderen Methode bereitstellen oder wenn Visual Studio das Gateway nicht neu starten kann, müssen Sie das Gateway möglicherweise manuell neu starten. Dies wird in den folgenden Schritten erläutert.

1. Wechseln Sie in Ihrem Browser zum [Azure-Vorschauportal](https://portal.azure.com).

2. Navigieren Sie zum Blatt **API-App** für die API-App, die Sie bereitgestellt haben.

	Informationen zum Blatt **API-App** und zum Suchen dieses Blatts finden Sie unter [Verwalten von API-Apps](../articles/app-service-api/app-service-api-manage-in-portal.md).

4. Klicken Sie auf den Link **Gateway**.

3. Klicken Sie im Blatt **Gateway** auf **Neu starten**.

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=July15_HO3-->