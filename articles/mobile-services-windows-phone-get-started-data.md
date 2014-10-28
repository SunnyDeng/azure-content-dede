<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy][mobile-services-selector-get-started-data-legacy]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Phone 8-App nutzen k&ouml;nnen. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Daten&auml;nderungen beim Ausf&uuml;hren der App anzuzeigen.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>Hinweis</b>
<p>Anhand dieses Lernprogramms soll Ihnen ein besseres Verst&auml;ndnis &uuml;ber die Mobile Services dahingehend vermittelt werden, wie Sie mit deren Hilfe Azure verwenden k&ouml;nnen, um Daten in einer Windows Phone 8-App zu speichern und abzurufen. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart f&uuml;r mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-wp8">Erste Schritte mit Mobile Services</a> abschlie&szlig;en.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Phone 8-App-Projekts][Herunterladen des Windows Phone 8-App-Projekts]
2.  [Erstellen des mobilen Dienstes][Erstellen des mobilen Dienstes]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

Dieses Lernprogramm erfordert Visual Studio 2012 Express für Windows Phone 8 und das [Windows Phone 8 SDK][Windows Phone 8 SDK] unter Windows 8. Wenn Sie dieses Lernprogramm ausführen, um eine Windows Phone 8.1-App zu erstellen, müssen Sie Visual Studio 2013 Update 2 oder eine höhere Version verwenden.

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

## <a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData-App][GetStartedWithData-App] auf, bei der es sich um ein Windows Phone Silverlight 8-App-Projekt handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie das GetStartedWithData-App-Beispielprojekt von der Website mit [Codebeispielen für Entwickler][GetStartedWithData-App] herunter.

    > [WACOM.NOTE]Zum Erstellen einer Windows Phone Silverlght 8.1-App ändern Sie einfach das Zielbetriebssystem im heruntergeladenen Windows Phone Silverlight 8-App-Projekt in Windows Phone 8.1. Zum Erstellen einer Windows Phone Store-App laden Sie die [Windows Phone Store-App-Version][Windows Phone Store-App-Version] des App-Beispielprojekts GetStartedWithData herunter.

2.  Öffnen Sie in Visual Studio das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

    Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection\<TodoItem\>**-Element gespeichert werden.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **Speichern**.

    ![][]

    Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

## <a name="create-service"></a><span class="short-header">Erstellen eines mobilen Dienstes</span>Erstellen eines neuen mobilen Dienstes im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">Erstellen einer neuen Tabelle</span>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][mobile-services-create-new-service-data-2]]

## <a name="update-app"></a><span class="short-header">Aktualisieren der App</span>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online**, suchen Sie nach `WindowsAzure.MobileServices`, klicken Sie auf **Installieren** für das Paket **Azure Mobile Services**, und akzeptieren Sie die Lizenzbedingungen.

    ![][1]

    Dadurch wird die Mobile Services-Clientbibliothek zum Projekt hinzugefügt.

3.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

4.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

    ![][2]

    Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

5.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs". Fügen Sie die folgende `using`-Anweisung hinzu, oder heben Sie deren Auskommentierung auf:

        using Microsoft.WindowsAzure.MobileServices;

6.  Heben Sie in derselben Datei die Auskommentierung für den Code auf, der die Variable **MobileService** festlegt, und stellen Sie die URL und den Anwendungsschlüssel aus dem mobilen Dienst im **MobileServiceClient**-Konstruktor bereit. Beachten Sie hierbei die angegebene Reihenfolge.

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    Dieser Code erstellt eine neue **MobileServiceClient**-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird.

7.  Fügen Sie in der Datei "MainPage.xaml.cs" die folgenden `using`-Anweisungen hinzu, oder heben Sie deren Auskommentierung auf:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  Ersetzen Sie in derselben Datei die **TodoItem**-Klassendefinition durch den folgenden Code:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  Kommentieren Sie die Zeile aus, welche die bestehende **items**-Sammlung festlegt. Heben Sie anschließend die Auskommentierung für die folgenden Zeilen auf:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    Dieser Code erstellt eine Bindungssammlung (**items**), die Mobile Services unterstützt, und eine Proxyklasse für die SQL-Datenbanktabelle **TodoItem** (**todoTable**).

10. Entfernen Sie aus der **InsertTodoItem**-Methode die Codezeile, in der die **TodoItem**.**Id**-Eigenschaft festgelegt wird. Fügen Sie der Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

        await todoTable.InsertAsync(todoItem);

    Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

11. Fügen Sie der **RefreshTodoItems**-Methode den **async**-Modifizierer hinzu, und heben Sie dann die Auskommentierung der folgenden Codezeile auf:

        items = await todoTable.ToCollectionAsync();

    Damit wird die Bindung an die Sammlung der Einträge in der todoTable festgelegt, die alle vom mobilen Dienst zurückgegebenen TodoItem-Objekte enthält.

12. Fügen Sie der **UpdateCheckedTodoItem**-Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

         await todoTable.UpdateAsync(item);

    Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen der App mit Ihrem neuen mobilen Dienst

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **Speichern**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][3]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Windows Phone 8 beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Phone 8-App für die Arbeit mit Daten in Mobile Services aufgezeigt. Als Nächstes können Sie das folgende Lernprogramm ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Sobald Sie die Datenreihe abgeschlossen haben, können Sie sich auch an einem der folgenden Windows Phone 8-Lernprogramme versuchen:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [Lernprogramm ansehen]: http://go.microsoft.com/fwlink/?LinkID=298628
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-wp8
  [Herunterladen des Windows Phone 8-App-Projekts]: #download-app
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [GetStartedWithData-App]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [Windows Phone Store-App-Version]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  []: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-wp8
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-wp8
