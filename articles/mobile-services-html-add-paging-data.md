<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Verfeinern von Mobile Services-Abfragen mit Paging

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre HTML-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>Mobile Services verwendet standardm&auml;&szlig;ig eine Seitengr&ouml;&szlig;e von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu &uuml;berfluten. Durch Angabe der Seitengr&ouml;&szlig;e k&ouml;nnen Sie bis zu 1.000 Elemente in einer Antwort abfragen.</p>
</div>

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten][Erste Schritte mit Daten].

1.  Führen Sie eine der folgenden Befehlsdateien aus dem **server**-Unterordner des Projekts aus, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] abgeschlossen haben.

    -   **launch-windows** (Windows-Computer)
    -   **launch-mac.command** (Mac OS X-Computer)
    -   **launch-linux.sh** (Linux-Computer)

    <div class="dev-callout"><b>Hinweis</b>
    <p>Bei einem Windows-Computer geben Sie &quot;R&quot; ein, wenn Sie von PowerShell zur Best&auml;tigung aufgefordert werden, dass Sie das Skript ausf&uuml;hren m&ouml;chten. Ihr Webbrowser r&auml;t Ihnen unter Umst&auml;nden von einer Ausf&uuml;hrung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall m&uuml;ssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortf&auml;hrt.</p>
</div>

    Daraufhin wird ein Webserver auf Ihrem lokalen Computer zum Hosten der App gestartet.

2.  Öffnen Sie <http://localhost:8000/> in einem Webbrowser, geben Sie Text unter **Add new task** ein und klicken Sie auf **Add**.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Ersetzen Sie in der Datei "app.js" die Definition der `query`-Variablen in der **refreshTodoItems**-Methode durch den folgenden Code:

        var query = todoItemTable.where({ complete: false }).take(3);

    Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5.  Aktualisieren Sie die Seite in Ihrem Webbrowser.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  (Optional) Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler].

    Beachten Sie, dass die **take(3)**-Methode im Abfrage-URI in die Abfrageoption **$top=3** übersetzt wurde.

7.  Ersetzen Sie die Abfrage durch den folgenden Code:

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Aktualisieren Sie die Seite in Ihrem Webbrowser.

    Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    <div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm &uuml;bergibt zur Vereinfachung fest codierte Werte an die <strong>Take</strong>- und <strong>Skip</strong>-Methoden. Tats&auml;chliche Anwendungen k&ouml;nnen &auml;hnliche Abfragen mit einem Pagersteuerelement oder einer &auml;hnlichen Benutzersteuerung ausf&uuml;hren, um zur vorherigen bzw. n&auml;chsten Seite zu navigieren.  Alternativ k&ouml;nnen Sie die <strong>includeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
</div>

9.  (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an.

    Beachten Sie, dass die **skip(3)**-Methode im Abfrage-URI in die Abfrageoption **$skip=3** übersetzt wurde.

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Lernen Sie unter [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung], wie Sie Benutzer Ihrer App authentifizieren können. Weitere Informationen zur Verwendung von mobilen Diensten mit HTML/JavaScript finden Sie unter [Mobile Services HTML/JavaScript How-to Conceptual Reference][Mobile Services HTML/JavaScript How-to Conceptual Reference]



  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html
  [Mobile Services HTML/JavaScript How-to Conceptual Reference]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client
