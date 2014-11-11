<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-iOS" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial for iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications (iOS)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news to iOS devices" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Verwenden von Notification Hubs zum Senden von lokalisierten Nachrichten an iOS-Geräte

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit der **Vorlagen**-Funktion von Azure Notification Hubs Benachrichtigungen senden können, die je nach Sprache und Gerät lokalisiert wurden. In diesem Lernprogramm beginnen Sie mit der Windows Store-App, die Sie in [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten][Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] erstellt haben. Sie werden anschließend in der Lage sein, sich für Kategorien zu registrieren, die Sie interessieren, eine Sprache für die Benachrichtigungen auszuwählen und nur Pushbenachrichtigungen für diese Kategorien in der jeweiligen Sprache zu empfangen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung dieses Szenarios behandelt:

1.  [Konzepte von Vorlagen][Konzepte von Vorlagen]
2.  [Die App-Benutzeroberfläche][Die App-Benutzeroberfläche]
3.  [Erstellen der iOS-App][Erstellen der iOS-App]
4.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]

Dieses Szenario besteht aus zwei Teilen:

-   Mit der iOS-App können Client-Geräte eine Sprache auswählen und verschiedene Nachrichtenkategorien abonnieren;

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

Anschließend stellen wir sicher, dass sich die Geräte mit einer Vorlage registrieren, die auf die korrekte Eigenschaft verweist. Eine iOS-App, die eine Registrierung für französische Nachrichten durchführen möchte, registriert beispielsweise Folgendes:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Vorlagen sind ein mächtiges Werkzeug, und Sie finden weitere Informationen in unserem [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden]. Eine Referenz für die Vorlagenausdruckssprache finden Sie unter [Vorgehensweise: Service Bus Notification Hubs (iOS-Apps)][Vorgehensweise: Service Bus Notification Hubs (iOS-Apps)].

## <a name="ui"></a><span class="short-header">App-Benutzeroberfläche</span>Die App-Benutzeroberfläche

Wir werden nun die App zu aktuellen Nachrichten aus dem Thema [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten][Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] so modifizieren, dass aktuelle Nachrichten mit Vorlagen verschickt werden.

Fügen Sie in "MainStoryboard\_iPhone.storyboard" ein segmentiertes Steuerelement mit den drei unterstützten Sprachen hinzu: Englisch, Französisch und Mandarin.

![][0]

Fügen Sie dann auf jeden Fall wie unten gezeigt ein IBOutlet in "ViewController.h" hinzu:

![][1]

## <a name="building-client"></a><span class="building app">Anwendungs-UI</span>Erstellen der iOS-App

Um lokalisierte Nachrichten empfangen zu können, müssen Sie die *native*-Registrierungen (d. h. die Registrierungen, in denen Sie eine Vorlage angeben) durch Vorlagen-Registrierungen ersetzen.

1.  Fügen Sie in "Notification.h" die *retrieveLocale*-Methode hinzu. Ändern Sie dann die store- und subscribe-Methoden wie unten gezeigt:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    Ändern Sie in "Notification.m" die *storeCategoriesAndSubscribe*-Methode, indem Sie den locale-Parameter hinzufügen und ihn in den Benutzerstandards speichern:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Ändern Sie dann die *subscribe*-Methode, um das Gebietsschema einzuschließen:

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Beachten Sie, dass jetzt die *registerTemplateWithDeviceToken*-Methode statt der *registerNativeWithDeviceToken*-Methode verwendet wird. Bei der Registrierung für eine Vorlage muss die json-Vorlage und ein Name für die Vorlage bereitgestellt werden (da die App möglicherweise unterschiedliche Vorlagen registrieren möchte). Registrieren Sie Ihre Kategorien auf jeden Fall als Tags, da Sie so sicherstellen, dass Sie Benachrichtigungen für diese Nachrichten erhalten.

    Fügen Sie schließlich eine Methode hinzu, um das Gebietsschema aus den Benutzerstandardeinstellungen abzurufen:

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2.  Da die Benachrichtigungsklasse jetzt geändert wurde, müssen Sie sicherstellen, dass der ViewController das neue UISegmentControl verwendet. Fügen Sie in der *viewDidLoad*-Methode die folgende Zeile hinzu, um sicherzustellen, dass das aktuell ausgewählte Gebietsschema angezeigt wird:

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Ändern Sie dann in der *subscribe*-Methode den Aufruf von *storeCategoriesAndSubscribe* in Folgendes:

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3.  Schließlich müssen Sie die *didRegisterForRemoteNotificationsWithDeviceToken*-Methode in "AppDelegate.m" aktualisieren, damit Ihre Registrierung beim Starten der App korrekt aktualisiert wird. Ändern Sie den Aufruf der *subscribe*-Methode für Benachrichtigungen folgendermaßen:

        NSSet* categories = [notifications retrieveCategories];
        int locale = [notifications retrieveLocale];
        [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="send"></a><span class="short-header">Senden von lokalisierten Benachrichtigungen</span>Senden von lokalisierten Benachrichtigungen aus Ihrem Back-End

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]

## Nächste Schritte

Weitere Informationen zum Verwenden von Vorlagen finden Sie unter:

-   [Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET][Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET]
-   [Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services][Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services]
-   [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden]

Sie finden eine Referenz für die Vorlagen-Ausdruckssprache unter [Notification Hub-Informationen für iOS][Vorgehensweise: Service Bus Notification Hubs (iOS-Apps)].

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-ios
  [Konzepte von Vorlagen]: #concepts
  [Die App-Benutzeroberfläche]: #ui
  [Erstellen der iOS-App]: #building-client
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
  [0]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
  [1]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png
  [notification-hubs-localized-back-end]: ../includes/notification-hubs-localized-back-end.md
  [Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  [Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services]: /de-de/manage/services/notification-hubs/notify-users
