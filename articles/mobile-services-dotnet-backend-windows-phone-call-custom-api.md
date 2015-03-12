<properties 
	pageTitle="Aufrufen einer benutzerdefinierten API aus einer Windows Phone-App - Mobile Services" 
	description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer Windows Phone-App aufrufen, die Microsoft Azure Mobile Services verwendet." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Aufrufen einer benutzerdefinierten API aus dem Client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird gezeigt, wie Sie eine benutzerdefinierte API aus einer Windows Phone-App aufrufen. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, die Sie beim Abschluss des Lernprogramms [Hinzufügen von Mobile Services zu einer vorhandenen App](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) erstellt haben. Dafür führen Sie die folgenden Schritte aus:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App]

Dieses Lernprogramm baut auf dem "GetStartedWithData"-Beispiel auf, einer einfachen TodoList-App. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) abschließen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]


## Nächste Schritte

Da Sie nun eine benutzerdefinierte API erstellt und diese über Ihre Windows Phone-App aufgerufen haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über die Erstellung benutzerdefinierter APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung]
  <br/> Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-phone-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Speichern von Serverskripts in der Quellcodeverwaltung]: /de-de/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
