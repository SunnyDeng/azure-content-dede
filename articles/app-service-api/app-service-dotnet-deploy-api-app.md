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

In diesem Lernprogramm stellen Sie das Web-API-Projekt, das Sie im [vorherigen Lernprogramm](app-service-dotnet-create-api-app.md) erstellt haben, in einer neuen [API-App](app-service-api-apps-why-best-platform.md) bereit. Sie verwenden Visual Studio zum Erstellen der API-App-Ressource in [Azure App Service](../app-service/app-service-value-prop-what-is.md) und zum Bereitstellen Ihres Web-API-Codes in der Azure API-App.

### Weitere Optionen für die Bereitstellung

Es gibt zahlreiche weitere Möglichkeiten zum Bereitstellen von API-Apps. Eine API-App ist eine [Web-App](../app-service-web/app-service-web-overview.md) mit zusätzlichen Funktionen zum Hosten von Webdiensten, und sämtliche der [verfügbaren Bereitstellungsmethoden für Web-Apps](../app-service-web/web-sites-deploy.md) können auch für API-Apps verwendet werden. Die Web-App, die eine API-App hostet, wird im Azure-Vorschauportal als API-App-Host bezeichnet, und Sie können die Bereitstellung über das Portalblatt "API-App-Host" konfigurieren. Weitere Informationen zum Blatt "API-App-Host" finden Sie unter [Verwalten einer API-App](app-service-api-manage-in-portal.md).

Die Tatsache, dass API-Apps auf Web-Apps basieren, bedeutet auch, dass Sie Code, der für andere Plattformen als ASP.NET geschrieben wurde, in API-Apps bereitstellen können. Ein Beispiel mit Verwendung von Git zum Bereitstellen von Node.js-Code in einer API-App finden Sie unter [Erstellen einer Node.js-API-App in Azure App Service](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Erstellen einer API-App in Azure 

In diesem Abschnitt verwenden Sie den Visual Studio-Assistenten **Web veröffentlichen** zum Erstellen einer API-App in Azure. Wenn Sie aufgefordert werden, einen Namen für die API-App einzugeben, geben Sie *ContactsList* ein.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Bereitstellen des Codes für die neue API-App

Verwenden Sie nochmals den Assistenten **Web veröffentlichen** zum Bereitstellen Ihres Codes für die neue API-App.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Anzeigen der App im Azure-Vorschauportal

In diesem Abschnitt zeigen Sie im Portal die für API-Apps verfügbaren grundlegenden Einstellungen an und nehmen iterative Änderungen an Ihrer API-App vor. Bei jeder Bereitstellung zeigt das Portal die Änderungen an, die Sie an Ihrer API-App vorgenommen haben.

1. Wechseln Sie im [Azure-Vorschauportal](https://portal.azure.com) zum Blatt **API-App** für die API-App, die Sie bereitgestellt haben.

4. Klicken Sie auf **API-Definition**.
 
	Das Blatt **API-Definition** der App zeigt die Liste der API-Vorgänge an, die Sie beim Erstellen der App definiert haben.

	![API-Definition](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Wechseln Sie jetzt zurück zum Projekt in Visual Studio, und fügen Sie der Datei **ContactsController.cs** den folgenden Code hinzu.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Hinzufügen der Post-Methode zum Controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	Dieser Code fügt eine **Post**-Methode hinzu, mit deren Hilfe neue `Contact`-Instanzen in der API platziert werden können.

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

Sie haben gesehen, wie die Visual Studio-Funktionen für die direkte Bereitstellung die Iteration und Bereitstellung vereinfachen und es Ihnen ermöglichen, die ordnungsgemäße Funktion der API zu testen. Im [nächsten Lernprogramm](../app-service-dotnet-remotely-debug-api-app.md) erfahren Sie, wie Sie die API-App debuggen, während sie in Azure ausgeführt wird.
 

<!---HONumber=July15_HO3-->