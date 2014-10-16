<properties linkid="mobile-services-call-custom-api-js" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store JS client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

Aufrufen einer benutzerdefinierten API aus dem Client
=====================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title=".NET backend">.NET backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api"  title="JavaScript backend" class="current">JavaScript backend</a></div>

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Windows Store-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started/) oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/) erstellen. Dafür führen Sie die folgenden Schritte aus:

1.  [Definieren der benutzerdefinierten API](#define-custom-api)
2.  [Aktualisieren der App zum Aufruf der benutzerdefinierten API](#update-app)
3.  [Testen der App](#test-app)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started/) oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/) abschließen, bevor Sie mit diesem Lernprogramm beginnen. In diesem Lernprogramm wird Visual Studio 2012 Express für Windows 8 verwendet.

<a name="define-custom-api">
Definieren der benutzerdefinierten API
--------------------------------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

Nächste Schritte
----------------

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Windows Store-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

-   [Definieren benutzerdefinierter APIs mit Unterstützung für periodische Benachrichtigungen](/de-de/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications)
    <br/>Erfahren Sie, wie Sie eine benutzerdefinierte API verwenden, um periodische Benachrichtigungen in einer Windows Store-App zu unterstützen. Durch die Aktivierung von periodischen Benachrichtigungen greift Windows periodisch auf Ihren benutzerdefinierten API-Endpunkt zu und verwendet die zurückgegebene XML, in einem kachelspezifischen Format, um die App-Kachel im Startmenü zu aktualisieren.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

-   [Speichern von Serverskripts in der Quellcodeverwaltung](/de-de/documentation/articles/mobile-services-store-scripts-source-control)
     <br/>Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.


<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Get started with authentication]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Define a custom API that supports periodic notifications]: /de-de/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
[Store server scripts in source control]: /de-de/documentation/articles/mobile-services-store-scripts-source-control