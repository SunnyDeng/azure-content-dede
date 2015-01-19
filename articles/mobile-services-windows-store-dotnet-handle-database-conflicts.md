<properties urlDisplayName="Optimistic concurrency" pageTitle="Behandeln von Datenbank-Schreibkonflikten mit optimistischer Nebenläufigkeit (Windows Store) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie, wie Sie mit Datenbank-Schreibkonflikten sowohl auf dem Server als auch in der Windows Store-Anwendung umgehen." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Behandeln von Schreibkonflikten in Datenbanken

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#" class="current">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	


Dieses Lernprogramm beschreibt die Behandlung von Konflikten, die auftreten, wenn zwei oder mehr Clients in denselben Datenbankeintrag in einer Windows Store-App schreiben. In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Azure Mobile Services bietet Unterstützung für die Erkennung und Auflösung dieser Konflikte. Dieser Artikel beschreibt die Schritte, mit denen Sie Datenbank-Schreibkonflikte sowohl auf dem Server als auch in Ihrer Anwendung behandeln können.

In diesem Lernprogramm fügen Sie eine Funktion zur Schnellstart-App hinzu, die Konflikte bei Änderungen in der TodoItem-Datenbank behandelt. In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren der Anwendung, um Aktualisierungen zu ermöglichen]
2. [Aktivieren der Konflikterkennung in Ihrer Anwendung]
3. [Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]
4. [Automatische Konfliktauflösung in Serverskripts]


Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2012 Express für Windows oder eine höhere Version.
+ Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen. 
+ [Azure-Konto]
+ Azure Mobile Services NuGet-Paket 1.1.0 oder neuer. Führen Sie die folgenden Schritte aus, um die neueste Version herunterzuladen:
	1. Öffnen Sie das Projekt in Visual Studio, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Nuget-Pakete verwalten**. 

		![][19]

	2. Erweitern Sie **Online**, und klicken Sie auf **Microsoft und .NET**. Geben Sie in das Suchfeld **Azure Mobile Services** ein. Klicken Sie für das **Azure Mobile Services** NuGet-Paket auf **Installieren**.

		![][20]


 

<h2><a name="uiupdate"></a>Aktualisieren der Anwendung, um Änderungen zu ermöglichen</h2>

In diesem Abschnitt aktualisieren Sie die TodoList-Benutzeroberfläche, um Änderungen an den Textelementen in einem Listenfeld-Steuerelement zu ermöglichen. Das Listenfeld enthält ein Kontrollkästchen und ein TextBox-Steuerelement für jeden Eintrag in der Datenbanktabelle. Sie können das Textfeld des TodoItems ändern. Die Anwendung verarbeitet das "LostFocus"-Ereignis von dieser TextBox, um das Element in der Datenbank zu aktualisieren.


1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] heruntergeladen haben.
2. Öffnen Sie die Datei "MainPage.xaml" im Projektmappen-Explorer von Visual Studio, ersetzen Sie die "ListView"-Definition durch die unten abgebildete "ListView", und speichern Sie die Änderung.

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


3. Fügen Sie in "MainPage.xaml.cs" die folgende "using"-Anweisung am Anfang der Seite hinzu.

		using System.Threading.Tasks;


4. Öffnen Sie MainPage.xaml.cs im Projektmappen-Explorer in Visual Studio. Fügen Sie den Ereignishandler zur MainPage für das TextBox-Ereignis "LostFocus" hinzu.


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

4. Fügen Sie, wie unten gezeigt, in "MainPage.xaml.cs" die Definition für die "UpdateToDoItem()"-Methode der MainPage hinzu, auf die im Ereignishandler verwiesen wird.

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

<h2><a name="enableOC"></a>Aktivieren der Konflikterkennung in Ihrer Anwendung</h2>

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Die [Steuerung für optimistische Nebenläufigkeit] setzt voraus, dass jede Transaktion Commits ausführen kann, und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Nebenläufigkeit, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt. Azure Mobile Services unterstützen optimistische Nebenläufigkeit, indem Änderungen an Elementen in der "__version"-Systemeigenschaftenspalte registriert werden, die in allen Tabellen existiert. In diesem Abschnitt erweitern wir die Anwendung um die Erkennung dieser Schreibkonflikte anhand der "__version"-Systemeigenschaft. Die Anwendung wird durch eine "MobileServicePreconditionFailedException" bei einem Änderungsversuch benachrichtigt, falls der Eintrag seit der letzten Abfrage geändert wurde. Anschließend kann die Anwendung entscheiden, ob die Änderung in die Datenbank geschrieben oder die letzte Änderung in der Datenbank erhalten bleiben soll. Weitere Informationen zu Systemeigenschaften für Mobile Services finden Sie unter [Systemeigenschaften].

1. Aktualisieren Sie die **TodoItem**-Klassendefinition in "MainPage.xaml.cs" mit dem folgenden Code, um die **__version**-Systemeigenschaft hinzuzufügen, die die Erkennung von Schreibkonflikten ermöglicht.

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
	<p>Bei der Verwendung von untypisierten Tabellen können Sie die optimistische Nebenläufigkeit aktivieren, indem sie das Version-Flag zu den Systemeigenschaften der Tabelle hinzufügen.</p>
	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. Wenn Sie die "Version"-Eigenschaft zur Klasse "TodoItem" hinzufügen, wird die Anwendung durch eine "MobileServicePreconditionFailedException" bei Änderungsversuchen benachrichtigt, falls der Eintrag seit der letzten Abfrage geändert wurde. Diese Ausnahme enthält den die neueste Version des Elements auf dem Server. Fügen Sie den folgenden Code in "MainPage.xaml.cs" hinzu, um die Ausnahme in der "UpdateToDoItem()"-Methode zu behandeln.

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


3. Fügen Sie in "MainPage.xaml.cs" die Definition für die "ResolveConflict()"-Methode hinzu, die in "UpdateToDoItem()" aufgerufen wird. Um den Konflikt zu lösen, setzen Sie die Version des lokalen Elements auf die aktualisierte Version vom Server, bevor Sie den Commit für die Entscheidung des Benutzers ausführen. Andernfalls wird der Konflikt weiterhin auftreten.


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



<h2><a name="test-app"></a>Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung</h2>

In diesem Abschnitt erstellen Sie ein Windows Store-App-Paket, um die App auf einem zweiten Computer bzw. virtuellen Computer zu installieren. Anschließend werden Sie die App auf beiden Computern ausführen, um den Code mit einem Schreibkonflikt zu testen. Beide Instanzen der App werden versuchen, die "text"-Eigenschaft desselben Elements zu aktualisieren, woraufhin der Benutzer den Konflikt auflösen muss.


1. Erstellen Sie ein Windows Store-App-Paket, um die App auf einem zweiten Computer bzw. virtuellen Computer zu installieren. Klicken Sie dazu in Visual Studio auf **Projekt**->**Speichern**->**App-Pakete erstellen**.

	![][0]

2. Klicken Sie im Bildschirm "Ihre Pakete erstellen" auf **Nein**, da Sie dieses Paket nicht in den Windows Store hochgeladen wird. Klicken Sie dann auf **Weiter**.

	![][1]

3. Akzeptieren Sie die Standardwerte im Bildschirm "Pakete auswählen und konfigurieren", und klicken Sie auf **Erstellen**.

	![][10]

4. Klicken Sie im Bildschirm "Paketerstellung abgeschlossen" auf den Link **Ausgabeverzeichnis**, um den Speicherort des Pakets zu öffnen.

   	![][11]

5. Kopieren Sie den Paketordner "todolist_1.0.0.0_AnyCPU_Debug_Test" auf den zweiten Computer. Öffnen Sie auf diesem Computer den Paketordner, und klicken Sie mit der rechten Maustaste auf das PowerShell-Skript **Add-AppDevPackage.ps1**, und klicken Sie auf **Mit PowerShell ausführen**, wie unten gezeigt. Folgen Sie den Anweisungen, um die App zu installieren.

	![][12]
  
5. Führen Sie die Instanz 1 der App in Visual Studio aus, indem Sie auf **Debuggen**->**Debuggen starten** klicken. Klicken Sie im Startbildschirm des zweiten Computers auf den Pfeil nach unten, um "Apps by Name" anzuzeigen. Klicken Sie anschließend auf die **todolist**-App, um Instanz 2 der App auszuführen. 

	App-Instanz 1	
	![][2]

	App-Instanz 2	
	![][2]


6. Aktualisieren Sie den Text des letzten Elements in Instanz 1 der App auf **Test Write 1**, und klicken Sie auf ein anderes Textfeld, damit der "LostFocus"-Ereignishandler die Datenbank aktualisiert. Der folgende Screenshot zeigt ein Beispiel.
	
	App-Instanz 1	
	![][3]

	App-Instanz 2	
	![][2]

7. Der Wert des entsprechenden Elements in Instanz 2 der App ist nun veraltet. Geben Sie in dieser Instanz der App den Wert **Test Write 2 **für die "text"-Eigenschaft ein. Klicken Sie dann auf ein anderes Textfeld, damit der "LostFocus"-Ereignishandler versucht, die Datenbank mit der alten "_version"-Eigenschaft zu aktualisieren.

	App-Instanz 1	
	![][4]

	App-Instanz 2	
	![][5]

8. Da der beim Aktualisierungsversuch verwendete "__version"-Wert nicht mit dem "__version"-Wert des Servers übereinstimmt, löst das Mobile Services SDK eine "MobileServicePreconditionFailedException" aus und gibt der App die Möglichkeit, den Konflikt aufzulösen. Um den Konflikt aufzulösen, können Sie auf **Commit Local Text** klicken, um einen Commit der Werte aus Instanz 2 durchzuführen. Alternativ können Sie auf **Leave Server Text** klicken, um die Werte in Instanz 2 zu verwerfen und die Werte aus Instanz 1 in der Datenbank zu behalten. 

	App-Instanz 1	
	![][4]

	App-Instanz 2	
	![][6]



<h2><a name="scriptsexample"></a>Automatische Konfliktauflösung in Serverskripts</h2>

Sie können Schreibkonflikte auch in Serverskripts erkennen und behandeln. Dies macht Sinn, wenn Sie geskriptete Logik anstelle einer Benutzereingabe für die Konfliktauflösung verwenden möchten. In diesem Abschnitt erstellen Sie ein Serverskript für die TodoItem-Tabelle der Anwendung. Die Logik in diesem Skript wird Konflikte wie folgt auflösen:

+  Falls das "complete"-Feld der TodoItem-Tabelle den Wert "true" hat, gilt das Element als abgeschlossen und "text" kann nicht mehr geändert werden.
+  Falls das "complete"-Feld von TodoItem immer noch den Wert "false" hat, wird für Änderungsversuche an "text" ein Commit ausgeführt.

Führen Sie die folgenden Schritte aus, um ein Serverskript zu erstellen und zu testen.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App. 

   	![][7]

2. Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

   	![][8]

3. Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Aktualisieren** aus.

   	![][9]

4. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

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
5. Führen Sie die **todolist**-App auf beiden Computern aus. Ändern Sie den "text" von TodoItem für das letzte Element in Instanz 2. Klicken Sie auf ein anderes Textfeld, damit der "LostFocus"-Ereignishandler die Datenbank aktualisiert.

	App-Instanz 1	
	![][4]

	App-Instanz 2	
	![][5]

6. Geben Sie in Instanz 1 der App einen anderen Wert text-Eigenschaft ein. Klicken Sie auf ein anderes Textfeld, damit der "LostFocus"-Ereignishandler versucht, die Datenbank mit der falschen "__version"-Eigenschaft zu aktualisieren.

	App-Instanz 1	
	![][13]

	App-Instanz 2	
	![][14]

7. Beachten Sie, dass keine Ausnahme in der App aufgetreten ist, da der Konflikt vom Serverskript behandelt und die Änderung durchgeführt wurde, da das Element nicht abgeschlossen ist. Klicken Sie in Instanz 2 auf **Aktualisieren**, um die Datenbank erneut abzufragen und sich zu vergewissern, dass die Änderung tatsächlich erfolgreich war.

	App-Instanz 1	
	![][15]

	App-Instanz 2	
	![][15]

8. Klicken Sie in Instanz 1 auf das Kontrollkästchen, um das letzte Todo-Element als abgeschlossen zu markieren.

	App-Instanz 1	
	![][16]

	App-Instanz 2	
	![][15]

9. Versuchen Sie in Instanz 2, den Text von TodoItem zu ändern, und lösen Sie das "LostFocus"-Ereignis aus. Das Skript löst den Konflikt, indem die Änderung abgelehnt wird, da das Element bereits abgeschlossen war. 

	App-Instanz 1	
	![][17]

	App-Instanz 2	
	![][18]

## <a name="next-steps"> </a>Next steps

In diesem Lernprogramm wurden die Grundlagen der Behandlung von Schreibkonflikten in Windows Store-Apps bei der Arbeit mit Daten in Mobile Services gezeigt. Anschließend könnten Sie eines der folgenden Lernprogramme in unserer Daten-Reihe ausführen:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Sobald Sie die Datenreihe abgeschlossen haben, können Sie sich auch an einem der folgenden Windows Store-Lernprogramme versuchen:

* [Erste Schritte mit der Authentifizierung] 
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.
 
<!-- Anchors. -->
[Aktualisieren der Anwendung, um Aktualisierungen zu ermöglichen]: #uiupdate
[Aktivieren der Konflikterkennung in Ihrer Anwendung]: #enableOC
[Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]: #test-app
[Automatische Konfliktauflösung in Serverskripts]: #scriptsexample
[Nächste Schritte]: #next-steps

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

<!-- URLs. -->
[Steuerung für optimistische Nebenläufigkeit]: http://go.microsoft.com/fwlink/?LinkId=330935
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Azure-Konto]: http://www.windowsazure.com/de-de/pricing/free-trial/
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Systemeigenschaften]: http://go.microsoft.com/fwlink/?LinkId=331143

<!--HONumber=35.2-->
