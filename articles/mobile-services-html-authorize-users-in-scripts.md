<properties pageTitle="Dienstseitige Autorisierung (HTML) | Mobile Dev Center" description="Erfahren Sie, wie Sie Benutzer im JavaScript-Back-End von Azure Mobile Services autorisieren." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/29/2014" ms.author="glenga"/>

# Dienstweite Autorisierung von Mobile Services-Benutzern

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


In diesem Thema erfahren Sie, wie Sie Serverskripts verwenden, um authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer HTML-App zu autorisieren.  In diesem Lernprogramm registrieren Sie Skripts mit Mobile Services, um Abfragen basierend auf der userId eines authentifizierten Benutzers zu filtern, sodass jeder Benutzer nur seine eigenen Daten sehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart, und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung] auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung] abschließen, bevor Sie mit diesem Lernprogramm beginnen.  

## <a name="register-scripts"></a>Registrieren von Skripts
Da die Schnellstart-App Daten liest und einfügt, müssen Sie Skripts für diese Operationen bei der TodoItem-Tabelle registrieren.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App. 

   	![][0]

2. Klicken Sie auf die Registerkarte **Daten**, dann auf die **TodoItem**-Tabelle.

   	![][1]

3. Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen** aus.

   	![][2]

4. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Dieses Skript fügt einen userId-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers handelt, bevor es in die TodoItem-Tabelle eingefügt wird. 

    > [AUZRE.IMPORTANT] Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **userId** zur **TodoItem**-Tabelle hinzu. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App veröffentlicht wird.

5. Wiederholen Sie die Schritte 3 und 4, um den vorhandenen Vorgang **Read** durch die folgende Funktion zu ersetzen:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

## Testen der App

1. Öffnen Sie die Seite index.html der Anwendung in einem Webbrowser und melden Sie sich beim Identitätsanbieter Ihrer Wahl an. 

   	Beachten Sie, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte userId eingefügt wurden und jetzt Nullwerte haben.

3. Geben Sie in der App Text in das Feld **Neue Aufgabe eingeben** ein und klicken Sie auf **Hinzufügen**.

   	![][3]

   	Dadurch wird der Text und die userId in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten userId-Wert hat, wird es im Mobile Service zurückgegeben und in der zweiten Spalte angezeigt.

5. Klicken Sie in der **todoitem**-Tabelle im [Verwaltungsportal][Azure-Verwaltungsportal] auf **Durchsuchen**, und überprüfen Sie, ob jedes neu hinzugefügte Element einen zugehörigen userId-Wert besitzt.

6. (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen (Alt+F4) und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und überprüfen, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden. 

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

* [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit HTML/JavaScript.
  
<!-- Anchors. -->
[Registrieren von Serverskripts]: #register-scripts
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client


<!--HONumber=42-->
