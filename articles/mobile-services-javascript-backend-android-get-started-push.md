<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# <a name="getting-started-with-push"> </a>Erste Schritte mit Pushbenachrichtigungen in Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET-Backend" >.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE]In diesem Lernprogramm wird die Integration von Mobile Services in Notification Hubs veranschaulicht, d. h., das Senden von Pushbenachrichtigungen von Ihrem mobilen Dienst. Bei Verwendung eines älteren mobilen Diensts, der Legacypushvorgänge verwendet und noch nicht für die Verwendung von Notification Hubs aktualisiert wurde, *empfehlen wir ein Upgrade* im Rahmen dieses Lernprogramms. Falls Sie sich jetzt gegen das Upgrade entscheiden, sollten Sie die folgende Lernprogrammversion nutzen: [Erste Schritte mit Pushbenachrichtigungen (Legacypushvorgang)][Erste Schritte mit Pushbenachrichtigungen (Legacypushvorgang)].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktivieren von Google Cloud Messaging][Aktivieren von Google Cloud Messaging]
2.  [Konfigurieren von Mobile Services][Konfigurieren von Mobile Services]
3.  [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
4.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
5.  [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden.

## <span id="register"></span></a>Aktivieren von Google Cloud Messaging

> [WACOM.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com][accounts.google.com], um ein neues Google-Konto zu erstellen.

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

## <span id="configure"></span></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][]

2.  Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert für **API-Schlüssel** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

    > [WACOM.NOTE]Wenn Sie dieses Lernprogramm mit einem älteren mobilen Dienst ausführen, kann unten auf der Registerkarte **Push** der Link **Erweiterten Push aktivieren** angezeigt werden. Klicken Sie jetzt auf diesen Link, um Ihren mobilen Dienst zu aktualisieren und Notification Hubs zu integrieren. Dies Änderung kann nicht rückgängig gemacht werden. Ausführliche Informationen zum Aktivieren erweiterter Pushbenachrichtigungen für einen mobilen Dienst in einer Produktionsumgebung finden Sie in [dieser Anleitung][dieser Anleitung].

    ![][1]

    <div class="dev-callout"><b>Wichtig</b>
<p>Wenn Sie Ihre GCM-Anmeldeinformationen f&uuml;r erweiterte Pushbenachrichtigungen auf der Registerkarte &quot;Push&quot; im Portal festlegen, werden diese f&uuml;r Notification Hubs freigegeben, sodass der Benachrichtigungshub mit Ihrer App konfiguriert wird.</p>
</div>

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit GCM und Notification Hubs.

## <span id="add-push"></span></a>Hinzufügen von Pushbenachrichtigungen zur App

### Prüfen der Version des Android-SDK

[WACOM.INCLUDE [Verify SDK][Verify SDK]]

Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss.

Wenn Sie den Testvorgang mit einem älteren Gerät durchführen, konsultieren Sie [Set Up Google Play Services SDK][Set Up Google Play Services SDK] (Einrichten des Google Play Services SDK, in englischer Sprache), um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

### Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Add Play Services][Add Play Services]]

### Hinzufügen des Codes

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][mobile-services-android-getting-started-with-push]]

## <span id="update-scripts"></span></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][2]

2.  Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][3]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    Daraufhin wird ein neues Einfügeskript registriert, das das [gcm-Objekt][gcm-Objekt] zum Senden einer Pushbenachrichtigung an alle registrierten Geräte verwendet, nachdem der Einfügevorgang erfolgreich war.

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

### Einrichten des Emulators für den Testvorgang

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1.  Starten Sie Eclipse neu, und klicken Sie dann im Paket-Explorer mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**, dann auf **Android**, überprüfen Sie **Google APIs**, und klicken Sie dann auf **OK**.

    ![][4]

    Dies gibt das Projekt für die Google-APIs vor.

2.  Wählen Sie unter **Fenster** **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

    ![][5]

3.  Wählen Sie **Google APIs** in **Ziel** aus, und klicken Sie auf **OK**.

    ![][6]

    Dies gibt das AVD zur Verwendung von Google-APIs vor.

### Ausführen des Tests

1.  Klicken Sie in Eclipse im Menü **Run** auf **Run**, um die App zu starten.

2.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Eine neue Mobile Services-Aufgabe hinzufügen*, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

    ![][7]

3.  Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Verwenden der Android-Clientbibliothek für Mobile Services][Verwenden der Android-Clientbibliothek für Mobile Services]
    Lernen Sie mehr über die Verwendung von Mobile Services mit Android.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Erfahren Sie mehr über das Implementieren von Geschäftslogik in Ihren mobilen Dienst.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript-Backend"
  [Erste Schritte mit Pushbenachrichtigungen (Legacypushvorgang)]: /de-de/documentation/articles/mobile-services-android-get-started-push/
  [Aktivieren von Google Cloud Messaging]: #register
  [Konfigurieren von Mobile Services]: #configure
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-android-get-started/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [dieser Anleitung]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [gcm-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-users
  [Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
  [Verwenden der Android-Clientbibliothek für Mobile Services]: /de-de/documentation/articles/mobile-services-android-how-to-use-client-library
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
