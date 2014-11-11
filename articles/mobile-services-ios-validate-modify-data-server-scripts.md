<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-validate-modify-data-server-scripts" title="Android" class="current">Android</a><a href="/de-de/documentation/articles/mobile-services-html-validate-modify-data-server-scripts" title="HTML" class="current">HTML</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre iOS-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen][Überprüfung der Zeichenfolgenlänge hinzufügen]
2.  [Client zur Unterstützung der Überprüfung aktualisieren][Client zur Unterstützung der Überprüfung aktualisieren]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen.

## <a name="string-length-validation"></a>Überprüfung hinzufügen

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

    ![][1]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

    ![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Das Skript überprüft die Länge der Eigenschaft **text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Sie k&ouml;nnen ein registriertes Skript auf der Registerkarte <strong>Script</strong> entfernen, indem Sie auf <strong>Clear</strong> und dann auf <strong>Save</strong> klicken.</p></div>

## <a name="update-client-validation"></a>Den Client aktualisieren

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] verändert haben.

2.  Drücken Sie die **Run**-Taste (Command + R), um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text mit mehr als 10 Zeichen in das Textfeld ein und klicken Sie auf das (**+**)-Symbol.

    Beachten Sie, dass die App einen unbehandelten Fehler als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.

3.  Suchen Sie die folgende Codezeile in der **addItem**-Methode in QSTodoService.m:

        [self logErrorIfNotNil:error]; 

      Ersetzen Sie im Anschluss an diese Codezeile den verbleibenden completion-Block durch den folgenden Code:

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

    Dadurch wird der Fehler im Ausgabefenster protokolliert und dem Benutzer angezeigt.

4.  Erstellen und starten Sie die App.

    ![][3]

    Beachten Sie, dass der Fehler behandelt und die Fehlermeldung für den Benutzer angezeigt wird.

<!--## <a name="add-timestamp"></a>Add a timestamp  The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.  1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.          function insert(item, user, request) {             if (item.text.length > 10) {                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');             } else {                 item.createdAt = new Date();                 request.execute();             }         }      This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.       <div class="dev-callout"><b>Note</b>     <p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>     </div>  2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.      Notice that the new timestamp does not appear in the app UI.  3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.         Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.    Next, you need to update the iOS app to display this new column.  ## <a name="update-client-timestamp"></a>Update the client again  The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.  1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:          public class TodoItem         {             public int Id { get; set; }                        [DataMember(Name="text")]             public string Text { get; set; }              [DataMember(Name="complete")]             public bool Complete { get; set; }                          [DataMember(Name="createdAt")]             public DateTime? CreatedAt { get; set; }         }          This new class definition includes the new timestamp property, as a nullable DateTime type.        <div class="dev-callout"><b>Note</b>     <p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>     </div>  5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:                    <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>      This displays the new **CreatedAt** property in a text box.       6. Press the **F5** key to run the app.      Notice that the timestamp is only displayed for items inserted after you updated the insert script.  7. Replace the existing **RefreshTodoItems** method with the following code:           private void RefreshTodoItems()         {              // This query filters out completed TodoItems and              // items without a timestamp.              items = todoTable                .Where(todoItem => todoItem.Complete == false                    && todoItem.CreatedAt != null)                .ToCollectionView();              ListItems.ItemsSource = items;         }      This method updates the query to also filter out items that do not have a timestamp value.      8. Press the **F5** key to run the app.      Notice that all items created without timestamp value disappear from the UI.  You have completed this working with data tutorial.-->

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging][Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts]
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.



  [Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
  [Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-ios
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
