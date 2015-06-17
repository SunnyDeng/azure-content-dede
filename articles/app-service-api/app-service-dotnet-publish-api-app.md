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
	ms.date="05/19/2015" 
	ms.author="bradyg;tarcher"/>

# Bereitstellen einer Azure App Service-API-App

## Übersicht

Wenn Sie aktiv eine eigene API-App mit Visual Studio entwickeln und Ihre API in der Cloud testen möchten, können Sie in Ihrem Azure-Abonnement eine neue API-App erstellen und den Code mithilfe der praktischen App Service-Bereitstellungsfunktionen von Visual Studio bereitstellen.

Dies ist das zweite von drei Lernprogrammen:

1. In [Erstellen einer API-App](app-service-dotnet-create-api-app.md) erstellen Sie ein API-App-Projekt. 
* In diesem Lernprogramm stellen Sie die API-App in Ihrem Azure-Abonnement bereit.
* In [Debuggen einer API-App](../app-service-dotnet-remotely-debug-api-app.md) führen Sie mithilfe von Visual Studio ein Remotedebugging aus, während der Code in Azure ausgeführt wird.

## Bereitstellen der API-App 

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie auf **Veröffentlichen...**.

![Menüoption zur Projektveröffentlichung](./media/app-service-dotnet-publish-api-app/20-publish-gesture-v3.png)

Klicken Sie auf die Registerkarte **Profil**, und klicken Sie auf **Microsoft Azure API-Apps (Vorschau)**.

![Dialogfeld zur Webveröffentlichung](./media/app-service-dotnet-publish-api-app/21-select-api-apps-for-deployment-v2.png)

Klicken Sie auf **Neu**, um eine neue API-App im Azure-Abonnement bereitzustellen.

![Dialogfeld zur Auswahl vorhandener API-Dienste](./media/app-service-dotnet-publish-api-app/23-publish-to-apiapps-v3.png)

Geben Sie im Dialogfeld **API-App erstellen** Folgendes ein:

- Geben Sie unter **Name der API-App** einen Namen für die App ein. 
- Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie unter "App Service-Plan" einen vorhandenen App Service-Plan aus, oder wählen Sie **Neuen App Service-Plan erstellen**, und geben Sie den Namen eines neuen Plans ein. 
- Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neue Ressourcengruppe erstellen**, und geben Sie einen Namen ein. Der Name muss eindeutig sein. Verwenden Sie eventuell den App-Namen als Präfix, und hängen Sie persönliche Informationen wie z. B. Ihre Microsoft-ID (ohne das @-Zeichen) an.  
- Wählen Sie unter **Zugriffsebene** die Option **Für alle Benutzer verfügbar** aus. Durch diese Option wird steht die gesamte API öffentlich zur Verfügung, was für dieses Lernprogramm in Ordnung ist. Sie können den Zugriff später über das Azure-Vorschauportal einschränken.
- Wählen Sie eine Region aus.  

![Dialogfeld zum Konfigurieren von Microsoft Azure Web-Apps](./media/app-service-dotnet-publish-api-app/24-new-api-app-dialog-v3.png)

Klicken Sie auf **OK**, um die API-App in Ihrem Abonnement zu erstellen. Der Vorgang kann einige Minuten dauern, daher werden Sie von Visual Studio in einem Dialogfeld darüber benachrichtigt, dass der Vorgang gestartet wurde.

![Bestätigungsmeldung zum Start der Erstellung von API-Diensten](./media/app-service-dotnet-publish-api-app/25-api-provisioning-started-v3.png)

Im Rahmen des Bereitstellungsprozesses werden die Ressourcengruppe und die API-App in Ihrem Azure-Abonnement erstellt. Visual Studio zeigt den Fortschritt im Fenster **Azure App Service-Aktivität** an.

![Statusbenachrichtigung über das Fenster "Azure App Service-Aktivität"](./media/app-service-dotnet-publish-api-app/26-provisioning-success-v3.png)

Sobald die API-App bereitgestellt wird, klicken Sie mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie **Veröffentlichen**, um das Dialogfeld für die Veröffentlichung erneut zu öffnen.
 Das Veröffentlichungsprofil, das Sie im vorherigen Schritt erstellt haben, sollte vorausgewählt sein. Klicken Sie auf **Veröffentlichen**, um den Bereitstellungsprozess zu beginnen.

![Bereitstellen der API-App](./media/app-service-dotnet-publish-api-app/26-5-deployment-success-v3.png)

Das Fenster **Azure App Service-Aktivität** zeigt den Fortschritt der Bereitstellung an.

![Statusbenachrichtigung im Fenster "Azure App Service-Aktivität"](./media/app-service-dotnet-publish-api-app/26-5-deployment-success-v4.png)

## Anzeigen der App im Azure-Portal

In diesem Abschnitt navigieren Sie zum Portal, um die grundlegenden Einstellungen für API-Apps anzuzeigen und iterative Änderungen an Ihrer API-App vorzunehmen. Bei jeder Bereitstellung zeigt das Portal die Änderungen an, die Sie in Ihrer API-App vorgenommen haben.

Navigieren Sie in Ihrem Browser zum [Azure-Vorschauportal](https://portal.azure.com).

Klicken Sie in der Randleiste auf die Schaltfläche **Durchsuchen**, und wählen Sie **API-Apps**.

![Suchoptionen im Azure-Portal](./media/app-service-dotnet-publish-api-app/27-browse-in-portal-v3.png)

Wählen Sie die erstellte API aus der Liste der API-Apps in Ihrem Abonnement aus.

![API-Apps-Liste](./media/app-service-dotnet-publish-api-app/28-view-api-list-v3.png)

Klicken Sie auf **API-Definition**. Das Blatt **API-Definition** der App zeigt die Liste der API-Vorgänge an, die Sie beim Erstellen der App definiert haben.

![API-Definition](./media/app-service-dotnet-publish-api-app/29-api-definition-v3.png)

Kehren Sie jetzt zum Projekt in Visual Studio zurück. Fügen Sie der Datei **ContactsController.cs** den folgenden Code hinzu:

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

Dieser Code fügt eine **Post**-Methode hinzu, mit deren Hilfe neue `Contact`-Instanzen in der API platziert werden können.

![Hinzufügen der Post-Methode zum Controller](./media/app-service-dotnet-publish-api-app/30-post-method-added-v3.png)

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

![Kontextmenü zur Projektveröffentlichung](./media/app-service-dotnet-publish-api-app/31-publish-gesture-v3.png)

Wählen Sie die in der Dropdownliste **Konfiguration** die Konfiguration **Debuggen** aus, und klicken Sie auf **Veröffentlichen**, um die API-App erneut bereitzustellen.

![Einstellungen für die Webveröffentlichung](./media/app-service-dotnet-publish-api-app/36.5-select-debug-option-v3.png)

Klicken Sie im Assistenten für die Webveröffentlichung auf der Registerkarte **Vorschau** auf **Veröffentlichen**.

![Dialogfeld zur Webveröffentlichung](./media/app-service-dotnet-publish-api-app/39-re-publish-preview-step-v2.png)

Nach Abschluss des Veröffentlichungsprozesses kehren Sie zum Portal zurück, schließen Sie das Blatt **API-Definition**, und öffnen Sie es erneut. Sie sehen den neuen API-Endpunkt, den Sie gerade erstellt und direkt in Ihrem Azure-Abonnement bereitgestellt haben.

![API-Definition](./media/app-service-dotnet-publish-api-app/38-portal-with-post-method-v4.png)

## Nächste Schritte

Sie haben gesehen, wie die Visual Studio-Funktionen für die direkte Bereitstellung die Iteration und Bereitstellung vereinfachen und es Ihnen ermöglichen, die ordnungsgemäße Funktion der API zu testen. Im [nächsten Lernprogramm](../app-service-dotnet-remotely-debug-api-app.md) erfahren Sie, wie Sie die API-App debuggen, während sie in Azure ausgeführt wird.


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->