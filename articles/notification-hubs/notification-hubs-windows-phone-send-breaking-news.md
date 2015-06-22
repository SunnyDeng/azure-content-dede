<properties 
	pageTitle="Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten (Windows Phone)" 
	description="Verwenden Sie Azure Notification Hubs zum Verwenden von Tags in Registrierungen, um aktuelle Nachrichten an eine Windows Phone-App zu senden." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/03/2014" 
	ms.author="glenga"/>

# Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="notification-hubs-windows-store-dotnet-send-breaking-news.md" title="Windows Universal">Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a><a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine Windows Phone 8.0/8.1 Silverlight-App senden können. Wenn Ihr Ziel eine Windows Store- oder Windows Phone 8.1-App ist, lesen Sie den Abschnitt zur [Windows Universal](notification-hubs-windows-store-dotnet-send-breaking-news.md)-Version. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc. 

Übertragungsszenarios werden durch das Einfügen von einem oder mehreren _Tags_ möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs-Leitfaden]. 

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung dieses Szenarios behandelt:

1. [Hinzufügen der Kategorieauswahl zur App]
2. [Registrieren für Benachrichtigungen]
3. [Senden von Benachrichtigungen vom Back-End aus]
4. [Ausführen der App und Erzeugen von Benachrichtigungen]

Dieses Thema baut auf der App auf, die Sie in [Erste Schritte mit Notification Hubs] erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs] abgeschlossen haben.

##<a name="adding-categories"></a>Hinzufügen der Kategorieauswahl zur App

Der erste Schritt besteht daraus, Benutzeroberflächenelemente zur vorhandenen Hauptseite hinzuzufügen, welche dem Benutzer die Auswahl der Kategorien für die Registrierung ermöglichen. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Beim Starten der App wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt. 

1. Öffnen Sie die Datei "MainPage.xaml", und ersetzen Sie die **Grid**-Elemente `TitlePanel` und `ContentPanel` durch den folgenden Code:
			
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

2. Erstellen Sie im Projekt eine neue Klasse namens **Notifications**, fügen Sie der Klassendefinition den Modifizierer **public** hinzu, und fügen Sie der neuen Codedatei die folgenden **using**-Anweisungen hinzu:

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;
		using System.IO.IsolatedStorage;

3. Kopieren Sie den folgenden Code in die neue **Notifications**-Klasse:

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

4. Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection string with listen access>` durch den Namen des Notification Hubs und die Verbindungszeichenfolge für  *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben.

	> [AZURE.NOTE] Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.

4. Fügen Sie in der Projektdatei "App.xaml.cs" der **App**-Klasse die folgende Eigenschaft hinzu:

		public Notifications notifications = new Notifications();

	Diese Eigenschaft wird verwendet, um eine **Notifications**-Instanz zu erstellen und darauf zuzugreifen.

5. Fügen Sie in "MainPage.xaml.cs" folgende Zeile hinzu:

		using Windows.UI.Popups;

6. Fügen Sie in der Projektdatei "MainPage.xaml.cs" die folgende Methode hinzu:

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

##<a name="register"></a>Registrieren für Benachrichtigungen

Durch diese Schritte findet beim Starten eine Registrierung beim Notification Hub statt, wobei die im lokalen Speicher gespeicherten Kategorien verwendet werden. 

> [AZURE.NOTE] Da sich der durch den Microsoft Push Notification Service (MPNS) zugeteilte Channel-URI jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.

1. Fügen Sie der **Notifications**-Klasse folgenden Code hinzu:

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

	Dadurch werden die in der Klasse definierten Kategorien zurückgegeben.

1. Öffnen Sie die Datei "App.xaml.cs", und fügen Sie den Modifizierer **async** der **Application_Launching**-Methode hinzu.

2. Suchen Sie in der **Application_Launching**-Methode den Registrierungscode des Notification Hubs, den Sie in [Erste Schritte mit Notification Hubs] erstellt haben, und ersetzen Sie ihn durch die folgende Codezeile:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	Dadurch wird sichergestellt, dass bei jedem Start der App die Kategorien vom lokalen Speicher abgerufen werden und eine Registrierung für diese Kategorien abgefragt wird. 

3. Fügen Sie in der Projektdatei "MainPage.xaml.cs" den folgenden Code hinzu, der die **OnNavigatedTo**-Methode implementiert:

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

Die App ist jetzt vollständig und kann verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert. Als nächstes wird ein Back-End definiert, das Kategoriebenachrichtigungen an diese App senden kann.

<h2><a name="send"></a>Senden von Benachrichtigungen vom Back-End aus</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Ausführen der App und Erzeugen von Benachrichtigungen

1. Drücken Sie in Visual Studio die Taste F5, um die App zu starten.

	![][1] 

	Die Benutzeroberfläche der App bietet verschiedene Umschaltmöglichkeiten, mit denen Sie die Kategorien auswählen können, die Sie abonnieren möchten. 

2. Aktivieren Sie eine oder mehrere Kategorien, und klicken Sie dann auf **Abonnieren**.

	Die App konvertiert die ausgewählten Kategorien in Tags und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an. Die registrierten Kategorien werden zurückgegeben und in einem Dialogfeld angezeigt.

	![][2]

4. Verwenden Sie eine dieser Möglichkeiten, um eine neue Benachrichtigung vom Back-End zu versenden:

	+ **Konsolenanwendung:** Starten Sie die Konsolenanwendung.

	+ **Java/PHP:** Führen Sie die App bzw. das Skript aus.

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

<!-- Anchors. -->
[Hinzufügen der Kategorieauswahl zur App]: #adding-categories
[Registrieren für Benachrichtigungen]: #register
[Senden von Benachrichtigungen vom Back-End aus]: #send
[Ausführen der App und Erzeugen von Benachrichtigungen]: #test-app
[Nächste Schritte]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Erste Schritte mit Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]: ../breakingnews-localized-wp8.md 
[Benachrichtigen von Benutzern mit Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs - Anleitungen für Windows Phone]: ??

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/





<!--HONumber=49--> 