<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Dienstweite Autorisierung von Mobile Services-Benutzern
=======================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript"  class="current">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Windows Store-App zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js) auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

<a name="register-scripts">
Registrieren von Skripts
------------------------

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

      ![](./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

      ![](./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-portal-data-tables.png)

3.  Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

      ![](./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-insert-script-users.png)

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

    Dieses Skript fügt einen Benutzer-ID-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird.

    <div class="dev-callout"><b>Hinweis</b>

    <p>Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **userId** zur Tabelle **TodoItem** hinzu. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.</p>
	</div>

5.  Wiederholen Sie die Schritte 3 und 4, um den vorhandenen Vorgang **Read** durch die folgende Funktion zu ersetzen:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

      Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

Testen der App
--------------

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js) abgeschlossen haben.

2.  Drücken Sie F5, um die App auszuführen und sich mit dem ausgewählten Identitätsanbieter anzumelden.

      Beachten Sie, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

      ![](./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png)

      Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Browse** und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen userId-Wert hat.

5.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen (Alt+F4) und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-js)
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[JavaScript and HTML]: mobile-services-win8-javascript/

[Azure Management Portal]: https://manage.windowsazure.com/