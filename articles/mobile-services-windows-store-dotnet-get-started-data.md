<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Daten in Mobile Services
===========================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[.NET-Back-End](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ ".NET-Back-End") | [JavaScript-Back-End](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/ "JavaScript-Back-End")

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

**Hinweis**

Für dieses Lernprogramm benötigen Sie Visual Studio 2013, mit dem eine Verbindung Ihrer Windows Store-App mit Mobile Services einfacher hergestellt werden kann. Um dasselbe grundlegende Verfahren mit Visual Studio 2012 auszuführen, befolgen Sie die Schritte im Thema [Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

1.  [Herunterladen des Windows Store-App-Projekts](#download-app)
2.  [Erstellen des mobilen Diensts in Visual Studio](#create-service)
3.  [Hinzufügen einer Datentabelle zum Speichern und Aktualisieren der App](#add-table)
4.  [Testen der App mit Mobile Services](#test-app)

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Windows Azure-Testkonto](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28).

Herunterladen des ProjektsHerunterladen des GetStartedWithData-Projekts
-----------------------------------------------------------------------

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App](http://go.microsoft.com/fwlink/p/?LinkId=328660) auf. Dabei handelt es sich um ein Windows Store-App-Projekt in Visual Studio 2013. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die C\#-Version der GetStartedWithMobileServices-Beispiel-App von der [Website mit den Codebeispielen für Entwickler](http://go.microsoft.com/fwlink/p/?LinkId=328660) herunter.

        ![][10]

2.  Öffnen Sie in Visual Studio 2013 das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

        Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection<TodoItem>**-Element gespeichert werden.

3.  Drücken Sie die Taste **F5**, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

        ![][0]  

        Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

Erstellen des mobilen DienstsErstellen eines mobilen Diensts in Visual Studio
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  Öffnen Sie im Projektmappen-Explorer die Codedatei "App.xaml.cs". Der \*\*App\*\*-Klasse wurde ein neues statisches Feld hinzugefügt, das dem im folgenden Beispiel entspricht:

    ``` {data-morhtml="true"}
    public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
            
    ```

    In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über eine Instanz der [MobileServiceClient-Klasse](http://go.microsoft.com/fwlink/p/?LinkId=302030) bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Dieses statische Feld steht allen Seiten in Ihrer App zur Verfügung.

Hinzufügen einer neuen TabelleHinzufügen einer neuen Tabelle zu dem mobilen Dienst und Aktualisieren der App
------------------------------------------------------------------------------------------------------------

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

3.  Kommentieren Sie die Zeile mit der Definition der vorhandenen Eintragssammlung aus, heben Sie dann die Auskommentierung der folgenden Zeilen auf, oder fügen Sie sie hinzu, und ersetzen Sie *&lt;yourClient\>* durch das `MobileServiceClient`-Feld, das der Datei "App.xaml.cs" beim Verbinden Ihres Projekts mit dem mobilen Dienst hinzugefügt wurde:

         private MobileServiceCollection<TodoItem data-morhtml="true", TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.<yourClient>.GetTable<TodoItem>();

    Dieser Code erstellt eine mobile dienstunterstützende Bindungssammlung (items) und eine Proxyklasse für die Datenbanktabelle (todoTable).

4.  Entfernen Sie aus der **InsertTodoItem**-Methode die Codezeile, in der die **TodoItem.Id**-Eigenschaft festgelegt wird, fügen Sie der Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

         await todoTable.InsertAsync(todoItem);

    Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

    **Hinweis**

    Neue Tabellen werden mit den Spalten "Id", "\_\_createdAt", "\_\_updatedAt" und "\_\_version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/de-de/library/windowsazure/jj193175.aspx).

5.  Fügen Sie der **RefreshTodoItems**-Methode den **async**-Modifizierer hinzu, und heben Sie dann die Auskommentierung der folgenden Codezeile auf:

         items = await todoTable.ToCollectionAsync();

    Damit wird die Bindung an die Sammlung der Einträge in `todoTable` festgelegt, die alle vom mobilen Dienst zurückgegebenen **TodoItem**-Objekte enthält.

6.  Fügen Sie der **UpdateCheckedTodoItem**-Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

         await todoTable.UpdateAsync(item);

    Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

Testen der AppTesten der App mit Ihrem neuen mobilen Dienst
-----------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

        Damit wird ein neuer Eintrag als Einfügung an den mobilen Dienst gesendet.

3.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

        ![][9]
          
        Die **TodoItem**-Tabelle enthält nun Daten, mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5.  Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

1.  Ersetzen Sie in der Projektdatei "MainPage.xaml.cs" die vorhandene **RefreshTodoItems**-Methode durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

         private async void RefreshTodoItems()
         {                       
             // Diese Abfrage filtert abgeschlossene TodoItems heraus. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

2.  Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Aktualisieren**.

        Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet)
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet)
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen](../mobile-services-windows-store-dotnet-get-started-push/)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library)
    Informationen zur Verwendung von Mobile Services mit .NET.


