<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")

[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/ "JavaScript-Backend")

In diesem Thema wird die Verwendung von Azure Mobile Services über Visual Studio 2013 zum Senden von Pushbenachrichtigungen an Ihre Windows Store-App erläutert. In diesem Lernprogramm verwenden Sie den Windows-Pushbenachrichtigungsdienst, um direkt in Visual Studio Pushbenachrichtigungen zum Schnellstartprojekt hinzuzufügen. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE]Mobile Services wird nun in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und Skalierung unterstützt werden können. Diese integrierten Funktionen befinden sich gegenwärtig in einem Vorschaustadium. Weitere Informationen finden Sie in dieser Version von [Erste Schritte mit Pushbenachrichtigungen](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services](#register)
2.  [Aktualisieren des erzeugten Pushbenachrichtigungscodes](#update-scripts)
3.  [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started/) oder [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-js/) abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

Registrierung Ihrer AppHinzufügen und Konfigurieren von Pushbenachrichtigungen in Ihrer App
-------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Öffnen Sie die generierte Codedatei push.register.js und überprüfen Sie den Code, der die Installations-ID und den Kanal für das Gerät abruft und diese Daten in die neue Tabelle **channels** einfügt.

    Diese Tabelle wurde in Ihrem mobilen Dienst vom Assistent für Pushbenachrichtigungen erstellt. Dieser Code stellt sicher, dass eine Registrierung des Gerätes versucht wird, sobald die App aktiviert wird.

2.  Erweitern Sie im Server-Explorer **Azure**, **Mobile Services**, Ihren Dienstnamen und **channels**, und öffnen Sie dann die Datei insert.js.

    Diese Datei, die in Ihrem mobilen Dienst gespeichert wird, enthält JavaScript-Code, der ausgeführt wird, wenn ein Client eine Anfrage zur Registrierung eines Gerätes durch Einfügen von Daten in die Kanaltabelle sendet.

    **Hinweis**

    Die erste Version dieser Datei enthält Code, der das Gerät auf eine eventuell bereits vorhandene Registrierung prüft. Außerdem enthält sie Code, der eine Pushbenachrichtigung sendet, wenn eine neue Registrierung zur Kanaltabelle hinzugefügt wird. Der Code, der eine Pushbenachrichtigung sendet, kann in jede registrierte Skriptdatei eingefügt werden. Der Speicherort dieses Skripts hängt davon ab, wie die Benachrichtigung ausgelöst wird. Skripts können während eines Einfügungs-, Aktualisierungs-, Lösch- oder Lesevorgangs in einer Tabelle, einem geplanten Auftrag oder als benutzerdefinierte API registriert werden. Weitere Informationen finden Sie unter [Verwenden von Serverskripts in Mobile Services](http://go.microsoft.com/fwlink/p/?LinkID=287178).

3.  Drücken Sie die Taste F5, um die App auszuführen und zu überprüfen, ob eine Benachrichtigung vom mobilen Dienst empfangen wird.

    Die Benachrichtigung wurde durch Einfügen einer Zeile in die Tabelle neuer Kanäle erzeugt. Dies ist die Geräteregistrierung.

Während der erzeugte Code das Anzeigen einer Benachrichtigung erleichtert, wenn die App ausgeführt wird, ist dies im Allgemein kein sinnvolles Szenario. Als Nächstes entfernen Sie den Benachrichtigungscode aus der Kanaltabelle und ersetzen ihn, mit einigen Änderungen, in der Tabelle TodoItem.

Aktualisieren des CodesAktualisieren des erzeugten Pushbenachrichtigungscodes
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Speichern**.

   	![][13]

   	Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

   	![][14]

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

-   [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
  <br/>Lernen Sie, wie Sie Notification Hubs in Ihrer Windows Store-App einsetzen können.

-   [Senden von Benachrichtigungen an Abonnenten](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
  <br/>Lernen Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users/)
  <br/>Lernen Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
  <br/>Lernen Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Backend auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-js/)
  <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js)
  <br/>Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.




<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png

