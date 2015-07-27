<properties 
	pageTitle="Nutzen einer internen API-App in Azure App Service aus einem .NET-Client" 
	description="Finden Sie heraus, wie Sie mithilfe des App Service-SDK eine API-App aus einer .NET-API-APP in der gleichen Ressourcengruppe nutzen." 
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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Nutzen einer internen API-App in Azure App Service aus einem .NET-Client 

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie Code für eine ASP.NET [API-App](app-service-api-apps-why-best-platform.md) schreiben, die eine andere API-App aufruft, die für die Zugriffsebene **Intern** konfiguriert wurde. Beide API-Apps müssen sich in derselben Ressourcengruppe befinden. Zum Aufrufen einer internen API-App aus einer [mobilen App](../app-service-mobile/app-service-mobile-value-prop-preview.md) kann der gleiche Code verwendet werden.

Sie erstellen eine ContactNames-Web-API. Die Get-Methode der Web-API ruft eine API-App "ContactsList" auf und gibt nur die Namen aus den mit der API-App "ContactsList" bereitgestellten Kontaktinformationen zurück. Nachfolgend sehen Sie den Bildschirm der Swagger-Benutzeroberfläche bei einem erfolgreichen Aufruf der ContactNames-Get-Methode.

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

Informationen zum Aufrufen von API-Apps, die für die Zugriffsebenen **Öffentlich (anonym)** oder **Öffentlich (authentifiziert)** finden Sie unter [Nutzen einer API-App in Azure App Service aus einem .NET-Client](app-service-api-dotnet-consume.md).

## Voraussetzungen

Für dieses Lernprogramm wird davon ausgegangen, dass Sie mit dem Erstellen von Projekten und Hinzufügen von Code zu diesen Projekten in Visual Studio vertraut sind. Außerdem wird vorausgesetzt, dass Sie mit den Schritten zum [Verwalten von API-Apps im Azure-Vorschauportal](../app-service-api-apps-manage-in-portal.md) vertraut sind.

Die Projekt- und Codebeispiele in diesem Artikel basieren auf dem API-App-Projekt, das in den folgenden Artikeln erstellt und bereitgestellt wird:

- [Erstellen einer API-App](app-service-dotnet-create-api-app.md)
- [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Für dieses Lernprogramm ist die Version 2.6 des Azure-SDK für .NET erforderlich.

### Einrichten der Ziel-API-App

1. Wenn Sie die erforderlichen Schritte noch nicht ausgeführt haben, befolgen Sie die Anweisungen des Lernprogramms [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md), um das Beispielprojekt "ContactsList" für eine API-App in Ihrem Azure-Abonnement bereitzustellen.

2. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/)auf dem Blatt **API-App** für die API-App "ContactsList", die Sie zuvor bereitgestellt haben, auf **Einstellungen > Anwendungseinstellungen**, und legen Sie für **Zugriffsebene** die Option **Intern** fest. Klicken Sie anschließend auf **Speichern**.

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## Erstellen einer neuen API-App, die die vorhandene API-App aufruft

- Erstellen Sie in Visual Studio ein API-App-Projekt mit dem Namen "ContactNames" mithilfe der Azure-Projektvorlage für API-Apps.

	Dabei handelt es sich um die gleichen Schritte wie in [Erstellen einer API-App](app-service-dotnet-create-api-app.md), Sie fügen dem Projekt jedoch später in diesem Lernprogramm anderen Code hinzu.
 
## Hinzufügen von Clientcode, der über das App Service-SDK erstellt wurde

Die folgenden Schritte werden ausführlicher unter [Nutzen eine API-App in Azure App Service aus einem .NET-Client](app-service-api-dotnet-consume.md) beschrieben.

3. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht auf die Projektmappe), und wählen Sie **Hinzufügen > Azure-API-App-Client**. 

3. Klicken Sie im Dialogfeld **Azure-API-App-Client hinzufügen** auf **Aus Azure-API-App herunterladen**.

5. Wählen Sie in der Dropdownliste die API-App aus, die aufgerufen werden soll. Wählen Sie für dieses Lernprogramm die API-App aus, die Sie zuvor erstellt haben.

7. Klicken Sie auf **OK**.

## Aktivieren der Swagger-Benutzeroberfläche

API-App-Projekte sind standardmäßig mit automatischer [Swagger](http://swagger.io/ "Offizielle Swagger-Informationen")-Metadatengenerierung aktiviert, aber die neue Azure-Projektvorlage für API-Apps deaktiviert die API-Testseite. In diesem Abschnitt aktivieren Sie die Testseite.

1. Öffnen Sie die Datei *App_Start/SwaggerConfig.cs*, und suchen Sie nach **EnableSwaggerUI**:

2. Entfernen Sie die Kommentare in den folgenden Codezeilen:

	        })
	    .EnableSwaggerUi(c =>
	        {

## Erstellen eines Controllers

5. Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann im Kontextmenü **Hinzufügen > Controller**. 

6. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **Web API 2-Controller – Leer** aus, und klicken Sie auf **Hinzufügen**.

7. Geben Sie dem Controller den Namen **ContactsController**, und klicken Sie auf **Hinzufügen**.

## Hinzufügen von Code zum Aufrufen der API-App

Um eine API-App aufzurufen, die durch Festlegen der Zugriffsebene auf **Intern** geschützt wurde, müssen Sie HTTP-Anforderungen interne Authentifizierungsheader hinzufügen. Diese Header informieren die Ziel-API-App darüber, dass die Aufrufquelle eine Peer-API-App ist, die den Aufruf innerhalb derselben Ressourcengruppe tätigt.

Das App Service-SDK generiert Clientklassen, die den Code vereinfachen, den Sie zum Aufrufen der API-App schreiben. Um eine API-App mit der Zugriffsebene **Öffentlich (anonym)** aufzurufen, müssen Sie lediglich ein Clientobjekt erstellen und Methoden für dieses Objekt aufrufen. Beispiel:

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

Um Authentifizierungsheader hinzuzufügen, benötigen Sie jedoch Zugriff auf das `HttpRequestMessage`-Objekt, den Sie bislang nicht haben. Um Zugriff auf die Anforderung zu erhalten und die Header hinzuzufügen, müssen Sie eine `DelegatingHandler`-Klasse erstellen und eine Instanz davon an den Konstruktor des generierten Clients übergeben.

1. Fügen Sie dem Projekt eine Klassendatei mit dem Namen *InternalCredentialHandler.cs* hinzu, und ersetzen Sie den Vorlagencode durch den folgenden Code.

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	Mit diesem Code wird `SignHttpRequest` aufgerufen, um jeder Anforderung, die vom generierten Client gesendet wird, die Authentifizierungsheader hinzuzufügen:

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. Ersetzen Sie in *ContactNamesController.cs* den Vorlagencode durch den folgenden Code.

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	Mit diesem Code übergeben Sie den Handler an den Konstruktor der generierten Clientklasse:

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### Bereitstellen

Bei einer lokalen Ausführung können Sie keine Tests durchführen. Sie müssen den Code bereitstellen und ihn in einer Azure-API-App ausführen. Andernfalls können Sie nicht die richtigen Authentifizierungsheader hinzufügen, und die Aufrufe werden abgelehnt.

Die folgenden Bereitstellungsschritte werden ausführlicher unter [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) beschrieben.

1. Erstellen Sie eine API-App "ContactNames".

	* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie auf **Veröffentlichen**. 

	* Klicken Sie auf die Registerkarte **Profil**, und klicken Sie dann auf **Microsoft Azure-API-Apps**.

	* Klicken Sie auf **Neu**, um eine neue API-App im Azure-Abonnement bereitzustellen.

	* Geben Sie im Dialogfeld **API-App erstellen** "ContactNames" als **API-App-Name** ein.

	* Geben Sie für die anderen Werte im Dialogfeld **API-App erstellen** die gleichen Werte ein, die Sie zuvor für [API-App bereitstellen](app-service-dotnet-deploy-api-app.md) angegeben haben.

		Stellen Sie jedoch auf jeden Fall sicher, dass Sie die neue API-App in der gleichen Ressourcengruppe wie die API-App erstellen, die Sie aufrufen möchten.

	* Klicken Sie auf **OK**.

2. Stellen Sie den Code für die neue API-App bereit.

	* Sobald die API-App bereitgestellt wurde, klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Veröffentlichen**, um das Dialogfeld "Veröffentlichen" erneut zu öffnen.

	* Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**, um den Bereitstellungsprozess zu beginnen.

### Test

In diesem Abschnitt verwenden Sie die Swagger-UI zum Testen der neuen API-App und um sicherzustellen, dass sie die API-App aufrufen kann, die Sie zuvor erstellt haben.

1. Öffnen Sie einen Browser mit der neuen API-App-URL.

	Mit den Standardeinstellungen zum Veröffentlichen wird automatisch ein Browser mit der URL der API-App geöffnet, wenn Visual Studio den Veröffentlichungsprozess abschließt. Wenn dies nicht der Fall ist oder wenn Sie dieses Browserfenster geschlossen haben, gehen Sie folgendermaßen vor, um die gleiche URL zu erreichen:

	* Wechseln Sie im Azure-Vorschauportal zum Blatt "API-App" für die neue API-App "ContactsName".

	* Klicken Sie auf **URL**.

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. Fügen Sie der URL in der Adresszeile des Browsers `/swagger` hinzu, und drücken Sie dann die EINGABETASTE.

	Die resultierend URL sieht beispielsweise folgendermaßen aus:

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. Klicken Sie auf der Seite "Swagger-UI" auf **ContactNames > Abrufen > Jetzt testen!**

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	Auf der Seite werden die Kontaktnamen im Bereich "Antworttext" angezeigt, womit sichergestellt wird, dass die API-App "ContactNames" erfolgreich Daten von der API-App "ContactsList" abgerufen hat.

	Wenn Sie sicherstellen möchten, dass die Einstellung **Intern** die API-App "ContactsList" schützt, kommentieren Sie den `SignHttpRequest`-Aufruf in *ContactNamesController.cs* aus, stellen Sie sie erneut bereit, führen Sie den Swagger-Befehl **Jetzt testen** erneut aus, und Sie erhalten eine Fehlermeldung.


## Hinzufügen von Code für den Aufruf der API-App mithilfe von HttpClient
 
Das App Service-SDK ist von den Swagger-API-Definitionen abhängig, um Clientklassen zu generieren. Wenn Sie eine API-App aufrufen möchten, für die keine Swagger-API-Definitionen implementiert wurden, können Sie dazu `HttpClient` verwenden. In diesem Fall verwenden Sie weiterhin die `SignHttpRequest`-Methode, aber Sie rufen sie direkt über das `HttpRequestMessage`-Objekt auf.

1. Fügen Sie *ContactNamesController.cs* den folgenden Code vor der `return names;`-Anweisung in der `Get`-Methode hinzu.

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	Mit diesem Code wird das eingehende Anforderungsobjekt an die `SignHttpRequest`-Methode übergeben, um das ausgehende Anforderungsobjekt zu signieren:

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. Wechseln Sie im Azure-Vorschauportal zum Blatt "API-App" für die API-App "ContactsList", und kopieren Sie die URL.

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. Ersetzen Sie die Platzhalterzeichenfolge "{yourapiappurl}" im Controller-Code durch die tatsächliche URL. Wenn Sie fertig sind, sieht diese Codezeile wie im folgenden Beispiel aus.

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### Bereitstellen und Testen

1. Führen Sie dieselben Schritte wie bereits zuvor zur Bereitstellung des API-App-Codes aus.

	**Hinweis:** Sie können das Dialogfeld **Web veröffentlichen** umgehen und die Bereitstellung erneut durchführen, indem Sie auf eine einzelne Schaltfläche auf der Symbolleiste klicken. Klicken Sie in Visual Studio auf **Ansicht > Symbolleisten**, und aktivieren Sie die Symbolleiste **Webveröffentlichung mit einem Klick**.
 
2. Führen Sie dieselben Schritte aus, die Sie zuvor zur Verwendung der Swagger-UI durchgeführt haben.

	Da Sie den HttpClient-Code beibehalten haben, zeigt die Ausgabe dieses Mal einen doppelten Satz an Namen.

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine interne API-App aus einem .NET-Client nutzen. Informationen zum Nutzen von API-Apps, die die Zugriffsebenen **Öffentlich (anonym)** oder **Öffentlich (authentifiziert)** haben, finden Sie unter [Nutzen einer API-App in Azure App Service aus einem .NET-Client](app-service-api-dotnet-consume.md).

Um weitere Codebeispiele für den Aufruf einer API-App aus einem .NET-Client zu erhalten, laden Sie die [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample)-Beispielanwendung herunter.

Informationen zur Authentifizierung in App Service finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=July15_HO3-->