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
	ms.date="11/27/2015"
	ms.author="tdykstra"/>

# Nutzen einer API-App aus JavaScript mit CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial erfahren Sie, wie Sie eine API-App aus JavaScript-Code auf einer Website nutzen, der aus einer anderen Domäne als der API-App bereitgestellt wird. Der Beispielclient verwendet AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Hierbei handelt es sich um das zweite Tutorial einer Serie, in der die Verwendung von API-Apps in Azure App Service erläutert wird. Informationen zu dieser Serie finden Sie unter [Erste Schritte mit API-Apps und ASP.NET in Azure App Service](app-service-api-dotnet-get-started.md).

## Einführung in CORS

Aus Sicherheitsgründen hindert das Standardverhalten des Browsers JavaScript daran, API-Aufrufe in einer anderen Domäne als der Domäne auszuführen, aus der der JavaScript-Code stammt. Beispiel: Sie können von einer Webseite der Domäne „contoso.com“ einen API-Endpunkt in „contoso.com“, aber nicht einen Endpunkt in „fabrikam.com“aufrufen. Cross-Origin Resource Sharing (CORS) ist ein Internetprotokoll, das für Szenarien entwickelt wurde, in denen Sie solche domänenübergreifenden API-Aufrufe ausführen müssen. Beispiel für solch ein Szenario in Azure App Service: Ihr JavaScript-Client wird in einer Web-App ausgeführt, wohingegen Ihre API in einer API-App ausgeführt wird.

In einem Web-API-Projekt können Sie CORS-NuGet-Pakete installieren, mit denen Sie im Code angeben, aus welchen Domänen Ihre API JavaScript-Aufrufe akzeptiert. Alternativ können Sie mit dem in Azure App Service integrierten CORS-Feature angeben, aus welchen Domänen Ihre API-App Aufrufe akzeptiert. Die erste Hälfte dieses Tutorials veranschaulicht die Verwendung des Azure-Features. Dieses Feature kann für alle Sprachen verwendet werden, die der API-Apps-Dienst unterstützt. Die zweite Hälfte ist optional und zeigt die NuGet-Paketmethode, die mit der ASP.NET-Web-API verwendet wird.

## Das Beispielprojekt „ContactsList.Angular“

In diesem Tutorial verwenden Sie die Beispielprojekte, die Sie im ersten Tutorial dieser Serie heruntergeladen haben, sowie die im ersten Tutorial erstellten Azure-Ressourcen (API-App und Web-App).

Das Projekt „ContactsList.Angular“ ist ein einfacher AngularJS-Client für das Web-API-Projekt „ContactsList.API“. Der AngularJS JavaScript-Code, der die API aufruft, befindet sich in der Datei *index.html* im Projekt „ContactsList.Angular“. Der Code definiert die Funktionen und fügt sie wie hier gezeigt dem Objekt `$scope` hinzu. Die Get-Methode der API wird als `$scope.refresh()` definiert.

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

Der Code ruft die Methode „$scope.refresh()“ beim Laden der Seite auf (am Ende des oben angegebenen Codeausschnitts) und wird mit der Schaltfläche **Aktualisieren** auf der UI verknüpft.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Lokales Ausführen des AngularJS-Projekts

In diesem Abschnitt stellen Sie sicher, dass Sie den Client lokal ausführen und die API aufrufen können, während sie ebenfalls lokal ausgeführt wird.

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

Sie können eine neue Web-App zum Bereitstellen des AngularJS-Projekts erstellen. In diesem Tutorial erfolgt die Bereitstellung jedoch in der zuvor erstellten Web-App. Der Name der Web-App spiegelt unter Umständen die Tatsache wider, dass Sie ursprünglich ein ASP.NET MVC-Projekt darin bereitgestellt haben. Nach dieser Bereitstellung wird jedoch der AngularJS-Code in ihr ausgeführt.

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.Angular“ und dann auf **Veröffentlichen**.

9. Klicken Sie auf die Registerkarte **Profil**.

3.  Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

4. Wählen Sie im Dialogfeld **App Service** Ihr Abonnement aus.

5. Legen Sie für **Ansicht** den Standardwert **Ressourcengruppe** fest. Erweitern Sie dann die für diese Tutorialserie erstellte Ressourcengruppe.

7. Wählen Sie die im ersten Tutorial erstellte Web-App (nicht die API-App), und klicken Sie dann auf **OK**.

8. Klicken Sie auf die Registerkarte **Einstellungen**.

9. Erweitern Sie **Dateiveröffentlichungsoptionen**, und wählen Sie dann **Zusätzliche Dateien am Ziel entfernen**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	In der Regel ist bei der Bereitstellung eines Webprojekts in einer vorhandenen App Service-Web-App die Option zum Entfernen zusätzlicher Dateien nicht erwünscht, da Änderungen normalerweise Updates oder neue Dateien beinhalten. In diesem Fall stellen Sie ein anderes Projekt in derselben Web-App bereit. Daher sind wahrscheinlich zahlreiche Dateien aus der früheren Bereitstellung vorhanden, die in der neuen Bereitstellung nicht benötigt werden.

10. Klicken Sie auf **Veröffentlichen**.

	Visual Studio stellt das Projekt „ContactsList.Angular“ in der Web-App bereit und öffnet einen Browser mit der URL der Web-App. Der Browser zeigt die gleiche AngularJS-UI, die auch lokal ausgeführt wurde. Ausnahme: Nun tritt ein Fehler auf, da das Front-End in einer anderen Domäne (Web-App-URL) als das Back-End (API-App-URL) ausgeführt wird.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## Konfigurieren von CORS für die Ziel-API-App in Azure

8. Wechseln Sie in einem anderen Browserfenster zum [Azure-Portal](https://portal.azure.com/).

9. Navigieren Sie zum API-App-Blatt für die API-App, die Sie im ersten Tutorial erstellt haben.

10. Klicken Sie auf **Einstellungen**.

11. Klicken Sie im Abschnitt **API** auf **CORS**.

12. Geben Sie im Textfeld die URL der Web-App ein, die Sie im ersten Tutorital für das ASP.NET MVC-Front-End erstellt haben. Beispiel: Wenn Sie die Web-App „ContactsListMVC“ genannt haben, geben Sie „http://contactslistmvc.azurewebsites.net“ ein.

	Beachten Sie, dass Sie anstelle einer URL auch ein Sternchen (*) eingeben können, um anzugeben, dass alle Ursprungsdomänen akzeptiert werden.

13. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Wechseln Sie zurück in das Browserfenster, in dem der in der Azure-Web-App ausgeführte AngularJS-Client angezeigt wird, und aktualisieren Sie die Seite, oder klicken Sie auf die Schaltfläche **Aktualisieren**.

	Auf der Seite werden nun die im Dateisystem der API-App gespeicherten Kontakte angezeigt.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Konfigurieren von CORS für die Ziel-API-App im Web-API-Code

Wenn Sie nicht die CORS-Unterstützung für Azure App Service verwenden möchten, können Sie CORS im ASP.NET-Web-API-Code aktivieren. Dieser Prozess ist unter [Aktivieren von ursprungsübergreifenden Anforderungen in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) dokumentiert . Für API-Apps, die mit dem ASP.NET-Web-API erstellt werden, ist die Vorgehensweise genau dieselbe, sie wird jedoch hier nochmals zusammengefasst.

In diesem Abschnitt deaktivieren Sie die CORS-Unterstützung für Azure App Service und aktivieren anschließend die Web-API-CORS-Unterstützung. Beachten Sie, dass die Deaktivierung der Azure-CORS-Unterstützung kein optionaler Schritt vor dem Aktivieren der Web-API-Unterstützung ist. Wenn Sie beide Methoden gemeinsam verwenden, hat Azure-CORS Vorrang. Web-API-CORS hat keine Auswirkung. Beispiel: Wenn Sie eine Ursprungsdomäne in Azure und alle Ursprungsdomänen in Ihrem Web-API-Code aktivieren, akzeptiert Ihre Azure-API-App nur Aufrufe von der in Azure angegebenen Domäne.

### Deaktivieren der Azure-CORS-Unterstützung

1. Wechseln Sie zum Deaktivieren der Azure-CORS-Unterstützung zurück zum Azure-Portal. Löschen Sie die auf dem Blatt „CORS“ eingegebene URL, und klicken Sie dann auf **Speichern**.
 
3.  Rufen Sie erneut die URL der Web-App auf, in der Sie die AngularJS-App bereitgestellt haben, und aktualisieren Sie die Seite, oder klicken Sie auf die Schaltfläche **Aktualisieren**.

	Die Meldung, dass ein Fehler beim Laden der Kontakte aufgetreten ist, wird erneut angezeigt.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

### Aktivieren der Web-API-CORS-Unterstützung

Die CORS-Funktionalität für die Web-API wird durch das NuGet-Paket [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) bereitgestellt. Das Paket wurde bereits mit der heruntergeladenen Beispielanwendung installiert – zum Aktivieren von CORS müssen Sie lediglich die Auskommentierung einiger Codezeilen aufheben.

1. Öffnen Sie im Projekt „ContactsList.API“ die Datei *App\_Start/WebApiConfig.cs*. Heben Sie in der **Register**-Methode von **WebApiConfig** die Auskommentierung der Codezeile „config.EnableCor“ auf. 

	Nachdem die Datei aktualisiert wurde, sieht der Code in etwa wie folgt aus:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // Uncomment the following line to control CORS by using Web API code
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

1. Öffnen Sie die Datei *Controllers/ContactsController.cs*, und heben Sie die Auskommentierung der Zeile auf, die der `ContactsController`-Klasse das `EnableCors`-Attribut hinzufügt.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	Bei Bedarf können Sie das Attribut auf einzelne Aktionsmethoden anstelle des gesamten Controllers anwenden.

	> **Hinweis**: Das Verwenden von Platzhaltern für sämtliche Parameter mit dem `EnableCors`-Attribut ist ausschließlich für Demonstrationszwecke gedacht, da es Ihre API für beliebige Ursprünge und HTTP-Anforderungen öffnet. Lassen Sie bei Verwendung dieses Attributs Vorsicht walten.

### Bereitstellen der API in Azure und erneutes Testen des Front-Ends

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.API“ und dann auf **Veröffentlichen**.

	Für das Veröffentlichungsprofil für die zuvor erstellte API-App wird automatisch der Schritt **Vorschau** des Assistenten **Web veröffentlichen** geöffnet, da dies das letzte Ziel ist, für das Sie dieses Projekt bereitgestellt haben.

3.  Klicken Sie auf **Veröffentlichen**.

	![](./media/app-service-api-cors-consume-javascript/pubapi.png)

	Nach Abschluss der Bereitstellung wird im Browser ein Fenster mit der API-App-URL geöffnet, in dem eine Seite mit einem Hinweis zur erfolgreichen Erstellung der Web-App angezeigt wird.

3.  Rufen Sie erneut die URL der Web-App auf, in der Sie die AngularJS-App bereitgestellt haben, und aktualisieren Sie die Seite, oder klicken Sie auf die Schaltfläche **Aktualisieren**.

	Die Seite wird erneut geladen.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Erneutes Aktivieren der Azure-CORS-Unterstützung

Um die integrierten Azure-Authentifizierungsdienste in den folgenden Tutorials verwenden zu können, sollten Sie Azure-CORS und nicht Web-API-CORS verwenden.
 
1. Wechseln Sie zum Aktivieren der Azure-CORS-Unterstützung zurück zum Blatt „CORS“ für Ihre API-App im Azure-Portal, und geben Sie erneut die URL der Web-App ein.

13. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

## Nächste Schritte 

In diesem Tutorial haben Sie zwei Möglichkeiten zum Aktivieren der CORS-Unterstützung kennengelernt, sodass der Client-JavaScript-Code eine API in einer anderen Domäne aufrufen kann. Im nächsten Tutorial erfahren Sie, wie Sie den [Zugriff auf Ihre API-App beschränken, sodass nur authentifizierte Benutzer darauf zugreifen können](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->