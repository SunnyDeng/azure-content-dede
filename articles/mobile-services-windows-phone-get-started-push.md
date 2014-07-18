<properties pageTitle="Get started with push notifications (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")

[.NET-Back-End](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET-Back-End") | [JavaScript-Back-End](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript-Back-End")

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Pushbenachrichtigungen an eine Windows Phone 8-App senden können. In diesem Lernprogramm fügen Sie dem Schnellstart-Projekt mit dem Microsoft-Pushbenachrichtigungsdienst (MPNS) Pushbenachrichtigungen hinzu. Nach Abschluss des Lernprogramms sendet Ihr mobiler Dienst bei jedem Einfügen eines Datensatzes eine Pushbenachrichtigung.

> [WACOM.NOTE]Mobile Services sind jetzt in Azure Notification Hubs zur Unterstützung weiterer Pushbenachrichtigungsfunktionen, wie Vorlagen, mehrere Plattformen und Skalierung, integriert. Diese integrierten Funktionen befinden sich gegenwärtig in einem Vorschaustadium. Weitere Informationen finden Sie in dieser Version von [Erste Schritte mit Pushbenachrichtigungen](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Registrations-Tabelle](#create-table)
2.  [Hinzufügen von Pushbenachrichtigungen zu der App](#add-push)
3.  [Aktualisieren von Skripten zum Senden von Pushbenachrichtigungen](#update-scripts)
4.  [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm benötigen Sie [Visual Studio 2012 Express für Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) oder eine später Version.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

> [WACOM.NOTE]Wenn Sie täglich mehr als 500 Nachrichten pro Benutzer senden, müssen Sie stattdessen Notification Hubs verwenden. Weitere Informationen finden Sie unter [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/).

Erstellen einer neuen Tabelle
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Hinzufügen von PushbenachrichtigungenHinzufügen von Pushbenachrichtigungen zu Ihrer App
---------------------------------------------------------------------------------------

1.  Öffnen Sie in Visual Studio die Projektdatei "MainPage.xaml.cs", und fügen Sie den folgenden Code hinzu, der eine neue **Registrations**-Klasse erstellt:

         public class Registrations
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "handle")]
             public string Handle { get; set; }
         }

    Mit der "Handle"-Eigenschaft wird der Kanal-URI gespeichert.

2.  Öffnen Sie die Datei "App.xaml.cs", und fügen Sie die folgende "using"-Anweisung hinzu:

         using Microsoft.Phone.Notification;

3.  Fügen Sie der Datei "App.xaml.cs" folgenden Code hinzu:

         public static HttpNotificationChannel CurrentChannel { get; private set; }

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellTile();
             }
                      
            IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
            var registration = new Registrations { Handle = CurrentChannel.Uri };
            await registrationsTable.InsertAsync(registration);
         }

        Dieser Code reserviert und speichert einen Kanal für ein Pushbenachrichtigungsabonnement und bindet ihn an die Standardkachel der App.

    **Hinweis**

    In diesem Lernprogramm sendet der mobile Dienst eine Benachrichtigung zum Umdrehen der Kachel an das Gerät. Wenn Sie eine Popupbenachrichtigung senden, müssen Sie stattdessen die **BindToShellToast**-Methode für den Kanal aufrufen. Um Popup- und Kachelbenachrichtigungen zu unterstützen, rufen Sie beide Methoden, **BindToShellTile** und **BindToShellToast**, auf.

4.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **Application\_Launching**-Ereignishandlers den folgenden Aufruf der neuen **AcquirePushChannel**-Methode hinzu:

         AcquirePushChannel();

        Damit wird sichergestellt, dass die **CurrentChannel**-Eigenschaft bei jedem Aufruf der Anwendung initialisiert wird.

5.  Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie die **ID\_\_*CAP*\_\_PUSH\_NOTIFICATION**-Funktion.

![](./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png) Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen empfangen kann.

Aktualisieren des EinfügeskriptsAktualisieren des registrierten Einfügeskripts im Verwaltungsportal
---------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Klicken Sie auf **TodoItem**, dann auf **Skript**, und wählen Sie **Einfügen** aus.

        ![][10]

2.  Ersetzen Sie die "insert"-Funktion durch den folgenden Code, und klicken Sie dann auf **Speichern**:

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
                            push.mpns.sendFlipTile(registration.uri, {
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

Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

1.  Wählen Sie in Visual Studio im Menü **Build** den Befehl **Projektmappe bereitstellen**.

2.  Streifen Sie im Emulator nach links, um die Liste der installierten Apps anzuzeigen, und suchen Sie die neue App **TodoList**.

3.  Tippen Sie auf das App-Symbol, und halten Sie den Finger darauf, und wählen Sie dann aus dem Kontextmenü **pin to start**.

![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png) Damit wird die Kachel **TodoList** an das Startmenü angeheftet.

1.  Tippen Sie auf die Kachel **TodoList**, um die App zu starten.

![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png)

1.  Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Save**.

        ![][4]

Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet.

1.  Drücken Sie auf die **Start**-Schaltfläche, um zum Startmenü zurückzukehren.

![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png) Die Anwendung hat die Pushbenachrichtigung empfangen, und der Titel der Kachel lautet nun **hello push**.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

-   [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)
    Informationen zur Nutzung von Notification Hubs in Ihrer Windows Store-App.

-   [Was sind Notification Hubs?](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    Informationen zum Erstellen und Senden von Benachrichtigungen an Benutzer auf mehreren Plattformen.

-   [Senden von Benachrichtigungen an Abonnenten](/en-us/manage/services/notification-hubs/breaking-news-wp8/)
    Informationen zum Registrieren und Empfangen von Pushbenachrichtigungen zu bestimmten Kategorien durch Benutzer.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    Informationen zur Authentifizierung von Benutzern Ihrer App mit Windows-Konten.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    Informationen zum Registrieren und Verwenden von Serverskripten.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    Informationen zur Verwendung von Mobile Services mit .NET.


