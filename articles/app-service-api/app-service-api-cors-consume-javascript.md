<properties
	pageTitle="Nutzen einer API-App aus JavaScript mit CORS | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine API-App in Azure App Service aus einem JavaScript-Client und mit CORS nutzen."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# Nutzen einer API-App aus JavaScript mit CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial erfahren Sie, wie Sie eine API-App aus JavaScript-Code auf einer Website nutzen, der aus einer anderen Domäne als der API-App bereitgestellt wird. Der Beispielclient verwendet AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Hierbei handelt es sich um das zweite Tutorial einer Serie, in der die Verwendung von API-Apps in Azure App Service erläutert wird. Um zu dem ersten der Serie zu wechseln, wählen Sie das erste Thema aus der Dropdownliste **Thema** am oberen Rand der Seite.

## CORS-Unterstützung in Azure App Service

Aus Sicherheitsgründen hindert das Standardverhalten des Browsers JavaScript daran, API-Aufrufe in einer anderen Domäne als der Domäne auszuführen, aus der der JavaScript-Code stammt. Beispiel: Sie können von einer Webseite der Domäne „contoso.com“ einen API-Endpunkt in „contoso.com“, aber nicht einen Endpunkt in „fabrikam.com“aufrufen. Cross-Origin Resource Sharing (CORS) ist ein Internetprotokoll, das für Szenarien entwickelt wurde, in denen Sie solche domänenübergreifenden API-Aufrufe ausführen müssen. Beispiel für solch ein Szenario in Azure App Service: Ihr JavaScript-Client wird in einer Web-App ausgeführt, wohingegen Ihre API in einer API-App ausgeführt wird.

Azure App Service bietet eine einfache Möglichkeit zum Konfigurieren von Domänen, die zum Aufrufen einer API-App berechtigt sind, und das CORS-Feature funktioniert identisch für alle Sprachen, die der API-Apps-Dienst unterstützt, z. B. Java und Node.js.

## So nutzen Sie dieses Tutorial

In diesem Tutorial wird eine Beispielanwendung genutzt, die Sie herunterladen, und für die Sie im [ersten Tutorial der ASP.NET-Version dieser Serie](app-service-api-dotnet-get-started.md) eine API-App erstellen.

Wechseln Sie bei den Tutorials mit den ersten Schritten zu Java oder Node.JS direkt zum [CORS-Konfigurationsabschnitt](#corsconfig), um die für alle API-Apps geltenden allgemeinen Anweisungen anzuzeigen.

## Das Beispielprojekt „ContactsList.Angular“

In der [Beispielanwendung „ContactsList“](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) ist das Projekt „ContactsList.Angular“ ein einfacher AngularJS-Client für das Web-API-Projekt „ContactsList.API“.

Der AngularJS JavaScript-Code, der die API aufruft, befindet sich im Projekt „ContactsList.Angular“ in der Datei *index.html*. Der Code definiert die Funktionen und fügt sie, wie hier gezeigt, dem Objekt `$scope` hinzu. Die Get-Methode der API wird als `$scope.refresh()` definiert.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

Der Code ruft die Methode „$scope.refresh()“ beim Laden der Seite auf (am Ende des oben angegebenen Codeausschnitts) und wird mit der Schaltfläche **Aktualisieren** in der Benutzeroberfläche verknüpft.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Lokales Ausführen des AngularJS-Projekts

In diesem Abschnitt stellen Sie sicher, dass Sie den Client lokal ausführen und die API aufrufen können, während sie ebenfalls lokal ausgeführt wird.

**Hinweis:** Diese Anweisungen gelten für Internet Explorer und Edge-Browser, da diese Browser ursprungsübergreifende Aufrufe von und an `http://localhost`-URLs ermöglichen. Wenn Sie Chrome verwenden, starten Sie den Browser mit dem Switch `--disable-web-security`. Wenn Sie Firefox verwenden, überspringen Sie diesen Abschnitt.

1. Legen Sie die Projekte „ContactsList.API“ und „ContactsList.Angular“ als Startprojekte fest. „ContactsList.API“ sollte dabei vor „ContactsList.Angular“ gestartet werden. 

2. Drücken Sie F5, um die Projekte zu starten.

	Auf der AngularJS-UI werden die lokal gespeicherten Kontakte angezeigt. Sie können über die UI Kontakte hinzufügen und löschen.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Schließen Sie die Browserfenster.

## Ändern des AngularJS-Projekts zum Verweisen auf die Azure-API-App 

Führen Sie im nächsten Schritt das AngularJS-Front-End in der Cloud aus, und rufen Sie das in der Cloud ausgeführte API-Back-End auf. Vor der Bereitstellung des Front-Ends in Azure müssen Sie den API-Endpunkt im AngularJS-Projekt ändern, sodass der Code die zuvor erstellte Azure-API-App aufruft.

1. Öffnen Sie im Projekt „ContactsList.Angular“ die Datei *index.html*.

2. Kommentieren Sie die Zeile aus, mit der `baseUrl` auf die localhost-URL festgelegt wird, und heben Sie die Auskommentierung für die Zeile auf, mit der für `baseUrl` eine URL in der Domäne „azurewebsites.net“ angegeben wird. Ersetzen Sie darüber hinaus den Platzhalter durch den tatsächlichen Namen der zuvor erstellten API-App. Wenn Sie die API-App „ContactsListAPI“ genannt haben, sieht der Code nun aus wie im folgenden Beispiel:

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Bereitstellen des Projekts „ContactsList.Angular“ in der Web-App

Sie können eine neue Web-App zum Bereitstellen des AngularJS-Projekts erstellen. In diesem Tutorial erfolgt die Bereitstellung jedoch in der gleichen Web-App, die Sie im vorherigen Tutorial erstellt haben. Der Name der Web-App spiegelt unter Umständen die Tatsache wider, dass Sie ursprünglich ein ASP.NET MVC-Projekt darin bereitgestellt haben. Nach dieser Bereitstellung wird jedoch der AngularJS-Code in ihr ausgeführt.

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.Angular“, und klicken Sie dann auf **Veröffentlichen**.

9. Klicken Sie auf die Registerkarte **Profil**.

3.  Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

4. Wählen Sie im Dialogfeld **App Service** Ihr Abonnement aus.

5. Legen Sie für **Ansicht** den Standardwert **Ressourcengruppe** fest. Erweitern Sie dann die für diese Tutorialserie erstellte Ressourcengruppe.

7. Wählen Sie die im ersten Tutorial erstellte Web-App (nicht die API-App) aus, und klicken Sie dann auf **OK**.

8. Klicken Sie auf die Registerkarte **Einstellungen**.

9. Erweitern Sie **Dateiveröffentlichungsoptionen**, und wählen Sie dann **Zusätzliche Dateien am Ziel entfernen**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	In der Regel ist bei der Bereitstellung eines Webprojekts in einer vorhandenen App Service-Web-App die Option zum Entfernen zusätzlicher Dateien nicht erwünscht, da Änderungen normalerweise Updates oder neue Dateien beinhalten. In diesem Fall stellen Sie ein anderes Projekt in derselben Web-App bereit. Daher sind wahrscheinlich zahlreiche Dateien aus der früheren Bereitstellung vorhanden, die in der neuen Bereitstellung nicht benötigt werden.

10. Klicken Sie auf **Veröffentlichen**.

	Visual Studio stellt das Projekt „ContactsList.Angular“ in der Web-App bereit und öffnet einen Browser mit der URL der Web-App. Der Browser zeigt die gleiche AngularJS-UI, die auch lokal ausgeführt wurde. Ausnahme: Nun tritt ein Fehler auf, da das Front-End in einer anderen Domäne (Web-App-URL) als das Back-End (API-App-URL) ausgeführt wird.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a> Konfigurieren von CORS für die Ziel-API-App in Azure

8. Wechseln Sie in einem anderen Browserfenster zum [Azure-Portal](https://portal.azure.com/).

9. Klicken Sie auf **Durchsuchen > API-Apps**, und wählen Sie die Ziel-API-App aus. Für dieses Lernprogramm ist dies die API-App, die Sie im ersten Lernprogramm für das ContactsList.API-Projekt erstellt haben.

10. Klicken Sie auf dem Blatt **API-App** auf **Einstellungen**.

11. Klicken Sie im Abschnitt **API** auf **CORS**.

12. Geben Sie im Textfeld die URL ein, von der Aufrufe ausgehen sollen. Wenn Sie z. B. die JavaScript-Anwendung für eine Web-App mit dem Namen „ContactsListMVC“ bereitgestellt haben, geben Sie „http://contactslistmvc.azurewebsites.net“ ein.

	Beachten Sie, dass Sie anstelle einer URL auch ein Sternchen (*) eingeben können, um anzugeben, dass alle Ursprungsdomänen akzeptiert werden.

13. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Wechseln Sie in das Browserfenster, in dem der AngularJS-Client angezeigt wird, und aktualisieren Sie die Seite, oder klicken Sie auf die Schaltfläche **Aktualisieren**.

	Auf der Seite werden nun die im Dateisystem der API-App gespeicherten Kontakte angezeigt.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### CORS in Azure-Ressourcen-Manager-Tools

Sie können CORS für eine API-App auch mithilfe von Azure-Ressourcen-Manager-Tools wie Azure PowerShell, Befehlszeilenschnittstelle ( Command Line Interface – CLI) oder [Ressourcen-Explorer](https://resources.azure.com/) konfigurieren.

Legen Sie die `cors`-Eigenschaft für Ihre Ressource <site name>/web auf den Ressourcentyp „Microsoft.Web/sites/config“ fest. Beispiel: Wechseln Sie im **Ressourcen-Explorer** zu **Abonnements > {Ihr Abonnement} > Ressourcengruppen > {Ihre Ressourcengruppe} > Anbieter > Microsoft.Web > Websites > {Ihre Website} > Konfigurieren > Web**, wo Sie die „cors“-Eigenschaft finden:

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

## CORS-Unterstützung im Web-API-Code

In einem Web-API-Projekt können Sie das [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/)-NuGet-Paket installieren, mit dem Sie im Code angeben können, aus welchen Domänen Ihre API JavaScript-Aufrufe akzeptiert.
 
Web-API-CORS-Unterstützung ist flexibler als App Service-CORS-Unterstützung. Im Code können Sie beispielsweise verschiedene zulässige Ursprünge für unterschiedliche Aktionsmethoden angeben, während Sie für App Service-CORS einen Satz von zulässigen Ursprüngen für alle Methoden einer API-App festlegen.

### App Service-CORS hat Vorrang vor Web-API-CORS.

Versuchen Sie nicht, Web-API-CORS und App Service-CORS in einer API-App zu verwenden. App Service-CORS hat Vorrang, und Web-API-CORS hat keine Auswirkung. Beispiel: Wenn Sie eine Ursprungsdomäne in App Service und alle Ursprungsdomänen in Ihrem Web-API-Code aktivieren, akzeptiert Ihre Azure-API-App nur Aufrufe von der in Azure angegebenen Domäne.

### Gewusst wie: Aktivieren von CORS im Web-API-Code

In den folgenden Schritten ist der Prozess zum Aktivieren der Web-API-Unterstützung zusammengefasst. Weitere Informationen finden Sie unter [Aktivieren von ursprungsübergreifenden Anforderungen in ASP.NET-Web-API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Fügen Sie in einem Web-API-Projekt, wie im folgenden Beispiel gezeigt, eine `config.EnableCors()`-Codezeile in die **Register**-Methode von **WebApiConfig** ein. 

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
				config.EnableCors();
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Fügen Sie im Web-API-Controller das `EnableCors`-Attribut der `ContactsController`-Klasse oder einzelnen Aktionsmethoden hinzu. Im folgenden Beispiel wird CORS-Unterstützung auf den gesamten Controller angewandt.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **Hinweis**: Das Verwenden von Platzhaltern für sämtliche Parameter mit dem `EnableCors`-Attribut ist ausschließlich für Demonstrationszwecke gedacht, da es Ihre API für beliebige Ursprünge und HTTP-Anforderungen öffnet. Lassen Sie bei Verwendung dieses Attributs Vorsicht walten.

## Nächste Schritte 

In diesem Tutorial haben Sie erfahren, wie Sie App Service-CORS-Unterstützung aktivieren, sodass der Client-JavaScript-Code eine API in einer anderen Domäne aufrufen kann. Im nächsten Artikel der Serie zu ersten Schritten mit API-Apps lernen Sie die [Authentifizierung für App Service-API-Apps](app-service-api-authentication.md) kennen.

<!---HONumber=AcomDC_0114_2016-->