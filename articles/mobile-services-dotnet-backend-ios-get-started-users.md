<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")

[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

5.  Öffnen Sie in Visual Studio die Datei Web.config für das Mobildienstprojekt, und geben Sie im Abschnitt "appSettings" den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben.

    Diese Einstellungen werden bei der lokalen Entwicklung verwendet. Nach der Veröffentlichung Ihres mobilen Dienstes in Azure werden diese Einstellungen durch die im Portal eingegebenen Werte überschrieben.

Einschränken von Berechtigungen für authentifizierte Benutzer
-------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

6.  Öffnen Sie in Xcode das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services](/en-us/documentation/articles/mobile-services-ios-get-started) erstellt haben.

7.  Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Überprüfen Sie, ob eine unverarbeitete Ausnahme mit dem Statuscode 401 (nicht autorisiert) nach dem Start der App ausgelöst wird.

    Dies geschieht, da die App versucht, als nicht autorisierter Benutzer auf Mobile Services zuzugreifen, die *TodoItem*-Tabelle jetzt aber eine Authentifizierung erfordert.

    Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.
 
## Hinzufügen von Authentifizierung zur App 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

## Nächste Schritte

Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern] [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. 


<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register 
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions 
[Hinzufügen von Authentifizierung zur App]: #add-authentication 
[Nächste Schritte]:#next-steps 

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ 
[Erste Schritte mit Daten]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[Erste Schritte mit Authentifizierung]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[Erste Schritte mit Pushbenachrichtigungen]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[Autorisieren von Benutzern mit Skripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/ 
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


