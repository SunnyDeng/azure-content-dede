<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push][mobile-services-selector-get-started-push]]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit JavaScript-Backend Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungs-Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom JavaScript-Backend an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE] In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von mobilen Diensten an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen in Mobile Services][Erste Schritte mit Pushbenachrichtigungen in Mobile Services].

> Wenn Sie kein Upgrade auf Visual Studio 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version][dieser Version] des Themas weitere Informationen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren der App für Pushbenachrichtigungen][Registrieren der App für Pushbenachrichtigungen]
2.  [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen][Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen]
3.  [Testen von Pushbenachrichtigungen in der App][Testen von Pushbenachrichtigungen in der App]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives [Microsoft Store-Konto][Microsoft Store-Konto].
-   [Visual Studio 2013 Express für Windows][Visual Studio 2013 Express für Windows] mit Update 3 oder eine höhere Version

## <span id="register"></span></a>Registrieren der App für Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013][mobile-services-create-new-push-vs2013]]

1.  Navigieren Sie zum Projektordner `\Services\MobileServices\your_service_name`, öffnen Sie die generierte Codedatei "push.register.js", und überprüfen Sie die Methode **UploadChannel**, welche die Kanal-ID des Geräts beim Benachrichtigungs-Hub registriert.

2.  Öffnen Sie die gemeinsam genutzte Codedatei "App.xaml.cs". Beachten Sie den Aufruf der neuen Methode **UploadChannel**, der dem Ereignishandler **OnLaunched**hinzugefügt wurde.

    Dies stellt sicher, dass versucht wird, das Gerät zu registrieren, sobald die App gestartet wird.

3.  Führen Sie die vorigen Schritte erneut aus, um dem Windows Phone Store-App-Projekt Pushbenachrichtigungen hinzuzufügen, und entfernen Sie dann in der gemeinsam genutzten Datei "App.xaml.cs" den zusätzlichen Aufruf von **UploadChannel** sowie den restlichen bedingten Wrapper `#if...#endif`.

    In beiden Projekten kann nun ein einziger Aufruf von **UploadChannel** verwendet werden.

    <div class="dev-callout"><strong>Hinweis</strong> <p>Sie k&ouml;nnen den generierten Code auch vereinfachen, indem Sie die in <code data-inline="1">#if...#endif</code> gekapselten <a href="http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App genutzt wird.</p></div>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen.

## <span id="update-service"></span></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird das Insert-Skript aktualisiert, das in der TodoItem-Tabelle registriert ist. Sie können ähnlichen Code in jedem Serverskript oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs][mobile-services-javascript-update-script-notification-hubs]]

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push][mobile-services-javascript-backend-windows-universal-test-push]]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Benachrichtigungs-Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Verwenden eines .NET-Clients für Azure Mobile Services][Verwenden eines .NET-Clients für Azure Mobile Services]
    Erfahren Sie mehr über die Verwendung von Mobile Services in C#-Windows-Apps.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [Erste Schritte mit Pushbenachrichtigungen in Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [dieser Version]: /de-de/documentation/articles/mobile-services-javscript-backend-windows-store-dotnet-get-started-push
  [Registrieren der App für Pushbenachrichtigungen]: #register
  [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen]: #update-service
  [Testen von Pushbenachrichtigungen in der App]: #test
  [Microsoft Store-Konto]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express für Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [MobileServiceClient]: http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users
  [Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
  [Verwenden eines .NET-Clients für Azure Mobile Services]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
