<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Phone 8 app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts
=============================================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#") [Windows Store JavaScript](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript") [Windows Phone](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone") [iOS](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ ".NET-Backend") | [JavaScript-Backend](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Windows Phone 8-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

[Lernprogramm ansehen](http://go.microsoft.com/fwlink/?LinkId=298629) [Video abspielen](http://go.microsoft.com/fwlink/?LinkId=298629)11:36

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen](#string-length-validation)
2.  [Client zur Unterstützung der Überprüfung aktualisieren](#update-client-validation)
3.  [Zeitstempel beim Einfügen hinzufügen](#add-timestamp)
4.  [Client zum Anzeigen des Zeitstempels aktualisieren](#update-client-timestamp)

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-wp8). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-wp8) abschließen.

Überprüfung hinzufügen
----------------------

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

    Das Skript überprüft die Länge der Eigenschaft **TodoItem.text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

    **Hinweis**

    Sie können ein registriertes Skript auf der Registerkarte **Script** entfernen, indem Sie auf **Clear** und dann auf **Save** klicken.

Aktualisieren des Clients
-------------------------

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1.  Öffnen Sie in Visual Studio 2012 Express für Windows Phone das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-wp8) abgeschlossen haben.

2.  Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit mehr als zehn Zeichen in das Textfeld ein, und klicken Sie auf **Save**.

     Beachten Sie, dass die App eine unbehandelt **MobileServiceInvalidOperationException** als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.

3.  Öffnen Sie die Datei "MainPage.xaml.cs", und ersetzen Sie die vorhandene **InsertTodoItem**-Methode durch den folgenden Code:

        private async void InsertTodoItem(TodoItem todoItem)
            {
                // Dieser Code fügt ein neues TodoItem in die Datenbank ein. 
            // Wenn die Operation endet und der mobile Dienst eine Id generiert
                // hat, wird das Element zur Sammlung hinzugefügt.
            try
                {
                    await todoTable.InsertAsync(todoItem);
                    items.Add(todoItem);
                }
                catch (MobileServiceInvalidOperationException e)
                {
                    MessageBox.Show(e.Message,
                        string.Format("{0} (HTTP {1})",
                        e.Response.ReasonPhrase,
                        (int)e.Response.StatusCode), 
                        MessageBoxButton.OK);
                }
            }

  Diese Version der Methode enthält die Fehlerbehandlung für **MobileServiceInvalidOperationException**, und die Fehlermeldung wird in einer MessageBox angezeigt.

Hinzufügen eines Zeitstempels
-----------------------------

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

**Hinweis**

Die hier demonstrierte Zeitstempeleigenschaft **createdAt** ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft **\_\_createdAt** für jede Tabelle. Sie könnten diese Systemeigenschaft in Ihrer Anwendung verwenden, indem Sie einfach das folgende Mitglied zur TodoItem-Klasse hinzufügen:

``` {}

[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
```

1.  Ersetzen Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf der Registerkarte **Scripts** das aktuelle Skript **Insert** durch die folgende Funktion und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft **createdAt** zum Objekt hinzufügt, bevor es durch den **request**.**execute**-Aufruf eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services automatisch die Spalte **createdAt** in die Tabelle **TodoItem** bei der ersten Ausführung ein. Das dynamische Schema ist standardmäßig für einen neuen mobilen Dienst aktiviert und sollte deaktiviert werden, bevor die App im Windows Phone Store veröffentlicht wird.

2.  Drücken Sie in Visual Studio die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit weniger als zehn Zeichen in das Textfeld ein, und klicken Sie auf **Save**.

      Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Browse** in der Tabelle **todoitem**.

      Beachten Sie, dass es hier nun ein Spalte **createdAt** gibt und das neu eingefügte Element über einen Zeitstempelwert verfügt.

Nun müssen Sie die Windows Phone-App aktualisieren, um diese neue Spalte anzuzeigen.

Den Client erneut aktualisieren
-------------------------------

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1.  Öffnen Sie die Datei "MainPage.xaml.cs" in Visual Studio und ersetzen Sie die vorhandene Klasse **TodoItem** durch die folgende Definition:

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

    **DataMemberAttribute** weist den Client an, die neue Eigenschaft **CreatedAt** in der App zu der in der TodoItem-Tabelle definierten Spalte **createdAt** zuzuordnen, die über eine andere Schreibweise verfügt. Durch die Verwendung dieses Attributs kann Ihre App Eigenschaftennamen zu Objekten haben, die von den Spaltennamen in der SQL-Datenbank abweichen. Ohne dieses Attribut würde aufgrund der unterschiedlichen Schreibweisen ein Fehler auftreten.

2.  Fügen Sie das folgende XAML-Element direkt unter dem Element **CheckBoxComplete** in der Datei "MainPage.xaml" ein:

         <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

      Neue Eigenschaft **CreatedAt** in einem Textfeld anzeigen. 

3.  Drücken Sie **F5**, um die App auszuführen.

      Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

4.  Ersetzen Sie die vorhandene Methode **RefreshTodoItems** durch den folgenden Code:

         private async void RefreshTodoItems()
         {
        // Diese Abfrage filtert alle abgeschlossenen TodoItems-Einträge
        // und Einträge ohne Zeitstempel heraus. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false
                    && todoItem.CreatedAt != null)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;
         }

      Diese Methode aktualisiert die Abfrage und filtert auch Elemente heraus, die keinen Zeitstempelwert haben.

5.  Drücken Sie **F5**, um die App auszuführen.

      Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren:

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen: [Optimieren von Abfragen mit Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-wp8).

-   [Autorisieren von Benutzern mit Skripts](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-wp8)
    <br/>Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-wp8)
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


