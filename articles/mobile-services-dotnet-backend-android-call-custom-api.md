<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Windows Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Aufrufen einer benutzerdefinierten API aus dem Client

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="iOS">iOS</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-android-call-custom-api"  title="JavaScript-Backend">JavaScript-Backend</a></div>

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Android-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Mit der im Rahmen dieses Themas erstellten benutzerdefinierten API können Sie eine einzelne POST-Anforderung senden, die das *completed*-Kennzeichen für sämtliche todo-Einträge in der Tabelle Ihres mobilen Diensts auf `true` festlegt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] erstellen. Dafür führen Sie die folgenden Schritte aus:

1.  [Definieren der benutzerdefinierten API][Definieren der benutzerdefinierten API]
2.  [Aktualisieren der App zum Aufruf der benutzerdefinierten API][Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3.  [Testen der App][Testen der App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api][mobile-services-dotnet-backend-create-custom-api]]

[WACOM.INCLUDE [mobile-services-android-call-custom-api][mobile-services-android-call-custom-api]]

## Nächste Schritte

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Android-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

-   [Speichern von Serverskripts in der Quellcodeverwaltung][Speichern von Serverskripts in der Quellcodeverwaltung]
     Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android"
  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-android-call-custom-api "JavaScript-Backend"
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
  [Definieren der benutzerdefinierten API]: #define-custom-api
  [Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
  [Testen der App]: #test-app
  [mobile-services-dotnet-backend-create-custom-api]: ../includes/mobile-services-dotnet-backend-create-custom-api.md
  [mobile-services-android-call-custom-api]: ../includes/mobile-services-android-call-custom-api.md
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/documentation/articles/mobile-services-store-scripts-source-control
