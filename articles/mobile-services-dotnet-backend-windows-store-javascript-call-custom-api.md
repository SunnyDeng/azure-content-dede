<properties pageTitle="Aufrufen einer benutzerdefinierten API von einem Windows Store-Client - Mobile Services" metaKeywords="" description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Windows Store-App aufrufen, die Microsoft Azure Mobile Services verwendet." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Aufrufen einer benutzerdefinierten API aus dem Client

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Windows Store-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügne diese Funktion zur App hinzu, die Sie entweder im [Erste Schritte mit mobilen Diensten] oder im [Erste Schritte mit Daten] Lernprogramm hinzugefügt haben. Führen Sie dazu folgende Schritte durch:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App] 

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, bevor Sie mit diesem Lernprogramm beginnen. In diesem Lernprogramm wird Visual Studio 2012 Express für Windows 8 verwendet.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]


## Nächste Schritte

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Windows Store-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Definieren benutzerdefinierter APIs mit Unterstützung für periodische Benachrichtigungen]
	<br/>Erfahren Sie, wie Sie eine benutzerdefinierte API zur Unterstützung regelmäßiger Benachrichtigungen in einer Windows Store-App verwenden. Wenn regelmäßige Benachrichtigungen aktiviert sind, greift Windows periodisch auf den benutzerdefinierten API-Endpunkt zu und verwendet das zurückgegebene XML in einem kachelspezifischen Format, um die App-Kachel im Startmenü zu aktualisieren.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr zum Erstellen benutzerdefinierter APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung]
  <br/> Erfahren Sie mehr darüber, wie Sie die Quellcodeverwaltung zum einfacheren und sichereren Entwickeln und Veröffentlichen von benutzerdefiniertem API-Skriptcoed verwenden können.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Definieren benutzerdefinierter APIs mit Unterstützung für periodische Benachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
[Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/documentation/articles/mobile-services-store-scripts-source-control
