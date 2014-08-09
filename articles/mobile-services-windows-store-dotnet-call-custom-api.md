<properties urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

Aufrufen einer benutzerdefinierten API aus dem Client
=====================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-ios-call-custom-api "iOS")[Android](/de-de/documentation/articles/mobile-services-android-call-custom-api "Android") [.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api ".NET-Backend") | [JavaScript-Backend](/de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "JavaScript-Back-End")

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Windows Store-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started/) oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/) erstellen. Dafür führen Sie die folgenden Schritte aus:

1.  [Definieren der benutzerdefinierten API](#define-custom-api)
2.  [Aktualisieren der App zum Aufruf der benutzerdefinierten API](#update-app)
3.  [Testen der App](#test-app)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started/) oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/) abschließen, bevor Sie mit diesem Lernprogramm beginnen. In diesem Lernprogramm wird Visual Studio 2012 Express für Windows 8 verwendet.

Definieren der benutzerdefinierten API
--------------------------------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]

Nächste Schritte
----------------

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Windows Store-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

-   [Define a custom API that supports periodic notifications (Definieren benutzerdefinierter APIs mit Unterstützung für periodische Benachrichtigungen)](/de-de/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications)
    
    Erfahren Sie, wie Sie eine benutzerdefinierte API verwenden, um periodische Benachrichtigungen in einer Windows Store-App zu unterstützen. Durch die Aktivierung von periodischen Benachrichtigungen greift Windows periodisch auf Ihren benutzerdefinierten API-Endpunkt zu und verwendet die zurückgegebene XML, in einem kachelspezifischen Format, um die App-Kachel im Startmenü zu aktualisieren.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

-   [Speichern von Serverskripts in der Quellcodeverwaltung](/de-de/documentation/articles/mobile-services-store-scripts-source-control)
     
     Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.


