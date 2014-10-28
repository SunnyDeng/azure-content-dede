<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript-Backend" class="current">JavaScript-Backend</a>
</div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Windows Phone 8-App senden.
In diesem Lernprogramm fügen Sie dem Schnellstartprojekt mithilfe des Microsoft-Pushbenachrichtigungsdiensts (MPNS) Pushbenachrichtigungen hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE]In diesem Thema werden *vorhandene* mobile Dienste behandelt, die noch nicht für die Verwendung der Notification Hubs-Integration *aktualisiert* wurden. Wenn Sie einen *neuen* mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].
>
> Mobile Services werden in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung unterstützt werden können. *Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren*. Nach dem Upgrade finden Sie weitere Informationen in dieser Version von [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Registrations-Tabelle][Erstellen der Registrations-Tabelle]
2.  [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
3.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
4.  [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm benötigen Sie [Visual Studio 2012 Express für Windows Phone][Visual Studio 2012 Express für Windows Phone] oder eine später Version.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

> [WACOM.NOTE]Wenn Sie täglich mehr als 500 Nachrichten pro Benutzer senden, müssen Sie stattdessen Notification Hubs verwenden. Weitere Informationen finden Sie unter [Erste Schritte mit Notification Hubs][Erste Schritte mit Notification Hubs].

## <a name="create-table"></a>Erstellen einer neuen Tabelle

[WACOM.INCLUDE [mobile-services-create-new-push-table][mobile-services-create-new-push-table]]

## <a name="add-push"></a><span class="short-header">Hinzufügen von Pushbenachrichtigungen</span>Hinzufügen von Pushbenachrichtigungen zu Ihrer App

1.  Öffnen Sie in Visual Studio die Projektdatei "MainPage.xaml.cs", und fügen Sie den folgenden Code hinzu, der eine neue **Registrations**-Klasse erstellt:

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    Mit der "Handle"-Eigenschaft wird der Kanal-URI gespeichert.

2.  Öffnen Sie die Datei App.xaml.cs, und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.Phone.Notification;

3.  Fügen Sie der Datei "App.xaml.cs" folgenden Code hinzu:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
           await registrationsTable.InsertAsync(registration);
        }

    Dieser Code reserviert und speichert einen Kanal für ein Pushbenachrichtigungsabonnement und bindet ihn an die Standardkachel der App.

    <div class="dev-callout"><b>Hinweis</b>
    <p>In diesem Lernprogramm sendet der mobile Dienst eine Benachrichtigung zum Umdrehen der Kachel an das Ger&auml;t. Wenn Sie eine Popupbenachrichtigung senden, m&uuml;ssen Sie stattdessen die <strong>BindToShellToast</strong>-Methode f&uuml;r den Kanal aufrufen. Um Popup- und Kachelbenachrichtigungen zu unterst&uuml;tzen, rufen Sie beide Methoden, <strong>BindToShellTile</strong> und <strong>BindToShellToast</strong>, auf. </p>
</div>

4.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **Application\_Launching**-Ereignishandlers den folgenden Aufruf der neuen **AcquirePushChannel**-Methode hinzu:

        AcquirePushChannel();

    Damit wird sichergestellt, dass die **CurrentChannel**-Eigenschaft bei jedem Aufruf der Anwendung initialisiert wird.

5.  Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie die Funktion \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\*.

    ![][]

    Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen empfangen kann.

## <a name="update-scripts"></a><span class="short-header">Aktualisieren des Einfügeskripts</span>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-update-registrations-script][mobile-services-update-registrations-script]]

1.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

    ![][1]

2.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.mpns.sendFlipTile(registration.handle, {
                                title: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Kanäle, die in der **Registrations**-Tabelle gespeichert sind.

## <a name="test"></a><span class="short-header">Testen der App</span>Testen von Pushbenachrichtigungen in Ihrer App

1.  Wählen Sie in Visual Studio im Menü **Build** den Befehl **Projektmappe bereitstellen**.

2.  Streifen Sie im Emulator nach links, um die Liste der installierten Apps anzuzeigen, und suchen Sie die neue App **TodoList**.

3.  Tippen Sie auf das App-Symbol, und halten Sie den Finger darauf, und wählen Sie dann aus dem Kontextmenü **pin to start**.

    ![][2]

    Damit wird die Kachel **TodoList** an das Startmenü angeheftet.

4.  Tippen Sie auf die Kachel **TodoList**, um die App zu starten.

    ![][3]

5.  Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Save**.

    ![][4]

    Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet.

6.  Drücken Sie auf die **Start**-Schaltfläche, um zum Startmenü zurückzukehren.

    ![][5]

    Die Anwendung hat die Pushbenachrichtigung empfangen, und der Titel der Kachel lautet nun **hello push**.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

-   [Erste Schritte mit Benachrichtigungshubs][Erste Schritte mit Benachrichtigungshubs]
    Lernen Sie, wie Sie Benachrichtigungshubs in Ihrer Windows Store-App einsetzen können.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Informationen zum Erstellen und Senden von Pushbenachrichtigungen an Benutzer auf mehreren Plattformen.

-   [Senden von Benachrichtigungen an Abonnenten][Senden von Benachrichtigungen an Abonnenten]
    Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

<!--+ [Send notifications to users]     <br/>Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript-Backend"
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Erstellen der Registrations-Tabelle]: #create-table
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Visual Studio 2012 Express für Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-wp8
  [Erste Schritte mit Notification Hubs]: /de-de/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  []: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [Erste Schritte mit Benachrichtigungshubs]: /de-de/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [Was sind Notification Hubs?]: /de-de/develop/net/how-to-guides/service-bus-notification-hubs/
  [Senden von Benachrichtigungen an Abonnenten]: /de-de/manage/services/notification-hubs/breaking-news-wp8/
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-wp8
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library/
