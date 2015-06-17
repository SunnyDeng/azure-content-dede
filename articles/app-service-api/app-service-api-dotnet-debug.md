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
	ms.date="06/01/2015" 
	ms.author="bradyg;tarcher"/>

# Debuggen einer API-App in Azure App Service

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie ASP.NET Web API-Code zu debuggen, die Ausführung konfiguriert ist ein [API-Anwendung](app-service-api-apps-why-best-platform.md) in [Azure Anwendungsdiensts](../app-service/app-service-value-prop-what-is.md). Sie können sowohl lokal und Remote Debuggen (obwohl sie in Azure ausgeführt wird). Im Lernprogramm wird die API-App verwendet, die Sie in den vorherigen Lernprogrammen in dieser Reihe [erstellt](app-service-dotnet-create-api-app.md) und [bereitgestellt](app-service-dotnet-deploy-api-app.md) haben.

## Eine API-Anwendung Remote Debuggen 

Die folgenden Schritte können Sie Ihre API-Anwendung zu debuggen, während der Ausführung in der Cloud über die Swagger-Benutzeroberfläche als der Testclient.

1. In Visual Studio **Projektmappen-Explorer**, mit der rechten Maustaste in des Projekts, das Sie [bereitgestellt, die im vorherigen Lernprogramm](app-service-dotnet-deploy-api-app.md), und wählen Sie **Veröffentlichen**.

	![Projekt veröffentlichen](./media/app-service-api-dotnet-debug/rd-publish.png)

2. In der **Web veröffentlichen** Dialog, wählen Sie die Einstellungen, Registerkarte, und überprüfen Sie, ob die **Debuggen** Buildkonfiguration ausgewählt ist. Klicken Sie abschließend auf **Veröffentlichen** Änderungen an Ihrem Azure-Abonnement übertragen.

	![Projekt veröffentlichen](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. Ein Browserfenster wird geöffnet und zeigt eine Meldung zur Bestätigung, dass Ihre API-Anwendung erfolgreich erstellt wurde.

4. Fügen Sie in der Adressleiste des Browsers /swagger am Ende der URL, und drücken Sie die & Lt; Geben Sie >. Dadurch wird den Swagger-UI-Client angezeigt.

	![Swagger-Benutzeroberfläche](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Kehren Sie zu Visual Studio und von der **Ansicht** klicken Sie im Menü **Server-Explorer**.

6. In **Server-Explorer**, erweitern Sie die **Azure > Anwendungsdiensts** Knoten.

7. Suchen Sie die Ressourcengruppe, die Sie beim Bereitstellen Ihrer API-App erstellt haben.

8. Klicken Sie unterhalb der Ressourcengruppe mit der rechten Maustaste auf den Knoten für die API-App, und wählen Sie **Debugger anfügen**.

	![Anfügen des Debuggers](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	Der Remotedebugger versucht, eine Verbindung herzustellen. In einigen Fällen müssen Sie erneut auf **Debugger anfügen** klicken, um eine Verbindung herzustellen. Versuchen Sie es daher ggf. noch einmal.

	![Anfügen des Debuggers](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Nachdem die Verbindung hergestellt wurde, öffnen Sie die Datei **ContactsController.cs** im API-App-Projekt, und fügen Sie Haltepunkte bei den Methoden `Get` und `Post` hinzu. Sie werden eventuell zunächst nicht als aktiv angezeigt. Wenn jedoch der Remotedebugger angefügt ist, sind Sie zum Debuggen bereit.

	![Anwenden von Haltepunkten auf Controller](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Zurück zu der Browsersitzung, klicken Sie auf die **Abrufen** Verb zum Anzeigen des Schemas für die *Kontakt* -Objekt, und klicken Sie dann auf **Probieren Sie es aus**. Wenn Sie einen Haltepunkt in des Controllers **Abrufen** Methode, Visual Studio beendet die Ausführung des Programms, und Sie können Ihren Controller Logik Debuggen.

	![Ausprobieren](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Eine API-Anwendung lokal zu debuggen. 

Möglicherweise möchten Sie Ihre API-Anwendung lokal zu debuggen; Blättern z. B. zur Vermeidung von potenziell langsame Roundtrips während der Test/Debug. Die folgenden Schritte veranschaulichen, wie die API-Anwendung mithilfe der Benutzeroberfläche Swagger lokal der Testclient zu debuggen.

1. Öffnen Sie des API-app-Projekts in Visual Studio *web.config* Datei. 
 
2. Navigieren Sie in Ihrem Browser zum [Azure-Vorschauportal](http://portal.azure.com).

3. Klicken Sie in der Randleiste auf die Schaltfläche **Durchsuchen**, und wählen Sie **API-Apps**.

	![Suchoptionen im Azure-Portal](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Wählen Sie aus der Liste der API-apps in Ihrem Abonnement die API-Anwendung, die Sie erstellt haben.

	![Liste der API-Apps](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. Klicken Sie in der API-app-Blade auf den **app-Host-API-**.

	![App-Host-API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. Klicken Sie in der API-app-Host-Blade auf **alle Einstellungen**.

	![API-Anwendung hosten, alle Einstellungen](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. In der **Einstellungen** Blade, klicken Sie auf **Anwendungseinstellungen**.

	![App-Host-API-Anwendungseinstellungen](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. In der **Web-app-Einstellungen** Blade, führen Sie einen Bildlauf nach unten, um die **App-Einstellungen** Abschnitt.

	![App-Host-API-Anwendungseinstellungen für lokales Debuggen](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

9. Von **App-Einstellungen**, suchen Sie die folgenden Werte, und fügen sie die *web.config* Datei **AppSettings** Abschnitt.
	- **EMA_MicroserviceId**
	- **EMA_Secret**
	- **EMA_RuntimeUrl**

	Abschließend die **AppSettings** Teil Ihrer *web.config* entspricht in etwa dem folgenden Screenshot.

	![App-Host-API-Anwendungseinstellungen für lokales Debuggen](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Hinweis:** der *EMA_* Werte Sie hinzugefügt Ihre *web.config* Datei in diesem Abschnitt enthalten sensible Autorisierungsinformationen. Aus diesem Grund wird empfohlen, dass Sie beim Ausführen eines Commits für diese Datei in einem öffentlichen Source Control Datenträger vorsichtig (z. B. *Github*) wie dieser geheimen Schlüssel dann für andere Benutzer sichtbar werden. Finden Sie im Artikel [Best Practices für die Bereitstellung von Kennwörter und andere vertraulichen Daten auf ASP.NET und Azure-Anwendungsdiensts](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) Weitere Informationen.

10. Legen Sie einen oder mehrere Haltepunkte in Ihre API-app Controller (in der `Get` und `Post` Methoden).

	![Festlegen von Haltepunkten](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. Klicken Sie auf & Lt; F5 > ein Visual Studio-Debugsitzung starten. Wenn der Browser die Seite geladen wird, sollten Sie eine Fehlermeldung angezeigt. Hinzufügen */swagger* am Ende der URL in der Adressleiste Ihres Browsers und drücken Sie die & Lt; Geben Sie >.

12. Nachdem die Swagger-Benutzeroberfläche geladen wurde, klicken Sie auf die **Abrufen** Verb im Browserfenster, das Schema für die Contact-Objekts anzuzeigen, und klicken Sie auf **Probieren Sie es aus**. Visual Studio wird jetzt die Programmausführung anzuhalten, auf die zuvor festgelegten Haltepunkte, und Sie können Ihren Controller Logik Debuggen.

	![Ausprobieren](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

## Nächste Schritte

Durch das Remotedebuggen für API-Apps können Sie einfacher sehen, wie Ihr Code in Azure App Service ausgeführt wird. Umfangreiche Diagnose- und Debugdaten stehen Ihnen direkt in der Visual Studio-IDE für Ihre remote ausgeführten Azure API-Apps zur Verfügung.

App Service-API-Apps sind App Service-Web-Apps, die über zusätzliche Features zum Hosten von Webdiensten verfügen, sodass Sie dieselben Debugging- und Problembehandlungstools für API-Apps verwenden können, die Sie für Web-Apps nutzen. Weitere Informationen finden Sie unter [Problembehandlung einer Web-App in Azure App Service mithilfe von Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

Die im Rahmen dieser Lernprogrammreihe erstellte API-App ist öffentlich verfügbar und kann durch beliebige Benutzer aufgerufen werden. Weitere Informationen zum Schutz der API-App, sodass nur authentifizierte darauf zugreifen können, finden Sie unter [Schützen einer API-App: Hinzufügen der Authentifizierung über Azure Active Directory oder ein soziales Netzwerk](app-service-api-dotnet-add-authentication.md).
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->