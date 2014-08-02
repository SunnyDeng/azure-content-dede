<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Dienstweite Autorisierung von Mobile Services-Benutzern
=======================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")

[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer iOS-App autorisieren. In diesem Lernprogramm fügen Sie Code zu den Datenzugriffsmethoden Ihres Controllers hinzu, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users) auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

Ändern der Datenzugriffsmethode
-------------------------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

Testen der App
--------------

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users) verändert haben.

2.  Klicken Sie auf die Schaltfläche **Run**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

	Beachten Sie, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

	![][3]

	Dadurch werden der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal] [Azure-Verwaltungsportal] auf **Browse**, und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen userId-Wert hat.

5.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/