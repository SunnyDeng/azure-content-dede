<properties writer="ricksal" pageTitle="Aufrufen einer benutzerdefinierten API von einem Android-Client | Mobile Dev Center" metaKeywords="" description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Android-App aufrufen, die Microsoft Azure Mobile Services verwendet." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# Aufrufen einer benutzerdefinierten API aus dem Client

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema erfahren Sie, wie Sie eine benutzerdefinierte API aus einer Android-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging; dazu gehört auch das Lesen und Festlegen von HTTP-Nachrichtenheadern und das Definieren eines von JSON abweichenden Nachrichtentextformats.

Mit der mithilfe dieses Themas erstellten benutzerdefinierten API können Sie eine einzelne POST-Anforderung senden, die das *Abgeschlossen*-Flag für alle ausstehenden Elemente in der Tabelle Ihres mobilen Diensts auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktion zur App hinzu, die Sie entweder im Lernprogramm [Erste Schritte mit mobilen Diensten] oder im Lernprogramm [Erste Schritte mit Daten] hinzugefügt haben. Führen Sie dazu folgende Schritte durch:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App] 

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen, bevor Sie mit diesem Lernprogramm beginnen. 

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

## Nächste Schritte

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Android-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

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
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/documentation/articles/mobile-services-store-scripts-source-control
