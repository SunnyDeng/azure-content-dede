<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Erste Schritte mit Daten in Mobile Services (universelle Windows-App)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/de-de/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer universellen Windows 8.1-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio-Projekt für eine universelle Windows-App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

> [WACOM.NOTE] Für dieses Lernprogramm ist Visual Studio 2013 Update 2 erforderlich.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts][Herunterladen des Windows Store-App-Projekts]
2.  [Erstellen des mobilen Diensts in Visual Studio][Erstellen des mobilen Diensts in Visual Studio]
3.  [Hinzufügen einer Datentabelle zum Speichern und Aktualisieren der App][Hinzufügen einer Datentabelle zum Speichern und Aktualisieren der App]
4.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

## <a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm basiert auf der [GetStartedWithMobileServices-App][GetStartedWithMobileServices-App], bei der es sich um ein Windows Store-App-Projekt in Visual Studio 2013 handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche gleichen sich, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die C#-Version der GetStartedWithMobileServices-Beispiel-App von der [Website mit den Codebeispielen für Entwickler][GetStartedWithMobileServices-App] herunter.

    ![][0]

2.  Öffnen Sie in Visual Studio 2013 das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

    Beachten Sie, dass die hinzugefügten **TodoItem**-Elemente in einem speicherinternen **ObservableCollection\<TodoItem\>**-Element gespeichert werden.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

    ![][1]

    Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

## <a name="create-service"></a><span class="short-header">Erstellen des mobilen Diensts</span>Erstellen eines mobilen Diensts in Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  Öffnen Sie im Projektmappen-Explorer die Codedatei "App.xaml.cs". Der \*\*App\*\*-Klasse wurde ein neues statisches Feld hinzugefügt, das dem im folgenden Beispiel entspricht:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über eine Instanz der [MobileServiceClient-Klasse][MobileServiceClient-Klasse] bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Dieses statische Feld steht allen Seiten in Ihrer App zur Verfügung.

## <a name="add-table"></a><span class="short-header">Hinzufügen einer neuen Tabelle</span>Hinzufügen einer neuen Tabelle zu dem mobilen Dienst und Aktualisieren der App

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  Fügen Sie in der Datei "MainPage.xaml.cs" die folgenden "using"-Anweisungen hinzu, oder heben Sie deren Auskommentierung auf:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  Ersetzen Sie in derselben Datei die "TodoItem"-Klassendefinition durch den folgenden Code:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    Mit dem **JsonPropertyAttribute**-Attribut wird die Zuordnung von Eigenschaftsnamen im Clienttyp zu Spaltennamen in der zugrunde liegenden Datentabelle definiert.

3.  Kommentieren Sie die Zeile mit der Definition der vorhandenen Eintragssammlung aus, heben Sie dann die Auskommentierung der folgenden Zeilen auf, oder fügen Sie sie hinzu, und ersetzen Sie *\<yourClient\>* durch das `MobileServiceClient`-Feld, das der Datei „App.xaml.cs“ beim Verbinden Ihres Projekts mit dem mobilen Dienst hinzugefügt wurde:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    Dieser Code erstellt eine mobile dienstunterstützende Bindungssammlung (items) und eine Proxyklasse für die Datenbanktabelle (todoTable).

4.  Entfernen Sie aus der **InsertTodoItem**-Methode die Codezeile, in der die **TodoItem.Id**-Eigenschaft festgelegt wird, fügen Sie der Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

        await todoTable.InsertAsync(todoItem);

    Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

    <div class="dev-callout"><strong>Hinweis</strong><p>Neue Tabellen werden mit den Spalten &quot;Id&quot;, &quot;__createdAt&quot;, &quot;__updatedAt&quot; und &quot;__version&quot; erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einf&uuml;ge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj193175.aspx">Dynamisches Schema</a>.</p></div>

5.  Fügen Sie der **RefreshTodoItems**-Methode den **async**-Modifizierer hinzu, und heben Sie dann die Auskommentierung der folgenden Codezeile auf:

        items = await todoTable.ToCollectionAsync();

    Damit wird die Bindung an die Sammlung der Einträge in `todoTable` festgelegt, die alle vom mobilen Dienst zurückgegebenen **TodoItem**-Objekte enthält.

6.  Fügen Sie der **UpdateCheckedTodoItem**-Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

        await todoTable.UpdateAsync(item);

    Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen der App mit Ihrem neuen mobilen Dienst

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][2]

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
  [Erstellen des mobilen Diensts in Visual Studio]: #create-service
  [Hinzufügen einer Datentabelle zum Speichern und Aktualisieren der App]: #add-table
  [Testen der App mit Mobile Services]: #test-app
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28
  [GetStartedWithMobileServices-App]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [Erste Schritte mit Pushbenachrichtigungen]: ../mobile-services-windows-store-dotnet-get-started-push/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
