<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="User server scripts to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts
=============================================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#") [Windows Store JavaScript](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript") [Windows Phone](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone") [iOS](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")[.NET backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET backend") | [JavaScript backend](de-de/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript backend")

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Windows Store-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services) [Video abspielen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services) 09:54

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen](#string-length-validation)
2.  [Client zur Unterstützung der Überprüfung aktualisieren](#update-client-validation)
3.  [Zeitstempel beim Einfügen hinzufügen](#add-timestamp)
4.  [Client zum Anzeigen des Zeitstempels aktualisieren](#update-client-timestamp)

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet) abschließen.

Überprüfung hinzufügen
----------------------

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Daten**, dann auf die Tabelle **TodoItem**.

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

    Das Skript überprüft die Länge der Eigenschaft **TodoItem.text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **Ausführen** aufgerufen, um das Einfügen abzuschließen.

    **Hinweis**

    Sie können ein registriertes Skript auf der Registerkarte **Skript** entfernen, indem Sie auf **Löschen** und dann auf **Speichern** klicken.

Den Client aktualisieren
------------------------

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet) abgeschlossen haben.

2.  Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit mehr als zehn Zeichen in **Insert a TodoItem** ein, und klicken Sie auf **Speichern**.

  Beachten Sie, dass die App eine unbehandelte **MobileServiceInvalidOperationException** als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.  

3.  Öffnen Sie die Datei "MainPage.xaml.cs", und fügen Sie die folgende **using**-Anweisung ein:

        using Windows.UI.Popups;

4.  Ersetzen Sie die vorhandene Methode **InsertTodoItem** durch:

         private async void InsertTodoItem(TodoItem todoItem)
         {
             // This code inserts a new TodoItem into the database.
             // When the operation completes and Mobile Services has
             // assigned an Id, the item is added to the collection.
             try
             {
                 await todoTable.InsertAsync(todoItem);
                 items.Add(todoItem);
             }
             catch (MobileServiceInvalidOperationException e)
             {
                 MessageDialog errormsg = new MessageDialog(e.Message, 
                     string.Format("{0} (HTTP {1})",                     
                     e.Response.ReasonPhrase,
                     (int)e.Response.StatusCode));
                 var ignoreAsyncOpResult = errormsg.ShowAsync();
             }
         }

   This version of the method includes error handling for the **MobileServiceInvalidOperationException** that displays the error response in a popup.

Hinzufügen eines Zeitstempels
-----------------------------

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

**Hinweis**

Die Zeitstempeleigenschaft **createdAt**, die hier vorgeführt wird, ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft **\_\_createdAt** für jede Tabelle. Sie könnten diese Systemeigenschaft in Ihrer Anwendung verwenden, indem Sie einfach das folgende Mitglied zur TodoItem-Klasse hinzufügen:

``` {}
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
```

1.  Ersetzen Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf der Registerkarte **Scripts** das aktuelle Skript **Einfügen** mit der folgenden Funktion, und klicken Sie dann auf **Speichern**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft **createdAt** zu dem Objekt hinzufügt, bevor es durch den **request**.**execute**-Aufruf eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services automatisch die Spalte **createdAt** in die Tabelle **TodoItem** bei der ersten Ausführung ein. Für einen neuen mobilen Dienst ist das dynamische Schema standardmäßig aktiviert, und es sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.

2.  Drücken Sie in Visual Studio die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit weniger als zehn Zeichen in **Insert a TodoItem** ein, und klicken Sie auf **Speichern**.

    Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Durchsuchen** in der Tabelle **TodoItem**.

    Beachten Sie, dass es hier nun ein Spalte **createdAt** gibt und das neu eingefügte Element über einen Zeitstempelwert verfügt.

Als Nächstes müssen Sie die Windows Store-App aktualisieren, um diese neue Spalte anzuzeigen.

Den Client erneut aktualisieren
-------------------------------

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1.  Öffnen Sie in Visual Studio die Datei "MainPage.xaml.cs", und ersetzen Sie dann die vorhandene Klasse **TodoItem** mit der folgenden Definition:

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
            
             [JsonProperty(PropertyName = "createdAt")]
             public DateTime
         CreatedAt { get; set; }
         }

    Diese neue Klassendefinition umfasst die neue Zeitstempeleigenschaft als nullbarer DateTime-Typ.

    **Hinweis**

    Das `DataMemberAttribute` weist den Client an, die neue Eigenschaft `CreatedAt` in der App zu der in der TodoItem-Tabelle definierten Spalte `createdAt` zuzuordnen, die über eine andere Schreibweise verfügt. Durch die Verwendung dieses Attributs kann Ihre App Eigenschaftennamen zu Objekten haben, die von den Spaltennamen in der SQL-Datenbank abweichen. Ohne dieses Attribut tritt aufgrund der unterschiedlichen Schreibweisen ein Fehler auf.

2.  Fügen Sie das folgende XAML-Element direkt unter dem Element **CheckBoxComplete** in der Datei "MainPage.xaml" ein:

         <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    This displays the new **CreatedAt** property in a text box. 

3.  Drücken Sie die Taste **F5**, um die App auszuführen.

    Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

4.  Ersetzen Sie die vorhandene Methode **RefreshTodoItems** mit dem folgenden Code:

         private async void RefreshTodoItems()
         {
             // This query filters out completed TodoItems and 
             // items without a timestamp. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false
                    && todoItem.CreatedAt != null)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;
         }

    Diese Methode aktualisiert die Abfrage, damit auch Elemente herausgefiltert werden, die nicht über einen Zeitstempelwert verfügen.

5.  Drücken Sie die Taste **F5**, um die App auszuführen.

    Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mittels Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet).

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Autorisieren von Benutzern mit Skripts](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-dotnet)
    
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library)
    
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


