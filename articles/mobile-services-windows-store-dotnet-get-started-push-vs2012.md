<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services mit Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows Store C#" class="current">Windows Store C#</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Windows Store-App senden.
In diesem Lernprogramm fügen Sie dem Schnellstartprojekt mithilfe des Windows-Pushbenachrichtigungsdiensts (WNS) Pushbenachrichtigungen hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

<div class="dev-callout"><b>Hinweis</b>
    <p>In diesem Lernprogramm werden Pushbenachrichtigungen zu einer in Visual Studio 2012 erstellten Windows Store-App hinzugef&uuml;gt. Visual Studio 2013 umfasst neue Funktionen, die das Einrichten von Pushbenachrichtigungen in Ihrer Windows Store-App mithilfe von Mobile Services vereinfachen. Siehe <a href="/de-de/develop/mobile/tutorials/get-started-with-push-dotnet">Erste Schritte mit Pushbenachrichtigungen</a> f&uuml;r die Visual Studio 2013-Version.</p>
</div>

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services][Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]
2.  [Erstellen der Registrations-Tabelle][Erstellen der Registrations-Tabelle]
3.  [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
4.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
5.  [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows 8
-   Ein aktives Windows Store-Konto

Dieses Lernprogramm baut auf das Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [dieses Lernprogramm][Erste Schritte mit Daten] abschließen.

## <a name="register"></a><span class="short-header">Registrieren Ihrer App</span>Registrieren Ihrer App für den Windows Store

Sie müssen die App an den Windows Store übermitteln, um Pushbenachrichtigungen von Mobile Services an Windows Store-Apps senden zu können. Anschließend müssen Sie Ihren Mobile Service zur Integration mit WNS konfigurieren.

[WACOM.INCLUDE [mobile-services-register-windows-store-app][mobile-services-register-windows-store-app]]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit WNS. Als Nächstes werden Sie eine neue Tabelle erstellen, um die Registrierungen zu speichern.

## <a name="create-table"></a>Erstellen einer neuen Tabelle

[WACOM.INCLUDE [mobile-services-create-new-push-table][mobile-services-create-new-push-table]]

## <a name="add-push"></a><span class="short-header">Hinzufügen von Pushbenachrichtigungen</span>Hinzufügen von Pushbenachrichtigungen zu Ihrer App

1.  Öffnen Sie die Projektdatei MainPage.xaml.cs, und fügen Sie den folgenden Code hinzu, der eine neue **Registrations**-Klasse erstellt:

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    Mit der "Handle"-Eigenschaft wird der Kanal-URI gespeichert.

2.  Öffnen Sie die Datei App.xaml.cs, und fügen Sie die folgende using-Anweisung hinzu:

        using Windows.Networking.PushNotifications;

3.  Fügen Sie der Datei "App.xaml.cs" folgenden Code hinzu:

        private async void AcquirePushChannel()
        {
           CurrentChannel = 
               await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.Uri };
           await registrationsTable.InsertAsync(registration);
        }

    Dieser Code fügt den aktuellen Channel in die Registrierungstabelle ein.

4.  Fügen Sie in der Datei App.xaml.cs am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf zu der neuen **AcquirePushChannel**-Methode hinzu:

        AcquirePushChannel();

Damit wird sichergestellt, dass die **CurrentChannel**-Eigenschaft bei jedem Aufruf der Anwendung initialisiert wird.

1.  Öffnen Sie die Datei Package.appxmanifest und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

    ![][]

    Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann.

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
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
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

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

    ![][2]

    Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

    ![][3]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

-   [Erste Schritte mit Benachrichtigungshubs][Erste Schritte mit Benachrichtigungshubs]
    Lernen Sie, wie Sie Benachrichtigungshubs in Ihrer Windows Store-App einsetzen können.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Informationen zum Erstellen und Senden von Pushbenachrichtigungen an Benutzer auf mehreren Plattformen.

-   [Senden von Benachrichtigungen an Abonnenten][Senden von Benachrichtigungen an Abonnenten]
    Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer][Senden von Benachrichtigungen an Benutzer]
    Lernen Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer][Senden plattformübergreifender Benachrichtigungen an Benutzer]
    Lernen Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

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

  [Windows Store C#]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows Store C#"
  [Windows Store JavaScript]: /de-de/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows Store JavaScript"
  [Windows Phone]: /de-de/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone"
  [iOS]: /de-de/develop/mobile/tutorials/get-started-with-push-ios "iOS"
  [Android]: /de-de/develop/mobile/tutorials/get-started-with-push-android "Android"
  [Xamarin.iOS]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android"
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]: #register
  [Erstellen der Registrations-Tabelle]: #create-table
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  []: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
  [Erste Schritte mit Benachrichtigungshubs]: /de-de/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Was sind Notification Hubs?]: /de-de/develop/net/how-to-guides/service-bus-notification-hubs/
  [Senden von Benachrichtigungen an Abonnenten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet/
  [Senden von Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users/
  [Senden plattformübergreifender Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library/
