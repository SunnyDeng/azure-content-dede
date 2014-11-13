<properties pageTitle="Erste Schritte mit Azure Mobile Services f&uuml;r HTML 5-Apps" metaKeywords="" description="In diesem Lernprogramm erhalten Sie Informationen zu den ersten Schritten mit Azure Mobile Services f&uuml;r die HTML-Entwicklung. " metaCanonical="" services="" documentationCenter="Mobile" title="Erste Schritte mit Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">

<div class="dev-onpage-left-content">

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer HTML-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

</div>

<div class="dev-onpage-video-wrapper">

[Lernprogramm ansehen][Lernprogramm ansehen] [<span class="icon">Video abspielen</span>][Lernprogramm ansehen] <span class="time">03:51:00</span>

</div>

</div>

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für HTML-Apps.

<div class="dev-callout">

**Hinweis**
Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

</div>

### Weitere Anforderungen

-   Für dieses Lernprogramm muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

    -   **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft-Webplattform-Installer][Microsoft-Webplattform-Installer] installiert.
    -   **Unter MacOS X**: Python, das bereits installiert sein sollte.
    -   **Unter Linux**: Python. Die [neueste Version von Python][neueste Version von Python] muss installiert werden.

    Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.

-   Ein Webbrowser, der HTML5 unterstützt.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

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

    <div class="dev-callout">

    **Hinweis**
    Wenn Sie die Schnellstart-App auf einem anderen Webserver als "localhost" bereitstellen, müssen Sie den Hostnamen des Webservers der Liste **Anforderungen von Hostnamen zulassen** hinzufügen. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing (CORS)][Cross-Origin Resource Sharing (CORS)].

    </div>

## Hosten und Ausführen der HTML-App

Der letzte Schritt dieses Lernprogramms besteht im Hosten und Ausführen der neuen App auf dem lokalen Computer.

1.  Navigieren Sie zum Speicherort der komprimierten Projektdateien, erweitern Sie die Dateien auf Ihrem Computer, und starten Sie eine der folgenden Befehlsdateien im Unterordner **server**.

    -   **launch-windows** (Windows-Computer)
    -   **launch-mac.command** (Mac OS X-Computer)
    -   **launch-linux.sh** (Linux-Computer)

    <div class="dev-callout">

    **Hinweis**
    Bei einem Windows-Computer geben Sie "R" ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

    </div>

    Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

2.  Öffnen Sie die URL <http://localhost:8000/> in einem Webbrowser, um die App zu starten.

3.  Geben Sie in der App unter **Neue Aufgabe eingeben** eine Beschreibung ein, wie zum Beispiel *Lernprogramm ausführen* und klicken Sie anschließend auf **Hinzufügen**.

    ![][4]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

    <div class="dev-callout">

    **Hinweis**
    Sie können den Code, der zur Abfrage und zum Einfügen von Daten auf Ihren mobilen Dienst zugreift, in der Datei app.js finden und prüfen.

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

  [Lernprogramm ansehen]: http://go.microsoft.com/fwlink/?LinkId=287040
  [0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [Microsoft-Webplattform-Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [neueste Version von Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
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
