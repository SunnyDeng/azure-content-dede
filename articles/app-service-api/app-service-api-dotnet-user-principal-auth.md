<properties
	pageTitle="Benutzerauthentifizierung für API-Apps in Azure App Service | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine API-App in Azure App Service schützen, indem Sie den Zugriff nur für authentifizierte Benutzer zulassen."
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
	ms.topic="hero-article"
	ms.date="01/08/2016"
	ms.author="tdykstra"/>

# Benutzerauthentifizierung für API-Apps in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial wird veranschaulicht, wie Sie die Authentifizierungs- und Autorisierungsfunktionen von Azure App Service zum Schützen einer API-App verwenden und wie Sie die API-App im Namen von Endbenutzern nutzen. Im Tutorial wird der Authentifizierungsanbieter Azure Active Directory angezeigt, die API ist die ASP.NET-Web- API, und der Beispielclient ist eine einseitige AngularJS-Anwendung, die in einem Browser ausgeführt wird.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Authentifizierung und Autorisierung in App Service

Eine Einführung in die Authentifizierungsfeatures, die in diesem Tutorial verwendet werden, finden Sie im vorherigen Tutorial dieser Reihe: [Authentifizierung und Autorisierung für API-Apps in Azure App Service](app-service-api-authentication.md).

## Inhalt dieses Tutorials

Dieses Tutorial baut auf einer Beispielanwendung auf, die Sie herunterladen und für die Sie im [ersten Tutorial der Reihe mit den ersten Schritten mit API-Apps und ASP.NET](app-service-api-dotnet-get-started.md) eine API-App erstellen.

## Beispielprojekt „ContactsList.Angular.AAD“

In der [Beispielanwendung „ContactsList“](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) ist das Projekt „ContactsList.Angular.AAD“ ein AngularJS-Client, der Code für die Zusammenarbeit mit Azure Active Directory enthält. Der Code basiert auf einem AAD-Beispiel, das im Repository [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) enthalten ist.

Der Code im Projekt „ContactsList.Angular.AAD“ ist anders strukturiert als beim einfacheren Projekt „ContactsLists.Angular“. Der Code, mit dem die API aufgerufen wird, ist in der Datei *app/scripts/contactsSvc.js* im Projekt „ContactsList.Angular.AAD“ enthalten.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

Hier ist die `Get`-Methode mit `getItems` bezeichnet. Im Controller (*app/scripts/contactsCtrl.js*) ist `getItems` mit `$scope.populate` verknüpft.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

In der Ansicht (*app/views/Contacts.html*) wird „$scope.populate“ bei der Initialisierung aufgerufen.

		<div ng-init="populate()">

Die zusätzliche Code für die Protokollierung und das Einbeziehen eines Autorisierungstokens in API-Anforderungen wird von der [Azure Active Directory-Authentifizierungsbibliothek für JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) in den Dateien *adal.js* und *adal-angular.js* bereitgestellt.

In der Datei *app.js* übergibt der Code Konfigurationsinformationen und den `$http`-Anbieter an die `adalProvider.init`-Funktion. Zu den Konfigurationsinformationen zählen die AAD-Anwendungsclient-ID, die zu jedem API-Endpunkt gehört, und die Client-ID, die zu dieser AngularJS-App gehört. Die `init`-Funktion fügt dem `$http`-Anbieter Interceptors so hinzu, dass diese das Autorisierungstoken Anforderungen hinzufügen.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Einrichten der Authentifizierung und Autorisierung in Azure

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt **API-App** der API-App, die Sie schützen möchten, damit nur authentifizierte Benutzer sie aufrufen können. (Wählen Sie für dieses Tutorial die API-App, für die Sie das Projekt „ContactsList.API“ bereitgestellt haben.)

2. Klicken Sie auf **Einstellungen**.

2. Suchen Sie nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

5. Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Klicken Sie auf dem Blatt **Azure Active Directory-Einstellungen** auf **Express**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	„Express“ bedeutet hier, dass Azure in Ihrem AAD-Mandanten automatisch eine AAD-Anwendung erstellt. Notieren Sie sich den Namen der neuen AAD-Anwendung. Sie wählen sie später aus, wenn Sie auf das klassische Azure-Portal zugreifen, um die Client-ID der neuen AAD-Anwendung abzurufen.

7. Klicken Sie auf **OK**.

10. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.

8. Greifen Sie zum Überprüfen, ob die API-App jetzt geschützt ist, wie im ersten Tutorial auf „URL + `/swagger`“ für die API-App zu, um die Swagger-Benutzeroberfläche zu verwenden.

	Dieses Mal werden Sie auf eine Anmeldungsseite umgeleitet.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) zu **Azure Active Directory**.

	Sie müssen zum klassischen Portal wechseln, da bestimmte Azure Active Directory-Einstellungen, auf die Sie zugreifen müssen, im derzeitigen Azure-Portal noch nicht verfügbar sind.

12. Klicken Sie auf der Registerkarte **Verzeichnis** auf Ihren AAD-Mandanten.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Klicken Sie auf **Anwendungen > Anwendungen im Besitz meines Unternehmens** und dann auf das Häkchen.

	Unter Umständen müssen Sie die Seite aktualisieren, um die neue Anwendung anzuzeigen.

15. Klicken Sie in der Liste mit den Anwendungen auf den Namen der Anwendung, die von Azure für Sie erstellt wurde, als Sie die Authentifizierung für Ihre API-App aktiviert haben.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Klicken Sie auf **Konfigurieren**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. Klicken Sie unten auf der Seite auf **Manifest verwalten > Manifest herunterladen**, und speichern Sie die Datei an einem Ort, an dem Sie sie bearbeiten können.

16. Suchen Sie in der heruntergeladenen Manifestdatei nach der `oauth2AllowImplicitFlow`-Eigenschaft. Ändern Sie den Wert dieser Eigenschaft von `false` in `true`, und speichern Sie die Datei anschließend.

	Diese Einstellung ist für den Zugriff aus einer einseitigen JavaScript-Anwendung erforderlich. Sie ermöglicht dem Oauth 2.0-Bearertoken die Rückgabe im URL-Fragment.

16. Klicken Sie auf **Manifest verwalten > Manifest hochladen**, und laden Sie die Datei hoch, die Sie im vorherigen Schritt aktualisiert haben.

17. Lassen Sie diese Seite geöffnet, damit Sie Werte kopieren und einfügen und Werte auf der Seite in späteren Schritten des Tutorials aktualisieren können.

## Konfigurieren des Projekts „ContactsList.Angular.AAD“ zum Aufrufen der Azure-API-App

Nachfolgend wird erläutert, wie Sie die Anwendung in Azure bereitstellen und dort ausführen. Doch mit geringfügigen Änderungen ist auch eine lokale Ausführung möglich. Der Beispielcode enthält URL-Endpunkte für „localhost“. Falls Sie eine lokale Ausführung wünschen, richten Sie die Projekte für SSL ein. Verwenden Sie SSL-URLs für „localhost“ erst im Projektcode und anschließend in der Konfiguration der AAD-Anwendung. Bei lokaler Ausführung erlaubt der AngularJS-Code nur angemeldeten Benutzern das Aufrufen der API. Doch auch nicht authentifizierte Aufrufer in anderen Clients können die API aufrufen.

1. Öffnen Sie im Projekt „ContactsList.Angular.AAD“ die Datei *app/scripts/app.js*.

8. Kommentieren Sie im Code, der die Variable `endpoints` festlegt, den Endpunkt „localhost“ aus, und heben Sie die Kommentierung für den Endpunkt „Azure“ auf.

10. Ersetzen Sie „yourclientid“ durch den tatsächlichen Wert der Client-ID der AAD-Anwendung auf der Registerkarte **Konfigurieren** der AAD-Anwendung im klassischen Portal.

2. Ersetzen Sie „{your api app name}“ durch den Namen der API-App, für die Sie das Projekt „ContactsList.API“ bereitgestellt haben.

	Der Code sieht jetzt wie im folgenden Beispiel aus.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Ersetzen Sie in *app.js* im Aufruf von `adalProvider.init` außerdem die Angaben „{your tenant url}“ und „{your client id}“ durch die tatsächlichen Werte.

	Der Code sieht etwa wie im folgenden Beispiel aus:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

1. Nehmen Sie in der Datei *app/scripts/contactsSvc.js* dieselbe Änderung an der Endpunkt-URL von „localhost“ in die URL der API-App wie zuvor in der Datei *app.js* vor.

	Der Code sieht jetzt wie im folgenden Beispiel aus.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Konfigurieren der AAD-Anwendung für die Azure-Web-App

1. Löschen Sie im klassischen Portal für die AAD-Anwendung auf der Registerkarte **Konfigurieren** im Feld **Anmelde-URL** die URL, die bereits vorhanden ist, und ersetzen Sie sie durch die Basis-URL der Web-App mit dem nachgestellten Schrägstrich. (Beachten Sie, dass dies die URL der Web-App ist, die den AngularJS-Code ausführt, nicht die URL der API-App.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. Ersetzen Sie im Feld **Antwort-URL** die URL, die bereits vorhanden ist, durch die Basis-URL der Web-App.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Klicken Sie auf **Speichern**.

## Bereitstellen des Projekts „ContactsList.Angular.AAD“ in Azure

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.Angular.AAD“ und dann auf **Veröffentlichen**.

9. Klicken Sie auf **Microsoft Azure App Service**.

10. Wählen Sie im Dialogfeld **App Service** in der Dropdownliste **Abonnement** Ihr Abonnement aus.

11. Erweitern Sie die Ressourcengruppe, die Sie für dieses Tutorial erstellt haben, und wählen Sie die Web-App aus, die Sie im zweiten Tutorial erstellt haben.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Klicken Sie auf **OK**.

12. Klicken Sie im Assistenten **Web veröffentlichen** auf die Registerkarte **Verbindung**, und ändern Sie im Feld **Ziel-URL** dann `http://` in `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	Diese Einstellung bestimmt, mit welcher URL der Standardbrowser nach einer erfolgreichen Bereitstellung geöffnet wird.

12. Klicken Sie im Assistenten **Web veröffentlichen** auf die Registerkarte **Einstellungen**, erweitern Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen **Nicht übereinstimmende Dateien im Ziel entfernen**.

7. Klicken Sie auf **Veröffentlichen**.

	Visual Studio stellt das Projekt bereit und öffnet einen Browser mit der Startseite der App.

## Testen der AngularJS-Web-App in Azure

8. Klicken Sie auf die Registerkarte **Kontakte**.

	Sie werden aufgefordert, sich anzumelden.

9. Melden Sie sich mit den Anmeldeinformationen eines Benutzers an Ihrem AAD-Mandanten an.

10. Die Seite **Kontakte** wird angezeigt.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

Das Front-End kann die API jetzt im Namen eines authentifizierten Benutzers aufrufen, aber nicht authentifizierte Benutzer können die API nicht aufrufen.

## Nächste Schritte

In diesem Tutorial haben Sie die App Service-Authentifizierung/Autorisierung verwendet, um den Zugriff auf eine API-App einzuschränken, damit sie nur von authentifizierten Benutzern aufgerufen werden kann. Im nächsten Tutorial der Reihe geht es um das [Einschränken des Zugriffs auf Ihre API-App für Dienst-zu-Dienst-Szenarien](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0114_2016-->