<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Dienstweite Autorisierung von Mobile Services-Benutzern
=======================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS")[Android](/de-de/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android")[HTML](/de-de/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android")
[.NET backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/ ".NET backend") | [JavaScript backend](/de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/ "JavaScript backend")

In diesem Thema erfahren Sie, wie Sie authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Windows Store-App autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi) [Video abspielen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi) 13:52

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte bei der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet) auf. Sie müssen zuerst [Erste Schritte bei der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

Registrieren von Skripts
------------------------

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Daten**, dann auf die Tabelle **TodoItem**.

    ![][1]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

    ![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

    Dieses Skript fügt einen Benutzer-ID-Wert zum Element hinzu, bei der es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **userId** zur Tabelle **TodoItem** hinzu. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.

5.  Wiederholen Sie die Schritte 3 und 4, um den vorhandenen Vorgang **Lesen** mit folgender Funktion zu ersetzen:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

    Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

Testen der App
--------------

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte bei der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet) abgeschlossen haben.

2.  Drücken Sie F5, um die App auszuführen und sich mit dem ausgewählten Identitätsanbieter anzumelden.

    Sie werden bemerken, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Speichern**.

    ![][3]

    Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Durchsuchen**, und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen Benutzer-ID-Wert hat.

5.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen (Alt+F4) und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)
    <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-dotnet)
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library)
    <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript and HTML]: mobile-services-win8-javascript/

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
