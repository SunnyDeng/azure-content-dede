<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Mobile Services
==================================
<div class="dev-center-tutorial-selector sublanding">
	<a href="/de-de/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a> 
	<a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/de-de/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/de-de/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/de-de/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS"> Xamarin.iOS</a>
	<a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/de-de/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a> 
	<a href="/de-de/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>
In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer HTML-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

[Lernprogramm ansehen](http://go.microsoft.com/fwlink/?LinkId=287040) [Video abspielen](http://go.microsoft.com/fwlink/?LinkId=287040) 3:51

Unten finden Sie einen Screenshot aus der fertigen App:

![](./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png)

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für HTML-Apps.

<div class="dev-callout"><b>Hinweis</b>
<p>
Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F).</p></div>

### Weitere Anforderungen

-   Für dieses Lernprogramm muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

    -   **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=286333) installiert.
    -   **Unter MacOS X**: Python, das bereits installiert sein sollte.
    -   **Unter Linux**: Python. Die [neueste Version von Python](http://go.microsoft.com/fwlink/p/?LinkId=286342) muss installiert werden.

    Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.

-   Ein Webbrowser, der HTML5 unterstützt.

Erstellen eines neuen mobilen Diensts
-------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Erstellen einer neuen AppErstellen einer neuen HTML-App</h2>

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue HTML-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue HTML-App erstellen**.

  ![][6]

  Hierdurch werden die drei einfachen Schritte zum Erstellen und Hosten einer mit dem mobilen Dienst verbundenen HTML-App angezeigt.

  ![](./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png)

3.  Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

4.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

  Hierdurch werden die Website-Dateien für die *To-Do-Listen*-Beispielanwendung, die mit dem mobilen Dienst verbunden ist, heruntergeladen. Speichern Sie die komprimierte Datei auf dem lokalen Computer, und merken Sie sich, wo Sie sie gespeichert haben.

5.  Auf der Registerkarte **Konfigurieren** vergewissern Sie sich, dass `localhost` bereits im Feld **ANFORDERUNGEN VON HOSTNAMEN ZULASSEN** unter **cross-origin resource sharing (cors)** eingetragen ist. Falls nicht, geben Sie `localhost` im Feld **Hostname** ein, und klicken Sie dann auf **Speichern**.

  ![](./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png)

  <div class="dev-callout"><b>Hinweis</b>
  <p>Wenn Sie die Schnellstart-App auf einem anderen Webserver als dem localhost bereitstellen, müssen Sie den Hostnamen des Webservers zur Liste von <strong>Anforderungen von Hostnamen zulassen</strong> hinzufügen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn155871.aspx" target="_blank">Cross-Origin Resource Sharing (CORS)</a>.</p>
  </div>

Hosten und Ausführen der HTML-App
---------------------------------

Der letzte Schritt dieses Lernprogramms besteht im Hosten und Ausführen der neuen App auf dem lokalen Computer.

1. Navigieren Sie zum Speicherort der komprimierten Projektdateien, erweitern Sie die Dateien auf Ihrem Computer, und starten Sie eine der folgenden Befehlsdateien im Unterordner **server**.
  - **launch-windows** (Windows-Computer)
  - **launch-mac.command** (Mac OS X-Computer)
  - **launch-linux.sh** (Linux-Computer)

  <div class="dev-callout"><b>Hinweis</b>
  <p>
  Bei einem Windows-Computer geben Sie "R" ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.</p></div>
  Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

2. Öffnen Sie den URL <http://localhost:8000/> in einem Webbrowser zum Starten der App.

3. Geben Sie in der App in **Neue Aufgabe eingeben** eine Beschreibung ein, wie zum Beispiel *Lernprogramm ausführen*, und klicken Sie dann auf **Hinzufügen**.

  ![][10]

  Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

  <div class="dev-callout"><b>Hinweis</b>
  <p>
  Sie können den Code, der zur Abfrage und zum Einfügen von Daten auf Ihren mobilen Dienst zugreift, in der Datei app.js finden und prüfen.</p></div>

3.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

  ![][11]

  Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

  ![][12]

Nächste Schritte
----------------

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   **[Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-html)**
    Lernen Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

-   **[Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-html)**
    Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   **[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)**
    Lernen Sie mehr über die Verwendung von Mobile Services mit HTML/JavaScript.



<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png
