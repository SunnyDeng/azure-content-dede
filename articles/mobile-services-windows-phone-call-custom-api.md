<properties urlDisplayName="Call a custom API from the client" pageTitle="Aufrufen einer benutzerdefinierten API von einem Windows Phone-Client - Mobile Services" metaKeywords="" description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Windows Phone-App aufrufen, die Azure Mobile Services verwendet." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/06/2014" ms.author="glenga" />

# Aufrufen einer benutzerdefinierten API aus dem Client

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Windows Phone-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das completed-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf "true" festlegt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Diese Funktionalität wird der App hinzugefügt, nachdem Sie das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App](/de-de/documentation/articles/mobile-services-windows-phone-get-started-data/) beendet haben. Führen Sie dazu folgende Schritte durch:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App] 

Dieses Lernprogramm basiert auf dem GetStartedWithData-Beispiel, eine einfache TodoList-App. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Hinzufügen von Mobile Services zu einer vorhandenen App](/de-de/documentation/articles/mobile-services-windows-phone-get-started-data/) ausführen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## Nächste Schritte

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Windows Phone-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr zum Erstellen benutzerdefinierter APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung]
  <br/> Erfahren Sie mehr darüber, wie Sie die Quellcodeverwaltung zum einfacheren und sichereren Entwickeln und Veröffentlichen von benutzerdefiniertem API-Skriptcode verwenden können.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-phone-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-push/

[Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/documentation/articles/mobile-services-store-scripts-source-control
