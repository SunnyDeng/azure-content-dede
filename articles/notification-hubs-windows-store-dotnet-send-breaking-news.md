<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-windowsdotnet" urlDisplayName="Breaking News" pageTitle="Notification Hubs Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="ricksal" solutions="" manager="" editor="" />

Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten
=========================================================================

[Windows Store C\#](/de-de/manage/services/notification-hubs/breaking-news-dotnet "Windows Store C#")[Windows Phone](/de-de/manage/services/notification-hubs/breaking-news-wp8 "Windows Phone")[iOS](/de-de/manage/services/notification-hubs/breaking-news-ios "iOS")

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine Windows Store-Anwendung senden können. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc.

Übertragungsszenarien werden durch das Einfügen von einem oder mehreren *Tags* möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs-Leitfaden](http://msdn.microsoft.com/de-de/library/jj927170.aspx).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung dieses Szenarios behandelt:

1.  [Hinzufügen der Kategorieauswahl zur App](#adding-categories)
2.  [Registrieren für Benachrichtigungen](#register)
3.  [Senden von Benachrichtigungen von Ihrem Back-End aus](#send)
4.  [Ausführen der Anwendung und Erzeugen von Benachrichtigungen](#test-app)

Dieses Thema baut auf die App auf, die Sie in [Erste Schritte mit Notification Hubs](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet/) erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet/) abgeschlossen haben.

Hinzufügen der Kategorieauswahl zur App
---------------------------------------

Der erste Schritt besteht daraus, Benutzeroberflächenelemente zur vorhandenen Hauptseite hinzuzufügen, welche dem Benutzer die Auswahl der Kategorien für die Registrierung ermöglichen. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Wenn die App gestartet wird, wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt.

1.  Öffnen Sie die Projektdatei "MainPage.xaml", und kopieren Sie folgenden Code in das Element **Grid**:

         <Grid Margin="120, 58, 120, 80" >
             <Grid.RowDefinitions>
                 <RowDefinition />
                 <RowDefinition />
                 <RowDefinition />
                 <RowDefinition />
                 <RowDefinition />
             </Grid.RowDefinitions>
             <Grid.ColumnDefinitions>
                 <ColumnDefinition />
                 <ColumnDefinition />
             </Grid.ColumnDefinitions>
             <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
             <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0"/>
             <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0"/>
             <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0"/>
             <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1"/>
             <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1"/>
             <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1"/>
             <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
         </Grid>

2.  Erstellen Sie in dem Projekt eine neue Klasse namens **Notifications**, fügen Sie den Modifizierer **public** zur Klassendefinition hinzu, und fügen Sie die folgenden **using**-Anweisungen zur neuen Codedatei hinzu:

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.Storage;

3.  Kopieren Sie den folgenden Code in die neue **Notifications**-Klasse:

         private NotificationHub hub;

         public Notifications()
         {
             hub = new NotificationHub("<hub name>", "<connection string with listen access>");
         }

         public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
         {
             ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
             await SubscribeToCategories(categories);
         }

         public async Task SubscribeToCategories(IEnumerable<string> categories)
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
             await hub.RegisterNativeAsync(channel.Uri, categories);
         }

    Diese Klasse verwendet den lokalen Speicher, um Nachrichtenkategorien zu speichern, die das Gerät empfangen soll. Sie enthält zudem Methoden zum Registrieren dieser Kategorien.

4.  Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection string with listen access>` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben.

    **Hinweis**

    Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.

5.  Fügen Sie in der Datei "App.xaml.cs" folgende Eigenschaft zur Klasse **App** hinzu:

         public Notifications notifications = new Notifications();

    Diese Eigenschaft wird verwendet, um eine **Notifications**-Instanz zu erstellen und darauf zuzugreifen.

6.  Fügen Sie in "MainPage.xaml.cs" folgende Zeile hinzu:

         using Windows.UI.Popups;

7.  Fügen Sie in der Projektdatei "MainPage.xaml.cs" die folgende Methode hinzu:

         private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
         {
             var categories = new HashSet<string>();
             if (WorldToggle.IsOn) categories.Add("World");
             if (PoliticsToggle.IsOn) categories.Add("Politics");
             if (BusinessToggle.IsOn) categories.Add("Business");
             if (TechnologyToggle.IsOn) categories.Add("Technology");
             if (ScienceToggle.IsOn) categories.Add("Science");
             if (SportsToggle.IsOn) categories.Add("Sports");

             await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

             var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Diese Methode erstellt eine Liste von Kategorien und verwendet die **Notifications**-Klasse zum Speichern der Liste im lokalen Speicher sowie zum Registrieren der entsprechenden Tags bei Ihrem Notification Hub. Wenn Kategorien geändert werden, wird die Registrierung mit neuen Kategorien neu erstellt.

Die App kann jetzt verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert.

Registrieren für Benachrichtigungen
-----------------------------------

Durch diese Schritte findet beim Starten eine Registrierung beim Notification Hub statt, wobei die im lokalen Speicher gespeicherten Kategorien verwendet werden.

**Hinweis**

Da sich der durch den Windows Notification Service (WNS) zugeteilte Channel-URI jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.

1.  Fügen Sie der **Notifications**-Klasse folgenden Code hinzu:

         public IEnumerable<string> RetrieveCategories()
         {
             var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
             return categories != null ? categories.Split(','): new string[0];
         }

    Dadurch werden die in der Klasse definierten Kategorien zurückgegeben.

2.  Öffnen Sie die Datei "App.xaml.cs", und fügen Sie den Modifizierer **async** zur Methode **OnLaunched** hinzu.

3.  Suchen und ersetzen Sie in der Methode **OnLaunched** den vorhandenen Aufruf zur **InitNotificationsAsync**-Methode mit folgender Codezeile:

         await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    Dadurch wird sichergestellt, dass bei jedem Start der App die Kategorien vom lokalen Speicher abgerufen werden und eine Registrierung für diese Kategorien abgefragt wird. Die Methode **InitNotificationsAsync** wurde als Teil des Lernprogramms [Erste Schritt mit Notification Hubs] erstellt, wird in diesem Thema jedoch nicht benötigt.

4.  Fügen Sie in der Projektdatei "MainPage.xaml.cs" den folgenden Code zur Methode *OnNavigatedTo* hinzu:

         var categories = ((App)Application.Current).notifications.RetrieveCategories();

         if (categories.Contains("World")) WorldToggle.IsOn = true;
         if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
         if (categories.Contains("Business")) BusinessToggle.IsOn = true;
         if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
         if (categories.Contains("Science")) ScienceToggle.IsOn = true;
         if (categories.Contains("Sports")) SportsToggle.IsOn = true;

    Dadurch wird die Hauptseite basierend auf dem Status der zuvor gespeicherten Kategorien aktualisiert.

Die App kann ist jetzt vollständig und kann verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert. Als nächstes wird ein Back-End definiert, das Kategoriebenachrichtigungen an diese App senden kann.

Senden von BenachrichtigungenSenden von Benachrichtigungen von Ihrem Back-End aus
---------------------------------------------------------------------------------

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

Ausführen der Anwendung und Erzeugen von Benachrichtigungen
-----------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App zu starten.

    ![](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png)

    Die Benutzeroberfläche der App bietet verschiedene Umschaltmöglichkeiten, mit denen Sie die Kategorien auswählen können, die Sie abonnieren möchten.

2.  Aktivieren Sie eine oder mehrere Kategorien, und klicken Sie dann auf **Subscribe**.

    Die App konvertiert die ausgewählten Kategorien in Tags, und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an. Die registrierten Kategorien werden zurückgegeben und in einem Dialogfeld angezeigt.

    ![](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png)

3.  Verwenden Sie eine dieser Möglichkeiten, um eine neue Benachrichtigung vom Back-End zu versenden:

    -   **Konsolenanwendung:** Starten Sie die Konsolenanwendung.

    -   **Mobile Services:** Klicken Sie auf die Registerkarte **Scheduler**, klicken Sie auf die Aufgabe, und klicken Sie dann auf **Einmal ausführen**.

    Die Benachrichtigungen für die ausgewählten Kategorien werden als Popupbenachrichtigungen angezeigt.

    ![](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png)

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie erfahren, wie aktuelle Nachrichten nach Kategorie übermittelt werden. Sie können nun eines der folgenden Lernprogramme durchführen, die andere komplexe Notification Hubs-Szenarien zeigen:

-   [Verwenden von Notification Hubs zum Übertragen lokalisierter Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-localized-dotnet/)

    Hier erfahren Sie, wie Sie die App zu aktuellen Nachrichten für das Versenden von lokalisierten Benachrichtigungen erweitern.

-   [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users)

    Hier erfahren Sie, wie Sie Pushbenachrichtigungen an bestimmte authentifizierte Benutzer versenden. Dies ist eine gute Möglichkeit, um Benachrichtigungen nur an bestimmte Benutzer zu versenden.


<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /de-de/manage/services/notification-hubs/getting-started-windows-dotnet/
[Use Notification Hubs to broadcast localized breaking news]: /de-de/manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Notify users with Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users
[Mobile Service]: /de-de/develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/de-de/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591


