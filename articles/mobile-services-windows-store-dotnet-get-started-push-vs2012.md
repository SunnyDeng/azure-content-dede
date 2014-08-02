<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services mit Visual Studio 2012
===================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS") [Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android") [Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Windows Store-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe des Windows Push Notification Services (WNS) zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

**Hinweis**

Dieses Lernprogramm erweitert eine in Visual Studio 2012 erstellte Windows Store-App um Pushbenachrichtigungen. Visual Studio 2013 enthält neue Features, die eine einfache Einrichtung von Pushbenachrichtigungen Ihrer Windows Store-App mit Mobile Services ermöglichen. Siehe [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet) für die Visual Studio 2013-Version.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services](#register)
2.  [Erstellen der Registrations-Tabelle](#create-table)
3.  [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
4.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
5.  [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows 8
-   Ein aktives Windows Store-Konto

Dieses Lernprogramm baut auf das Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet) auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [dieses Lernprogramm](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet) abschließen.

Registrieren Ihrer AppRegistrieren Ihrer App für den Windows Store
------------------------------------------------------------------

Sie müssen die App an den Windows Store übermitteln, um Pushbenachrichtigungen von Mobile Services an Windows Store-Apps senden zu können. Anschließend müssen Sie Ihren Mobile Service zur Integration mit WNS konfigurieren.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit WNS. Als Nächstes werden Sie eine neue Tabelle erstellen, um die Registrierungen zu speichern.

Erstellen einer neuen Tabelle
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Hinzufügen von PushbenachrichtigungenHinzufügen von Pushbenachrichtigungen zu Ihrer App
---------------------------------------------------------------------------------------

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

5.  Öffnen Sie die Datei Package.appxmanifest und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png)

      Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen empfangen kann. 

Aktualisieren des EinfügeskriptsAktualisieren des registrierten Einfügeskripts im Verwaltungsportal
---------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png)

2.  Ersetzen Sie die Einfügefunktion durch den folgenden Code und klicken Sie auf **Save**:

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

Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png)

      Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png)

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

-   [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Lernen Sie, wie Sie Notification Hubs in Ihrer Windows Store-App einsetzen können.

-   [Was sind Notification Hubs?](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    <br/>Informationen zum Erstellen und Senden von Benachrichtigungen an Benutzer auf mehreren Plattformen.

-   [Senden von Benachrichtigungen an Abonnenten](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>Lernen Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>Lernen Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>Lernen Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Backend auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


