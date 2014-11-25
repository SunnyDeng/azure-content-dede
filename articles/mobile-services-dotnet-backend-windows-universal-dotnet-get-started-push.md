<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Backend Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungs-Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom .NET-Backend an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE] In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von mobilen Diensten an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen in Mobile Services][Erste Schritte mit Pushbenachrichtigungen in Mobile Services].

> Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version][dieser Version] des Themas weitere Informationen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren der App für Pushbenachrichtigungen][Registrieren der App für Pushbenachrichtigungen]
2.  [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen][Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen]
3.  [Aktivieren von Pushbenachrichtigungen für lokale Tests][Aktivieren von Pushbenachrichtigungen für lokale Tests]
4.  [Testen von Pushbenachrichtigungen in der App][Testen von Pushbenachrichtigungen in der App]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives [Microsoft Store-Konto][Microsoft Store-Konto].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] (Update 3 oder eine höhere Version).
    Dazu ist eine kostenlose Testversion verfügbar.

## <span id="register"></span></a>Registrieren der App für Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Navigieren Sie zum Projektordner `\Services\MobileServices\your_service_name`, öffnen Sie die generierte Codedatei "push.register.js", und überprüfen Sie die Methode **UploadChannel**, welche die Kanal-ID des Geräts beim Benachrichtigungs-Hub registriert.

2.  Öffnen Sie die gemeinsam genutzte Codedatei "App.xaml.cs". Beachten Sie den Aufruf der neuen Methode **UploadChannel**, der dem Ereignishandler **OnLaunched**hinzugefügt wurde.

    Dies stellt sicher, dass versucht wird, das Gerät zu registrieren, sobald die App gestartet wird.

3.  Führen Sie die vorigen Schritte erneut aus, um dem Windows Phone Store-App-Projekt Pushbenachrichtigungen hinzuzufügen, und entfernen Sie dann in der gemeinsam genutzten Datei "App.xaml.cs" den zusätzlichen Aufruf von **UploadChannel** sowie den restlichen bedingten Wrapper `#if...#endif`.

    In beiden Projekten kann nun ein einziger Aufruf von **UploadChannel** verwendet werden.

    <div class="dev-callout"><strong>Hinweis</strong> <p>Sie k&ouml;nnen den generierten Code auch vereinfachen, indem Sie die in <code data-inline="1">#if...#endif</code> gekapselten <a href="http://msdn.microsoft.com/de-de/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App genutzt wird.</p></div>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen.

## <span id="update-service"></span></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird die bereits vorhandene Klasse "TodoItemController" so aktualisiert, dass sie eine Pushbenachrichtigung an alle registrierten Geräte sendet, wenn ein neues Element eingefügt wird. Sie können ähnlichen Code in jedem benutzerdefinierten [ApiController], [TableController] oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Die restlichen Schritte in diesem Abschnitt sind optional. Sie dienen dazu, die App mit einem mobilen Dienst zu testen, der auf einem lokalen Computer ausgeführt wird. Wenn Sie planen, die Pushbenachrichtigungen mit dem in Azure gehosteten mobilen Dienst zu testen, können Sie diese Schritte überspringen und einfach zum letzten Abschnitt wechseln. Dies ist möglich, weil der Assistent zum Hinzufügen von Pushbenachrichtigungen die App bereits für die Verbindung mit dem in Azure gehosteten Dienst konfiguriert hat.

> [WACOM.NOTE] Verwenden Sie niemals einen produktiv genutzten mobilen Dienst für Test- und Entwicklungsarbeiten. Veröffentlichen Sie das mobile Dienstprojekt zum Testen stets in einem getrennten Staging-Dienst.

1.  Öffnen Sie die gemeinsam genutzte Projektdatei "App.xaml.cs", und suchen Sie alle Codezeilen, in denen eine neue Instanz der Klasse [MobileServiceClient][MobileServiceClient] erzeugt wird, um auf den in Azure gehosteten mobilen Dienst zuzugreifen.

2.  Kommentieren Sie diesen Code aus, und fügen Sie Code hinzu, der einen neuen [MobileServiceClient][MobileServiceClient] mit demselben Namen erzeugt, im Konstruktor jedoch die URL des lokalen Computers verwendet, ähnlich dem folgenden Code:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        public static MobileServiceClient todolistClient = new MobileServiceClient(
            "http://localhost:4584"
        );

    Bei Verwendung dieses [MobileServiceClient][MobileServiceClient] verbindet sich die App nicht mit der in Azure gehosteten Version des Dienstes, sondern mit dem lokalen Dienst. Wenn Sie wieder umschalten und die App mit dem in Azure gehosteten mobilen Dienst ausführen möchten, wechseln Sie wieder zurück zu den ursprünglichen [MobileServiceClient][MobileServiceClient]-Definitionen.

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

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



  [Erste Schritte mit Pushbenachrichtigungen in Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [dieser Version]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Registrieren der App für Pushbenachrichtigungen]: #register
  [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen]: #update-service
  [Aktivieren von Pushbenachrichtigungen für lokale Tests]: #local-testing
  [Testen von Pushbenachrichtigungen in der App]: #test
  [Microsoft Store-Konto]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [MobileServiceClient]: http://msdn.microsoft.com/de-de/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users
  [Verwenden eines .NET-Clients für Azure Mobile Services]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
