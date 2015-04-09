<properties 
   pageTitle="Verbinden von mobilen und Web-Apps mit SaaS-APIs (O365, SharePoint, Salesforce) in Azure App Service" 
   description="In diesem Lernprogramm wird veranschaulicht, wie eine SaaS-API-App in einer in Azure App Service Web Apps gehosteten ASP.NET-Anwendung genutzt wird." 
   services="app-service\web" 
   documentationCenter="" 
   authors="syntaxc4" 
   manager="yochayk" 
   editor=""/>

<tags
   ms.service="app-service-web"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web" 
   ms.date="03/24/2015"
   ms.author="cfowler"/>

# Verbinden von mobilen und Web-Apps mit SaaS-APIs (O365, SharePoint, Salesforce) in Azure App Service

In diesem Lernprogramm wird veranschaulicht, wie eine SaaS-API-App in einer in [ App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) gehosteten ASP.NET-Anwendung genutzt wird.

## Übersicht

Sie erhalten Informationen zu folgenden Themen:

- Nutzen einer API-App in einer in Web Apps gehosteten ASP.NET-Anwendung

## Voraussetzungen

Dieses Lernprogramm baut auf der Reihe der API-App-Lernprogramme auf:

1. [Erstellen einer Azure-API-App](app-service-dotnet-create-api-app)
2. [Veröffentlichen einer Azure-API-App](app-service-dotnet-publish-api-app)
3. [Bereitstellen einer Azure-API-App](app-service-dotnet-deploy-api-app)
4. [Debuggen einer Azure-API-App](app-service-dotnet-remotely-debug-api-app)

## Ermöglichen des öffentlichen Zugriffs auf die API-App

Wählen Sie im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) die API-App aus. Klicken Sie auf der Befehlsleiste auf die Schaltfläche **Einstellungen**. Ändern Sie auf dem Blatt **Grundeinstellungen** die **Zugriffsebene** in **Öffentlich (anonym)**.

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Erstellen einer ASP.NET MVC-Anwendung in Visual Studio

1. Öffnen Sie Visual Studio. Fügen Sie im Dialogfeld **Neues Projekt** eine neue **ASP.NET-Webanwendung** hinzu. Klicken Sie auf **OK**.

	![File > New > Web > ASP.NET Web Application](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. Wählen Sie die Vorlage **MVC** aus. Klicken Sie auf **Authentifizierung ändern**, und wählen Sie dann **Keine Authentifizierung** aus. Klicken Sie zweimal auf **OK**.

	![New ASP.NET Application](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das neu erstellte Webanwendungsprojekt, und wählen Sie **Azure-App-Verweis hinzufügen**.

	![Add Azure API App Reference...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. Wählen Sie in der Dropdownliste **Vorhandene API-Apps** die API-App aus, mit der Sie eine Verbindung herstellen möchten.

	![Select Existing API App](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE] Der Clientcode für die Verbindung mit der API-Anwendung wird von einem Swagger-API-Endpunkt automatisch generiert.

1. Um den generierten API-Code zu nutzen, öffnen Sie die Datei "HomeController.cs" und ersetzen die Aktion  `Contact` durch Folgendes:

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![HomeController.cs Code Updates](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. Aktualisieren Sie die Ansicht  `Contact` entsprechend der dynamischen Liste von Kontakten mit dem folgenden Code:  
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact&gt;
	
	// Replace the default email addresses with the following
    &lt;h3&gt;Public Contacts&lt;/h3&gt;
    &lt;ul&gt;
        @foreach (var contact in Model)
        {
            &lt;li&gt;&lt;a href=&quot;mailto:@contact.EmailAddress&quot;&gt;@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a&gt;&lt;/li&gt;
        }
    &lt;/ul&gt; 
	</pre>

	![Contact.cshtml Code Updates](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## Bereitstellen der Webanwendung in Web-Apps in App Service

Befolgen Sie die Anweisungen unter [Bereitstellen einer Azure-Web-App](web-sites-deploy.md).

>[AZURE.NOTE] Wenn Sie in Azure App Service einsteigen möchten, ohne sich zuvor für ein Azure-Konto zu registrieren, wechseln Sie zur Website [App Service ausprobieren](http://go.microsoft.com/fwlink/?LinkId=523751), auf der Sie eine Web-App mit kurzer Gültigkeitsdauer für Einsteiger in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Was sich geändert hat
* Informationen über die Änderung von Websites zu App Service finden Sie unter: [Azure App Service und seine Auswirkungen auf vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714)
* Informationen über die Änderung vom alten Portal zum neuen Portal finden Sie unter: [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->