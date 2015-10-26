<properties 
	pageTitle="Erstellen einer ASP.NET-API-App in Azure App Service mithilfe von Visual Studio 2015 " 
	description="Erfahren Sie, wie Sie mithilfe von Visual Studio 2015 eine ASP.NET-API-App in Azure App Service erstellen." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/14/2015" 
	ms.author="tdykstra"/>

# Erstellen einer ASP.NET-API-App in Azure App Service mithilfe von Visual Studio 2015

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Übersicht

In diesem Lernprogramm erstellen Sie ein ASP.NET-Web-API 2-Projekt mithilfe von [Visual Studio 2015 RC](https://www.visualstudio.com/de-DE/downloads/visual-studio-2015-downloads-vs.aspx) und konfigurieren es zur Bereitstellung in der Cloud als [API-App](app-service-api-apps-why-best-platform.md) in [Azure App Service](../app-service/app-service-value-prop-what-is.md). Sie stellen das Projekt auch für Azure bereit. Am Ende des Lernprogramms haben Sie eine API-App, die in der Azure-Cloud ausgeführt wird.

Für das Lernprogramm wird davon ausgegangen, dass Sie die Arbeit mit Dateien und Ordnern im Visual Studio **Projektmappen-Explorer** beherrschen.

Das Lernprogramm funktioniert mit der derzeit veröffentlichten Version der ASP.NET-Web-API. Informationen zum Erstellen einer ASP.NET MVC 6-API-App finden Sie im Blogbeitrag: [https://alexanderzeitler.com/articles/Deploying-a-ASP-NET-MVC-6-API-as-Azure-API-App-in-Azure-App-Services/](https://alexanderzeitler.com/articles/Deploying-a-ASP-NET-MVC-6-API-as-Azure-API-App-in-Azure-App-Services/ "Bereitstellen einer ASP.NET MVC 6-API als Azure-API-App in Azure App Services").

[AZURE.INCLUDE [install-sdk-2015-only](../../includes/install-sdk-2015-only.md)]

Für dieses Lernprogramm ist die Version 2.6 des Azure-SDK für .NET erforderlich.

## Erstellen eines API-App-Projekts 

Visual Studio 2015 RC verfügt noch nicht über eine API-App-Projektvorlage. Verwenden Sie daher die Web-API-Projektvorlage, um ein API-App-Projekt von Grund auf zu erstellen.

1. Öffnen Sie Visual Studio 2015 RC.

2. Klicken Sie auf **Datei > Neues Projekt**.

3. Klicken Sie unter **Vorlagen** auf **Web**, und klicken Sie dann auf die Vorlage **ASP.NET-Webanwendung**.

4. Geben Sie dem Projekt den Namen *ContactsList*.

5. Stellen Sie sicher, dass das Kontrollkästchen **Add Application Insights to Project** deaktiviert ist.

5. Klicken Sie auf **OK**.

	![](./media/app-service-dotnet-create-api-app-vs2015/newproj.png)

6. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** unter **ASP.NET 4.6-Vorlagen** die Projektvorlage **Leer** aus.

7. Aktivieren Sie das Kontrollkästchen **Web-API**.

8. Heben Sie die Auswahl des Kontrollkästchens **In der Cloud hosten** auf.

7. Klicken Sie auf **OK**.

	![](./media/app-service-dotnet-create-api-app-vs2015/newaspnet.png)

## Hinzufügen von NuGet-Paketen

Die App Service-Runtime für API-Apps wird durch das NuGet-Paket [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) angegeben und die dynamische [Swagger](http://swagger.io/ "Offizielle Swagger-Informationen")-API-Metadatengenerierung durch das NuGet-Paket [Swashbuckle](http://www.nuget.org/packages/Swashbuckle/).

> **Hinweis:** Bei der Installation des Swashbuckle-Pakets wird standardmäßig eine API-Testseite aktiviert. Wenn Sie die API-App veröffentlichen, und ihre Zugriffsebene auf **Öffentlich (anonym)** setzen, kann jede Person, die die Testseiten-URL findet, über diese Ihre API aufrufen. Sie verwenden die Testseite später in diesem Lernprogramm.

1. Klicken Sie auf **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole**.

2. Geben Sie in der **Paket-Manager-Konsole (PCM)** die folgenden Befehle ein.

		install-package Microsoft.Azure.AppService.ApiApps.Service
		install-package Swashbuckle

	Sie müssen möglicherweise einige Minuten warten, nachdem der PMC die Meldung anzeigt, in der darauf hingewiesen wird, dass die Abhängigkeiten überprüft werden.

## Hinzufügen von API-App-Metadatendateien

Die Metadaten, dank derer ein Web-API-Projekt als eine API-App bereitgestellt werden kann, sind in der Datei *apiapp.json* und dem Ordner *Metadata* sowie dessen Unterordern und Dateien enthalten. In den folgenden Schritten fügen Sie diese Dateien mit Standardwerten hinzu.

Im Abschnitt [API-App-Metadaten](#api-app-metadata) später in diesem Lernprogramm wird erläutert, wie Sie diese Metadaten anpassen.

1. Erstellen Sie im Projektordner eine *JSON*-Datei mit dem Namen "apiapp.json", und ersetzen Sie den Inhalt der neuen Datei mit dem folgenden JSON-Text.

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsListTitle",
		    "summary": "Summary",
		    "author": "Author",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

3. Erstellen Sie im Projektordner einen Ordner namens *Metadata*, und erstellen Sie im Ordner *Metadata* einen Ordner mit dem Namen *deploymentTemplates*.

5. Erstellen Sie im Ordner *deploymentTemplates* eine *JSON*-Datei mit dem Namen *apiappconfig.azureresource.json*, und ersetzen Sie den Inhalt der neuen Datei mit dem folgenden JSON-Text.

		{
		  "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "$system": {
		      "type": "Object"
		    }
		  },
		  "resources": []
		}

## Hinzufügen von Web-API-Code

In den folgenden Schritten fügen Sie Code für eine einfache HTTP-Get-Methode hinzu, die eine hartcodierte Liste mit Kontakten zurückgibt.

1. Erstellen Sie im Projektordner den Ordner *Models*, sofern er noch nicht vorhanden ist.

2. Fügen Sie im Ordner *Models* eine Klassendatei mit dem Namen *Contact.cs* hinzu, und ersetzen Sie den Inhalt der Datei durch den folgenden Code. namespace ContactsList.Models { public class Contact { public int Id { get; set; } public string Name { get; set; } public string EmailAddress { get; set; } } }

5. Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann im Kontextmenü **Hinzufügen > Controller**.

	![](./media/app-service-dotnet-create-api-app-vs2015/05-new-controller-v3.png)

6. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **Web API 2-Controller – Leer** aus, und klicken Sie auf **Hinzufügen**.

	![](./media/app-service-dotnet-create-api-app-vs2015/06-new-controller-dialog-v3.png)

7. Nennen Sie den Controller **ContactsController**, und klicken Sie auf **Hinzufügen**.

	![](./media/app-service-dotnet-create-api-app-vs2015/07-new-controller-name-v2.png)

8. Ersetzen Sie den Inhalt der Datei *ContactsController.cs* durch den folgenden Code:

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

## Testen der Web-API

Führen Sie die folgenden Schritte aus, um die API-Testseite anzuzeigen.

1. Führen Sie die App lokal aus (STRG+F5), und fügen Sie `/swagger` am Ende der URL in der Adressleiste des Browsers ein. 

	![](./media/app-service-dotnet-create-api-app-vs2015/14-swagger-ui.png)

2. Klicken Sie auf **Contacts > Abrufen > Jetzt testen**. Sie sehen, dass die API funktioniert und das erwartete Ergebnis zurückgibt.

	![](./media/app-service-dotnet-create-api-app-vs2015/15-swagger-ui-post-test.png)

## Erstellen einer API-App in Azure

1. Erstellen Sie eine API-App im [Azure-Vorschauportal](https://portal.azure.com). 

	* Klicken Sie auf **Neu > Web + Mobil > API-App**.

		![](./media/app-service-dotnet-create-api-app-vs2015/createapiapp1.png)

	* Geben Sie unter **Name** den Namen "ContactsList" ein.

	* Klicken Sie in **App Service-Plan** auf **Neu erstellen**, und geben Sie einen Namen ein, zum Beispiel: **ContactsList**.

		Weitere Informationen zu Azure App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](azure-web-sites-web-hosting-plans-in-depth-overview.md).

	* Klicken Sie auf **Tarif > Alle anzeigen > Free > Auswählen**, um den kostenlosen Tarif auszuwählen.

		Sie können eine kostenpflichtige Preisstufe wählen, im Rahmen dieses Lernprogramms ist dies jedoch nicht erforderlich.

	* Klicken Sie in **Ressourcengruppe** auf **Neu erstellen**, und geben Sie dann einen Namen ein, z. B. Beispiel "ContactsList".

		Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-overview.md).

	* Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten.

	* Wählen Sie einen Standort in Ihrer Nähe aus.

	* Klicken Sie auf **Erstellen**.

		![](./media/app-service-dotnet-create-api-app-vs2015/createapiapp2.png)

2. Wenn die Erstellung der API-App in Azure beendet ist, legen Sie als Zugriffsebene der API-App **Öffentlich (anonym)** fest.

	* Klicken Sie auf **Durchsuchen > Ressourcengruppen > [die erstellte Ressourcengruppe] > [die erstellte API-App]**.

	* Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

	* Legen Sie die **Zugriffsebene** auf **Öffentlich (anonym)** fest.
	 
	* Klicken Sie auf **Speichern**.

		![](./media/app-service-dotnet-create-api-app-vs2015/setpublicanon.png)
	
2. Notieren Sie den Namen der zugrunde liegenden Web-App, die API-App hostet. Sie werden diesen beim Bereitstellen des Visual Studio-Projekts verwenden.

	* Klicken Sie unter **API-App-Host** auf **ContactsList**.

		![](./media/app-service-dotnet-create-api-app-vs2015/clickapiapphost.png)

	* Der Name befindet sich im Titel des Blatts **API-App-Host**.

		![](./media/app-service-dotnet-create-api-app-vs2015/apiapphostname.png)

## Bereitstellen des Web-API-Projekts in der neuen API-App in Azure
 
API-Apps sind im Grunde Web-Apps, für die Azure zusätzliche Funktionen für die Web Service-Funktionalität bereitstellt. In Visual Studio 2015 RC führen Sie die Veröffentlichung auf der zugrunde liegenden Web-App der API-App durch, da der Assistent "Web veröffentlichen" keine spezielle Auswahl für API-Apps bietet.

2. Klicken Sie im Visual Studio **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie dann im Kontextmenü auf **Veröffentlichen**.

3. Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Microsoft Azure-Web-Apps**.

	![](./media/app-service-dotnet-create-api-app-vs2015/pubwebselwebapp.png)

4. Wählen Sie in der Dropdownliste **Vorhandene Web-Apps** den Eintrag mit dem API-App-Namen aus, den Sie zuvor notiert haben.

	![](./media/app-service-dotnet-create-api-app-vs2015/pubwebselapiapp.png)

5. Klicken Sie auf **Veröffentlichen**.

	Im Browser wird die Web-App-URL geöffnet, der eine Seite "API-Anwendung wurde erstellt" zeigt.

6. Fügen Sie in der Adresszeile des Browsers am Ende der URL "swagger/" hinzu. Beispiel:

		https://microsoft-apiappb001b62a9033493a33748332233fca2.azurewebsites.net/swagger/

	Sie sehen die gleiche Swagger-UI, die Sie bereits zuvor mit einer lokalen Ausführung gesehen haben. Jetzt wird sie jedoch in der Cloud ausgeführt.

2. Klicken Sie auf **Contacts > Abrufen > Jetzt testen**. Sie sehen, dass die API funktioniert und das erwartete Ergebnis zurückgibt.

	![](./media/app-service-dotnet-create-api-app-vs2015/runninginazure.png)

## Anzeigen der API-Definition im Azure-Vorschauportal

In diesem Abschnitt navigieren Sie zum Portal, um die API-Definition für die soeben erstellte API-App anzuzeigen.

1. Navigieren Sie im [Azure-Vorschauportal](https://portal.azure.com) zum Blatt **API-App** für Ihre API-App: Klicken Sie auf **Durchsuchen > Ressourcengruppen > [die erstellte Ressourcengruppe] > [die erstellte API-App]**.

4. Klicken Sie auf **API-Definition**.

	Das Blatt **API-Definition** der App zeigt die Liste der API-Vorgänge an, die Sie beim Erstellen der App definiert haben. (Wenn Sie die Anweisungen in diesem Lernprogramm befolgt haben, wird nur ein Get-Vorgang angezeigt.)

	![API-Definition](./media/app-service-dotnet-create-api-app-vs2015/29-api-definition-v3.png)

## Hinzufügen eines Vorgangs zum Web-API-Code

5. Wechseln Sie zurück zum Projekt in Visual Studio, und fügen Sie der Datei **ContactsController.cs** den folgenden Code hinzu. Dieser Code fügt eine **Post**-Methode hinzu, mit deren Hilfe neue `Contact`-Instanzen in der API platziert werden können.  

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Hinzufügen der Post-Methode zum Controller](./media/app-service-dotnet-create-api-app-vs2015/30-post-method-added-v3.png)

6. Veröffentlichen Sie das Projekt mit den gleichen Schritten wie zuvor. (Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, klicken Sie auf **Veröffentlichen** und dann im Assistenten zum **Veröffentlichen im Web** auf **Veröffentlichen**.)

12. Wechseln Sie nach Abschluss des Veröffentlichungsprozesses wieder zum Portal, und starten Sie wie zuvor das Gateway neu.

14. Wechseln Sie im Portal wieder zum Blatt **API-Definition**.

	Sie sehen nun den neuen API-Endpunkt, den Sie gerade erstellt und in Ihrem Azure-Abonnement bereitgestellt haben.

	![API-Definition](./media/app-service-dotnet-create-api-app-vs2015/38-portal-with-post-method-v4.png)

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Nächste Schritte

Sie haben nun eine API-Anwendung mithilfe von Visual Studio 2015 RC erstellt und bereitgestellt. Zusätzliche Dokumentation zu API-Apps finden Sie unter den Einträgen im Navigationsbereich auf der linken Seite der Seite (bei breiten Browserfenstern) oder am oberen Rand der Seite (bei schmalen Browserfenstern). Der Großteil der API-App-Dokumentation zeigt derzeit Visual Studio 2013, aber vieles davon gilt auch für VS 2015, da die Benutzeroberfläche ähnlich ist. Der Code, den Sie schreiben, ist derselbe und auch die Portalbenutzeroberfläche ist identisch.
 

<!---HONumber=Oct15_HO3-->