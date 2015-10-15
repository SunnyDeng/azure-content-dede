<properties
	pageTitle="Erste Schritte mit Mobile App-Back-Ends für HTML- und JavaScript-Apps | Azure App Service Mobile Apps"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung mobiler Azure-App-Back-Ends für die Entwicklung von Web-Apps in HTML5 und JavaScript."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="09/24/2015"
	ms.author="glenga"/>


#Erstellen einer HTML-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Übersicht

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer HTML5- oder JavaScript-Web-App hinzufügen. Sie erstellen sowohl ein neues mobiles App-Back-End als auch eine einfache *Todo list*-Web-App, die App-Daten in Azure speichert.

Unten sehen Sie einen Screenshot aus der fertigen App:

![Screenshot der fertigen App](./media/app-service-mobile-dotnet-backend-html-get-started-preview/mobile-quickstart-completed-html.png)

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Apps-Lernprogramme für HTML-Apps.

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] oder höher.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile) sofort kostenlos eine kurzlebige mobile Starter-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Erstellen eines neuen mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Herunterladen des Serverprojekts

1. Klicken Sie im [Azure-Portal] auf **Alle durchsuchen** > **Web-Apps**, und klicken Sie anschließend auf das mobile App-Back-End, das Sie gerade erstellt haben. 

2. Klicken Sie im Mobile App-Back-End auf **Alle Einstellungen**, und klicken Sie unter **Mobile App** auf **Schnellstart** > **HTML/JavaScript**.

3. Klicken Sie unter **Herunterladen und Serverprojekt ausführen** in **Neue App erstellen** auf **Herunterladen**, extrahieren Sie die komprimierten Projektdateien auf den lokalen Computer, und öffnen Sie die Projektmappe in Visual Studio.

4. Erstellen Sie das Projekt, um die NuGet-Pakete wiederherzustellen.

##Aktivieren von CORS im Serverprojekt

Cross-Origin Resource Sharing (CORS) ist eine Möglichkeit für webbasierte Apps, um anzuzeigen, von welchen Domänen Anforderungen sicher sind und vom Browser zugelassen werden sollen. Sie müssen einen CORS-Eintrag für jede Website hinzufügen, die Zugriff auf Ihr Mobile App-Back-End haben soll. Sie steuern die CORS-Einstellungen mithilfe des ASP.NET Web-API-Standardverhaltens. Weitere Informationen finden Sie unter [Aktivieren von ursprungsübergreifenden Anforderungen in ASP.NET Web-API](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors).

Standardmäßig wird das aus dem Portal heruntergeladene Client-Schnellstartprojekt auf "localhost" auf Port 8000 ausgeführt. Aus diesem Grund aktivieren Sie als Nächstes CORS für `http://localhost:8000` im Serverprojekt.

1. Klicken Sie in Visual Studio im Menü "Extras" auf **NuGet-Paket-Manager** > **Paket-Manager-Konsole**, wählen Sie "NuGet.org" als **Paketquelle**, und führen Sie den folgenden Befehl im Konsolenfenster aus:
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. Öffnen Sie die Projektdatei "App\_Start/Startup.MobileApp.cs", und fügen Sie die folgende "using"-Anweisung hinzu:

		using System.Web.Http.Cors;

3. Fügen Sie als Nächstes den folgenden Code in die **Startup.ConfigureMobileApp**-Methode ein, nachdem **HttpConfiguration** (*config*) erstellt wurde:

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. Speichern Sie Ihre Updates.

Dann stellen Sie das CORS-fähige Projekt in Azure bereit.

##Veröffentlichen des Serverprojekts in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##Herunterladen und Ausführen des Clientprojekts

1. Klicken Sie zurück im Blatt für ihr Mobile App-Back-End auf **Alle Einstellungen**, und klicken Sie unter **Mobile App** auf **Schnellstart** > **HTML/JavaScript**. 

2.  Klicken Sie unter **Herunterladen und HTML/Javascript-Projekt ausführen** in **Neue App erstellen** auf **Herunterladen**, und speichern Sie die komprimierten Projektdateien auf dem lokalen Computer.

3. Navigieren Sie zum Speicherort der komprimierten Projektdateien, erweitern Sie die Dateien auf Ihrem Computer, und starten Sie eine der folgenden Befehlsdateien im Unterordner **server**.

	+ **launch-windows** (Windows-Computer)
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	> [AZURE.NOTE]Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

	Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

4. Öffnen Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in einem Webbrowser, um die App zu starten. Die Client-App wird zum Herstellen einer Verbindung mit dem mobilen App-Back-End in Azure konfiguriert.

5. Geben Sie in der App in _Neue Aufgabe eingeben_ eine Beschreibung ein, wie zum Beispiel **Lernprogramm ausführen**, und klicken Sie dann auf **Hinzufügen**.

   	![Ausführen der App](./media/app-service-mobile-dotnet-backend-html-get-started-preview/mobile-quickstart-startup-html.png)

   	Dadurch wird eine POST-Anforderung an das neue in Azure gehostete mobile App-Back-End gesendet. Daten von der Anforderung werden in die "TodoItem"-Tabelle im Mobile App-Schema eingefügt. In der Tabelle gespeicherte Werte werden vom Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

	> [AZURE.TIP]Sie können den Code, der zur Abfrage und zum Einfügen von Daten auf Ihren mobilen Dienst zugreift, in der Datei app.js finden und prüfen.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure-Portal]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!-----HONumber=Oct15_HO1-->