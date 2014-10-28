<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer HTML-App hinzuf&uuml;gen k&ouml;nnen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen</em>-App, die App-Daten im neuen mobilen Dienst speichert. Sie k&ouml;nnen sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">03:51:00</span></div>

</div>

Unten sehen Sie einen Screenshot aus der fertigen App:

![][]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für HTML-Apps.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

### Weitere Anforderungen

-   Für dieses Lernprogramm muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

    -   **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft-Webplattform-Installer][Microsoft-Webplattform-Installer] installiert.
    -   **Unter MacOS X**: Python, das bereits installiert sein sollte.
    -   **Unter Linux**: Python. Die [neueste Version von Python][neueste Version von Python] muss installiert werden.

    Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.

-   Ein Webbrowser, der HTML5 unterstützt.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## 

## <span class="short-header">Erstellen einer neuen App</span>Erstellen einer neuen HTML-App

</h2>
Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue HTML-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue HTML-App erstellen**.

    ![][1]

    Hierdurch werden die drei einfachen Schritte zum Erstellen und Hosten einer mit dem mobilen Dienst verbundenen HTML-App angezeigt.

    ![][2]

3.  Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

4.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

    Hierdurch werden die Websitedateien für die *To-Do-Listen*-Beispielanwendung, die mit dem mobilen Dienst verbunden ist, heruntergeladen. Speichern Sie die komprimierte Datei auf dem lokalen Computer, und merken Sie sich, wo Sie sie gespeichert haben.

5.  Vergewissern Sie sich auf der Registerkarte **Konfigurieren**, dass `localhost` bereits im Feld **Anforderungen von Hostnamen zulassen** unter **cross-origin resource sharing (CORS)** eingetragen ist. Falls nicht, geben Sie `localhost` im Feld **Hostname** ein, und klicken Sie dann auf **Speichern**.

    ![][3]

    <div class="dev-callout"><b>Hinweis</b>
    <p>Wenn Sie die Schnellstart-App auf einem anderen Webserver als &quot;localhost&quot; bereitstellen, m&uuml;ssen Sie den Hostnamen des Webservers der Liste <strong>Anforderungen von Hostnamen zulassen</strong> hinzuf&uuml;gen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn155871.aspx" target="_blank">Cross-Origin Resource Sharing (CORS)</a>.</p>
</div>

## Hosten und Ausführen der HTML-App

Der letzte Schritt dieses Lernprogramms besteht im Hosten und Ausführen der neuen App auf dem lokalen Computer.

1.  Navigieren Sie zum Speicherort der komprimierten Projektdateien, erweitern Sie die Dateien auf Ihrem Computer, und starten Sie eine der folgenden Befehlsdateien im Unterordner **server**.

    -   **launch-windows** (Windows-Computer)
    -   **launch-mac.command** (Mac OS X-Computer)
    -   **launch-linux.sh** (Linux-Computer)

    <div class="dev-callout"><b>Hinweis</b>
    <p>Bei einem Windows-Computer geben Sie &quot;R&quot; ein, wenn Sie von PowerShell zur Best&auml;tigung aufgefordert werden, dass Sie das Skript ausf&uuml;hren m&ouml;chten. Ihr Webbrowser r&auml;t Ihnen unter Umst&auml;nden von einer Ausf&uuml;hrung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall m&uuml;ssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortf&auml;hrt.</p>
</div>

    Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

2.  Öffnen Sie die URL <http://localhost:8000/> in einem Webbrowser, um die App zu starten.

3.  Geben Sie in der App unter **Neue Aufgabe eingeben** eine Beschreibung ein, wie zum Beispiel *Lernprogramm ausführen* und klicken Sie anschließend auf **Hinzufügen**.

    ![][4]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Sie k&ouml;nnen den Code, der zur Abfrage und zum Einf&uuml;gen von Daten auf Ihren mobilen Dienst zugreift, in der Datei app.js finden und pr&uuml;fen.</p> 
</div>

4.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][5]

    Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![][6]

## <a name="next-steps"> </a>Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   **[Erste Schritte mit Daten][Erste Schritte mit Daten]**
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   **[Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung][Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung]**
    Verbinden Sie Ihre HTML-Anwendung mit einer benutzerdefinierten API, die von Mobile Services gehostet wird.

-   **[Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]**
    Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   **[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz][Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]**
    Lernen Sie mehr über die Verwendung von Mobile Services mit HTML/JavaScript.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Lernprogramm ansehen]: http://go.microsoft.com/fwlink/?LinkId=287040
  []: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [Microsoft-Webplattform-Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [neueste Version von Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [Cross-Origin Resource Sharing (CORS)]: http://msdn.microsoft.com/de-de/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
  [Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung]: /de-de/documentation/articles/mobile-services-html-call-custom-api
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html
  [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client
