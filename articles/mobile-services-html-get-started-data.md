<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in HTML-Apps. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

> [WACOM.NOTE]Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer HTML-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des HTML-App-Projekts][Herunterladen des HTML-App-Projekts]
2.  [Erstellen des mobilen Dienstes][Erstellen des mobilen Dienstes]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

### Weitere Anforderungen

Sie können die GetStartedWithData-App auf jedem beliebigen Webserver hosten. Wir haben jedoch praktische Skripts bereitgestellt, mit denen Sie die App auf `http://localhost:8000` ausführen können.

-   Um localhost für dieses Lernprogramm verwenden zu können, muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

    -   **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft-Webplattform-Installer] installiert.
    -   **Unter MacOS X**: Python, das bereits installiert sein sollte.
    -   **Unter Linux**: Python. Sie müssen die [neueste Version von Python] installieren.

    Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.

-   Ein Webbrowser, der HTML5 unterstützt.

## <a name="download-app"></a><span class="short-header">Herunterladen des Projekts</span>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm verwendet die HTML5-App [GetStartedWithData][GetStartedWithData]. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  [Download der HTML-App-Projektdateien][GetStartedWithData].

2.  Öffnen Sie das heruntergeladene Projekt in einem HTML-Editor und sehen Sie sich die Datei app.js an.

    Beachten Sie, dass hinzugefügte Elemente in einem speicherinternen **Array**-Objekt (**staticItems**) gespeichert werden. Aktualisieren Sie die Seite, und die Daten verschwinden. Die Daten werden nicht persistent gespeichert.

3.  Starten Sie eine der folgenden Befehlsdateien aus dem **server**-Unterordner.

    -   **launch-windows** (Windows-Computer)
    -   **launch-mac.command** (Mac OS X-Computer)
    -   **launch-linux.sh** (Linux-Computer)

    <div class="dev-callout"><b>Hinweis</b>
    <p>Bei einem Windows-Computer geben Sie &quot;R&quot; ein, wenn Sie von PowerShell zur Best&auml;tigung aufgefordert werden, dass Sie das Skript ausf&uuml;hren m&ouml;chten. Ihr Webbrowser r&auml;t Ihnen unter Umst&auml;nden von einer Ausf&uuml;hrung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall m&uuml;ssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortf&auml;hrt.</p>
</div>

    Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

4.  Öffnen Sie die URL <http://localhost:8000/> in einem Webbrowser, um die App zu starten.

5.  Geben Sie in der App unter **Neue Aufgabe eingeben** eine Beschreibung ein, wie zum Beispiel *Lernprogramm ausführen* und klicken Sie anschließend auf **Hinzufügen**.

    ![][]

      Beachten Sie, dass der gespeicherte Text dem **staticItems**-Array hinzugefügt wird. Anschließend wird die Seite aktualisiert, um das neue Element anzuzeigen.

## <a name="create-service"></a><span class="short-header">Erstellen eines mobilen Dienstes</span>Erstellen eines neuen mobilen Dienstes im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">Erstellen einer neuen Tabelle</span>Hinzufügen einer neuen Tabelle zum mobilen Dienst

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

    ![][1]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Geben Sie unter **Tabellenname** den Namen *TodoItem* ein und klicken Sie auf das Kontrollkästchen.

    ![][2]

    Dadurch wird die neue Speichertabelle **TodoItem** mit Standardberechtigungen erstellt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das f&uuml;r einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.</p> 
</div>

4.  Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5.  Klicken Sie auf die Registerkarte **Spalten**. Überprüfen Sie, dass folgende Standardspalten automatisch erstellt werden:

    | Spaltenname   | Typ               | Index                              |
    |---------------|-------------------|------------------------------------|
    | id            | string            | Indiziert                          |
    | \_\_createdAt | date              | Indiziert                          |
    | \_\_updatedAt | date              | <font color="transparent">-</font> |
    | \_\_version   | timestamp (MSSQL) | <font color="transparent">-</font> |

    Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn f&uuml;r Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte &uuml;ber einen Einf&uuml;ge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.</p>
</div>

6.  Vergewissern Sie sich auf der Registerkarte **Konfigurieren**, dass `localhost` bereits im Feld **Anforderungen von Hostnamen zulassen** unter **cross-origin resource sharing (CORS)** eingetragen ist. Falls nicht, geben Sie `localhost` im Feld **Hostname** ein, und klicken Sie dann auf **Speichern**.

    ![][3]

    <div class="dev-callout"><b>Hinweis</b>
    <p>Wenn Sie die Schnellstart-App auf einem anderen Webserver als &quot;localhost&quot; bereitstellen, m&uuml;ssen Sie den Hostnamen des Webservers der Liste <strong>Anforderungen von Hostnamen zulassen</strong> hinzuf&uuml;gen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn155871.aspx" target="_blank">Cross-Origin Resource Sharing (CORS)</a>.</p>
</div>

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

## <a name="update-app"></a><span class="short-header">Aktualisieren der App</span>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

    ![][4]

    Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

3.  Öffnen Sie die Projektdatei index.html in Ihrem Web-Editor und fügen Sie den folgenden Code zu den Skriptverweisen der Seite hinzu:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  Öffnen Sie die Datei app.js im Editor und fügen Sie den folgenden Code hinzu, um die **MobileServiceClient**-Variable zu definieren und übergeben Sie Anwendungs-URL und -Schlüssel in dieser Reihenfolge an den **MobileServiceClient**-Konstruktor:

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    Dieser Code erstellt eine neue MobileServiceClient-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird.

5.  Entfernen Sie die Kommentare in den folgenden Codezeilen:

        var itemCount = 0;
        var staticItems = [];

    Die Daten werden im mobilen Dienst gespeichert, und nicht im speicherinternen Array.

6.  Entfernen Sie die Kommentare in den folgenden Codezeilen:

        todoItemTable = client.getTable('todoitem');

    Dieser Code erstellt ein Proxyobjekt (**todoItemTable**) für die SQL-Datenbank **TodoItem**.

7.  Ersetzen Sie den **$('\#add-item').submit**-Ereignishandler durch den folgenden Code:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems);
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

    Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

8.  Ersetzen Sie die **refreshTodoItems**-Methode durch den folgenden Code:

        function refreshTodoItems() {

            var query = todoItemTable;

            query.read().then(function(todoItems) {
                listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
                        .append($('<div class="dev-callout"><b>Hinweis</b>
<p>Wiederholen Sie die Schritte im ersten Abschnitt, falls Sie den Webserver neu starten m&uuml;ssen.</p>
</div>

2.  Geben Sie wie zuvor Text in das Feld **Neue Aufgabe eingeben** ein und klicken Sie auf **Hinzufügen**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][5]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5.  Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

    Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6.  Suchen Sie die **RefreshTodoItems**-Methodein in der Projektdatei "app.js", und ersetzen Sie die Codezeile, in der `query` definiert wird, durch die folgende Zeile:

        var query = todoItemTable.where({ complete: false });

7.  Laden Sie die Seite erneut und markieren Sie eines der anderen Elemente in der Liste.

    Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Änderung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen für die Integration von Daten in HTML-Apps in Mobile Services behandelt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Nach Abschluss der Daten-Lernprogramme können Sie herausfinden, wie Sie Benutzer Ihrer App authentifizieren können. Probieren Sie eines der anderen Lernprogramme aus, z. B. [Erste Schritte mit Authentifizierung][Erste Schritte mit Authentifizierung].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
  [Herunterladen des HTML-App-Projekts]: #download-app
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F
  [GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345
  []: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [Cross-Origin Resource Sharing (CORS)]: http://msdn.microsoft.com/de-de/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-html
  [Erste Schritte mit Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html
