<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Dienstweite Autorisierung von Mobile Services-Benutzern

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android" class="current">Android</a><a href="/de-de/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts/"  title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer iOS-App zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung] auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung] abschließen, bevor Sie mit diesem Lernprogramm beginnen.

## <a name="register-scripts"></a>Registrieren von Skripts

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][]

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

    ![][1]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

    ![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Dieses Skript fügt einen Benutzer-ID-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird.

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn dieses insert-Skript zum ersten Mal ausgef&uuml;hrt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, f&uuml;gt Mobile Services bei der ersten Ausf&uuml;hrung automatisch die Spalte <strong>userId</strong> zur Tabelle <strong>TodoItem</strong> hinzu. Das dynamische Schema ist standardm&auml;&szlig;ig f&uuml;r einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store ver&ouml;ffentlicht wird.</p>
</div>

5.  Wiederholen Sie die Schritte 3 und 4, um den vorhandenen Vorgang **Read** durch die folgende Funktion zu ersetzen:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

## Testen der App

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung] verändert haben.

2.  Klicken Sie auf die Schaltfläche **Run**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

    Beachten Sie, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

    ![][3]

    Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

4.  Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal][Azure-Verwaltungsportal] auf **Browse** und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen userId-Wert hat.

5.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android"
  [HTML]: /de-de/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML"
  [Xamarin.iOS]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS"
  [Xamarin.Android]: /de-de/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android"
  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "JavaScript-Backend"
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
