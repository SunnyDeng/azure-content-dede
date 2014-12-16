<properties pageTitle="Erste Schritte mit Pushbenachrichtigungen mit einem mobilen JavaScript-Back-End-Dienst" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />


# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .JavaScript-Back-End Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungs-Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom JavaScript-Back-End an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

>[WACOM.NOTE]In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von Mobile Services an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Wenn Sie kein Upgrade auf Visual Studio 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) des Themas weitere Informationen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen](#register)
2. [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen](#update-service)
3. [Testen von Pushbenachrichtigungen in der App](#test)

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein Aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* [Visual Studio 2013 Express für Windows](http://go.microsoft.com/fwlink/?LinkId=257546) mit Update 3 oder eine höhere Version 

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navigieren Sie zum Projektordner <code>\services\mobileServices\scripts</code>, kopieren Sie die erzeugte Skriptdatei "<<em>your_service_name</em>>.push.register.js" in den freigegebenen Ordner <code>\js</code>, und löschen Sie diese Datei dann sowohl aus dem Windows-App- als auch aus dem Windows Phone-App-Projekt.</p></li> 
<li><p>Öffnen Sie die Skriptdatei im freigegebenen Projektordner <code>\js</code>, suchen Sie den Ereignis-Listener <em>activated</em>, der die Channel-URL des Geräts beim Benachrichtigungshub registriert, und löschen Sie die Promise-Funktion<strong>done</strong>.</p>
<p>In diesem Lernprogramm werden Benachrichtigungen nicht beim Aufruf einer benutzerdefinierten API, sondern beim Einfügen eines neuen Elements gesendet.</p></li>
<li><p>Öffnen Sie im Windows-App-Projekt die Datei "default.html", und ändern Sie den im Verweis auf die Skriptdatei angegebenen Pfad zum gemeinsam genutzten Projektordner <code>\js</code>, sodass er folgendermaßen lautet:</p><pre><code><script src="/js/your_service_name.push.register.js"></script></code></pre></li>
<li><p>Wiederholen Sie diesen Schritt für das Windows Phone-App-Projekt.</p>
<p>Nun wird in beiden Projekten eine gemeinsame Version des Skripts für die Pushregistrierung verwendet.</p></li>
</ol>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen. 

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird das Insert-Skript aktualisiert, das in der TodoItem-Tabelle registriert ist. Sie können ähnlichen Code in jedem Serverskript oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren. 

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Benachrichtigungshubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr zum Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr darüber, wie sich Benutzer Ihrer App mit verschiedenen Kontotypen mit Mobile Services authentifizieren können.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Notification Hubs arbeiten, um Benachrichtigungen auf allen großen Clientplattformen an Ihre Apps zu senden.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services in HTML- und JavaScript-Apps.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/

[So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library
