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
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Benutzerauthentifizierung für API-Apps in Azure App Service

[AZURE.INCLUDE [Auswahl](../../includes/app-service-api-auth-selector.md)]

## Übersicht

In diesem Artikel lernen Sie Folgendes:

* Schützen einer App Service-API-App, damit sie nur von authentifizierten Benutzern aufgerufen werden kann
* Konfigurieren eines Authentifizierungsanbieters mit Details für Azure Active Directory (Azure AD)
* Nutzen einer geschützten API-App mit der [Active Directory Authentication Library (ADAL) für JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js)

Der Artikel enthält zwei Abschnitte:

* Der Abschnitt [Konfigurieren der Benutzerauthentifizierung in Azure App Service](#authconfig) enthält eine allgemeine Beschreibung, wie Sie die Benutzerauthentifizierung für eine API-App konfigurieren. Sie gilt für alle Frameworks, die von App Service unterstützt werden, z. B. .NET, Node.js und Java.

* Im [weiteren Verlauf des Artikels](#tutorialstart) werden Sie durch die Konfiguration einer .NET-Beispielanwendung geführt, die in App Service ausgeführt wird und bei der Azure Active Directory für die Benutzerauthentifizierung verwendet wird.

## <a id="authconfig"></a> Konfigurieren der Benutzerauthentifizierung in Azure App Service

Dieser Abschnitt enthält allgemeine Anweisungen, die für alle API-Apps gelten. Die speziellen Schritte für die .NET-Beispielanwendung (Aufgabenliste) sind in [Fortsetzen der „Erste Schritte“-Tutorials für .NET](#tutorialstart) enthalten.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt **API-App** der API-App, die Sie schützen möchten, und klicken Sie dann auf **Einstellungen**.

2. Suchen Sie auf dem Blatt **Einstellungen** nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** eine der Optionen aus.

	* Wenn nur authentifizierte Aufrufe die API-App erreichen sollen, wählen Sie eine der Optionen unter **Anmelden mit...** aus. Mit dieser Option können Sie die API-App schützen, ohne Code zu schreiben, der darin ausgeführt wird.

	* Wenn Sie möchten, dass alle Aufrufe Ihre API-App erreichen, wählen Sie **Anforderung zulassen (keine Aktion)**. Sie können diese Option verwenden, um nicht authentifizierte Aufrufer an verschiedene Authentifizierungsanbieter weiterzuleiten. Bei dieser Option müssen Sie Code schreiben, um die Autorisierung durchzuführen.

	Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung für API-Apps in Azure App Service](app-service-api-authentication.md#multiple-protection-options).

5. Wählen Sie einen oder mehrere **Authentifizierungsanbieter** aus.

	Die Abbildung zeigt die Optionen, bei denen alle Aufrufer von Azure AD authentifiziert werden müssen.
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	Bei der Auswahl eines Authentifizierungsanbieters wird im Portal ein Konfigurationsblatt für den Anbieter angezeigt.

	Eine ausführliche Anleitung zur Konfiguration des Authentifizierungsanbieters auf dem jeweiligen Blatt finden Sie unter [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (Der Link führt zu einem Artikel über Azure AD, aber der Artikel selbst enthält Registerkarten mit Links zu Artikeln für die anderen Authentifizierungsanbieter.)

7. Wenn Sie mit dem Blatt für die Konfiguration des Authentifizierungsanbieters fertig sind, klicken Sie auf **OK**.

7. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.

Wenn dies abgeschlossen ist, authentifiziert App Service alle API-Aufrufe, bevor sie die API-App erreichen. Die Authentifizierungsdienste funktionieren für alle Sprachen gleich, die von App Service unterstützt werden, z. B. .NET, Node.js und Java.

Zum Durchführen authentifizierter API-Aufrufe fügt der Aufrufer das OAuth 2.0-Bearertoken des Authentifizierungsanbieters in den Autorisierungsheader von HTTP-Anforderungen ein. Das Token kann mit dem SDK des Authentifizierungsanbieters abgerufen werden.

## <a id="tutorialstart"></a> Fortsetzen der Erste-Schritte-Tutorials für .NET

Falls Sie die Reihe „Erste Schritte“ für Node.js oder Java und API-Apps durcharbeiten, können Sie mit dem nächsten Artikel [Dienstprinzipalauthentifizierung für API-Apps](app-service-api-dotnet-service-principal-auth.md) fortfahren.

Wenn Sie die API-App-Reihe „Erste Schritte“ für .NET durcharbeiten und die Beispielanwendung schon bereitgestellt haben, wie dies im [ersten](app-service-api-dotnet-get-started.md) und [zweiten](app-service-api-cors-consume-javascript.md) Tutorial beschrieben ist, können Sie mit dem Abschnitt [Konfigurieren der Authentifizierung](#azureauth) fortfahren.

Falls Sie das erste und zweite Tutorial nicht durchgearbeitet haben und mit diesem Tutorial beginnen möchten, können Sie die Schaltfläche **In Azure bereitstellen** in der [Infodatei im Repository mit dem Beispiel für eine To-Do-Liste](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) verwenden, um die API-Apps und die Web-App bereitzustellen.

Nach Abschluss der Bereitstellung wird ein HTTP-Link zur Web-App angezeigt. Ändern Sie diese URL in eine HTTPS-URL, um die Anwendung auszuführen und zu überprüfen, ob sie funktioniert.

## <a id="azureauth"></a> Einrichten der Authentifizierung in Azure

An diesem Punkt wird die Anwendung in Azure App Service ausgeführt, ohne dass Benutzer authentifiziert sein müssen. In diesem Abschnitt fügen Sie die Authentifizierung hinzu, indem Sie Folgendes durchführen:

* Konfigurieren Sie App Service so, dass zum Aufrufen der API-App der mittleren Ebene die Azure Active Directory-Authentifizierung (Azure AD) erforderlich ist.
* Erstellen Sie eine Azure AD-Anwendung.
* Konfigurieren Sie die Azure AD-Anwendung so, dass das Bearertoken nach der Anmeldung an das AngularJS-Front-End gesendet wird. 

### Konfigurieren der Authentifizierung in App Service

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt **API-App** der API-App, die Sie für das Projekt ToDoListAPI erstellt haben.

2. Klicken Sie auf **Einstellungen**.

2. Suchen Sie auf dem Blatt **Einstellungen** nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

	Mit dieser Option wird sichergestellt, dass die API-App keine nicht authentifizierten Anforderungen erreichen.

5. Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Klicken Sie auf dem Blatt **Azure Active Directory-Einstellungen** auf **Express**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Mit der Option **Express** kann von App Service automatisch eine Azure AD-Anwendung in Ihrem Azure AD-[Mandanten](https://msdn.microsoft.com/de-DE/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) erstellt werden.

	Sie müssen keinen Mandanten erstellen, da jedes Azure-Konto automatisch darüber verfügt.

7. Klicken Sie unter **Verwaltungsmodus** auf **Neue AD-App erstellen**.

	Das Portal fügt einen Standardwert in das Eingabefeld **App erstellen** ein.
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. Notieren Sie sich den Wert im Eingabefeld **App erstellen**. Sie werden später im klassischen Azure-Portal nach dieser AAD-Anwendung suchen.

	Azure erstellt in Ihrem Azure AD-Mandanten automatisch eine Azure AD-Anwendung. Standardmäßig hat die Azure AD-Anwendung den gleichen Namen wie die API-App. Wenn Sie möchten, können Sie auch einen anderen Namen eingeben.
 
7. Klicken Sie auf **OK**.

7. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.

Jetzt kann die API-App nur von Benutzern aufgerufen werden, die im Azure AD-Mandanten enthalten sind.

### Optional: Testen der API-App

1. Navigieren Sie in einem Browser zur URL der API-App: Klicken Sie auf dem Blatt **API-App** im Azure-Portal auf den Link unter **URL**.  

	Sie werden auf eine Anmeldeseite umgeleitet, da nicht authentifizierte Anforderungen die API-App nicht erreichen dürfen.

	Wenn in Ihrem Browser die Seite mit der Meldung zur erfolgreichen Erstellung angezeigt wird, kann es sein, dass der Browser bereits angemeldet ist. Öffnen Sie in diesem Fall ein InPrivate- oder Incognito-Fenster, und greifen Sie auf die URL der API-App zu.

2. Melden Sie sich mit Anmeldeinformationen für einen Benutzer im Azure AD-Mandanten an.

	Nachdem Sie sich angemeldet haben, wird im Browser die Seite mit dem Hinweis zur erfolgreichen Erstellung angezeigt.

9. Schließen Sie den Browser.

### Konfigurieren der Azure AD-Anwendung

Wenn Sie die Azure AD-Authentifizierung konfiguriert haben, erstellt App Service eine Azure AD-Anwendung für Sie. Standardmäßig ist die Azure AD-Anwendung so konfiguriert, dass das Bearertoken für die URL der API-App bereitgestellt wird. In diesem Abschnitt konfigurieren Sie die Azure AD-Anwendung, um das Bearertoken für das AngularJS-Front-End bereitzustellen, anstatt direkt für die API-App der mittleren Ebene. Das AngularJS-Front-End sendet das Token an die API-App, wenn es die API-App aufruft.

11. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) zu **Azure Active Directory**.

	Sie müssen das klassische Portal verwenden, da bestimmte Azure Active Directory-Einstellungen, auf die Sie zugreifen müssen, im derzeitigen Azure-Portal noch nicht verfügbar sind.

12. Klicken Sie auf der Registerkarte **Verzeichnis** auf Ihren AAD-Mandanten.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Klicken Sie auf **Anwendungen > Anwendungen im Besitz meines Unternehmens** und dann auf das Häkchen.

	Unter Umständen müssen Sie die Seite aktualisieren, um die neue Anwendung anzuzeigen.

15. Klicken Sie in der Liste mit den Anwendungen auf den Namen der Anwendung, die von Azure für Sie erstellt wurde, als Sie die Authentifizierung für Ihre API-App aktiviert haben.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Klicken Sie auf **Konfigurieren**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Legen Sie die **Anmelde-URL** auf die URL für Ihre AngularJS-Web-App ohne nachgestellten Schrägstrich fest.

	Beispiel: https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Legen Sie die **Antwort-URL** auf die URL für Ihre Web-App ohne nachgestellten Schrägstrich fest.

	Beispiel: https://todolistsangular.azurewebsites.net

16. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. Klicken Sie unten auf der Seite auf **Manifest verwalten > Manifest herunterladen**.

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Laden Sie die Datei an einen Ort herunter, an dem Sie sie bearbeiten können.

16. Suchen Sie in der heruntergeladenen Manifestdatei nach der `oauth2AllowImplicitFlow`-Eigenschaft. Ändern Sie den Wert dieser Eigenschaft von `false` in `true`, und speichern Sie die Datei anschließend.

	Diese Einstellung ist für den Zugriff aus einer einseitigen JavaScript-Anwendung erforderlich. Sie ermöglicht dem Oauth 2.0-Bearertoken die Rückgabe im URL-Fragment.

16. Klicken Sie auf **Manifest verwalten > Manifest hochladen**, und laden Sie die Datei hoch, die Sie im vorherigen Schritt aktualisiert haben.

17. Kopieren Sie den Wert für **Client-ID**, und speichern Sie ihn an einem Ort, an dem Sie später darauf zugreifen können.

## Konfigurieren der Authentifizierung für das Projekt ToDoListAngular

In diesem Abschnitt ändern Sie das AngularJS-Front-End, damit es die Active Directory Authentication Library (ADAL) für JS zum Abrufen eines Bearertokens für den angemeldeten Benutzer aus Azure AD verwendet. Der Code enthält das Token in HTTP-Anforderungen, die an die mittlere Ebene gesendet werden. Dies ist in der folgenden Abbildung dargestellt.

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Nehmen Sie die folgenden Änderungen an den Dateien im Projekt ToDoListAngular vor.

1. Öffnen Sie die Datei *index.html*.

2. Kommentieren Sie die Zeilen aus, in denen auf die Active Directory Authentication Library (ADAL) für JS-Skripts verwiesen wird.

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. Öffnen Sie die Datei *app/scripts/app.js*.

2. Kommentieren Sie den Codeblock aus, der über die Kennzeichnung „without authentication“ verfügt, und heben Sie die Auskommentierung für den Codeblock auf, der über die Kennzeichnung „with authentication“ verfügt.

	Durch diese Änderung wird auf den ADAL-JS-Authentifizierungsanbieter verwiesen, und es werden Konfigurationswerte dafür bereitgestellt. In den folgenden Schritten legen Sie die Konfigurationswerte für Ihre API-App und die Azure AD-Anwendung fest.

8. Legen Sie im Code, mit dem die Variable `endpoints` definiert wird, die API-URL auf die URL der API-App fest, die Sie für das Projekt ToDoListAPI erstellt haben. Legen Sie außerdem die ID der Azure AD-Anwendung auf die Client-ID fest, die Sie aus dem klassischen Azure-Portal kopiert haben.

	Der Code sieht jetzt wie im folgenden Beispiel aus.

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Legen Sie im Aufruf von `adalProvider.init` das `tenant`-Element auf Ihren Mandantennamen und `clientId` auf denselben Wert wie im vorherigen Schritt fest.

	Der Code sieht jetzt wie im folgenden Beispiel aus.

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

	Mit diesen Änderungen an `app.js` wird angegeben, dass sich der aufrufende Code und die aufgerufene API in derselben Azure AD-Anwendung befinden.

1. Öffnen Sie die Datei *app/scripts/homeCtrl.js*.

2. Kommentieren Sie den Codeblock aus, der über die Kennzeichnung „without authentication“ verfügt, und heben Sie die Auskommentierung für den Codeblock auf, der über die Kennzeichnung „with authentication“ verfügt.

1. Öffnen Sie die Datei *app/scripts/indexCtrl.js*.

2. Kommentieren Sie den Codeblock aus, der über die Kennzeichnung „without authentication“ verfügt, und heben Sie die Auskommentierung für den Codeblock auf, der über die Kennzeichnung „with authentication“ verfügt.

### Bereitstellen des Projekts ToDoListAngular in Azure

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt ToDoListAngular, und klicken Sie dann auf **Veröffentlichen**.

9. Klicken Sie auf **Veröffentlichen**.

	Visual Studio stellt das Projekt bereit und öffnet einen Browser mit der Basis-URL der Web-App.

	Sie müssen trotzdem eine Änderung an der API-App der mittleren Ebene vornehmen, bevor Sie die Anwendung testen können.

10. Schließen Sie den Browser.

## Konfigurieren der Authentifizierung für das Projekt ToDoListAPI

Derzeit sendet das Projekt ToDoListAPI „*“ als `owner`-Wert an ToDoListDataAPI. In diesem Abschnitt ändern Sie den Code zum Senden der ID des angemeldeten Benutzers.

Nehmen Sie am Projekt ToDoListAPI die folgenden Änderungen vor.

1. Öffnen Sie die Datei *Controllers/ToDoListController.cs*, und heben Sie die Auskommentierung der Zeile in allen Aktionsmethoden auf, mit denen `owner` auf den Azure AD-Anspruchswert `NameIdentifier` festgelegt wird. Beispiel:

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### Bereitstellen des Projekts ToDoListAPI in Azure

8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt ToDoListAPI, und klicken Sie dann auf **Veröffentlichen**.

9. Klicken Sie auf **Veröffentlichen**.

	Visual Studio stellt das Projekt bereit und öffnet ein Browserfenster mit der Basis-URL der API-App.

10. Schließen Sie den Browser.

### Testen der Anwendung

9. Navigieren Sie zur URL der Web-App, indem Sie **HTTPS verwenden, nicht HTTP**.

8. Klicken Sie auf die Registerkarte **Aufgabenliste**.

	Sie werden aufgefordert, sich anzumelden.

9. Melden Sie sich mit den Anmeldeinformationen eines Benutzers an Ihrem AAD-Mandanten an.

10. Die Seite **Aufgabenliste** wird angezeigt.

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	Es werden keine Aufgabenelemente angezeigt, da sie bis jetzt alle für den Besitzer „*“ galten. Jetzt fordert die mittlere Ebene Elemente für den angemeldeten Benutzer an, aber es wurden noch keine erstellt.

11. Fügen Sie neue Aufgabenelemente hinzu, um zu überprüfen, ob die Anwendung funktioniert.

12. Navigieren Sie in einem anderen Browserfenster zur Swagger-Benutzeroberflächen-URL für die API-App ToDoListDataAPI, und klicken Sie auf **ToDoList > Get**. Geben Sie ein Sternchen für den Parameter `owner` ein, und klicken Sie dann auf **Ausprobieren**.

	Die Antwort zeigt, dass die neuen Aufgabenelemente in der Owner-Eigenschaft über die richtige Azure AD-Benutzer-ID verfügen.

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## Neues Erstellen der Projekte von Grund auf

Die beiden Web-API-Projekte wurden erstellt, indem die Projektvorlage **Azure-API-App** verwendet und der standardmäßige Values-Controller durch einen ToDoList-Controller ersetzt wurde.

Weitere Informationen zum Erstellen einer AngularJS-Single-Page-Anwendung mit einem Web-API 2-Back-End finden Sie unter [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs) (Hands On Lab: Erstellen einer Single-Page-Anwendung (SPA) mit ASP.NET-Web-API und „Angular.js“). Informationen zum Hinzufügen von Azure AD-Authentifizierungscode finden Sie unter den folgenden Ressourcen:

* [Schützen von einseitigen AngularJS-Apps mit Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introducing ADAL JS v1 (Einführung in ADAL JS v1)](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## Problembehandlung

Wenn Sie die Anwendung ohne Authentifizierung erfolgreich ausgeführt haben und diese beim Implementieren der Authentifizierung dann nicht richtig funktioniert, sind häufig fehlerhafte oder inkonsistente Konfigurationseinstellungen das Problem. Beginnen Sie, indem Sie alle Einstellungen in Azure App Service und Azure Active Directory noch einmal gründlich überprüfen. Vorschläge:

* Überprüfen Sie in Azure AD auf der Registerkarte **Konfigurieren** die **Antwort-URL**.
* Laden Sie in Azure AD das Manifest herunter, und stellen Sie sicher, dass `oauth2AllowImplicitFlow` erfolgreich in `true` geändert wurde. 
* Überprüfen Sie im AngularJS-Quellcode die URL für die API-App der mittleren Ebene und die Azure AD-Client-ID.
* Stellen Sie nach dem Konfigurieren des Codes in einem Projekt sicher, dass Sie das richtige Projekt erneut bereitstellen, und nicht eines der anderen Projekte.
* Achten Sie darauf, dass Sie in Ihrem Browser auf HTTPS-URLs zugreifen, nicht auf HTTP-URLs.
* Stellen Sie sicher, dass CORS für die API-App der mittleren Ebene noch aktiviert ist, sodass Aufrufe an die mittlere Ebene über die Front-End-HTTPS-URL möglich sind. Falls Sie unsicher sind, ob das Problem mit CORS zusammenhängt, können Sie versuchen, „*“ als zulässige Ursprungs-URL zu verwenden.
* Sorgen Sie dafür, dass Sie in Fehlermeldungen so viele Informationen wie möglich erhalten, indem Sie den [customErrors-Modus auf „Off“ festlegen](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Die Registerkarte mit der Entwicklertools-Konsole im Browser enthält häufig weitere Fehlerinformationen, und Sie können HTTP-Anforderungen auf der Registerkarte „Netzwerk“ untersuchen.

## Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie die App Service-Authentifizierung für eine API-App verwenden und die API-App mit der ADAL-JS-Bibliothek aufrufen. Im nächsten Tutorial geht es um das [Schützen des Zugriffs auf Ihre API-App für Dienst-zu-Dienst-Szenarien](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0302_2016-->