<properties writer="ricksal" pageTitle="Aufrufen einer benutzerdefinierten API über einen Android-Client | Mobile Dev Center" description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Android-App aufrufen, die Azure Mobile Services verwendet." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga"/>

# Aufrufen einer benutzerdefinierten API aus dem Client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Android-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Mit der im Rahmen dieses Themas erstellten benutzerdefinierten API können Sie eine einzelne POST-Anforderung senden, die das  *completed*-Kennzeichen für sämtliche todo-Elemente in der Tabelle Ihres mobilen Diensts auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] erstellen. Dafür führen Sie die folgenden Schritte aus:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App]


>[AZURE.NOTE] Den Quellcode der fertiggestellten App finden Sie <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">hier</a>.

##Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## Nächste Schritte

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Android-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:



* [Mobile Services: Serverskriptreferenz]
  <br/>Weitere Informationen zum Erstellen von benutzerdefinierten APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung]
  <br/> Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]: #next-steps

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-android-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-android-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-android-get-started-push/

[Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
