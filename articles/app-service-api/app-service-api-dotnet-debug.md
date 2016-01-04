<properties 
	pageTitle="Debuggen einer API-App in Azure App Service" 
	description="Erfahren Sie, wie Sie mithilfe von Visual Studio eine API-App in Azure App Service debuggen." 
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
	ms.date="10/08/2015" 
	ms.author="tdykstra"/>

# Debuggen einer API-App in Azure App Service

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie ASP.NET-Web-API-Code debuggen, der zur Ausführung in einer [API-App](app-service-api-apps-why-best-platform.md) in [Azure App Service](../app-service/app-service-value-prop-what-is.md) konfiguriert ist. Sie debuggen den Code, während er lokal und remote in Azure ausgeführt wird.

Im Lernprogramm wird die API-App verwendet, die Sie in den vorherigen Lernprogrammen in dieser Reihe [erstellt](app-service-dotnet-create-api-app.md) und [bereitgestellt](app-service-dotnet-deploy-api-app.md) haben.

## Remotedebuggen einer API-App 

Um remotes Debuggen zu aktivieren, müssen Sie einen Debugbuild in Azure bereitstellen.

1. Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, das Sie [im vorherigen Lernprogramm bereitgestellt haben](app-service-dotnet-deploy-api-app.md), und wählen Sie **Veröffentlichen**.

2. Wählen Sie im Dialogfeld **Webveröffentlichung** die Registerkarte **Einstellungen** aus, und wählen Sie die Buildkonfiguration **Debug** aus.

4. Klicken Sie auf **Veröffentlichen**.

	![Projekt veröffentlichen](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	Ein Browserfenster wird basierend auf der URL Ihrer API-App geöffnet.

4. Fügen Sie in der Adresszeile des Browsers am Ende der URL "/swagger" hinzu, und drücken Sie die Eingabetaste.

	Dieser Schritt setzt voraus, dass Sie die Swagger-Benutzeroberfläche aktiviert haben, wie im Lernprogramm zum [Erstellen](app-service-dotnet-create-api-app.md) gezeigt.

	![Swagger-Benutzeroberfläche](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Kehren Sie zu Visual Studio zurück, und klicken Sie auf **Ansicht > Server-Explorer**.

6. Erweitern Sie im **Server-Explorer** den Knoten **Azure > App Service**.

7. Suchen Sie die Ressourcengruppe, die Sie beim Bereitstellen Ihrer API-App erstellt oder ausgewählt haben.

8. Klicken Sie unterhalb der Ressourcengruppe mit der rechten Maustaste auf den Knoten für die API-App, und wählen Sie **Debugger anfügen**.

	![Anfügen des Debuggers](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	Der Remotedebugger versucht, eine Verbindung herzustellen. In einigen Fällen müssen Sie erneut auf **Debugger anfügen** klicken, um eine Verbindung herzustellen. Versuchen Sie es daher ggf. noch einmal.

	![Anfügen des Debuggers](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Nachdem die Verbindung hergestellt wurde, öffnen Sie die Datei **ContactsController.cs** im API-App-Projekt, und fügen Sie Haltepunkte in der `Get`-Methode hinzu.

	![Anwenden von Haltepunkten auf Controller](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Kehren Sie zur Browsersitzung zurück, klicken Sie auf das **Get**-Verb, um das Schema für das *Contact*-Objekt anzuzeigen, und klicken Sie dann auf **Try it Out**. Visual Studio stoppt die Ausführung des Programms auf dem Haltepunkt, und kann die Logik des Controllers debuggen.

	![Ausprobieren](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Lokales Debuggen einer API-App 

Unter Umständen ist es erforderlich, Ihre API-App lokal zu debuggen, wenn z. B. Roundtrips zum Azure-Server das Debuggen verlangsamen. Der folgende Abschnitt zeigt, wie Sie Ihre API-App mit der Swagger-Benutzeroberfläche als Testclient lokal debuggen.

2. Navigieren Sie in Ihrem Browser zum [Azure-Vorschauportal](https://portal.azure.com). 

3. Klicken Sie in der Randleiste auf die Schaltfläche **Durchsuchen**, und wählen Sie **API-Apps**.

	![Suchoptionen im Azure-Portal](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Wählen Sie die erstellte API-App aus der Liste der API-Apps in Ihrem Abonnement aus.

	![API-App-Liste](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. Klicken Sie auf dem Blatt für die API-App auf **API-App-Host**.

	![API-App-Host](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. Klicken Sie auf dem Blatt für den API-App-Host auf **Alle Eigenschaften**.

	![API-App-Host – Alle Einstellungen](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. Klicken Sie auf dem Blatt **Einstellungen** auf **Anwendungseinstellungen**.

	![API-App-Host – Anwendungseinstellungen](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. Führen Sie auf dem Blatt **Web-App-Einstellungen** einen Bildlauf bis zum Abschnitt **App-Einstellungen** durch.

	![API-App-Host – Anwendungseinstellungen für das lokale Debuggen](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. Öffnen Sie in Visual Studio die Datei *web.config* des API-App-Projekts.

9. Fügen Sie aus den **App-Einstellungen** jeden der folgenden Schlüssel und Werte zum Abschnitt **appSettings** in der Datei *web.config* hinzu.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Anschließend sollte der **appSettings**-Abschnitt der Datei *web.config* in etwa wie der folgende Screenshot aussehen.

	![API-App-Host – Anwendungseinstellungen für das lokale Debuggen](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Hinweis:** Die *EMA\_*-Werte, die Sie der Datei *web.config* in diesem Abschnitt hinzufügen, enthalten sensible Autorisierungsinformationen. Aus diesem Grund sollten Sie es vermeiden, diese Werte in einem öffentlichen Quellcodeverwaltungsrepository zu speichern. Weitere Informationen finden Sie unter [Best Practices für das Bereitstellen von Kennwörtern und anderen vertraulichen Daten in ASP.NET und Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Platzieren Sie in der `Get`-Methode einen Haltepunkt in Ihrem API-App-Controllercode.

11. Drücken Sie F5, um ein Visual Studio-Debugsitzung zu starten.
 
13.  Wenn die Zugriffsebene der API-App auf **Öffentlich (anonym)** festgelegt ist, können Sie die Seite mit der Swagger-Benutzeroberfläche für Tests verwenden.

	* Wenn der Browser die Seite lädt, wird eine HTTP 403-Fehlerseite angezeigt. Fügen Sie in der Adresszeile des Browsers am Ende der URL *swagger/* hinzu, und drücken Sie die EINGABETASTE.

	* Sobald die Swagger-Benutzeroberfläche geladen wurde, klicken Sie auf das **Get**-Verb im Browserfenster, um das Schema für das Contact-Objekt anzuzeigen, und klicken Sie dann auf **Try it Out**.

		Visual Studio stoppt die Ausführung des Programms auf dem Haltepunkt, und kann die Logik des Controllers debuggen.

14.	Wenn die Zugriffsebene der API-App auf **Öffentlich (authentifiziert)** festgelegt ist, müssen Sie gemäß den unter [Schützen einer API-App](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) gezeigten Verfahren für eine POST-Anforderung eine Authentifizierung vornehmen und ein Browsertool verwenden. Gehen Sie so vor:

	* Wechseln Sie zur Anmelde-URL für das Gateway, und geben Sie zum Anmelden die Anmeldeinformationen ein.
	* Rufen Sie den Wert des Zumo-Tokens aus dem "x-zumo-auth"-Cookie ab.
	* Fügen Sie den Header "x-zumo-auth" Ihrer Anforderung hinzu, und legen Sie seinen Wert auf den des "x-zumo-auth"-Cookies fest.
	* Senden Sie die Anforderung.

	**Hinweis:** Bei einer lokalen Ausführung kann Azure nicht den Zugriff auf die API-App steuern, um sicherzustellen, dass nur authentifizierte Benutzer deren Methoden ausführen dürfen. Bei Ausführung in Azure wird sämtlicher für die API-App bestimmter Datenverkehr über das Gateway weitergeleitet, wobei das Gateway nur authentifizierte Anforderungen weiterleitet. Bei lokaler Ausführung erfolgt keine Umleitung, was bedeutet, dass nicht authentifizierte Anforderungen nicht am Zugriff auf die API-App gehindert werden. Der Nutzen der oben beschriebenen Authentifizierung ist, dass Sie authentifizierungsbezogenen Code in der API-App erfolgreich ausführen können, wie z. B. Code, der Informationen zum angemeldeten Benutzer abruft. Weitere Informationen dazu, wie das Gateway die Authentifizierung für API-Apps abwickelt, finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie das Debuggen bei API-Apps erfolgt.

Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung von Web-Apps in Azure App Service in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md). Da API-Apps Web-Apps sind, die über zusätzliche Features zum Hosten von Webdiensten verfügen, können Sie dieselben Debugging- und Problembehandlungstools für API-Apps verwenden, die Sie für Web-Apps nutzen.

<!---HONumber=AcomDC_1203_2015-->