<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push][mobile-services-selector-get-started-push]]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Backend Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungs-Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom .NET-Backend an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE] In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von mobilen Diensten an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version][dieser Version] des Themas weitere Informationen.

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

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013][mobile-services-create-new-push-vs2013]]

1.  Navigieren Sie zum Projektordner `\services\mobileServices\scripts`, kopieren Sie die erzeugte Skriptdatei "\<*Name\_Ihres\_Diensts*\>.push.register.js" in den gemeinsam genutzten Ordner `\js`, und löschen Sie dann diese Datei aus dem Windows-App- und aus dem Windows Phone-App-Projekt.

2.  Öffnen Sie die Skriptdatei im gemeinsam genutzten Projektordner `\js`, suchen Sie den Ereignis-Listener *activated*, der die Kanal-URL des Geräts beim Benachrichtigungs-Hub registriert, und löschen Sie die Promise-Funktion **done**.

    In diesem Lernprogramm werden Benachrichtigungen nicht beim Aufruf einer benutzerdefinierten API, sondern beim Einfügen eines neuen Elements gesendet.

3.  Öffnen Sie im Windows-App-Projekt die Datei "default.html", und ändern Sie den im Verweis auf die Skriptdatei angegebenen Pfad zum gemeinsam genutzten Projektordner `\js`, sodass er folgendermaßen lautet:

        <script src="/js/your_service_name.push.register.js"></script>

4.  Wiederholen Sie diesen Schritt für das Windows Phone-App-Projekt.

    Nun wird in beiden Projekten eine gemeinsame Version des Skripts für die Pushregistrierung verwendet.

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen.

## <span id="update-service"></span></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird die bereits vorhandene Klasse "TodoItemController" so aktualisiert, dass sie eine Pushbenachrichtigung an alle registrierten Geräte sendet, wenn ein neues Element eingefügt wird. Sie können ähnlichen Code in jedem benutzerdefinierten [ApiController], [TableController] oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][mobile-services-dotnet-backend-update-server-push]]

## <span id="local-testing"></span></a>Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013][mobile-services-dotnet-backend-configure-local-push-vs2013]]

Die restlichen Schritte in diesem Abschnitt sind optional. Sie dienen dazu, die App mit einem mobilen Dienst zu testen, der auf einem lokalen Computer ausgeführt wird. Wenn Sie planen, die Pushbenachrichtigungen mit dem in Azure gehosteten mobilen Dienst zu testen, können Sie diese Schritte überspringen und einfach zum letzten Abschnitt wechseln. Dies ist möglich, weil der Assistent zum Hinzufügen von Pushbenachrichtigungen die App bereits für die Verbindung mit dem in Azure gehosteten Dienst konfiguriert hat.

> [WACOM.NOTE] Verwenden Sie niemals einen produktiv genutzten mobilen Dienst für Test- und Entwicklungsarbeiten. Veröffentlichen Sie das mobile Dienstprojekt zum Testen stets in einem getrennten Staging-Dienst.

1.  Navigieren Sie zum Projektordner `\services\mobileServices\settings`, kopieren Sie die erzeugte Skriptdatei "\<*Name\_Ihres\_Diensts*\>.js" in den gemeinsam genutzten Projektordner `\js`, und löschen Sie dann diese Datei aus dem Windows-App- und aus dem Windows Phone-App-Projekt. Löschen Sie die Datei außerdem auch aus dem Ordner `\services\mobileServices\scripts` aller App-Projekte, sofern sie dort vorhanden ist.

2.  Öffnen Sie die Skriptdatei im gemeinsam genutzten Projektordner `\js`, und kommentieren Sie den vorhandenen Code aus, in dem das [MobileServiceClient-Objekt][MobileServiceClient-Objekt] für den Zugriff auf den in Azure gehosteten mobilen Dienst definiert wird.

3.  Fügen Sie eine neue **MobileServiceClient**-Objektdefinition mit demselben Namen hinzu, die jedoch im Konstruktor die URL des lokalen Computers verwendet, ähnlich dem folgenden Code:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        var todolistClient = new WindowsAzure.MobileServiceClient(
            "http://localhost:4584");

    Bei Verwendung dieses **MobileServiceClient**-Objekts verbindet sich die App nicht mit der in Azure gehosteten Version des Dienstes, sondern mit dem lokalen Dienst. Wenn Sie wieder umschalten und die App mit dem in Azure gehosteten mobilen Dienst ausführen möchten, wechseln Sie wieder zurück zu den ursprünglichen **MobileServiceClient**-Objektdefinitionen.

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push][mobile-services-dotnet-backend-windows-universal-test-push]]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung von Windows Store-Apps für die Verwendung von Mobile Services und Benachrichtigungs-Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Verwenden eines HTML-/JavaScript-Clients für Azure Mobile Services][Verwenden eines HTML-/JavaScript-Clients für Azure Mobile Services]
    Erfahren Sie mehr über die Verwendung von Mobile Services in HTML- und JavaScript-Apps.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [dieser Version]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Registrieren der App für Pushbenachrichtigungen]: #register
  [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen]: #update-service
  [Aktivieren von Pushbenachrichtigungen für lokale Tests]: #local-testing
  [Testen von Pushbenachrichtigungen in der App]: #test
  [Microsoft Store-Konto]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
  [MobileServiceClient-Objekt]: http://msdn.microsoft.com/en-us/library/azure/jj554219.aspx
  [mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users
  [Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
  [Verwenden eines HTML-/JavaScript-Clients für Azure Mobile Services]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library
