<properties
	pageTitle="Authentifizierung und Autorisierung in Azure Mobile Apps | Microsoft Azure"
	description="Eine grundlegende Übersicht über das Feature „Authentifizierung/Autorisierung“ für Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="mahender"/>

# Authentifizierung und Autorisierung in Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Was ist App Service-Authentifizierung/Autorisierung?

App Service-Authentifizierung/Autorisierung ist ein Feature, das einer Anwendung die Anmeldung von Benutzern ermöglicht, ohne das Codeänderungen am Back-End der App erforderlich werden. Es stellt eine einfache Möglichkeit zum Schutz Ihrer Anwendung und für die Arbeit mit benutzerspezifischen Daten bereit.

App Service verwendet die Verbundidentität, in der ein **Identitätsanbieter** (IDP) eines Drittanbieters Konten speichert und Benutzer authentifiziert und die Anwendung diese anstelle der eigenen Identität verwendet. App Service unterstützt standardmäßig fünf Identitätsanbieter: _Azure Active Directory_, _Facebook_, _Google_, das _Microsoft-Konto_ und _Twitter_. Sie können diese Unterstützung für Ihre Apps auch erweitern, indem Sie einen anderen Identitätsanbieter oder eine eigene benutzerdefinierte Identitätslösung integrieren.

Ihre App kann eine beliebige Anzahl dieser Identitätsanbieter nutzen, sodass Sie Ihren Endbenutzern verschiedene Optionen für die Anmeldung bereitstellen können.

Wenn Sie sofort beginnen möchten, schauen Sie sich die folgenden Tutorials an:

- [Hinzufügen der Authentifizierung zu Ihrer iOS-App]
- [Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App]
- [Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App]
- [Hinzufügen der Authentifizierung zu Ihrer Windows-App]

## Funktionsweise der Authentifizierung

Zur Authentifizierung mit einem der Identitätsanbieter müssen Sie zunächst den Identitätsanbieter konfigurieren, damit er Ihre Anwendung erkennt. Der Identitätsanbieter stellt anschließend IDs und geheime Schlüssel bereit, die Sie wiederum der Anwendung bereitstellen. Damit wird die Vertrauensstellung hergestellt und App Service kann bereitgestellte Identitäten überprüfen.

Diese Schritte werden in den folgenden Themen genauer beschrieben:

- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Google-Anmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Twitter-Anmeldung]

Sobald das Back-End entsprechend konfiguriert ist, können Sie Ihren Client für die Anmeldung ändern. Dazu gibt es zwei Ansätze:

- Lassen Sie das Mobile Apps-Client-SDK die Anmeldung von Benutzern unter Verwendung einer einzelnen Codezeile durchführen.
- Nutzen Sie ein von einem bestimmten Identitätsanbieter veröffentlichtes SDK zum Einrichten der Identität und für den Zugriff auf App Service.

>[AZURE.TIP]Die meisten Anwendungen sollten ein Anbieter-SDK verwenden, um eine homogenere Anmeldeumgebung zu bieten und von Aktualisierungsunterstützung sowie anderen anbieterspezifischen Vorteile zu profitieren.

### Funktionsweise der Authentifizierung ohne ein Anbieter-SDK

Wenn Sie kein Anbieter-SDK einrichten möchten, können Sie Mobile Apps erlauben, die Anmeldung für Sie auszuführen. Das Mobile Apps-Client-SDK öffnet eine Webansicht für den Anbieter Ihrer Wahl und schließt die Anmeldung ab. Gelegentlich wird dies in Blogs und Foren als „Serverfluss“ oder „servergesteuerter Datenfluss“ bezeichnet, da der Server die Anmeldung verwaltet und das Client-SDK das Anbieter-Token nie empfängt.

Der zum Starten dieses Datenflusses benötigte Code wird im Tutorial zur Authentifizierung für die einzelnen Plattformen behandelt. Am Ende des Datenflusses verfügt das Client-SDK über ein App Service-Token, und das Token wird automatisch an alle Anfragen an das Back-End angefügt.

### Funktionsweise der Authentifizierung mit einem Anbieter-SDK

Die Verwendung eines Anbieter-SDKs ermöglicht der Anmeldeumgebung, auf der die App ausgeführt wird, enger mit der Betriebssystemplattform zu interagieren. Dadurch erhalten Sie außerdem ein Anbietertoken und bestimmte Benutzerinformationen auf dem Client, was die Nutzung von Graph-APIs und die Anpassung der Benutzerumgebung wesentlich vereinfacht. Gelegentlich wird dies in Blogs und Foren als „Clientfluss“ oder „clientgesteuerter Datenfluss“ bezeichnet, da Code auf dem Client die Anmeldung verarbeitet und der Clientcode Zugriff auf ein Anbieter-Token erhält.

Sobald ein Anbietertoken abgerufen wurde, muss es zur Überprüfung an App Service gesendet werden. Am Ende des Datenflusses verfügt das Client-SDK über ein App Service-Token, und das Token wird automatisch an alle Anfragen an das Back-End angefügt. Der Entwickler kann bei Bedarf auch einen Verweis auf das Anbietertoken speichern.

## Funktionsweise der Autorisierung

App Service-Authentifizierung/Autorisierung stellt mehrere Optionen für **Maßnahmen bereit, die ergriffen werden können, wenn die Anforderung nicht authentifiziert werden kann**. Bevor Ihr Code eine bestimmte Anforderung erhält, können Sie App Service prüfen lassen, ob die Anforderung authentifiziert wurde. Wenn nicht, wird die Anforderung abgelehnt, und bevor ein neuer Versuch unternommen wird, muss der Benutzer sich anmelden.

Eine Möglichkeit besteht darin, nicht authentifizierte Anforderungen an einen der Identitätsanbieter umzuleiten. In einem Webbrowser würde dies den Benutzer auf eine neue Seite leiten. Ihre mobiler Client kann jedoch nicht auf diese Weise umgeleitet werden, und nicht authentifizierte Antworten erhalten eine HTTP _401 Unauthorized_-Antwort. Angesichts dessen sollte die erste Anforderung Ihres Clients immer an den Anmeldeendpunkt erfolgen. Anschließend können beliebige andere APIs aufgerufen werden. Wenn Sie versuchen, eine andere API aufzurufen, bevor Sie sich angemeldet haben, erhält der Client eine Fehlermeldung.

Wenn Sie eine präzisere Kontrolle darüber haben möchten, für welche Endpunkte eine Authentifizierung erforderlich ist, können Sie für nicht authentifizierte Anforderungen auch die Option „Keine Aktion (Anforderung zulassen)“ auswählen. In diesem Fall werden alle Authentifizierungsentscheidungen in Ihren Anwendungscode verschoben. Das ermöglicht Ihnen außerdem, bestimmten Benutzern basierend auf benutzerdefinierten Autorisierungsregeln Zugriff zu gewähren.

## Dokumentation

Die folgenden Tutorials zeigen, wie Sie die Authentifizierung zu Ihren mobilen Clients mithilfe von App Service hinzufügen können:

- [Hinzufügen der Authentifizierung zu Ihrer iOS-App]
- [Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App]
- [Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App]
- [Hinzufügen der Authentifizierung zu Ihrer Windows-App]

Die folgenden Tutorials zeigen, wie Sie App Service konfigurieren, um verschiedene Authentifizierungsanbieter nutzen zu können:

- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Google-Anmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung]
- [Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Twitter-Anmeldung]

Wenn Sie ein anderes Identitätssystem als die hier beschriebenen verwenden möchten, können Sie die [Vorschau zur Unterstützung benutzerdefinierter Authentifizierung aus dem Server .NET-SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth) nutzen.

Einige weitere Details finden Sie auch in den oben beschriebenen Workflows in der [Übersicht über die App Service-Authentifizierung](app-service-authentication-overview.md). Dieses Thema enthält auch Informationen zum App Service-Gateway, das in Mobile Apps zwar nicht mehr verwendet wird, dessen konzeptioneller Inhalt aber weiterhin gilt.

[Hinzufügen der Authentifizierung zu Ihrer iOS-App]: app-service-mobile-ios-get-started-users.md
[Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App]: app-service-mobile-xamarin-ios-get-started-users.md
[Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App]: app-service-mobile-xamarin-android-get-started-users.md
[Hinzufügen der Authentifizierung zu Ihrer Windows-App]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung]: app-service-mobile-how-to-configure-facebook-authentication.md
[Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Google-Anmeldung]: app-service-mobile-how-to-configure-google-authentication.md
[Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Twitter-Anmeldung]: app-service-mobile-how-to-configure-twitter-authentication.md

<!---HONumber=AcomDC_1203_2015-->