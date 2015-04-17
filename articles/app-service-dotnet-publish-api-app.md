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
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Bereitstellen einer API-App in Azure App Service

## Übersicht

Wenn Sie aktiv eine eigene API-App mit Visual Studio entwickeln und Ihre API in der Cloud testen möchten, können Sie in Ihrem Azure-Abonnement eine neue API-App erstellen und den Code mithilfe der praktischen App Service-Bereitstellungsfunktionen von Visual Studio bereitstellen. 

Dies ist das zweite von drei Lernprogrammen:

1. In [Erstellen einer API-App](app-service-dotnet-create-api-app.md) haben Sie ein API-App-Projekt erstellt. 
* In diesem Lernprogramm werden Sie die API-App in Ihrem Azure-Abonnement bereitstellen.
* In [Debuggen einer API-App](app-service-dotnet-remotely-debug-api-app.md) werden Sie den Code mithilfe von Visual Studio remote debuggen, während er in Azure ausgeführt wird.

## Bereitstellen der API-App 

Klicken Sie im **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie auf **Veröffentlichen**. 

![Project publish menu option](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

Klicken Sie auf die Registerkarte **Profil**, und klicken Sie auf **Microsoft Azure-API-Apps (Vorschau)**. 

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

Klicken Sie auf **Neu**, um eine neue API-App im Azure-Abonnement bereitzustellen.

![Select Existing API Services dialog](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

Geben Sie im Dialogfeld **API-App erstellen** Folgendes ein:

- Geben Sie unter **Name der API-App** einen Namen für die App ein. 
- Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten.
Treffen Sie unter "App Service-Plan" eine Auswahl aus Ihren vorhandenen App Service-Plänen, oder wählen Sie **Neuen App Service-Plan erstellen**, und geben Sie den Namen eines neuen Plans ein. 
- Treffen Sie unter **Ressourcengruppe** eine Auswahl aus Ihren vorhandenen Ressourcengruppen, oder wählen Sie **Neue Ressourcengruppe erstellen**, und geben Sie einen Namen ein. Der Name muss eindeutig sein. Verwenden Sie eventuell den App-Namen als Präfix, und hängen Sie einige persönliche Informationen wie z. B. Ihre Microsoft-ID (ohne das @-Zeichen) an.  
- Wählen Sie unter **Zugriffsebene** die Option **Für jeden verfügbar** aus. Durch diese Option wird die API komplett öffentlich, was für dieses Lernprogramm in Ordnung ist. Sie können den Zugriff später über das Azure-Portal einschränken.
- Wählen Sie eine Region aus.  

![Configure Microsoft Azure Web App dialog](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

Klicken Sie auf **OK**, um die API-App in Ihrem Abonnement zu erstellen. Der Prozess kann einige Minuten dauern, daher werden Sie von Visual Studio über ein Dialogfeld benachrichtigt, dass der Vorgang gestartet wurde. 

![API Service Creation Started confirmation message](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

Im Rahmen des Bereitstellungsprozesses werden die Ressourcengruppe und die API-App im Azure-Abonnement erstellt. Visual Studio zeigt den Fortschritt im Fenster **Azure App Service-Aktivität** an. 

![Status notification via the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

Sobald die API-App bereitgestellt wird, klicken Sie mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie **Veröffentlichen**, um das Dialogfeld "Veröffentlichen" erneut zu öffnen. Das Veröffentlichungsprofil, das Sie im vorherigen Schritt erstellt haben, sollte vorab ausgewählt sein. Klicken Sie auf **Veröffentlichen**, um den Bereitstellungsprozess zu beginnen. 

![Deploying the API App](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

Das Fenster **Azure App Service-Aktivität** zeigt den Fortschritt der Bereitstellung an. 

![Status notification of the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Anzeigen der App im Azure-Portal

In diesem Abschnitt navigieren Sie zum Portal, um die grundlegenden Einstellungen für API-Apps anzuzeigen und iterative Änderungen an Ihrer API-App vorzunehmen. Bei jeder Bereitstellung zeigt das Portal die Änderungen an, die Sie in Ihrer API-App vorgenommen haben. 

Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com). 

Klicken Sie in der Randleiste auf die Schaltfläche **Durchsuchen**, und wählen Sie **API-Apps**.

![Browse options on Azure portal](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

Wählen Sie die API, die Sie erstellt haben, aus der Liste der API-Apps in Ihrem Abonnement aus.

![API Apps list](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

Klicken Sie auf **API-Definition**. Das Blatt **API-Definition** der App zeigt die Liste der API-Vorgänge an, die Sie beim Erstellen der App definiert haben. 

![API Definition](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Kehren Sie jetzt zum Projekt in Visual Studio zurück. Fügen Sie der Datei **Contactsontroller.cs** den folgenden Code hinzu:  

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

Dieser Code fügt eine **Post**-Methode hinzu, mit deren Hilfe neue `Kontakt`-Instanzen in der API gepostet werden können. 

![Adding the Post method to the controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus. 

![Project Publish context menu](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

Wählen Sie die Konfiguration **Debuggen** aus der Dropdownliste **Konfiguration** aus, und klicken Sie auf **Veröffentlichen**, um die API-App erneut bereitzustellen. 

![Publish Web settings](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Vorschau** auf **Veröffentlichen**.  

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

Nach Abschluss des Veröffentlichungsprozesses kehren Sie zum Portal zurück, schließen Sie das Blatt **API-Definition**, und öffnen Sie es erneut. Sie sehen den neuen API-Endpunkt, den Sie gerade erstellt und direkt in Ihrem Azure-Abonnement bereitgestellt haben.

![API Definition](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Nächste Schritte

Sie haben gesehen, wie die direkten Bereitstellungsfunktionen in Visual Studio die Iteration und Bereitstellung sowie das Testen der ordnungsgemäßen Funktion der API vereinfachen. Im [nächsten Lernprogramm](app-service-dotnet-remotely-debug-api-app.md) erfahren Sie, wie Sie die API-App debuggen, während sie in Azure ausgeführt wird.



<!--HONumber=49-->