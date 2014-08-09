<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012
==================================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2012-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen. Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) [Video abspielen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services)15:33

**Hinweis**

Dieses Lernprogramm erweitert eine in Visual Studio 2012 erstellte Windows Store-App um Mobile Services-Funktionen. Visual Studio 2013 enthält neue Features, die eine einfache Verbindung Ihrer Windows Store-App mit Mobile Services ermöglichen. Weitere Informationen finden Sie unter [Erste Schritte mit Daten in Mobile Services](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

1.  [Herunterladen des Windows Store-App-Projekts](#download-app)
2.  [Erstellen des mobilen Diensts](#create-service)
3.  [Erstellen einer Datentabelle als Datenspeicher](#add-table)
4.  [Aktualisieren der App zur Verwendung von mobilen Diensten](#update-app)
5.  [Testen der App mit Mobile Services](#test-app)

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28).

Herunterladen des ProjektsHerunterladen des GetStartedWithData-Projekts
-----------------------------------------------------------------------

Dieses Lernprogramm baut auf der [GetStartedWithData](http://go.microsoft.com/fwlink/?LinkId=262308)-App auf, bei der es sich um eine Windows Store-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die C\#-Version der GetStartedWithData-Beispiel-App von der Website mit den [Codebeispielen für Entwickler](http://go.microsoft.com/fwlink/?LinkId=262308) herunter.

   	![][10]

2.  Öffnen Sie das heruntergeladene Projekt in Visual Studio 2012 Express für Windows 8, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection<TodoItem>**-Element gespeichert werden.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

   	![][0]  

   	Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

Erstellen des mobilen DienstesErstellen eines neuen mobilen Dienstes im Verwaltungsportal
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Erstellen einer neuen TabelleFügen Sie eine neue Tabelle zum mobilen Dienst hinzu
---------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Aktualisieren der AppAktualisieren der App für den Datenzugriff über mobile Dienste
-----------------------------------------------------------------------------------

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass diese Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online** und **Include Prerelease** aus, suchen Sie nach `WindowsAzure.MobileServices`, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie dem Lizenzvertrag zu.

   	![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png)

   	Dadurch wird die Mobile Services-Clientbibliothek zum Projekt hinzugefügt.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Dienste** und klicken Sie auf den zuvor erstellten mobilen Dienst.

2.  Klicken Sie die Registerkarte **Dashboard** und notieren Sie die **Site-URL**. Klicken Sie anschließend auf **Schlüssel verwalten** und notieren Sie den **Anwendungsschlüssel**.

   	![][8]

   	Sie brauchen diese Werte für den Zugriff auf den mobilen Dienst in Ihrem App-Code.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs". Fügen Sie die folgende `using`-Anweisung hinzu bzw. entfernen Sie den entsprechenden Kommentar:

         using Microsoft.WindowsAzure.MobileServices;

2.  Heben Sie in derselben Datei die Auskommentierung für den Code auf, der die Variable **MobileService** festlegt, und stellen Sie die URL und den Anwendungsschlüssel aus dem mobilen Dienst im **MobileServiceClient**-Konstruktor bereit. Beachten Sie hierbei die angegebene Reihenfolge.

         //public static MobileServiceClient MobileService = new MobileServiceClient( 
         //    "AppUrl", 
         //    "AppKey" 
         //); 

   	Dieser Code erstellt eine neue **MobileServiceClient**-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird.

1.  Fügen Sie in der Datei "MainPage.xaml.cs" die folgenden `using`-Anweisungen hinzu, oder heben Sie deren Auskommentierung auf:

         using Microsoft.WindowsAzure.MobileServices;
         using Newtonsoft.Json;

2.  Ersetzen Sie in derselben Datei die **TodoItem**-Klassendefinition durch den folgenden Code:

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
         }

3.  Kommentieren Sie die Zeile aus, welche die bestehende **items**-Sammlung festlegt. Heben Sie anschließend die Auskommentierung für die folgenden Zeilen auf:

         private MobileServiceCollection<TodoItem, TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.MobileService.GetTable<TodoItem>();

   	Dieser Code erstellt eine mobile dienstunterstützende Bindungssammlung (**items**) und eine Proxyklasse für die SQL-Datenbanktabelle **TodoItem** (**todoTable**). 

4.  Entfernen Sie aus der **InsertTodoItem**-Methode die Codezeile, in der die **TodoItem**.**Id**-Eigenschaft festgelegt wird. Fügen Sie der Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

         await todoTable.InsertAsync(todoItem);

   	Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

1.  Fügen Sie der **RefreshTodoItems**-Methode den **async**-Modifizierer hinzu, und heben Sie dann die Auskommentierung der folgenden Codezeile auf:

         items = await todoTable.ToCollectionAsync();

   	Damit wird die Bindung an die Sammlung der Einträge in der todoTable festgelegt, die alle vom mobilen Dienst zurückgegebenen TodoItem-Objekte enthält. 

2.  Fügen Sie der **UpdateCheckedTodoItem**-Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

          await todoTable.UpdateAsync(item);

   	Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

Testen der AppTesten der App mit Ihrem neuen mobilen Dienst
-----------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

   	Dieser Code schickt ein neues einzufügendes Element an den mobilen Dienst.

3.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
          
   	 Die **TodoItem**-Tabelle enthält nun Daten, deren ID-Werte vom mobilen Dienst generiert wurden, und die entsprechenden Spalten für die TodoItem-Klasse in der App wurden automatisch in der Tabelle erstellt.

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

2.  Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Refresh**.

   	Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
  <br/>Informationen zur Verwendung von Serverskripts in mobilen Diensten, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet)
  <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet)
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)
  <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library)
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png




[7]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png
