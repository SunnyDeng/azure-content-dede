<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Dienstweite Autorisierung von Mobile Services-Benutzern
=======================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone" class="current">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS">iOS</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/" title=".NET backend" class="current">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/"  title="JavaScript backend">JavaScript backend</a></div>

In diesem Thema erfahren Sie, wie Sie authentifizierte Benutzer für den Zugriff auf Daten in Azure Mobile Services von einer Windows Phone-App autorisieren. In diesem Lernprogramm fügen Sie Code zu den Datenzugriffsmethoden in Ihrem Controller hinzu. Die Methoden filtern Abfragen auf der Grundlage der Benutzer-ID eines authentifizierten Benutzers und stellen somit sicher, dass jeder Benutzer ausschließlich seine eigenen Daten einsehen kann.

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart und baut auf dem vorherigen Lernprogramm [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users) auf. Sie müssen zuerst [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users) abschließen, bevor Sie mit diesem Lernprogramm beginnen.

<a name="register-scripts">
Modifizieren der Datenzugriffsmethoden
--------------------------------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

Testen der App
--------------

1.  Öffnen Sie in Visual Studio 2013 für Windows Phone das Projekt, das Sie bei der Bearbeitung des Lernprogramms [Erste Schritte bei der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users) geändert haben.

2.  Drücken Sie F5, um die App auszuführen und sich mit dem ausgewählten Identitätsanbieter anzumelden.

   	Sie werden bemerken, dass dieses Mal keine Elemente zurückgegeben werden, obwohl aus vorherigen Lernprogrammen bereits Elemente in der TodoItem-Tabelle vorhanden sind. Dies liegt daran, dass vorherige Elemente ohne die Spalte Benutzer-ID eingefügt wurden und jetzt Nullwerte haben.
	
3.  Geben Sie in der App in das Textfeld einen Text ein, und klicken Sie dann auf **Save**.

   	![][3]

   	Dadurch wird der Text und die Benutzer-ID in der TodoItem-Tabelle im Mobile Service eingefügt. Da das neue Element den korrekten Benutzer-ID-Wert aufweist, wird es vom mobilen Dienst zurückgegeben.

4.  (Optional) Wenn Sie zusätzliche Anmeldekonten haben, können Sie verifizieren, dass Benutzer nur ihre eigenen Daten sehen können, indem Sie die App schließen (Alt+F4) und dann erneut ausführen. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein und vergewissern Sie sich, dass die unter dem vorherigen Konto eingegebenen Elemente nicht angezeigt werden.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, die die Grundlagen der Arbeit mit Authentifizierung veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    Lernen Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit Pushbenachrichtigungen](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/)
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/