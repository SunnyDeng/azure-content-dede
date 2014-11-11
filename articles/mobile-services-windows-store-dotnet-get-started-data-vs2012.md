<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen k&ouml;nnen. In diesem Lernprogramm laden Sie ein Visual Studio 2012-Projekt f&uuml;r eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Daten&auml;nderungen beim Ausf&uuml;hren der App anzuzeigen. Sie k&ouml;nnen sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">15:33:00</span></div>

</div>

<div class="dev-callout"><b>Hinweis</b>
<p>Durch dieses Lernprogramm werden Mobile Services-Funktionen einer in Visual Studio 2012 erstellten Windows Store-App hinzugef&uuml;gt. Visual Studio 2013 umfasst neue Funktionen, die das Verkn&uuml;pfen Ihrer Windows Store-App mit Mobile Services vereinfachen. Weitere Informationen finden Sie unter <a href="/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/">Erste Schritte mit Daten in Mobile Services</a>.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts][Herunterladen des Windows Store-App-Projekts]
2.  [Erstellen des mobilen Dienstes][Erstellen des mobilen Dienstes]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Herunterladen des Projekts</span>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData][GetStartedWithData]-App auf, bei der es sich um eine Windows Store-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die C#-Version der GetStartedWithData-Beispiel-App von der Website mit den [Codebeispielen für Entwickler][GetStartedWithData] herunter.

    ![][0]

2.  Öffnen Sie das heruntergeladene Projekt in Visual Studio 2012 Express für Windows 8, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

    Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection\<TodoItem\>**-Element gespeichert werden.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

    ![][1]

    Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

## <a name="create-service"></a><span class="short-header">Erstellen eines mobilen Dienstes</span>Erstellen eines neuen mobilen Dienstes im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Erstellen einer neuen Tabelle</span>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Aktualisieren der App</span>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online** und **Include Prerelease** aus, suchen Sie nach `WindowsAzure.MobileServices`, klicken Sie auf **Installieren** im Paket **Azure Mobile Services**, und stimmen Sie den Lizenzbedingungen zu.

    ![][2]

    Dadurch wird die Mobile Services-Clientbibliothek zum Projekt hinzugefügt.

3.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

4.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

    ![][3]

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

2.  Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][4]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5.  Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

    Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6.  Ersetzen Sie in der Projektdatei "MainPage.xaml.cs" die vorhandene **RefreshTodoItems**-Methode durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Refresh**.

    Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.



  [Herunterladen des Windows Store-App-Projekts]: #download-app
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
