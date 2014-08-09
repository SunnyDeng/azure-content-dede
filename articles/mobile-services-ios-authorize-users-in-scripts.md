<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Dienstweite Autorisierung von Mobile Services-Benutzern
=======================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS")[Android](/de-de/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android")[HTML](/de-de/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android")
[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ ".NET-Backend") | [JavaScript-Backend](/de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer iOS-App zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-ios) auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-ios) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

Registrieren von Skripts
------------------------

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

      ![](./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

      ![](./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png)

3.  Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

      ![](./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png)

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

    Dieses Skript fügt einen Benutzer-ID-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **userId** zur Tabelle **TodoItem** hinzu. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.

5.  Wiederholen Sie die Schritte 3 und 4, um den vorhandenen Vorgang **Read** durch die folgende Funktion zu ersetzen:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

      Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

Testen der App
--------------

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-ios) verändert haben.

2.  Klicken Sie auf die Schaltfläche **Run**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

      Beachten Sie, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

      ![](./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png)

      Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Browse** und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen userId-Wert hat.

5.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-ios)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-ios)
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


