<properties
	pageTitle="Erstellen und Bereitstellen einer Java-API-App in Azure App Service"
	description="Lernen Sie, wie Sie ein Java-API-App-Paket erstellen und in Azure App Service bereitstellen."
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Erstellen und Bereitstellen einer Java-API-App in Azure App Service

In diesem Lernprogramm erfahren Sie, wie Sie eine Java-Anwendung erstellen und mit [Git](http://git-scm.com) als Azure App Service-API-App bereitstellen. Die Anweisungen in diesem Lernprogramm gelten für alle Betriebssysteme, unter denen Java ausgeführt werden kann. Dieses Lernprogramm basiert zudem auf [Gradle](https://gradle.org), um die Build-Automatisierung und die Paketabhängigkeitsauflösung für die Java-Anwendung zu aktivieren. [RESTEasy](http://resteasy.jboss.org/) wird schließlich zum Erstellen des RESTful-Dienstes verwendet, womit die [JaxRS](https://jax-rs-spec.java.net/)-Spezifikation vollständig implementiert wird.

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![][sample-api-app-page]

## Erstellen einer API-App im Azure-Portal

> [AZURE.NOTE]Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](/pricing/member-offers/msdn-benefits-details/) oder [sich für eine kostenlose Testversion registrieren](/pricing/free-trial/). Sie können auch die kostenlosen [App Service-App-Beispiele](http://tryappservice.azure.com) nutzen.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie links unten im Portal auf **NEU**.

3. Klicken Sie auf **Web + Mobil > API-App**.

	![][portal-quick-create]

4. Geben Sie einen Wert für **Name** ein, beispielsweise "JavaAPIApp".

5. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen neuen. Wenn Sie einen neuen Plan erstellen, wählen Sie Tarif, Standort und weitere Optionen.

	![][portal-create-api]

6. Klicken Sie auf **Erstellen**.

	![][api-app-blade]

	Wenn Sie das Kontrollkästchen **Zum Startboard hinzufügen** aktiviert lassen, öffnet das Portal nach dem Erstellen der API-App automatisch das zugehörige Blatt. Wenn Sie das Kontrollkästchen deaktiviert haben, klicken Sie auf der Startseite des Portals auf **Benachrichtigungen**, um den Status der API-App-Erstellung anzuzeigen. Klicken Sie anschließend auf die Benachrichtigung, um zum Blatt für die neue API-App zu wechseln.

7. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

9. Legen Sie die Zugriffsebene auf **Öffentlich (anonym)** fest.

11. Klicken Sie auf **Speichern**.

	![][set-api-app-access-level]

## Aktivieren der Git-Veröffentlichung für die neue API-App

[Git](http://git-scm.com) ist ein verteiltes Versionskontrollsystem, mit dem Sie Ihre Azure-Website bereitstellen können. Sie speichern den Code, den Sie für Ihre API-App geschrieben haben, in einem lokalen Git-Repository. Anschließend stellen Sie Ihren Code in Azure bereit, indem Sie einen Pushvorgang zu einem Remoterepository durchführen. Diese Methode der Bereitstellung ist eine Funktion von App Service-Web-Apps, die Sie in einer API-App nutzen können, da API-Apps auf Web-Apps basieren: Eine API-App in Azure App Service ist eine Web-App mit zusätzlichen Funktionen für das Hosten von Webdiensten.

Im Portal verwalten Sie die API-App-spezifischen Funktionen im Blatt **API-App**. Die Funktionen, die eine API-App mit einer Web-App gemeinsam hat, werden im Blatt **API-App-Host** verwaltet. Sie wechseln daher in diesem Abschnitt zum Blatt **API-App-Host**, um die Git-Bereitstellungsfunktion zu konfigurieren.

1. Klicken Sie im Blatt für die API-App auf **API-App-Host**.

	![][api-app-host]

2. Suchen Sie im Blatt **API-App** nach dem Abschnitt **Bereitstellung**, und klicken Sie auf ** Kontinuierliche Bereitstellung einrichten**. Sie müssen möglicherweise einen Bildlauf nach unten durchführen, um diesen Teil des Blatts anzuzeigen.

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

## Aktivieren der Java-Laufzeitumgebung für die neue API-App

Damit die App-API eine Java-App erfolgreich hosten kann, müssen wir die Java-Laufzeitumgebung aktivieren und einen Anwendungsserver auswählen. Das Portal bietet Ihnen dafür eine einfache Möglichkeit. Zum Hosten der Anwendung aktivieren wir Java 7 und Jetty.

1. Klicken Sie im Blatt für die API-App auf **API-App-Host**.

	![][api-app-host]

2. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**. Aktivieren Sie dort Java, und wählen Sie "Jetty" als Anwendungsserver. Klicken Sie auf **Speichern**.

	![][api-app-enable-java]

Dadurch **aktivieren Sie die Java-Laufzeitumgebung** für die API-App und erstellen einen Ordner **webapps/** im Stammverzeichnis Ihrer Website. Dieser Ordner enthält alle WAR-Dateien Ihrer Anwendungen.

## Herunterladen und Prüfen des Codes für eine Java-API-App

In diesem Abschnitt laden Sie Code herunter und prüfen ihn, der als Teil des JavaAPIApp-Beispiels bereitgestellt wird.

1. Laden Sie den Code in [diesem GitHub-Repository](http://go.microsoft.com/fwlink/?LinkId=571009) herunter. Sie können das Repository entweder klonen oder auf **ZIP herunterladen** klicken, um den Code als ZIP-Datei herunterzuladen. Wenn Sie die ZIP-Datei herunterladen, extrahieren Sie sie auf Ihre lokale Festplatte.

2. Navigieren Sie zu dem Ordner, in dem Sie das Beispiel extrahiert haben, und navigieren Sie dann zum `build\libs`-Ordner.

	![][api-app-folder-browse]

3. Öffnen Sie die Datei **apiapp.json** in einem Text-Editor, und untersuchen Sie die Dateiinhalte.

	![][apiapp-json]

	Für Azure App Service gelten zwei Voraussetzungen, damit eine Java-Anwendung als API-App erkannt wird:

	+ Es muss eine Datei namens *apiapp.json* im Stammverzeichnis der Anwendung vorhanden sein.
	+ Durch die Anwendung muss ein Swagger 2.0-Metadatenendpunkt verfügbar gemacht werden. Die URL dieses Endpunkts wird in der Datei *apiapp.json* angegeben.

	Beachten Sie die Eigenschaft **apiDefinition**. Der Pfad für diese URL ist relativ zur URL Ihrer API und verweist auf den Swagger 2.0-Endpunkt. Azure App Service verwendet diese Eigenschaft zum Ermitteln der Definition Ihrer API und zum Aktivieren zahlreicher der App Service-API-App-Funktionen.

4. Navigieren Sie zu `src\main\java\com\microsoft\trysamples\javaapiapp`, öffnen Sie die Datei **App.java**, und prüfen Sie den Code.

	![][app-java]

	Der Code verwendet das Swagger-Paket für JaxRS, um den Swagger 2.0-Endpunkt zu erstellen.

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	Mit der `setVersion`-Methode wird die API-Version in Metadaten festgelegt, die von Swagger bereitgestellt werden.

	Mit der `setBasePath`-Methode wird der Basispfad festgelegt, den Swagger zum Generieren der Metadaten verwendet. Diese URL ist relativ zum Basispfad Ihrer API-App.

	Mit der `setHost`-Methode wird der Host festgelegt, den die API überwacht. In diesem Fall verwenden wir die Variable "`websitehostname`", die wir ein paar Zeilen zuvor so zugewiesen haben, dass sie dynamisch auf `localhost` festgelegt wird, wenn sie lokal ausgeführt. Oder wir verwenden den Hostnamen der API-App, wenn die Anwendung in Azure App Service ausgeführt wird.

	Mit der `setResourcePackage`-Methode wird das Paket festgelegt, das Swagger überprüft und in die Datei "Swagger.json" mit den API-Metadaten einschließt.

	Mit der `setSchemes`-Methode werden die unterstützten Schemas definiert.

	Mit der `setScan`-Methode kann Swagger die App-Dokumentation generieren.

	Es gibt mehrere Methoden, mit denen die Ausgabe von Swagger angepasst wird, wenn Sie RESTEasy verwenden. Informationen dazu finden Sie auf der [Wiki-Seite](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig) von Swagger.

	> [AZURE.NOTE]Die Swagger-Metadatendatei befindet sich unter `/JavaAPIApp/api/swagger.json`.

## Lokale Ausführung des API-App-Codes

In diesem Abschnitt führen Sie die Anwendung lokal aus, um vor der Bereitstellung deren Funktion zu überprüfen.

1. Navigieren Sie zu dem Ordner, in den Sie das Beispiel heruntergeladen haben.

2. Öffnen Sie eine Eingabeaufforderung, und geben Sie den folgenden Befehl ein:

		gradlew.bat

3. Geben Sie nach Abschluss des Befehls den folgenden Befehl ein:

		gradlew.bat jettyRunWar

	Das Befehlszeilenfenster zeigt die folgende Meldung an:

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. Navigieren Sie im Browser zu `http://localhost:8080/JavaAPIApp/`

	Sie sehen die folgende Seite:

	![][sample-api-app-page]

6. Navigieren Sie zur Anzeige der Datei "Swagger.json" zu `http://localhost:8080/JavaAPIApp/api/Swagger.json`.

## Veröffentlichen Ihres API-App-Codes in Azure App Service

In diesem Abschnitt erstellen Sie ein lokales Git-Repository und führen einen Pushvorgang von diesem Repository zu Azure durch, um Ihre Beispielanwendung in der API-App bereitzustellen, die in Azure App Service ausgeführt wird.

1. Falls Git nicht installiert ist, führen Sie die Installation über die [Git-Downloadseite](http://git-scm.com/download) durch.

1. Wechseln Sie über die Befehlszeile zum Verzeichnis der Beispielanwendung und dann zu `build\libs`, und geben Sie die folgenden Befehle ein, um ein lokales Git-Repository zu initialisieren.

		git init


2. Verwenden Sie die folgenden Befehle, um dem Repository Dateien hinzuzufügen:

		git add .
		git commit -m "Initial commit of the API App"

3. Erstellen Sie eine Remotereferenz, um Updates für die zuvor erstellte Web-App (API-App-Host) zu pushen, und verwenden Sie hierbei die zuvor kopierte Git-URL:

		git remote add azure [URL for remote repository]

4. Übertragen Sie Ihre Änderungen mit dem folgenden Befehl per Push an Azure:

		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

	Die Ausgabe dieses Befehls führt zu einer Meldung, dass die Bereitstellung erfolgreich durchgeführt wurde:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Anzeigen der API-Definition im Azure-Portal

Nun, da Sie eine API in Ihrer API-App bereitgestellt haben, können Sie die API-Definition im Azure-Portal anzeigen. Im ersten Schritt starten Sie das *Gateway* neu, durch das Azure erkennt, dass eine API-Definition einer API-App geändert wurde. Das Gateway ist eine Web-App, die API-Verwaltung und -Autorisierung für die API-Apps in einer Ressourcengruppe übernimmt.

6. Wechseln Sie im Azure-Portal zum Blatt **API-App** für die zuvor erstellte API-App, und klicken Sie dann auf den Link **Gateway**.

	![][click-gateway]

7. Klicken Sie im Blatt **Gateway** auf **Neu starten**. Sie können dieses Blatt nun schließen.

	![][restart-gateway]

8. Klicken Sie im Blatt **API-App** auf **API-Definition**.

	![][api-definition-click]

	Das Blatt **API-Definition** zeigt eine Get-Methode.

	![][api-definition-blade]

## Ausführen der Beispielanwendung in Azure

Wechseln Sie im Azure-Portal zum Blatt **API-App-Host** für Ihre API-App, und klicken Sie auf **Durchsuchen**.

![][browse-api-app-page]

Der Browser zeigt die Startseite an, die Sie zuvor beim lokalen Ausführen der Beispiel-App gesehen haben.

## Nächste Schritte

Sie haben eine Java-Webanwendung bereitgestellt, die ein API-App-Back-End zu Azure verwendet. Weitere Informationen zum Verwenden von Java in Azure finden Sie im [Java Developer Center](/develop/java/).

Sie können diese Beispiel-API-App unter [TryApp Service](http://tryappservice.azure.com) ausprobieren.

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=58_postMigration-->