<properties 
	pageTitle="Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service" 
	description="Erfahren Sie, wie Sie ein Node.js-API-App-Paket erstellen und dieses in Azure App Service bereitstellen." 
	services="app-service\api" 
	documentationCenter="nodejs" 
	authors="pkefal" 
	manager="", 
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service

In diesem Lernprogramm erfahren Sie, wie Sie eine [Node.js](http://nodejs.org)-Anwendung erstellen und mit [Git](http://git-scm.com) als Azure App Service-API-App bereitstellen. Die Anweisungen in diesem Lernprogramm gelten für alle Betriebssysteme, unter denen Node ausgeführt werden kann.

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![][sample-api-app-page]

## Erstellen einer API-App im Azure-Vorschauportal

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](/pricing/member-offers/msdn-benefits-details/) oder [sich für eine kostenlose Testversion registrieren](/pricing/free-trial/). Sie können auch die kostenlosen [App Service-App-Beispiele](http://tryappservice.azure.com) nutzen.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie links unten im Portal auf **NEU**.

3. Klicken Sie auf **Web + Mobil > API-App**.

	![][portal-quick-create]

4. Geben Sie einen Wert für **Name** ein, beispielsweise "NodejsAPIApp".

5. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen neuen Plan. Wenn Sie einen neuen Plan erstellen, wählen Sie Tarif, Standort und weitere Optionen.

	![][portal-create-api]

6. Klicken Sie auf **Erstellen**.

	![][api-app-blade]

	Wenn Sie das Kontrollkästchen **Zum Startboard hinzufügen** aktiviert lassen, öffnet das Portal nach dem Erstellen der API-App automatisch das zugehörige Blatt. Wenn Sie das Kontrollkästchen deaktiviert haben, klicken Sie auf der Startseite des Portals auf **Benachrichtigungen**, um den Status der API-App-Erstellung anzuzeigen. Klicken Sie anschließend auf die Benachrichtigung, um zum Blatt für die neue API-App zu wechseln.

7. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

9. Legen Sie die Zugriffsebene auf **Öffentlich (anonym)** fest.

11. Klicken Sie auf **Speichern**.

	![][set-api-app-access-level]

## Aktivieren der Git-Veröffentlichung für die neue API-App

[Git](http://git-scm.com/%20target="_blank) ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen können. Sie speichern den Code, den Sie für Ihre API-App geschrieben haben, in einem lokalen Git-Repository. Anschließend stellen Sie Ihren Code in Azure bereit, indem Sie einen Pushvorgang zu einem Remoterepository durchführen. Diese Methode der Bereitstellung ist eine Funktion von App Service-Web-Apps, die Sie in einer API-App nutzen können, da API-Apps auf Web-Apps basieren: Eine API-App in Azure App Service ist eine Web-App mit zusätzlichen Funktionen für das Hosten von Webdiensten.

Im Portal verwalten Sie die API-App-spezifischen Funktionen im Blatt **API-App**. Die Funktionen, die eine API-App mit einer Web-App gemeinsam hat, werden im Blatt **API-App-Host** verwaltet. Sie wechseln daher in diesem Abschnitt zum Blatt **API-App-Host**, um die Git-Bereitstellungsfunktion zu konfigurieren.

1. Klicken Sie im Blatt für die API-App auf **API-App-Host**.

	![][api-app-host]

2. Suchen Sie im Blatt **API-App** nach dem Abschnitt **Bereitstellung**, und klicken Sie auf ** 	Kontinuierliche Bereitstellung einrichten**. Sie müssen möglicherweise einen Bildlauf nach unten durchführen, um diesen Teil des Blatts anzuzeigen.

	![][deployment-part]

3. Klicken Sie auf **Quelle auswählen > Lokales Git-Repository**.

5. Klicken Sie auf **OK**.

	![][setup-git-publishing]

6. Wenn Sie noch keine Anmeldeinformationen für das Veröffentlichen einer API-App oder einer anderen App Service-App eingerichtet haben, holen Sie dies nun nach.

	* Klicken Sie auf **Anmeldeinformationen für die Bereitstellung einrichten**.

	* Geben Sie einen Benutzernamen und ein Kennwort ein.

	* Klicken Sie auf **Speichern**.

	![][deployment-credentials]

1. Klicken Sie im Blatt **API-App-Host** auf **Einstellungen > Eigenschaften**. Die URL des Git-Remoterepositorys, in dem Sie die Bereitstellung durchführen möchten, wird unter "GIT-URL" angezeigt.

2. Kopieren Sie die URL zur späteren Verwendung im Lernprogramm.

	![][git-url]

## Herunterladen und Prüfen von Code für eine Node.js-API-App

In diesem Abschnitt laden Sie im Rahmen des NodeAPIApp-Beispiels Code herunter und prüfen diesen.


1. Laden Sie den Code in [diesem GitHub-Repository](http://go.microsoft.com/fwlink/?LinkID=534023&clcid=0x409) herunter. Sie können das Repository entweder klonen oder auf **ZIP herunterladen** klicken, um den Code als ZIP-Datei herunterzuladen. Wenn Sie die ZIP-Datei herunterladen, extrahieren Sie sie auf Ihre lokale Festplatte.

2. Navigieren Sie zu dem Ordner, in dem Sie das Beispiel extrahiert haben.

	![][api-app-folder-browse]

3. Öffnen Sie die Datei **apiapp.json** in einem Text-Editor, und untersuchen Sie die Dateiinhalte.

	![][apiapp-json]

	Für Azure App Service gelten zwei Voraussetzungen, damit eine Node.js-Anwendung als API-App erkannt wird:

	+ Es muss eine Datei namens *apiapp.json* im Stammverzeichnis der Anwendung vorhanden sein.
	+ Durch die Anwendung muss ein Swagger 2.0-Metadatenendpunkt verfügbar gemacht werden. Die URL dieses Endpunkts wird in der Datei *apiapp.json* angegeben.

	Beachten Sie die Eigenschaft **apiDefinition**. Der Pfad für diese URL ist relativ zur URL Ihrer API und verweist auf den Swagger 2.0-Endpunkt. Azure App Service verwendet diese Eigenschaft zum Ermitteln der Definition Ihrer API und zum Aktivieren zahlreicher der App Service-API-App-Funktionen.

	> [AZURE.NOTE]Der Endpunkt muss der Swagger 2.0-Spezifikation entsprechen, da ältere Versionen \(beispielsweise 1.2\) nicht von der Plattform unterstützt werden. Die Beispielanwendung verwendet "swaggerize-express" zum Erstellen eines Endpunkts nach Swagger 2.0-Spezifikation.

4. Öffnen Sie die Datei **server.js**, und untersuchen Sie den Code.

	![][server-js]

	Der Code verwendet das swaggerize-express-Modul, um den Swagger 2.0-Endpunkt zu erstellen.

		app.use(swaggerize({
		    api: require('./api.json'),
		    docspath: '/swagger',
		    handlers: './handlers/'
		}));

	Die Eigenschaft `api` verweist auf die Datei "api.json", die die API-Definition nach Swagger 2.0-Spezifikation enthält. Sie können die Datei manuell in einem Text-Editor erstellen oder den [Online-Editor von Swagger](http://editor.swagger.io) verwenden und die JSON-Datei von dort herunterladen. \(Die Datei *api.json* legt eine Eigenschaft `host` fest, aber der Wert dieser Eigenschaft wird zur Laufzeit dynamisch ermittelt und ersetzt.\)

	Die Eigenschaft `docspath` verweist auf den Swagger 2.0-Endpunkt. Diese URL ist relativ zum Basispfad Ihrer API. Der Basispfad wird in der Datei "api.json" deklariert. In unserem Beispiel lautet der Basispfad */api/data*, deshalb lautet der relative Pfad zum Swagger-Endpunkt */api/data/swagger*.

	> [AZURE.NOTE]Da der Basispfad in der Datei *api.json* deklariert ist, führt der Zugriff auf den */swagger*-Endpunkt als relativen Pfad zur API-App-URL zu einem 404-Fehler. Dies ist ein häufiger Fehler beim Versuch des Zugriffs auf den Swagger-Endpunkt.

	Die Eigenschaft `handlers` verweist auf den lokalen Ordner, der die Routenhandler für das Express.js-Modul enthält.

## Lokale Ausführung des API-App-Codes

In diesem Abschnitt führen Sie die Anwendung lokal aus, um vor der Bereitstellung deren Funktion zu überprüfen.

1. Navigieren Sie zu dem Ordner, in den Sie das Beispiel heruntergeladen haben.

2. Öffnen Sie eine Eingabeaufforderung, und geben Sie den folgenden Befehl ein:

		npm install

3. Geben Sie nach Abschluss des Befehls *install* den folgenden Befehl ein:

		node server.js

	Das Befehlszeilenfenster zeigt die Meldung "Server gestartet..." an.

5. Navigieren Sie im Browser zu http://localhost:1337/

	Sie sehen die folgende Seite:

	![][sample-api-app-page]

6. Navigieren Sie zur Anzeige der Datei "Swagger.json" zu http://localhost:1337/api/data/swagger.

## Veröffentlichen Ihres API-App-Codes in Azure App Service

In diesem Abschnitt erstellen Sie ein lokales Git-Repository und führen einen Pushvorgang von diesem Repository zu Azure durch, um Ihre Beispielanwendung in der API-App bereitzustellen, die in Azure App Service ausgeführt wird.

1. Falls Git nicht installiert ist, führen Sie die Installation über die [Git-Downloadseite](http://git-scm.com/download%20target="_blank") durch.

1. Wechseln Sie über die Befehlszeile zum Verzeichnis der Beispielanwendung, und geben Sie die folgenden Befehle ein, um ein lokales Git-Repository zu initialisieren.

		git init


2. Verwenden Sie die folgenden Befehle, um dem Repository Dateien hinzuzufügen:

		git add .
		git commit -m "Initial commit of the API App"

3. Erstellen Sie eine Remotereferenz, um Updates für die zuvor erstellte Web-App \(API-App-Host\) zu pushen, und verwenden Sie hierbei die zuvor kopierte Git-URL:

		git remote add azure [URL for remote repository]

4. Übertragen Sie Ihre Änderungen mit dem folgenden Befehl per Push an Azure:

		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

	Die Ausgabe dieses Befehls führt zu einer Meldung, dass die Bereitstellung erfolgreich durchgeführt wurde:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		* [new branch] master -> master

## Anzeigen der API-Definition im Azure-Vorschauportal

Nun, da Sie eine API in Ihrer API-App bereitgestellt haben, können Sie die API-Definition im Azure-Vorschauportal anzeigen. Im ersten Schritt starten Sie das *Gateway* neu, durch das Azure erkennt, dass eine API-Definition einer API-App geändert wurde. Das Gateway ist eine Web-App, die API-Verwaltung und -Autorisierung für die API-Apps in einer Ressourcengruppe übernimmt.

6. Wechseln Sie im Azure-Vorschauportal zum Blatt **API-App** für die zuvor erstellte API-App, und klicken Sie auf den Link **Gateway**.

	![](./media/app-service-api-nodejs-api-app/clickgateway.png)

7. Klicken Sie im Blatt **Gateway** auf **Neu starten**. Sie können dieses Blatt nun schließen.

	![](./media/app-service-api-nodejs-api-app/gatewayrestart.png)

8. Klicken Sie im Blatt **API-App** auf **API-Definition**.

	![](./media/app-service-api-nodejs-api-app/apidef.png)

	Das Blatt **API-Definition** zeigt zwei Get-Methoden.

	![](./media/app-service-api-nodejs-api-app/apidefblade.png)

## Ausführen der Beispielanwendung in Azure

Wechseln Sie im Azure-Vorschauportal zum Blatt **API-App-Host** für Ihre API-App, und klicken Sie auf **Durchsuchen** .

![][browse-api-app-page]

Der Browser zeigt die Startseite an, die Sie zuvor beim lokalen Ausführen der Beispiel-App gesehen haben.

## API-App-Metadaten

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../includes/app-service-api-direct-deploy-metadata.md)]

## Nächste Schritte

Sie haben eine Node.js-Web-App bereitgestellt, die ein API-App-Back-End zu Azure verwendet. Weitere Informationen zum Verwenden von Node.js in Azure finden Sie im [Node.js Developer Center](/develop/nodejs/).

* Sie können diese Beispiel-API-App unter [TryApp Service](http://tryappservice.azure.com) ausprobieren.

[portal-quick-create]: ./media/app-service-api-nodejs-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-nodejs-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-nodejs-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-nodejs-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-nodejs-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-nodejs-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-nodejs-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-nodejs-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-nodejs-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-nodejs-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-nodejs-api-app/apiapp-json.png
[server-js]: ./media/app-service-api-nodejs-api-app/server-js.png
[sample-api-app-page]: ./media/app-service-api-nodejs-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-nodejs-api-app/browse-api-app-page.png

<!--HONumber=52-->
