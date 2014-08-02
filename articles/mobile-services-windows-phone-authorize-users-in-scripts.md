<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Dienstweite Autorisierung von Mobile Services-Benutzern
=======================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS")[Android](/en-us/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android")[HTML](/en-us/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android")

[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/ "JavaScript backend")

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Windows Phone 8-App zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte bei der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-wp8) auf. Sie müssen zuerst [Erste Schritte bei der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-wp8) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

[Lernprogramm ansehen](http://go.microsoft.com/fwlink/?LinkId=298630) [Video abspielen](http://go.microsoft.com/fwlink/?LinkId=298630) 15:00:00

Registrieren von Skripts
------------------------

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

   	![][0]

2.  Klicken Sie auf die Registerkarte **Daten**, dann auf die Tabelle **TodoItem**.

   	![][1]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

   	![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
           item.userId = user.userId;    
           request.execute();
         }

    Dieses Skript fügt einen Benutzer-ID-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **userId** zur Tabelle **TodoItem** hinzu. Das dynamische Schema ist standardmäßig für einen neuen mobilen Dienst aktiviert und sollte deaktiviert werden, bevor die App im Windows Phone Store veröffentlicht wird.

5.  Wiederholen Sie die Schritte 3 und 4, um den vorhandenen Vorgang **Lesen** mit folgender Funktion zu ersetzen:

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

   	Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

Testen der App
--------------

1.  Öffnen Sie in Visual Studio 2012 Express für Windows Phone das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte bei der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-wp8) abgeschlossen haben.

2.  Drücken Sie F5, um die App auszuführen und sich mit dem ausgewählten Identitätsanbieter anzumelden.

   	Sie werden bemerken, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App in das Textfeld einen Text ein, und klicken Sie dann auf **Save**.

   	![][3]

   	Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert aufweist, wird es vom mobilen Dienst zurückgegeben.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Durchsuchen**, und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen Benutzer-ID-Wert hat.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
  <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
  <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png