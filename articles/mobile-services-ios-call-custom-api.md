<properties pageTitle="Call a custom API from an iOS app | Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an iOS app that uses Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Call a custom API from the client" authors="krisragh" solutions="" writer="krisragh" manager="" editor="" />

Aufrufen einer benutzerdefinierten API aus dem Client
=====================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-ios-call-custom-api "iOS")[Android](/de-de/documentation/articles/mobile-services-android-call-custom-api "Android")

[.NET backend](/de-de/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api ".NET backend") | [JavaScript backend](/de-de/documentation/articles/mobile-services-ios-call-custom-api "JavaScript backend")

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer iOS-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-ios-get-started/) oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-ios-get-started-data/) erstellen. Dafür führen Sie die folgenden Schritte aus:

1.  [Definieren der benutzerdefinierten API](#define-custom-api)
2.  [Aktualisieren der App zum Aufruf der benutzerdefinierten API](#update-app)
3.  [Testen der App](#test-app)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-ios-get-started/) abschließen. Dieses Lernprogramm erfordert das [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) und [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) sowie iOS 5.0 oder neuere Versionen.

Definieren der benutzerdefinierten API
--------------------------------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

Nächste Schritte
----------------

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre iOS-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

-   [Speichern von Serverskripts in der Quellcodeverwaltung](/de-de/documentation/articles/mobile-services-store-scripts-source-control)
  <br/> Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.


