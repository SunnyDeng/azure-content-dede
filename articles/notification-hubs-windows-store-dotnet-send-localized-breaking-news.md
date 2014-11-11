<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Verwenden von Notification Hubs zum Senden lokalisierter Nachrichten

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit der **Vorlagen**-Funktion von Azure Notification Hubs Benachrichtigungen senden können, die je nach Sprache und Gerät lokalisiert wurden. In diesem Lernprogramm beginnen Sie mit der Windows Store-App, die Sie in [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten][Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] erstellt haben. Sie werden anschließend in der Lage sein, sich für Kategorien zu registrieren, die Sie interessieren, eine Sprache für die Benachrichtigungen auszuwählen und nur Pushbenachrichtigungen für diese Kategorien in der jeweiligen Sprache zu empfangen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung dieses Szenarios behandelt:

1.  [Konzepte von Vorlagen][Konzepte von Vorlagen]
2.  [Die App-Benutzeroberfläche][Die App-Benutzeroberfläche]
3.  [Erstellen der Windows Store-Client-App][Erstellen der Windows Store-Client-App]
4.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]

Dieses Szenario besteht aus zwei Teilen:

-   In der Windows Store-App können Client-Geräte eine Sprache auswählen und verschiedene Nachrichtenkategorien abonnieren;

-   Das Back-End verteilt die Benachrichtigungen mithilfe der **tag**- und **template**-Features von Azure Notification Hubs.

## Voraussetzungen

Sie müssen zuvor das Lernprogramm [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten][Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] abschließen und den Code verfügbar haben, da dieses Lernprogramm direkt auf dem Code aufbaut.

Außerdem benötigen Sie Visual Studio 2012.

## <a name="concepts"></a><span class="short-header">Konzepte</span>Konzepte von Vorlagen

Im Lernprogramm [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten][Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] haben Sie eine App erstellt, in der Benutzer mit **Tags** Benachrichtigungen für verschiedene Nachrichtenkategorien abonnieren können.
Viele Apps richten sich jedoch an verschiedene Märkte und müssen lokalisiert werden. In diesen Fällen muss auch der Inhalt der Benachrichtigungen lokalisiert und an die korrekten Geräte ausgeliefert werden.
In diesem Thema erfahren Sie, wie Sie mit der **Vorlagen**-Funktion von Notification Hubs lokalisierte Benachrichtigungen für aktuelle Nachrichten problemlos verschicken können.

Hinweis: Sie können mehrere Versionen der einzelnen Tags erstellen, um lokalisierte Benachrichtigungen zu verschicken. Für Englisch, Französisch und Mandarin müssten Sie z. B. drei verschiedene Tags für Weltnachrichten erstellen: "world\_en", "world\_fr" und "world\_ch". Anschließend müssten Sie eine lokalisierte Version der Nachrichten an die einzelnen Tags schicken. Dieser Artikel verwendet Vorlagen, um die Anzahl der Tags einzugrenzen und den Versand mehrerer Nachrichten zu vermeiden.

Mit Vorlagen können Sie auf einer hohen Ebene festlegen, wie ein bestimmtes Gerät eine Benachrichtigung empfangen soll. Die Vorlage gibt das exakte Format der Nutzlast anhand von Eigenschaften an, die Teil der von Ihrem Back-End verschickten Nachricht sind. In unserem Fall senden wir eine sprachenunabhängige Nachricht, die alle unterstützten Sprachen enthält:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Anschließend stellen wir sicher, dass sich die Geräte mit einer Vorlage registrieren, die auf die korrekte Eigenschaft verweist. Eine Windows Store-App, die einfache Popup-Nachrichten empfangen möchte, registriert sich z. B. für die folgende Vorlage:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>

Vorlagen sind ein mächtiges Werkzeug, und Sie finden weitere Informationen in unserem [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden]. Sie finden eine Referenz für die Vorlagen-Ausdruckssprache unter [Notification Hub-Informationen für Windows Store][Notification Hub-Informationen für Windows Store].

## <a name="ui"></a><span class="short-header">App-Benutzeroberfläche</span>Die App-Benutzeroberfläche

Wir werden nun die App zu aktuellen Nachrichten aus dem Thema [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten][Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] so modifizieren, dass aktuelle Nachrichten mit Vorlagen verschickt werden.

Um lokalisierte Nachrichten empfangen zu können, müssen Sie die *native*-Registrierungen (d. h. die Registrierungen, in denen Sie eine Vorlage angeben) durch Vorlagen-Registrierungen ersetzen.

In Ihrer Windows Store-App:

Fügen Sie ein Sprachen-Kombinationsfeld zu Ihrer MainPage.xaml hinzu:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
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
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

## <a name="building-client"></a><span class="building app">App-Benutzeroberfläche</span>Erstellen der Windows Store-Client-App

1.  Fügen Sie in Ihrer Notifications-Klasse einen locale-Parameter zu den Methoden *StoreCategoriesAndSubscribe* und *SubscribeToCateories* hinzu.

        public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

    Beachten Sie, dass wir anstelle der Methode *RegisterNativeAsync* nun *RegisterTemplateAsync* aufrufen: Wir registrieren ein bestimmtes Benachrichtigungsformat, dessen Vorlage von der Sprache abhängt. Außerdem geben wir einen Namen für die Vorlage an ("newsTemplate"), da wir uns möglicherweise für mehr als eine Vorlage registrieren möchten (z. B. eine für Popupbenachrichtigungen und eine für Tiles), und wir brauchen den Namen, um die Registrierungen ändern oder löschen zu können.

    Wenn sich ein Gerät für mehrere Vorlagen mit demselben Tag registriert, werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Dies ist hilfreich, um dieselbe logische Nachricht in mehreren visuellen Darstellungen anzuzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.

2.  Fügen Sie die folgende Methode hinzu, um die gespeicherte Sprache abzurufen:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3.  Aktualisieren Sie in Ihrer MainPage.xaml.cs den Klick-Handler, indem Sie den aktuellen Wert des Sprachen-Kombinationsfelds abrufen und im Aufruf an die Notifications-Klasse angeben:

         var locale = (string)Locale.SelectedItem;

         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4.  Stellen Sie zuletzt in der Datei "App.xaml.cs" sicher, dass Sie Ihren Aufruf
    an den Notifications-Singleton in der Methode *OnLaunched* ändern:

        Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());

## <a name="send"></a><span class="short-header">Senden von lokalisierten Benachrichtigungen</span>Senden von lokalisierten Benachrichtigungen aus Ihrem Back-End

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]

## Nächste Schritte

Weitere Informationen finden Sie unter [Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET][Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET], [Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services][Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services] sowie unter [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden]. Sie finden eine Referenz für die Vorlagen-Ausdruckssprache unter [Notification Hub-Informationen für Windows Store][Notification Hub-Informationen für Windows Store].



  [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
  [Konzepte von Vorlagen]: #concepts
  [Die App-Benutzeroberfläche]: #ui
  [Erstellen der Windows Store-Client-App]: #building-client
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
  [Notification Hub-Informationen für Windows Store]: http://msdn.microsoft.com/de-de/library/jj927172.aspx
  [notification-hubs-localized-back-end]: ../includes/notification-hubs-localized-back-end.md
  [Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  [Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services]: /de-de/manage/services/notification-hubs/notify-users
