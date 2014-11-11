<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-ios" urlDisplayName="Authorize Users in Scripts (Xamarin.iOS)" pageTitle="Authorize users in scripts (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.iOS scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" documentationCenter="Mobile" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Verwenden von Skripts zur Autorisierung von Benutzern in Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios" title="iOS">iOS</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-android" title="Android">Android</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-html" title="HTML">HTML</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Xamarin.iOS-App zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung] auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung] abschließen, bevor Sie mit diesem Lernprogramm beginnen.

## <a name="register-scripts"></a>Registrieren von Skripts

Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][0]

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

1.  Öffnen Sie in Xamarin Studio oder Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung] verändert haben.

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



  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
