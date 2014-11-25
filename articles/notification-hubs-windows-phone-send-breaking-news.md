<properties linkid="notification-hubs-windows-phone-send-breaking-news" pageTitle="Use Notification Hubs to send breaking news (Windows Phone)" metaKeywords="" description="Use  Azure Notification Hubs to use tag in registrations to send breaking news to a Windows Phone app." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Use Notification Hubs to send breaking news" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone" class="current">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine Windows Phone 8.0/8.1 Silverlight-App senden können. Wenn Ihr Ziel eine Windows Store- oder Windows Phone 8.1-App ist, konsultieren Sie den Abschnitt zur [Windows Universal][1]-Version. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc.

Übertragungsszenarien werden durch das Einfügen von einem oder mehreren *Tags* möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung dieses Szenarios behandelt:

1.  [Hinzufügen der Kategorieauswahl zur App][Hinzufügen der Kategorieauswahl zur App]
2.  [Registrieren für Benachrichtigungen][Registrieren für Benachrichtigungen]
3.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]
4.  [Ausführen der Anwendung und Erzeugen von Benachrichtigungen][Ausführen der Anwendung und Erzeugen von Benachrichtigungen]

Dieses Thema baut auf die App auf, die Sie in [Erste Schritte mit Notification Hubs][Erste Schritte mit Notification Hubs] erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs][Erste Schritte mit Notification Hubs] abgeschlossen haben.

## <a name="adding-categories"></a>Hinzufügen der Kategorieauswahl zur App

Der erste Schritt besteht daraus, Benutzeroberflächenelemente zur vorhandenen Hauptseite hinzuzufügen, welche dem Benutzer die Auswahl der Kategorien für die Registrierung ermöglichen. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Wenn die App gestartet wird, wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt.

1.  Öffnen Sie die Datei "MainPage.xaml", und ersetzen Sie die **Grid**-Elemente namens `TitlePanel` und `ContentPanel` durch den folgenden Code:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2.  Erstellen Sie in dem Projekt eine neue Klasse namens **Notifications**, fügen Sie den Modifizierer **public** zur Klassendefinition hinzu, und fügen Sie die folgenden **using**-Anweisungen zur neuen Codedatei hinzu:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;

3.  Kopieren Sie den folgenden Code in die neue **Notifications**-Klasse:

        private NotificationHub hub;

        public Notifications()
        {
            hub = new NotificationHub("<hub name>", "<connection string with listen access>");
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            await SubscribeToCategories(categories);
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories)
        {
            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
            }

            await hub.RegisterNativeAsync(channel.ChannelUri.ToString(), categories);
        }

    Diese Klasse verwendet den lokalen Speicher, um Nachrichtenkategorien zu speichern, die das Gerät empfangen soll. Sie enthält zudem Methoden zum Registrieren dieser Kategorien.

4.  Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection string with listen access>` durch den Namen des Notification Hub und die Verbindungszeichenfolge für *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben.

    <div class="dev-callout"><strong>Hinweis</strong> 
    <p>Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schl&uuml;ssel f&uuml;r den Abh&ouml;rzugriff mit Ihrer Client-App verteilen. Der Abh&ouml;rzugriff erm&ouml;glicht der App, sich f&uuml;r Benachrichtigungen zu registrieren, aber es k&ouml;nnen keine vorhandenen Registrierungen ge&auml;ndert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschl&uuml;ssel wird in einem gesicherten Back-End-Dienst f&uuml;r das Versenden von Benachrichtigungen und das &Auml;ndern vorhandener Benachrichtigungen verwendet.</p>
</div>

5.  Fügen Sie in der Datei "App.xaml.cs" folgende Eigenschaft zur Klasse **App** hinzu:

        public Notifications notifications = new Notifications();

    Diese Eigenschaft wird verwendet, um eine **Notifications**-Instanz zu erstellen und darauf zuzugreifen.

6.  Fügen Sie in "MainPage.xaml.cs" folgende Zeile hinzu:

        using Windows.UI.Popups;

7.  Fügen Sie in der Projektdatei "MainPage.xaml.cs" die folgende Methode hinzu:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldCheckBox.IsChecked == true) categories.Add("World");
            if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
            if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
            if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
            if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
            if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            MessageBox.Show("Subscribed to: " + string.Join(",", categories));
        }

    Diese Methode erstellt eine Liste von Kategorien und verwendet die **Notifications**-Klasse zum Speichern der Liste im lokalen Speicher sowie zum Registrieren der entsprechenden Tags bei Ihrem Notification Hub. Wenn Kategorien geändert werden, wird die Registrierung mit neuen Kategorien neu erstellt.

Die App kann jetzt verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert.

## <a name="register"></a>Registrieren für Benachrichtigungen

Durch diese Schritte findet beim Starten eine Registrierung beim Notification Hub statt, wobei die im lokalen Speicher gespeicherten Kategorien verwendet werden.

<div class="dev-callout"><strong>Hinweis</strong> 
    <p>Da sich der durch den Microsoft Push Notification Service (MPNS) zugeteilte Channel-URI jederzeit &auml;ndern kann, sollten Sie sich regelm&auml;&szlig;ig f&uuml;r Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal f&uuml;r Benachrichtigungen, wenn die App gestartet wird. F&uuml;r h&auml;ufig ausgef&uuml;hrte Anwendungen (&ouml;fters als einmal t&auml;glich) k&ouml;nnen Sie die Registrierung wahrscheinlich &uuml;berspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.</p>
</div>

1.  Fügen Sie der **Notifications**-Klasse folgenden Code hinzu:

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

    Dadurch werden die in der Klasse definierten Kategorien zurückgegeben.

2.  Öffnen Sie die Datei "App.xaml.cs", und fügen Sie den Modifizierer **async** zur Methode **Application\_Launching** hinzu.

3.  Suchen und ersetzen Sie in der Methode **Application\_Launching** den vorhandenen Notification Hub-Registrierungscode, den Sie in [Erste Schritte mit Notification Hubs][Erste Schritte mit Notification Hubs] erstellt haben, durch die folgende Codezeile:

        await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    Dadurch wird sichergestellt, dass bei jedem Start der App die Kategorien vom lokalen Speicher abgerufen werden und eine Registrierung für diese Kategorien abgefragt wird.

4.  Fügen Sie in der Projektdatei "MainPage.xaml.cs" den folgenden Code hinzu, der die Methode **OnNavigatedTo** implementiert:

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }

    Dadurch wird die Hauptseite basierend auf dem Status der zuvor gespeicherten Kategorien aktualisiert.

Die App kann ist jetzt vollständig und kann verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert. Als nächstes wird ein Back-End definiert, das Kategoriebenachrichtigungen an diese App senden kann.

## <a name="send"></a><span class="short-header">Senden von Benachrichtigungen</span>Senden von Benachrichtigungen von Ihrem Back-End aus

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>Ausführen der Anwendung und Erzeugen von Benachrichtigungen

1.  Drücken Sie in Visual Studio die Taste F5, um die App zu starten.

    ![][0]

    Die Benutzeroberfläche der App bietet verschiedene Umschaltmöglichkeiten, mit denen Sie die Kategorien auswählen können, die Sie abonnieren möchten.

2.  Aktivieren Sie eine oder mehrere Kategorien, und klicken Sie dann auf **Subscribe**.

    Die App konvertiert die ausgewählten Kategorien in Tags, und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an. Die registrierten Kategorien werden zurückgegeben und in einem Dialogfeld angezeigt.

    ![][2]

3.  Verwenden Sie eine dieser Möglichkeiten, um eine neue Benachrichtigung vom Back-End zu versenden:

    -   **Konsolenanwendung:** Starten Sie die Konsolenanwendung.

    -   **Java/PHP:** Führen Sie die App/das Skript aus.

    Die Benachrichtigungen für die ausgewählten Kategorien werden als Popupbenachrichtigungen angezeigt.

    ![][3]

Sie haben dieses Lernprogramm nun abgeschlossen.

<!--## <a name="next-steps"> </a>Next steps
In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:
+ [Use Notification Hubs to broadcast localized breaking news]

	Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

	Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.

-->



<!-- URLs.-->

  [Windows Universal]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ "Windows Universal"
  [1]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
  [Hinzufügen der Kategorieauswahl zur App]: #adding-categories
  [Registrieren für Benachrichtigungen]: #register
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [Ausführen der Anwendung und Erzeugen von Benachrichtigungen]: #test-app
  [Erste Schritte mit Notification Hubs]: /de-de/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [0]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
  [2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
  [3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png
