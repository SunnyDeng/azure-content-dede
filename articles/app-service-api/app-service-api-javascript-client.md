<properties 
	pageTitle="Zugreifen auf eine Azure-API-App mithilfe von HTML und Javascript" 
	description="Erfahren Sie, wie Sie mithilfe von HTML und JavaScript auf Ihr API-App-Back-End zugreifen." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="bradygaster"/>

# Nutzen einer Azure-API-App mithilfe von HTML und Javascript

## Übersicht

Dieser Artikel zeigt, wie Sie einen HTML- und einen JavaScript-Client für eine [API-App](app-service-api-apps-why-best-platform.md) in[ Azure App Service](/documentation/services/app-service/) erstellen. In diesem Artikel wird vorausgesetzt, dass Sie bereits mit der Arbeit in HTML und JavaScript vertraut sind. Zum Durchführen von REST-Aufrufen an die API-App wird das JavaScript-Framework [AngularJS](https://angularjs.org/) verwendet.

Nachfolgend werden einige Artikel aufgeführt, die Sie für einen einfacheren Einstieg vor der Bearbeitung dieses Artikels lesen sollten.

1. In [Erstellen einer API-App](app-service-dotnet-create-api-app.md) erstellen Sie ein API-App-Projekt.
2. In [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) stellen Sie die API-App für Ihr Azure-Abonnement bereit.
3. In [Debuggen einer API-App](../app-service-dotnet-remotely-debug-api-app.md) verwenden Sie Visual Studio, um ein Remotedebugging des Codes durchzuführen, während dieser in Azure ausgeführt wird.

Der vorliegende Artikel baut auf den zuvor genannten Artikeln auf und veranschaulicht, wie Ihre HTML-Anwendungen mithilfe von JavaScript auf Ihre Back-End-API-Apps zugreifen können.

## Aktivieren von CORS

Typischerweise wird CORS (Cross-Origin Resource Sharing) in HTML-Anwendungen benötigt, die von anderen Hosts bedient werden als von der API selbst. Bei API-Apps stehen mindestens zwei Optionen für die Aktivierung von CORS zur Verfügung. Diese Optionen werden im vorliegenden Abschnitt beschrieben.

### Aktivieren von CORS für API-App-Gateways

API-App-Gateways können mit dem Azure-Vorschauportal zur Aktivierung von CORS konfiguriert werden. Durch Hinzufügen von **MS_CrossDomainOrigins** *appSetting* können Sie angeben, welche URLs zum Aufrufen Ihrer API-App berechtigt sind. In diesem Abschnitt wird erläutert, wie Sie *appSetting* zum Aktivieren von CORS auf API-Gatewayebene verwenden.

1. Navigieren Sie im Azure-Vorschauportal zum Blatt für die API-App, für die Sie CORS aktivieren möchten. Klicken Sie auf dem Blatt auf das Symbol *Gateway* für Ihre API-App. 

	![Klick auf die Schaltfläche für das API-App-Gateway](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. Klicken Sie im Portalblatt auf den Link **Gatewayhost**.

	![Klick auf den Link für den API-App-Gatewayhost](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. Klicken Sie im Portalblatt auf den Link **Alle Einstellungen**.

	![Gateway – Alle Einstellungen (Link)](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. Klicken Sie im Portalblatt auf die Schaltfläche **Anwendungseinstellungen**.

	![Gateway – Anwendungseinstellungen](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. Fügen Sie die Anwendung **MS_CrossDomainOrigins** hinzu. Legen Sie die Einstellung auf die durch Trennzeichen getrennte Liste der HTTP-Hosts fest, denen Sie Zugriff auf Ihre API-App gewähren möchten. Wenn Sie mehreren Hosts Zugriff erteilen möchten, können Sie den Wert von *appSetting* auf einen ähnlichen wie den nachstehenden Code festlegen.

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	In diesem Abschnitt erfahren Sie Schritt für Schritt, wie Sie eine separate Webanwendung mit einer einfachen HTML-Seite erstellen, die eine API-App aus einer Web-App aufruft, die in derselben Azure-Ressourcengruppe ausgeführt wird wie die API-App. Da dies der einzige Host ist, der Zugriff auf die API-App erhält, enthält die Einstellung nur einen Host.

		http://contactlistwebapp.azurewebsites.net

	Der nachstehende Screenshot zeigt, wie die Einstellung aussehen sollte, nachdem Sie sie im Azure-Vorschauportal gespeichert haben.

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

Die Anwendungseinstellung **MS_CrossDomainOrigins** wird im Blogbeitrag [Azure Mobile Service .NET Updates](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/) ausführlich besprochen. Wenn Sie daher weitere Informationen zu dieser Einstellung benötigen, besuchen Sie diesen Blog.

### Aktivieren von CORS im Web-API-Code

Das Vorgehen zum Aktivieren von CORS in Web API wird im ASP.NET-Artikel [Aktivieren von ursprungsübergreifenden Anforderungen in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) ausführlich erläutert. Für API-Apps, die mit ASP.NET Web API erstellt werden, ist die Vorgehensweise genau dieselbe, diese wird jedoch nachstehend nochmals zusammengefasst. Überspringen Sie diesen Abschnitt, wenn Sie CORS bereits aktiviert haben, da Ihre API-App bereits ordnungsgemäß eingerichtet sein sollte.

1. Die CORS-Funktionalität wird durch das NuGet-Paket [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) bereitgestellt. Installieren Sie dieses Paket, indem Sie die **Paket-Manager-Konsole** öffnen und das folgende PowerShell-Skript ausführen. 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. Nachdem der Befehl ausgeführt wurde, zeigen Paket-Manager-Konsole und**packages.config** an, dass das neue NuGet-Paket hinzugefügt wurde.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/01-cors-installed.png)

1. Öffnen Sie die Datei *App_Start/WebApiConfig.cs*. Fügen Sie in der Datei die nachstehende Codezeile zur Methode **Register** der Klasse **WebApiConfig** hinzu.

		config.EnableCors();

	Nachdem die Datei aktualisiert wurde, sollte der Code in etwa so aussehen:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Der abschließende Schritt zum Aktivieren von CORS besteht darin, die Aktionsmethoden zu markieren, die Sie aktivieren möchten. Fügen Sie jeder Methode oder dem gesamten Controller das Attribut **EnableCors** hinzu, wie im nachstehenden Code gezeigt.

	> **Hinweis**: Das Verwenden von Platzhaltern für sämtliche Parameter mit dem EnableCors-Attribut ist ausschließlich für Demonstrationszwecke gedacht, da es Ihre API für beliebige Ursprünge und HTTP-Anforderungen öffnet. Sie sollten dieses Attribut mit Vorsicht einsetzen und sich der Auswirkungen seiner Verwendung bewusst sein.

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. Wenn Sie Ihre API-App bereits vor dem Hinzufügen der CORS-Unterstützung in Azure bereitgestellt haben, führen Sie die Bereitstellung erneut durch, damit CORS in Ihrer durch Azure gehosteten API aktiviert wird. 

## Erstellen einer Web-App zum Nutzen der API-App

In diesem Abschnitt erstellen Sie eine neue leere Webanwendung, installieren AngularJS in der Webanwendung und verknüpfen ein einfaches HTML-Front-End mit der API-App. Sie stellen die Web-App anschließend in Azure App Service bereit. Die HTML-Web-App verfügt über ein Bindung mit der API-App, zeigt die aus der API-App abgerufenen Daten an und stellt für die Benutzer eine einfache Oberfläche für die Contacts-API bereit.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, das Sie zuvor in [Erstellen einer API-App](app-service-dotnet-create-api-app.md) erstellt haben, und wählen Sie **Hinzufügen -> Neues Projekt**.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/02-add-project.png)

1. Wählen Sie die Vorlage **ASP.NET-Webanwendung**.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/03-new-web-project.png)

1. Wählen Sie im Dialogfeld "One ASP.NET" die Vorlage **Leer**.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/04-empty-web.png)

1. Installieren Sie entweder über den **Paket-Manager** oder durch den Kontextmenüeintrag **NuGet-Pakete verwalten** das NuGet-Paket [AngularJS](https://www.nuget.org/packages/angularjs).

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/05-install-angular.png)

1. Installieren Sie entweder über den **Paket-Manager** oder durch den Kontextmenüeintrag **NuGet-Pakete verwalten** das NuGet-Paket [Bootstrap](https://www.nuget.org/packages/bootstrap).

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. Fügen Sie dem Web-App-Projekt eine neue HTML-Datei hinzu.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/06-new-html-file.png)

1. Geben Sie der Datei den Namen *index.html*.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/07-index-html.png)

1. Fügen Sie die Bootstrap-CSS- und die AngularJS JavaScript-Dateien zur HTML-Seite hinzu, verwenden Sie eine einfache Bootstrapvorlage ([wie diese](http://getbootstrap.com/examples/starter-template/)), und erstellen Sie ein leeres script-Tag, um die Seite vorzubereiten.
	
	> Hinweis: Die Kommentare im nachstehenden HTML- und JavaScript-Code sind eine Einleitung der nachfolgenden Schritte in diesem Abschnitt.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. Fügen Sie den unten gezeigten HTML-Tabellencode zum **container** *div*-Element in der HTML hinzu.

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. Ersetzen Sie in den `tbody`- und `tfoot`-Elementen jedes Vorkommen von [ durch { und jedes Vorkommen von ] durch }. (Aktuell ist es auf dieser Website nicht möglich, Ausdrücke mit doppelten geschweiften Klammern in Codeblöcken anzuzeigen.)

2. Klicken Sie mit der rechten Maustaste auf die Datei *index.html*, und klicken Sie auf **Als Startseite festlegen**.

3. Klicken Sie mit der rechten Maustaste auf die Datei *index.html*, und klicken Sie auf **In Browser anzeigen**.

	Beachten Sie die handlebars-Vorlagen in der HTML-Ausgabe. Sie führen im nächsten Schritt eine Datenbindung für diese HTML-Elemente durch.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/09-template-ui.png)

1. Fügen Sie den nachstehenden JavaScript-Code der Datei *index.html* hinzu, um die API aufzurufen und eine Datenbindung zwischen der HTML-Oberfläche und der API-Ausgabe herzustellen.

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

Ersetzen Sie im Code, den Sie gerade der Datei "index.html" hinzugefügt haben, die Portnummer in der Basis-URL (`http://localhost:1578`) durch die tatsächliche Portnummer für Ihr API-Projekt.

	> **Note** Don't use the port number of the HTML client project. You can right-click the API project and click **Debug > Start New Instance** to get a browser window that shows the port number.

1. Stellen Sie sicher, dass das API-App-Projekt beim Ausführen des HTML-Clients ebenfalls ausgeführt wird, sonst funktioniert der JavaScript-HTML-Code nicht ordnungsgemäß. Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Eigenschaften**. Legen Sie anschließend beide Webprojekte auf **Starten ohne Debugging** fest, und geben Sie an, dass das API-Projekt zuerst ausgeführt wird. 

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. Führen Sie das Projekt aus. Der HTML/JavaScript-Client stellt eine Verbindung mit dem API-App-Projekt her und zeigt Daten aus diesem an.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/11-web-client-running.png)

## Bereitstellen der Web-App

In diesem Abschnitt stellen Sie den HTML/JavaScript-Client als eine App Service-Web-App bereit. Nach Abschluss der Bereitstellung ändern Sie die URL, die JavaScript zur Nutzung der bereitgestellten API-App verwendet.

> Hinweis: Im vorliegenden Abschnitt wird vorausgesetzt, dass Sie den Artikel [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) gelesen oder bereits eine eigene API-App bereitgestellt haben.

1. Öffnen Sie das Blatt der API-App im Azure-Vorschauportal. Klicken Sie auf die URL auf dem Blatt, um die API-App in Ihrem Browser zu öffnen. Kopieren Sie nach dem Öffnen die URL der API-App aus der Browseradressleiste. 

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. Fügen Sie die URL zur API-App in die Eigenschaft **$scope.baseUrl** im JavaScript-Code ein, um den vorherigen Wert zu überschreiben.

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	Beachten Sie, dass die URL HTTPS angibt. Die Verwendung von HTTPS ist nicht optional. API-Apps bieten keine Unterstützung für HTTP.

1. Klicken Sie mit der rechten Maustaste auf das HTML/JavaScript-Webprojekt, und wählen Sie den Kontextmenüeintrag **Veröffentlichen**.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. Wählen Sie im Dialogfeld für die Webveröffentlichung die Option **Microsoft Azure-Web-Apps**.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. Klicken Sie auf die Schaltfläche **Neu**, um eine neue Web-App zu erstellen.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/15-new-web-app.png)

1. Wählen Sie denselben App-Hostingplan und dieselbe Ressourcengruppe, in der bereits Ihre API-App ausgeführt wird.

	> **Hinweis**: Diese Vorgehensweise ist nicht erforderlich. Sie wird jedoch zu Demonstrationszwecken empfohlen, da Sie Ihre Azure-Ressourcen später einfacher bereinigen können, wenn sich alle Elemente in einer Ressourcengruppe befinden.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. Schließen Sie die Schritte zur Webveröffentlichung ab, um den HTML/JavaScript-Client in App Service-Web-Apps bereitzustellen.
1. Nachdem die Web-App bereitgestellt wurde, sollte sie automatisch in Ihrem Webbrowser geöffnet werden und Daten aus der API-App anzeigen. 

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. Wenn Sie zu diesem Zeitpunkt zur Ressourcengruppe wechseln, sehen Sie, dass die neue Web-App neben der API-App ausgeführt wird.

	!["apiapp.json" und "Metadata" im Projektmappen-Explorer](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## Nächste Schritte 

Das vorliegende Beispiel hat gezeigt, wie Sie AngularJS als JavaScript-Plattform für den Zugriff auf API-App-Back-Ends verwenden können. Sie können die REST-Zugriffsfunktionalität ändern, um auf ein beliebiges anderes JavaScript-Framework zuzugreifen.

Dieses Beispiel veranschaulicht den nicht authentifizierten Zugriff auf eine API-App. Informationen zur Authentifizierung in App Service finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](../app-service/app-service-authentication-overview.md).

<!---HONumber=July15_HO4-->