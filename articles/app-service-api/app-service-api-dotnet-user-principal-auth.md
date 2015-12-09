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
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Benutzerauthentifizierung für API-Apps in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial wird veranschaulicht, wie Sie die Authentifizierungs- und Autorisierungsfunktionen von Azure App Service zum Schützen einer API-App verwenden und wie Sie die API-App im Namen von Endbenutzern nutzen. Im Tutorial wird der Authentifizierungsanbieter Azure Active Directory angezeigt, und der Beispielclient ist eine einseitige AngularJS-Anwendung, die in einem Browser ausgeführt wird.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
Hierbei handelt es sich um das dritte Tutorial einer Reihe, mit der die Verwendung von API-Apps in Azure App Service erläutert wird. Informationen zu dieser Reihe finden Sie unter [Erste Schritte mit API-Apps und ASP.NET in Azure App Service](app-service-api-dotnet-get-started.md).

## Authentifizierung und Autorisierung in Azure App Service

Azure App Service enthält integrierte Dienste für die Authentifizierung und Autorisierung. Sie können die Authentifizierung auch mit Ihrem eigenen Code durchführen. App Service umfasst aber eine fertige Lösung, falls Sie die Menge an Code verringern möchten, die Sie schreiben und verwalten müssen.

Sie können jeden der fünf Authentifizierungsanbieter verwenden, der von App Service unterstützt wird: Azure Active Directory, Facebook, Google, Twitter und das Microsoft-Konto. Und Sie können eine API schützen, die in einer von App Service unterstützten Sprache geschrieben ist: Ohne Code in Ihrer API zu schreiben, können Sie erzwingen, dass eine Benutzeranmeldung durchgeführt wird oder ein Token für den Zugriff verwendet wird.

Azure App Service übernimmt die Authentifizierung und überlässt die Autorisierung Ihrem Code. Sie können App Service so konfigurieren, dass nur authentifizierte Benutzer Ihre API aufrufen können, oder Sie können alle Aufrufer zulassen. In beiden Fällen werden die Authentifizierungsinformationen von App Service an Ihre App in den HTTP-Headern übergeben, und in Ihrem Code können diese Informationen genutzt werden, um Autorisierungsentscheidungen zu treffen.

* In einer .NET-API können Sie das Attribut `Authorize` verwenden, und für die fein abgestufte Autorisierung können Sie leicht Code schreiben, der auf Ansprüchen basiert. Anspruchsinformationen werden für Sie in .NET-Klassen eingefügt.

* Für APIs, die in anderen Sprachen geschrieben wurden, übergibt App Service das JWT-Token im Autorisierungsheader einer HTTP-Anforderung. Außerdem werden in Azure einige spezielle Header festgelegt (z. B. `x-ms-client-principal-id`), damit Sie leichter Zugriff auf die wichtigsten Ansprüche haben.

Weitere Informationen zu Authentifizierungs- und Autorisierungsdiensten in Azure App Service finden Sie unter [Erweitern der App Service-Authentifizierung/Autorisierung](/blog/announcing-app-service-authentication-authorization/) und [App Service-API-Apps – Änderungen](app-service-api-whats-changed.md).

## Beispielprojekt „ContactsList.Angular.AAD“

In diesem Tutorial verwenden Sie die Beispielprojekte, die Sie im [ersten Tutorial dieser Reihe](app-service-api-dotnet-get-started.md) heruntergeladen haben, sowie die im ersten Tutorial erstellten Azure-Ressourcen (API-App und Web-App).

Das Projekt „ContactsList.Angular.AAD“ ist ein AngularJS-Client, der Code für die Zusammenarbeit mit Azure Active Directory enthält. Der Code basiert auf einem AAD-Beispiel, das im Repository [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) enthalten ist.

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

Hier trägt die `Get`-Methode die Bezeichnung `getItems`, und im Controller (*app/scripts/contactsCtrl.js*) ist `getItems` mit `$scope.populate` verknüpft.

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

## Einrichten der Authentifizierung und Autorisierung in Azure

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt **API-App** der API-App, die Sie im ersten Tutorial erstellt haben, und klicken Sie dann auf **Einstellungen**.

2. Suchen Sie nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

5. Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Klicken Sie auf dem Blatt **Azure Active Directory-Einstellungen** auf **Express**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure erstellt in Ihrem AAD-Mandanten automatisch eine AAD-Anwendung. Notieren Sie sich den Namen der neuen AAD-Anwendung. Sie wählen sie später aus, wenn Sie auf das klassische Azure-Portal zugreifen, um die Client-ID der neuen AAD-Anwendung abzurufen.

7. Klicken Sie auf **OK**.

10. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.

8. Greifen Sie zum Überprüfen, ob die API-App jetzt geschützt ist, wie im ersten Tutorial auf „URL + `/swagger`“ für die API-App zu, um die Swagger-Benutzeroberfläche zu verwenden.

	Dieses Mal werden Sie auf eine Anmeldungsseite umgeleitet.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) zu **Azure Active Directory**.

12. Klicken Sie auf der Registerkarte „Verzeichnis“ auf Ihren AAD-Mandanten.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Klicken Sie auf **Anwendungen > Anwendung im Besitz meines Unternehmens** und dann auf das Häkchen.

	Unter Umständen müssen Sie die Seite aktualisieren, um die neue Anwendung anzuzeigen.

15. Klicken Sie in der Liste mit den Anwendungen auf den Namen der Anwendung, die von Azure für Sie erstellt wurde, als Sie die Authentifizierung für Ihre API-App aktiviert haben.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Klicken Sie auf **Konfigurieren**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. Klicken Sie unten auf der Seite auf **Manifest verwalten > Manifest herunterladen**, und speichern Sie die Datei an einem Ort, an dem Sie sie bearbeiten können.

16. Suchen Sie in der heruntergeladenen Manifestdatei nach der `oauth2AllowImplicitFlow`-Eigenschaft. Ändern Sie den Wert dieser Eigenschaft von `false` in `true`, und speichern Sie die Datei anschließend.

16. Klicken Sie auf **Manifest verwalten > Manifest hochladen**, und laden Sie die Datei hoch, die Sie im vorherigen Schritt aktualisiert haben.

17. Lassen Sie diese Seite geöffnet, damit Sie Werte kopieren und einfügen und Werte auf der Seite in späteren Schritten des Tutorials aktualisieren können.

## Einrichten der Visual Studio-Projekte für https

1. Klicken Sie im **Projektmappen-Explorer** auf das Projekt „ContactList.API“, und ändern Sie dann im Fenster **Eigenschaften** die Option **SSL aktiviert** in **True**.

2. Kopieren Sie die SSL-URL.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. Klicken Sie mit der rechten Maustaste auf das Projekt „ContactsList.API“, und klicken Sie auf **Eigenschaften**.

5. Klicken Sie auf die Registerkarte **Web**, fügen Sie die SSL-URL in das Feld **Projekt-URL** ein, und speichern Sie Ihre Änderungen.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. Führen Sie die gleichen Schritte aus, um SSL für das Projekt „ContactsList.Angular.AAD“ zu aktivieren.

2. Legen Sie das Projekt „ContactsList.API“ und die ContactsList.Angular.AAD-Projekte als Startprojekte fest, und geben Sie an, dass das Projekt „ContactsList.API“ zuerst gestartet werden soll.

## Aktualisieren von Endpunkt-URL und AAD-Einstellungen im Projekt „ContactsList.Angular.AAD“

7. Öffnen Sie im Projekt „ContactsList.Angular.AAD“ die Datei *app/scripts/app.js*.

8. Stellen Sie im Code, mit dem die Variable `endpoints` festgelegt wird, sicher, dass die richtige SSL-URL für das Projekt „ContactsList.API“ vorhanden ist. Ersetzen Sie beide Instanzen von „yourclientid“ durch den tatsächlichen Wert der Client-ID aus der AAD-Anwendung, der im klassischen Portal auf der Registerkarte **Konfigurieren** für die AAD-Anwendung angegeben ist. Achten Sie darauf, dass die Endpunkt-URL mit einem Schrägstrich endet.

	Der Code sieht etwa wie im folgenden Beispiel aus:

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Ersetzen Sie in *app.js* im Code `adalProvider.init` außerdem die Texte „{your tenant url}“ und „{your client id}“ durch die tatsächlichen Werte.

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

10. Stellen Sie in *app/scripts/contactsSvc.js* sicher, dass das Element „apiEndpoint“ auf die richtige SSL-URL für das Projekt „ContactsList.API“ festgelegt ist.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## Aktualisieren der AAD-Einstellungen im Projekt „ContactsList.API“

1. Öffnen Sie im Projekt „ContactsList.API“ die Anwendungsdatei *Web.config*.

2. Legen Sie „ida:Authority“ im appSettings-Element auf den Wert „https://login.windows.net/{Ihre Mandanten-URL}“ fest, und legen Sie „ida:ClientId“ auf die Client-ID Ihrer AAD-Anwendung fest. Dies ist im folgenden Beispiel dargestellt.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## Konfigurieren der AAD-Anwendung für localhost 

1. Fügen Sie im klassischen Portal auf der Registerkarte **Konfigurieren** für die AAD-Anwendung im Feld **Anmelde-URL** die SSL-URL des Projekts „ContactsList.Angular.AAD“ ein, und entfernen Sie den nachgestellten Schrägstrich.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. Fügen Sie unten auf der Registerkarte **Konfigurieren** im Feld **Antwort-URL** die SSL-URL des Projekts „ContactsList.Angular.AAD“ ein, indem Sie den bereits vorhandenen Wert ersetzen und den nachgestellten Schrägstrich beibehalten.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. Klicken Sie auf **Speichern**.

## Lokales Ausführen der API- und Clientprojekte

Beim lokalen Ausführen von HTTPS erhalten Sie Warnmeldungen zum IIS Express-SSL-Zertifikat für localhost. Zum lokalen Ausführen können Sie diese Meldungen wegklicken. Sie können diesen Abschnitt auch überspringen und direkt auf den nächsten Abschnitt zugreifen, um das Ausführen der Anwendung und API in Azure vorzubereiten.

Wenn beim Anmelden Probleme auftreten, können Sie versuchen, einen anderen Browser zu verwenden oder ein Incognito- oder InPrivate-Fenster mit der URL des AngularJS-Projekts zu öffnen, z. B. `https://localhost:44301`.

5. Drücken Sie in Visual Studio die F5-TASTE, um die API- und AngularJS-Projekte lokal auszuführen.

	Die Registerkarte „Startseite“ der AngularJS-Anwendung wird angezeigt.

10. Klicken Sie auf die Registerkarte **Anmeldung**.

	Sie werden aufgefordert, sich anzumelden.

7. Melden Sie sich mit den Anmeldeinformationen für einen Benutzer an Ihrem AAD-Mandanten an.

10. Klicken Sie auf die Registerkarte **Kontakte**.

	Die Seite **Kontakte** wird angezeigt.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. Schließen Sie die Browserfenster.

Für den Code des Projekts „ContactsList.Angular.AAD“ müssen Sie angemeldet sein, bevor die API aufgerufen wird und Kontakte angezeigt werden. Es wird aber nicht verhindert, dass nicht authentifizierte Aufrufer die API aufrufen. Sie können dies überprüfen, indem Sie die Swagger-Benutzeroberfläche im Browserfenster ausführen, die für die SSL-URL des Projekts „ContactsList.API“ geöffnet wird. Die API wird nur vor nicht authentifizierten Aufrufern geschützt, wenn sie in Azure App Service ausgeführt wird.

In den folgenden Abschnitten konfigurieren Sie die Projekte und AAD für die Ausführung des Clients und der API in Azure App Service, stellen die Projekte in Azure bereit und testen sie in Azure.

## Konfigurieren des Projekts „ContactsList.Angular.AAD“ zum Aufrufen der Azure-API-App

1. Öffnen Sie im Projekt „ContactsList.Angular.AAD“ die Datei *app/scripts/app.js*.

2. Kommentieren Sie den localhost-Endpunkt aus, heben Sie die Auskommentierung des Azure-Endpunkts auf, und ersetzen Sie „{your api app name}“ durch den Namen Ihrer API-App.

	Der Code sieht jetzt wie im folgenden Beispiel aus.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. Nehmen Sie in der Datei *app/scripts/contactsSvc.js* die gleiche Änderung der Endpunkt-URL vor.

	Der Code sieht jetzt wie im folgenden Beispiel aus.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Konfigurieren der AAD-Anwendung für die Azure-Web-App

1. Löschen Sie im klassischen Portal auf der Registerkarte **Konfigurieren** für die AAD-Anwendung im Feld **Anmelde-URL** die SSL-URL des Projekts „ContactsList.Angular.AAD“, und ersetzen Sie sie durch die Basis-URL der Web-App ohne den nachgestellten Schrägstrich. (Beachten Sie, dass dies die URL der Web-App ist, nicht die URL der API-App.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. Ersetzen Sie im Feld **Antwort-URL** die SSL-URL des Projekts „ContactsList.Angular.AAD“ durch die Basis-URL der Web-App, und behalten Sie den nachgestellten Schrägstrich bei.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Klicken Sie auf **Speichern**.

## Bereitstellen des Projekts „ContactsList.API“ in Azure

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.API“ und dann auf **Veröffentlichen**.

	Der Assistent **Web veröffentlichen** wird mit dem letzten verwendeten Veröffentlichungsprofil für dieses Projekt geöffnet. Dies ist das Profil zur Bereitstellen für die API-App, die Sie bereits erstellt haben.

7. Klicken Sie auf **Veröffentlichen**.

8. Schließen Sie das Browserfenster, das automatisch geöffnet wird.

## Bereitstellen des Projekts „ContactsList.Angular.AAD“ in Azure

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „ContactsList.Angular.API“ und dann auf **Veröffentlichen**.

9. Klicken Sie auf **Microsoft Azure App Service**.

10. Wählen Sie im Dialogfeld **App Service** in der Dropdownliste **Abonnement** Ihr Abonnement aus.

11. Erweitern Sie die Ressourcengruppe, die Sie für dieses Tutorial erstellt haben, und wählen Sie die Web-App aus, die Sie im zweiten Tutorial erstellt haben.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Klicken Sie auf **OK**.

12. Klicken Sie im Assistenten **Web veröffentlichen** auf die Registerkarte **Verbindung**, und ändern Sie im Feld **Ziel-URL** dann `http://` in `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. Klicken Sie im Assistenten **Web veröffentlichen** auf die Registerkarte **Einstellungen**, erweitern Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen **Nicht übereinstimmende Dateien im Ziel entfernen**.

7. Klicken Sie auf **Veröffentlichen**.

	Visual Studio stellt das Projekt bereit und öffnet einen Browser mit der Startseite der App.

## Testen der AngularJS-Web-App in Azure

8. Klicken Sie auf die Registerkarte **Kontakte**.

	Sie werden aufgefordert, sich anzumelden.

9. Melden Sie sich mit den Anmeldeinformationen eines Benutzers an Ihrem AAD-Mandanten an.

10. Die Seite **Kontakte** wird angezeigt.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. Um sicherzustellen, dass die API-App geschützt ist, navigieren Sie in einem InPrivate- oder Incognito-Browserfenster zur URL der Swagger-Benutzeroberfläche.

	Sie werden auf eine Anmeldeseite umgeleitet.

	Das Front-End kann die API jetzt im Namen eines authentifizierten Benutzers aufrufen, aber nicht authentifizierte Benutzer können die API nicht aufrufen.

## Nächste Schritte

In diesem Tutorial haben Sie die App Service-Authentifizierung/Autorisierung verwendet, um den Zugriff auf eine API-App einzuschränken, damit sie nur von authentifizierten Benutzern aufgerufen werden kann. Im nächsten Tutorial der Reihe geht es um das [Einschränken des Zugriffs auf Ihre API-App für Dienst-zu-Dienst-Szenarien](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->