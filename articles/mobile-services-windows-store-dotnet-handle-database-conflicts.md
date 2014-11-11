<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-dotnet" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Behandeln von Schreibkonflikten in Datenbanken

<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>

Dieses Lernprogramm beschreibt die Behandlung von Konflikten, die auftreten, wenn zwei oder mehr Clients in denselben Datenbankeintrag in einer Windows Store-App schreiben. In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Azure Mobile Services bietet Unterstützung für die Erkennung und Auflösung dieser Konflikte. Dieser Artikel beschreibt die Schritte, mit denen Sie Datenbank-Schreibkonflikte sowohl auf dem Server als auch in Ihrer Anwendung behandeln können.

In diesem Lernprogramm fügen Sie eine Funktion zur Schnellstart-App hinzu, die Konflikte bei Änderungen in der TodoItem-Datenbank behandelt. In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Aktualisieren der Anwendung, um Änderungen zu ermöglichen]
2.  [Aktivieren der Konflikterkennung in Ihrer Anwendung]
3.  [Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]
4.  [Automatische Konfliktauflösung in Serverskripts]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows oder eine höhere Version.
-   Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen.
-   [Azure-Konto]
-   Azure Mobile Services NuGet-Paket 1.1.0 oder neuer. Führen Sie die folgenden Schritte aus, um die neueste Version herunterzuladen:

    1.  Öffnen Sie das Projekt in Visual Studio, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Manage Nuget Packages** aus.

        ![][19]

    2.  Erweitern Sie **Online**, und klicken Sie auf **Microsoft and .NET**. Geben Sie im Suchfeld **Azure Mobile Services** ein. Klicken Sie auf **Install** für das **Azure Mobile Services** NuGet-Paket.

        ![][20]

## <a name="uiupdate"></a><span class="short-header">Aktualisieren der Anwendung</span>Aktualisieren der Anwendung, um Änderungen zu ermöglichen

In diesem Abschnitt aktualisieren Sie die TodoList-Benutzeroberfläche, um Änderungen an den Textelementen in einem Listenfeld-Steuerelement zu ermöglichen. Das Listenfeld enthält ein Kontrollkästchen und ein TextBox-Steuerelement für jeden Eintrag in der Datenbanktabelle. Sie können das Textfeld des TodoItems ändern. Die Anwendung verarbeitet das `LostFocus`-Ereignis von TextBox, um das Element in der Datenbank zu aktualisieren.

1.  Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] heruntergeladen haben.
2.  Öffnen Sie die Datei "MainPage.xaml" im Projektmappen-Explorer in Visual Studio, ersetzen Sie die `ListView`-Definition durch die folgende `ListView`-Definition, und speichern Sie Ihre Änderungen.

        <ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

3.  Fügen Sie in "MainPage.xaml.cs" die folgende `using`-Direktive am Anfang der Seite hinzu.

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
            Exception exception = null;         
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }           
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

Die Anwendung schreibt nun die Textänderungen der einzelnen Elemente in die Datenbank, wenn der Fokus der TextBox verloren geht.

## <a name="enableOC"></a><span class="short-header">Aktivieren der optimistischen Nebenläufigkeit</span>Aktivieren der Konflikterkennung in Ihrer Anwendung

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. [Die Steuerung für optimistische Nebenläufigkeit] nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Nebenläufigkeit, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt. Azure Mobile Services unterstützen die optimistische Nebenläufigkeitssteuerung, indem Änderungen an Elementen in der `__version`-Systemeigenschaftenspalte, die jeder Tabelle hinzugefügt wird, nachverfolgt werden. In diesem Abschnitt erweitern wir die Anwendung um die Erkennung dieser Schreibkonflikte anhand der `__version`-Systemeigenschaft. Die Anwendung wird bei einem Aktualisierungsversuch durch eine `MobileServicePreconditionFailedException` benachrichtigt, falls der Eintrag seit der letzten Abfrage geändert wurde. Anschließend kann die Anwendung entscheiden, ob die Änderung in die Datenbank geschrieben oder die letzte Änderung in der Datenbank erhalten bleiben soll. Weitere Informationen zu Systemeigenschaften für Mobile Services finden Sie unter [Systemeigenschaften].

1.  Aktualisieren Sie die **TodoItem**-Klassendefinition in "MainPage.xaml.cs" mit dem folgenden Code, um die **__version**-Systemeigenschaft hinzuzufügen, die die Erkennung von Schreibkonflikten ermöglicht.

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
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }

3.  Fügen Sie in "MainPage.xaml.cs" die Definition für die `ResolveConflict()`-Methode hinzu, auf die in `UpdateToDoItem()` verwiesen wird. Um den Konflikt zu lösen, setzen Sie die Version des lokalen Elements auf die aktualisierte Version vom Server, bevor Sie den Commit für die Entscheidung des Benutzers ausführen. Andernfalls wird der Konflikt weiterhin auftreten.

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);          
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;             
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };          
            ServerBtn.Invoked = async (IUICommand command) =>
            {
                RefreshTodoItems();
            };          
            await msgDialog.ShowAsync();
        }

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung

In diesem Abschnitt erstellen Sie ein Windows Store-App-Paket, um die App auf einem zweiten Computer bzw. virtuellen Computer zu installieren. Anschließend werden Sie die App auf beiden Computern ausführen, um den Code mit einem Schreibkonflikt zu testen. Beide Instanzen der App werden versuchen, die `text`-Eigenschaft desselben Elements zu aktualisieren, woraufhin der Benutzer den Konflikt auflösen muss.

1.  Erstellen Sie ein Windows Store-App-Paket, um die App auf einem zweiten Computer bzw. virtuellen Computer zu installieren. Klicken Sie dazu auf **Projekt**-\>**Speichern**-\>**App-Pakete erstellen** in Visual Studio.

    ![][0]

2.  Klicken Sie im Bildschirm "Ihre Pakete erstellen" auf **Nein**, da Sie dieses Paket nicht im Windows Store hochladen werden. Klicken Sie auf **Next**.

    ![][1]

3.  Akzeptieren Sie die Standardwerte im Bildschirm "Pakete auswählen und konfigurieren", und klicken Sie auf **Erstellen**.

    ![][10]

4.  Klicken Sie im Bildschirm "Paketerstellung abgeschlossen" auf den Link **Ausgabeverzeichnis**, um den Speicherort des Pakets zu öffnen.

    ![][11]

5.  Kopieren Sie den Paketordner "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test" auf den zweiten Computer. Öffnen Sie auf diesem Computer den Paketordner, und klicken Sie mit der rechten Maustaste auf das **Add-AppDevPackage.ps1** PowerShell-Skript, und klicken Sie auf **Run with PowerShell**, wie unten gezeigt. Folgen Sie den Anweisungen, um die App zu installieren.

    ![][12]

6.  Führen Sie die Instanz 1 der App in Visual Studio aus, indem Sie auf **Debuggen**-\>**Debuggen starten** klicken. Klicken Sie im Startbildschirm des zweiten Computers auf den Pfeil nach unten, um "Apps by Name" anzuzeigen. Klicken Sie anschließend auf die **todolist**-App, um Instanz 2 der App auszuführen.

    App-Instanz 1	
    ![][2]

    App-Instanz 2	
    ![][2]

7.  Aktualisieren Sie den Text des letzten Elements in Instanz 1 der App zu **Test Write 1**, und klicken Sie auf ein anderes Textfeld, damit der `LostFocus`-Ereignishandler die Datenbank aktualisiert. Der folgende Screenshot zeigt ein Beispiel.

    App-Instanz 1	
    ![][3]

    App-Instanz 2	
    ![][2]

8.  Der Wert des entsprechenden Elements in Instanz 2 der App ist nun veraltet. Geben Sie in dieser Instanz der App den Wert **Test Write 2** für die `text`-Eigenschaft ein. Klicken Sie auf ein anderes Textfeld, damit der `LostFocus`-Ereignishandler versucht, die Datenbank mit der alten `_version`-Eigenschaft zu aktualisieren.

    App-Instanz 1	
    ![][4]

    App-Instanz 2	
    ![][5]

9.  Da der beim Aktualisierungsversuch verwendete `__version`-Wert nicht mit dem `__version`-Serverwert übereinstimmte, löst das Mobile Services SDK `MobileServicePreconditionFailedException` aus, sodass die App diesen Konflikt auflösen kann. Zum Lösen des Konflikts können Sie auf **Commit Local Text** klicken, um für die Werte von Instanz 2 einen Commit auszuführen. Alternativ können Sie auf **Leave Server Text** klicken, um die Werte in Instanz 2 zu verwerfen und die Werte aus Instanz 1 in der Datenbank zu behalten.

    App-Instanz 1	
    ![][4]

    App-Instanz 2	
    ![][6]

## <a name="scriptsexample"></a><span class="short-header">Konfliktbehandlung mit Skripts</span>Automatische Konfliktauflösung in Serverskripts

Sie können Schreibkonflikte auch in Serverskripts erkennen und behandeln. Dies macht Sinn, wenn Sie geskriptete Logik anstelle einer Benutzereingabe für die Konfliktauflösung verwenden möchten. In diesem Abschnitt erstellen Sie ein Serverskript für die TodoItem-Tabelle der Anwendung. Die Logik in diesem Skript wird Konflikte wie folgt auflösen:

-   Falls das `complete`-Feld von "TodoItem" den Wert "true" hat, gilt das Element als abgeschlossen, und `text` kann nicht mehr geändert werden.
-   Falls das `complete`-Feld von "TodoItem" immer noch den Wert "false" hat, wird für Aktualisierungen an `text` ein Commit ausgeführt.

Führen Sie die folgenden Schritte aus, um ein Serverskript zu erstellen und zu testen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][7]

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

    ![][8]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Aktualisieren**.

    ![][9]

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

5.  Führen Sie die **todolist**-App auf beiden Computern aus. Ändern Sie den TodoItem-`text` des letzten Elements in Instanz 2, und klicken Sie auf ein anderes Textfeld, sodass die Datenbank vom `LostFocus`-Ereignishandler aktualisiert wird.

    App-Instanz 1	
    ![][4]

    App-Instanz 2	
    ![][5]

6.  Geben Sie in Instanz 1 der App einen anderen Wert text-Eigenschaft ein. Klicken Sie auf ein anderes Textfeld, damit der `LostFocus`-Ereignishandler versucht, die Datenbank mit einer falschen `__version`-Eigenschaft zu aktualisieren.

    App-Instanz 1	
    ![][13]

    App-Instanz 2	
    ![][14]

7.  Beachten Sie, dass keine Ausnahme in der App aufgetreten ist, da der Konflikt vom Serverskript behandelt und die Änderung durchgeführt wurde, da das Element nicht abgeschlossen ist. Klicken Sie in Instanz 2 auf **Aktualisieren**, um die Datenbank erneut abzufragen und sich zu vergewissern, dass die Änderung tatsächlich erfolgreich war.

    App-Instanz 1	
    ![][15]

    App-Instanz 2	
    ![][15]

8.  Klicken Sie in Instanz 1 auf das Kontrollkästchen, um das letzte Todo-Element als abgeschlossen zu markieren.

    App-Instanz 1	
    ![][16]

    App-Instanz 2	
    ![][15]

9.  Versuchen Sie in Instanz 2, den Text des TodoItems zu aktualisieren, und lösen Sie das `LostFocus`-Ereignis aus. Das Skript löst den Konflikt, indem die Änderung abgelehnt wird, da das Element bereits abgeschlossen war.

    App-Instanz 1	
    ![][17]

    App-Instanz 2	
    ![][18]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Behandlung von Schreibkonflikten in Windows Store-Apps bei der Arbeit mit Daten in Mobile Services gezeigt. Anschließend könnten Sie eines der folgenden Lernprogramme in unserer Daten-Reihe ausführen:

-   [Prüfen und Ändern von Daten mit Skripten]
    <br/>Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging]
    <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Sobald Sie die Datenreihe abgeschlossen haben, können Sie sich auch an einem der folgenden Windows Store-Lernprogramme versuchen:

-   [Erste Schritte mit der Authentifizierung]
    <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen]
    <br/>Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Aktualisieren der Anwendung, um Änderungen zu ermöglichen]: #uiupdate
  [Aktivieren der Konflikterkennung in Ihrer Anwendung]: #enableOC
  [Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]: #test-app
  [Automatische Konfliktauflösung in Serverskripts]: #scriptsexample
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
  [Azure-Konto]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Die Steuerung für optimistische Nebenläufigkeit]: http://go.microsoft.com/fwlink/?LinkId=330935
  [Systemeigenschaften]: http://go.microsoft.com/fwlink/?LinkId=331143
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png