<properties 
	pageTitle="Bereitstellen einer API-App in Azure App Service" 
	description="Erfahren Sie, wie Sie ein API-App-Projekt für Ihr Azure-Abonnement bereitstellen." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Bereitstellen einer API-App in Azure App Service 

## Übersicht

In diesem Lernprogramm stellen Sie das Web-API-Projekt, das Sie im [vorherigen Lernprogramm](app-service-dotnet-create-api-app.md) erstellt haben, in einer neuen [API-App](app-service-api-apps-why-best-platform.md) bereit. Sie verwenden Visual Studio zum Erstellen der API-App-Ressource in [Azure App Service](app-service-value-prop-what-is.md) und zum Bereitstellen Ihres Web-API-Codes in der Azure API-App.

### Weitere Optionen für die Bereitstellung

Es gibt zahlreiche weitere Möglichkeiten zum Bereitstellen von API-Apps. Eine API-App ist eine [Web-App](app-service-web-overview.md) mit zusätzlichen Funktionen zum Hosten von Webdiensten, und sämtliche der [verfügbaren Bereitstellungsmethoden für Web-Apps](web-sites-deploy.md) können auch für API-Apps verwendet werden. Die Web-App, die eine API-App hostet, wird im Azure-Vorschauportal als API-App-Host bezeichnet, und Sie können die Bereitstellung über das Portalblatt "API-App-Host" konfigurieren. Weitere Informationen zum Blatt "API-App-Host" finden Sie unter [Verwalten einer API-App](app-service-api-manage-in-portal.md).

Die Tatsache, dass API-Apps auf Web-Apps basieren, bedeutet auch, dass Sie Code, der für andere Plattformen als ASP.NET geschrieben wurde, in API-Apps bereitstellen können. Ein Beispiel mit Verwendung von Git zum Bereitstellen von Node.js-Code in einer API-App finden Sie unter [Erstellen einer Node.js-API-App in Azure App Service](app-service-api-nodejs-api-app.md).
 
## Bereitstellen der API-App 

In diesem Abschnitt werden die erforderlichen Schritte zum Bereitstellen einer API-App in einem Azure-Abonnement gezeigt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie auf **Veröffentlichen...**. 

	![Menüoption zur Projektveröffentlichung](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. Klicken Sie auf die Registerkarte **Profil**, und klicken Sie auf **Microsoft Azure API-Apps (Vorschau)**.

	![Dialogfeld zur Webveröffentlichung](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. Klicken Sie auf **Neu**, um eine neue API-App in Ihrem Azure-Abonnement bereitzustellen.

	![Dialogfeld zur Auswahl vorhandener API-Dienste](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. Geben Sie im Dialogfeld **API-App erstellen** Folgendes ein:

	- Geben Sie als **API-App-Name** "ContactsList" ein. 
	- Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten.
	- Wählen Sie unter **App Service-Plan** einen vorhandenen App Service-Plan aus, oder wählen Sie **Neuen App Service-Plan erstellen**, und geben Sie den Namen eines neuen Plans ein. 
	- Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neue Ressourcengruppe erstellen**, und geben Sie einen Namen ein. Der Name muss eindeutig sein. Verwenden Sie eventuell den App-Namen als Präfix, und hängen Sie persönliche Informationen wie z. B. Ihre Microsoft-ID (ohne das @-Zeichen) an.  
	- Wählen Sie unter **Zugriffsebene** die Option **Für alle Benutzer verfügbar** aus. Durch diese Option wird steht die gesamte API öffentlich zur Verfügung, was für dieses Lernprogramm in Ordnung ist. Sie können den Zugriff später über das Azure-Vorschauportal einschränken.
	- Wählen Sie unter **Region** eine Region in Ihrer Nähe aus.  

	![Dialogfeld zum Konfigurieren von Microsoft Azure Web-Apps](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. Klicken Sie auf **OK**, um die API-App in Ihrem Abonnement zu erstellen. Da dieser Vorgang einige Minuten in Anspruch nehmen kann, zeigt Visual Studio ein Bestätigungsdialogfeld an.

	![Bestätigungsmeldung zum Start der Erstellung von API-Diensten](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. Klicken Sie im Bestätigungsdialogfeld auf **OK**. Im Rahmen des Bereitstellungsprozesses werden die Ressourcengruppe und die API-App in Ihrem Azure-Abonnement erstellt. Visual Studio zeigt den Fortschritt im Fenster **Azure App Service-Aktivität** an.

	![Statusbenachrichtigung über das Fenster "Azure App Service-Aktivität"](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

7. Sobald die API-App bereitgestellt wird, klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Veröffentlichen**, um das Dialogfeld für die Veröffentlichung erneut zu öffnen. Das Veröffentlichungsprofil, das Sie im vorherigen Schritt erstellt haben, sollte vorab ausgewählt sein. Klicken Sie auf **Veröffentlichen**, um den Bereitstellungsprozess zu beginnen.

	![Bereitstellen der API-App](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

Das Fenster **Azure App Service-Aktivität** zeigt den Fortschritt der Bereitstellung an.

![Statusbenachrichtigung im Fenster "Azure App Service-Aktivität"](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Anzeigen der App im Azure-Vorschauportal

In diesem Abschnitt navigieren Sie zum Portal, um die grundlegenden Einstellungen für API-Apps anzuzeigen und iterative Änderungen an Ihrer API-App vorzunehmen. Bei jeder Bereitstellung zeigt das Portal die Änderungen an, die Sie in Ihrer API-App vorgenommen haben.

1. Navigieren Sie in Ihrem Browser zum [Azure-Vorschauportal](https://portal.azure.com). 

2. Klicken Sie in der Randleiste auf die Schaltfläche **Durchsuchen**, und wählen Sie **API-Apps**.

	![Suchoptionen im Azure-Portal](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. Wählen Sie die erstellte API aus der Liste der API-Apps in Ihrem Abonnement aus.

	![API-Apps-Liste](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. Klicken Sie auf **API-Definition**. Das Blatt **API-Definition** der App zeigt die Liste der API-Vorgänge an, die Sie beim Erstellen der App definiert haben.

	![API-Definition](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Wechseln Sie jetzt zurück zum Projekt in Visual Studio, und fügen Sie der Datei **ContactsController.cs** den folgenden Code hinzu. Dieser Code fügt eine **Post**-Methode hinzu, mit deren Hilfe neue `Contact`-Instanzen in der API platziert werden können.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Hinzufügen der Post-Methode zum Controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

	![Kontextmenü zur Projektveröffentlichung](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. Klicken Sie auf die Registerkarte **Einstellungen**.

8. Wählen Sie in der Dropdownliste **Konfiguration** die Einstellung **Debuggen** aus.

	![Einstellungen für die Webveröffentlichung](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. Klicken Sie auf die Registerkarte **Vorschau**.

10. Klicken Sie auf **Vorschau starten**, um die Änderungen anzuzeigen, die durchgeführt werden.

	![Dialogfeld zur Webveröffentlichung](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Klicken Sie auf **Veröffentlichen**.

12. Nach Abschluss des Veröffentlichungsprozesses kehren Sie zum Portal zurück, schließen Sie das Blatt **API-Definition**, und öffnen Sie es erneut. Sie sehen den neuen API-Endpunkt, den Sie gerade erstellt und direkt in Ihrem Azure-Abonnement bereitgestellt haben.

	![API-Definition](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Nächste Schritte

Sie haben gesehen, wie die direkten Bereitstellungsfunktionen in Visual Studio die Iteration und Bereitstellung sowie das Testen der ordnungsgemäßen Funktion der API vereinfachen. Im [nächsten Lernprogramm](app-service-dotnet-remotely-debug-api-app.md) erfahren Sie, wie Sie die API-App debuggen, während sie in Azure ausgeführt wird.

<!--HONumber=54-->