 <properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Authorize Users in Scripts (Xamarin.Android)" pageTitle="Authorize users in scripts (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.Android scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" authors="" />

Verwenden von Skripts zur Autorisierung von Benutzern in Mobile Services
========================================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios "iOS")[Android](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-android "Android")[HTML](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-html "HTML")[iOS C\#](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios "Xamarin.iOS")[Android C\#](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Xamarin.Android-App zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android) auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

Registrieren von Skripts
------------------------

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

	![][0]

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

	![][1]

3.  Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

	![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

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

1.  Öffnen Sie in Xamarin Studio oder Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android) verändert haben.

2.  Klicken Sie auf **Run**, um die App zu starten und sich mit dem von Ihnen gewählten Identitätsanbieter anzumelden.

	Sie werden bemerken, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App einen Text in **TodoItem einfügen** ein, und klicken Sie dann auf **Speichern**.

	Dadurch werden der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Durchführen** und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen userId-Wert hat.

5.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und vergewissern Sie sich, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, die die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android)
    Lernen Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Get started with push notifications]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/