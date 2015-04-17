<properties 
	pageTitle="Erstellen einer API-App in Azure App Service" 
	description="Dieser Artikel beschreibt, wie Sie mit Visual Studio 2013 eine API-App in Azure App Service erstellen" 
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

# Erstellen einer API-App in Azure App Service

## Übersicht

Dies ist das erste von drei Lernprogrammen, die Ihnen den Einstieg in API-Apps in Azure App Service ermöglichen.

1. In diesem Lernprogramm erstellen Sie eine neue API-App und bereiten sie für die Bereitstellung in Ihrem Azure-Abonnement vor. 
* In [Bereitstellen einer API-App](app-service-dotnet-create-api-app.md) stellen Sie die API-App in Ihrem Azure-Abonnement bereit.
* In [Debuggen einer API-App](app-service-dotnet-remotely-debug-api-app.md) verwenden Sie Visual Studio zum Remotedebuggen des Codes, während er in Azure ausgeführt wird.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Erstellen Ihrer ersten API-App ##

Öffnen Sie Visual Studio 2013, und wählen Sie **Datei -> Neues Projekt** aus. Wählen Sie die Vorlage **ASP.NET-Webanwendung**.  Benennen Sie das Projekt *ContactsList*, und klicken Sie auf **OK**.

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

Wählen Sie die Projektvorlage **Azure API-App** aus, und klicken Sie dann auf **OK**.

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

Klicken Sie im Web-API-Projekt mit der rechten Maustaste auf den Ordner **Modelle**, und wählen Sie dann im Kontextmenü **Hinzufügen > Klasse**. 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

Nennen Sie die neue Datei *Contact.cs*, und klicken Sie dann auf **Hinzufügen**. 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code. 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann im Kontextmenü **Hinzufügen > Controller**. 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **Web API 2 Controller - Leer** aus, und klicken Sie auf **Hinzufügen**. 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

Nennen Sie den Controller **ContactsController**, und klicken Sie auf **Hinzufügen**. 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

Ersetzen Sie den Code in dieser Datei durch den folgenden Code. 

	using ContactsList.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Web.Http;
	
	namespace ContactsList.Controllers
	{
	    public class ContactsController : ApiController
	    {
	        [HttpGet]
	        public IEnumerable<Contact> Get()
	        {
	            return new Contact[]{
					new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
					new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

API-App-Projekte sind aktiviert mit automatischer [Swagger](http://swagger.io/ "Official Swagger information")-Metadatengenerierung und einer API-Testseite. Standardmäßig ist die API-Testseite deaktiviert. Öffnen Sie zum Aktivieren der API-Testseite die Datei *App_Start/SwaggerConfig.cs*. Suchen Sie nach **EnableSwaggerUI**:

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

Entfernen Sie die Kommentare in den folgenden Codezeilen:

        })
    .EnableSwaggerUi(c =>
        {

Wenn der Vorgang abgeschlossen ist, sollte die Datei in Visual Studio 2013 wie folgt aussehen.

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

Um die API-Testseite anzuzeigen, führen Sie die App lokal aus, und navigieren Sie zu`/swagger`. 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

Klicken Sie auf die Schaltfläche **Testen Sie es**. Sie sehen, dass die API funktioniert und das erwartete Ergebnis zurückgibt. 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## Nächste Schritte

Die API-App kann jetzt bereitgestellt werden. Dazu können Sie das Lernprogramm [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) befolgen.

Weitere Informationen finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->