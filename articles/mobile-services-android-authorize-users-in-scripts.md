<properties pageTitle="Dienstseitige Autorisierung (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Dienstseitige Autorisierung von Mobile Services-Benutzern

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Android-App zu autorisieren.  In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der Benutzer-ID eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung] auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung] abschließen, bevor Sie mit diesem Lernprogramm beginnen.  

## <a name="register-scripts"></a>Registrieren von Skripts
Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der Tabelle TodoItem registrieren.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihre App. 

   	![][0]

2. Klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

   	![][1]

3. Klicken Sie auf **Skript** und dann auf den Vorgang **Einfügen**.

   	![][2]

4. Ersetzen Sie das vorhandene Skript durch folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Dieses Skript fügt einen Benutzer-ID-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird. 

    <div class="dev-callout"><b>Hinweis</b>
	<p>Bei der ersten Ausführung dieses Skripts muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte <strong>userId</strong> zur Tabelle <strong>TodoItem</strong> hinzu. Das dynamische Schema ist standardmäßig für einen neuen mobilen Dienst aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.</p>
    </div>


5. Wiederholen Sie die Schritte 3 und 4, um den vorhandenen **Lesevorgang** durch folgende Funktion zu ersetzen:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

## Testen der App

1. Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit der Authentifizierung] verändert haben.

2. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten, und melden Sie sich mit Ihrem gewählten Identitätsanbieter an. 

   	Sie werden bemerken, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.

3. Geben Sie in der App Text unter **Ein TodoItem einfügen** ein, und klicken Sie dann auf **Speichern**.

   	Dadurch werden der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

5. Klicken Sie in der Tabelle **todoitem** im [Verwaltungsportal][Azure-Verwaltungsportal] auf **Browse**, und überprüfen Sie, ob jedes neu hinzugefügte Element jetzt einen zugehörigen userId-Wert hat.

6. (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. -->
[Registrieren von Serverskripts]: #register-scripts
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
