<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-wp8" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Behandeln von Schreibkonflikten in Datenbanken

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

Dieses Lernprogramm beschreibt die Behandlung von Konflikten, die auftreten, wenn zwei oder mehr Clients in denselben Datenbankeintrag in einer Windows Phone 8-App schreiben. In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Mobile Services bietet Unterstützung für die Erkennung und Auflösung dieser Konflikte. Dieser Artikel beschreibt die Schritte, mit denen Sie Datenbank-Schreibkonflikte sowohl auf dem Server als auch in Ihrer Anwendung behandeln können.

In diesem Lernprogramm fügen Sie eine Funktion zur Schnellstart-App hinzu, die Konflikte bei Änderungen in der TodoItem-Datenbank behandelt. In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Aktualisieren der Anwendung, um Änderungen zu ermöglichen][Aktualisieren der Anwendung, um Änderungen zu ermöglichen]
2.  [Aktivieren der Konflikterkennung in Ihrer Anwendung][Aktivieren der Konflikterkennung in Ihrer Anwendung]
3.  [Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung][Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]
4.  [Automatische Konfliktauflösung in Serverskripts][Automatische Konfliktauflösung in Serverskripts]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows Phone 8 oder eine höhere Version.
-   [Windows Phone 8 SDK][Windows Phone 8 SDK] auf Windows 8.
-   [Azure-Konto][Azure-Konto]
-   Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.
-   Azure Mobile Services NuGet-Paket 1.1.0 oder neuer. Führen Sie die folgenden Schritte aus, um die neueste Version herunterzuladen:

    1.  Öffnen Sie das Projekt in Visual Studio, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Manage Nuget Packages** aus.

        ![][]

    2.  Erweitern Sie **Online**, und klicken Sie auf **Microsoft and .NET**. Geben Sie im Suchfeld **Azure Mobile Services** ein. Klicken Sie auf **Install** für das **Azure Mobile Services** NuGet-Paket.

        ![][1]

## <a name="uiupdate"></a><span class="short-header">Aktualisieren der Anwendung</span>Aktualisieren der Anwendung, um Änderungen zu ermöglichen

In diesem Abschnitt aktualisieren Sie die TodoList-Benutzeroberfläche, um Änderungen an den Textelementen in einem Listenfeld-Steuerelement zu ermöglichen. Das Listenfeld enthält ein Kontrollkästchen und ein TextBox-Steuerelement für jeden Eintrag in der Datenbanktabelle. Sie können das Textfeld des TodoItems ändern. Die Anwendung verarbeitet das `LostFocus`-Ereignis von TextBox, um das Element in der Datenbank zu aktualisieren.

1.  Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit mobilen Diensten][Erste Schritte mit Mobile Services] heruntergeladen haben.
2.  Öffnen Sie die Datei "MainPage.xaml" im Projektmappen-Explorer in Visual Studio, ersetzen Sie die `phone:LongListSelector`-Definition durch die folgende ListBox-Definition, und speichern Sie Ihre Änderungen.

        <ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
            <ListBox.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListBox.ItemTemplate>
        </ListBox>

3.  Öffnen Sie im Projektmappen-Explorer in Visual Studio "MainPage.xaml.cs". Fügen Sie dann die folgende `using`-Direktive hinzu.

        using System.Threading.Tasks;

4.  Öffnen Sie im Projektmappen-Explorer in Visual Studio "MainPage.xaml.cs". Fügen Sie den Ereignishandler dem MainPage-Element des TextBox-`LostFocus`-Ereignisses wie unten gezeigt hinzu.

        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

5.  Fügen Sie in "MainPage.xaml.cs" die Definition für die zum MainPage-Element gehörige `UpdateToDoItem()`-Methode hinzu, auf die im Ereignishandler verwiesen wird, wie unten dargestellt.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

Die Anwendung schreibt nun die Textänderungen der einzelnen Elemente in die Datenbank, wenn der Fokus der TextBox verloren geht.

## <a name="enableOC"></a><span class="short-header">Aktivieren der optimistischen Nebenläufigkeit</span>Aktivieren der Konflikterkennung in Ihrer Anwendung

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. [Die Steuerung für optimistische Nebenläufigkeit][Die Steuerung für optimistische Nebenläufigkeit] nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Nebenläufigkeit, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt. Azure Mobile Services unterstützen die optimistische Nebenläufigkeitssteuerung, indem Änderungen an Elementen in der `__version`-Systemeigenschaftenspalte, die jeder Tabelle hinzugefügt wird, nachverfolgt werden. In diesem Abschnitt erweitern wir die Anwendung um die Erkennung dieser Schreibkonflikte anhand der `__version`-Systemeigenschaft. Die Anwendung wird bei einem Aktualisierungsversuch durch eine `MobileServicePreconditionFailedException` benachrichtigt, falls der Eintrag seit der letzten Abfrage geändert wurde. Anschließend kann die Anwendung entscheiden, ob die Änderung in die Datenbank geschrieben oder die letzte Änderung in der Datenbank erhalten bleiben soll. Weitere Informationen zu Systemeigenschaften für Mobile Services finden Sie unter [Systemeigenschaften][Systemeigenschaften].

1.  Aktualisieren Sie die **TodoItem**-Klassendefinition in "MainPage.xaml.cs" mit dem folgenden Code, um die \*\*\_\_version\*\*-Systemeigenschaft hinzuzufügen, die die Erkennung von Schreibkonflikten ermöglicht:

        public class TodoItem
        {
            public string Id { get; set; }            
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }            
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }            
            [JsonProperty(PropertyName = "__version")]
            public string Version { set; get; }
        }

    <div class="dev-callout"><strong>Hinweis</strong>
<p>Bei der Verwendung von untypisierten Tabellen k&ouml;nnen Sie die optimistische Nebenl&auml;ufigkeit aktivieren, indem sie das Version-Flag zu den Systemeigenschaften der Tabelle hinzuf&uuml;gen.</p>
<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
</div>

2.  Wenn Sie der `TodoItem`-Klasse die `Version`-Eigenschaft hinzufügen, wird die Anwendung während einer Aktualisierung durch eine `MobileServicePreconditionFailedException`-Ausnahme, falls der Eintrag seit der letzten Abfrage geändert wurde. Diese Ausnahme enthält den die neueste Version des Elements auf dem Server. Fügen Sie den folgenden Code in "MainPage.xaml.cs" hinzu, um die Ausnahme in der `UpdateToDoItem()`-Methode zu behandeln.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

3.  Fügen Sie in "MainPage.xaml.cs" die Definition für die `ResolveConflict()`-Methode hinzu, auf die in `UpdateToDoItem()` verwiesen wird. Um den Konflikt zu lösen, setzen Sie die Version des lokalen Elements auf die aktualisierte Version vom Server, bevor Sie den Commit für die Entscheidung des Benutzers ausführen. Andernfalls wird der Konflikt weiterhin auftreten.

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)     
        {
            // Ask user to choose between the local text value or leaving the 
            // server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
                // occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
            // the server item intact and refresh this client's query discarding 
            // the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung

In diesem Abschnitt testen Sie den Code, der Schreibkonflikte durch Ausführen der App in zwei verschiedenen Windows Phone 8-Emulatoren (WVGA und WVGA 512M) verarbeitet. Beide Client-Apps werden versuchen, die `text`-Eigenschaft desselben Elements zu aktualisieren, woraufhin der Benutzer den Konflikt auflösen muss.

1.  Stellen Sie in Visual Studio sicher, dass **Emulator WVGA 512MB** in der Dropdownliste als Bereitstellungsziel ausgewählt ist, wie im Screenshot unten gezeigt wird.

    ![][2]

2.  Klicken Sie in Visual Studio im Menü auf **BUILD** und dann auf **Projektmappe bereitstellen**. Falls der Emulator zuvor noch nicht ausgeführt wurde, dauert es einige Minuten, bis der Emulator das Windows Phone 8-Betriebssystem geladen hat. Vergewissern Sie sich im Ausgabefenster unten, dass der Build und die Bereitstellung im Windows Phone 8-Emulator erfolgreich war.

    ![][3]

3.  Ändern Sie in Visual Studio das Dropdownfeld für das Bereitstellungsziel in **Emulator WVGA**.

    ![][4]

4.  Klicken Sie in Visual Studio im Menü auf **BUILD** und dann auf **Projektmappe bereitstellen**. Vergewissern Sie sich im Ausgabefenster unten, dass der Build und die Bereitstellung im Windows Phone 8-Emulator erfolgreich war.

    ![][3]

5.  Führen Sie beide Emulatoren nebeneinander aus. Wir können gleichzeitige Schreibkonflikte zwischen den auf diesen Emulatoren ausgeführten Client-Apps simulieren. Streifen Sie in beiden Emulatoren von rechts nach links, um die Liste der installierten Anwendungen anzuzeigen. Blättern Sie in beiden Listen nach unten und klicken Sie auf die App **todolist**.

    ![][5]

6.  Aktualisieren Sie im linken Emulator `text` für das letzte "TodoItem" auf **Test Write 1**. Klicken Sie dann auf ein weiteres Textfeld, sodass der `LostFocus`-Ereignishandler die Datenbank aktualisiert. Der folgende Screenshot zeigt ein Beispiel.

    ![][6]

7.  An diesem Punkt hat das entsprechende Element im rechten Emulator eine alte Version und einen alten Textwert. Geben Sie im rechten Emulator **Test Write 2** als Texteigenschaft ein. Klicken Sie dann auf ein weiteres Textfeld, sodass der `LostFocus`-Ereignishandler im rechten Emulator versucht, die Datenbank mit der alten Version zu aktualisieren.

    ![][7]

8.  Da die Version, die mit dem Aktualisierungsversuch verwendet wurde, nicht der Serverversion entspricht, löst das Mobile Services SDK `MobileServicePreconditionFailedException` aus, sodass die App diesen Konflikt auflösen kann. Zum Auflösen des Konflikts klicken Sie auf **OK**, um die Werte aus der rechten App zu übernehmen. Sie können auch auf **Abbrechen** klicken, um die Werte in der rechten App zu verwerfen und die Werte aus der linken App zu übernehmen.

    ![][8]

## <a name="scriptsexample"></a><span class="short-header">Konfliktbehandlung mit Skripts</span>Automatische Konfliktauflösung in Serverskripts

Sie können Schreibkonflikte auch in Serverskripts erkennen und behandeln. Dies macht Sinn, wenn Sie geskriptete Logik anstelle einer Benutzereingabe für die Konfliktauflösung verwenden möchten. In diesem Abschnitt erstellen Sie ein Serverskript für die TodoItem-Tabelle der Anwendung. Die Logik in diesem Skript wird Konflikte wie folgt auflösen:

-   Falls das `complete`-Feld von "TodoItem" den Wert "true" hat, gilt das Element als abgeschlossen, und `text` kann nicht mehr geändert werden.
-   Falls das `complete`-Feld von "TodoItem" immer noch den Wert "false" hat, wird für Aktualisierungen an `text` ein Commit ausgeführt.

Führen Sie die folgenden Schritte aus, um ein Serverskript zu erstellen und zu testen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][9]

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

    ![][10]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Aktualisieren**.

    ![][11]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

        function update(item, user, request) { 
            request.execute({ 
                conflict: function (serverRecord) {
                    // Only committing changes if the item is not completed.
                    if (serverRecord.complete === false) {
                        //write the updated item to the table
                        request.execute();
                    }
                    else
                    {
                        request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                    }
                }
            }); 
        }   

5.  Ändern Sie den TodoItem-Text für das letzte Element in der App im linken Emulator. Klicken Sie auf ein anderes Textfeld, damit der `LostFocus`-Ereignishandler die Datenbank aktualisiert.

    ![][6]

6.  Geben Sie im rechten Emulator einen anderen Wert für die Texteigenschaft des letzten TodoItem ein. Klicken Sie dann auf ein weiteres Textfeld, sodass der `LostFocus`-Ereignishandler im rechten Emulator versucht, die Datenbank mit der alten Version zu aktualisieren.

    ![][7]

7.  Beachten Sie, dass keine Ausnahme in der App aufgetreten ist, da der Konflikt vom Serverskript behandelt und die Änderung durchgeführt wurde, da das Element nicht abgeschlossen ist. Um zu überprüfen, ob die Aktualisierung erfolgreich war, klicken Sie in der App im linken Emulator auf **Refresh**, um die Datenbank erneut abzufragen.

    ![][12]

8.  Klicken Sie in der App im linken Emulator auf das Kontrollkästchen, um das TodoItem abzuschließen.

    ![][13]

9.  Versuchen Sie in der App im rechten Emulator, den Text des gleichen "TodoItem" zu aktualisieren, und lösen Sie das `LostFocus`-Ereignis aus. Das Skript löst den Konflikt, indem die Änderung abgelehnt wird, da das Element bereits abgeschlossen war.

    ![][14]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Behandlung von Schreibkonflikten in Windows Phone 8-Apps bei der Arbeit mit Daten in Mobile Services gezeigt. Anschließend könnten Sie eines der folgenden Lernprogramme in unserer Daten-Reihe ausführen:

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

  [Windows Store C#]: /de-de/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/ "Windows Store JavaScript"
  [Windows Phone]: /de-de/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone"
  [Aktualisieren der Anwendung, um Änderungen zu ermöglichen]: #uiupdate
  [Aktivieren der Konflikterkennung in Ihrer Anwendung]: #enableOC
  [Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]: #test-app
  [Automatische Konfliktauflösung in Serverskripts]: #scriptsexample
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Azure-Konto]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-wp8
  []: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
  [1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png
  [Die Steuerung für optimistische Nebenläufigkeit]: http://go.microsoft.com/fwlink/?LinkId=330935
  [Systemeigenschaften]: http://go.microsoft.com/fwlink/?LinkId=331143
  [2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
  [3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
  [4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
  [5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
  [6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
  [7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
  [8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
  [10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
  [11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
  [12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
  [13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
  [14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-wp8
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-wp8
