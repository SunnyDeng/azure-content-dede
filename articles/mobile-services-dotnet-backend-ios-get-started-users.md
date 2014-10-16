<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" class="current">iOS</a><!--<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title=".NET backend" class="current">.NET backend</a> | <a href="/de-de/documentation/articles/mobile-services-ios-get-started-users/"  title="JavaScript backend">JavaScript backend</a></div>

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

<a name="register"></a>
Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>Öffnen Sie in Visual Studio die Datei Web.config für das Mobildienstprojekt, und geben Sie im Abschnitt "appSettings" den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben.</p>

    <p>Diese Einstellungen werden bei der lokalen Entwicklung verwendet. Nach der Veröffentlichung Ihres mobilen Dienstes in Azure werden diese Einstellungen durch die im Portal eingegebenen Werte überschrieben.</p></li>
</ol>

<a name="permissions">
Einschränken von Berechtigungen für authentifizierte Benutzer
-------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>Öffnen Sie in Xcode das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-ios-get-started) erstellt haben.</p></li>

<li><p>Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Überprüfen Sie, ob eine unverarbeitete Ausnahme mit dem Statuscode 401 (nicht autorisiert) nach dem Start der App ausgelöst wird.</p>

    <p>Dies geschieht, da die App versucht, als nicht autorisierter Benutzer auf Mobile Services zuzugreifen, die *TodoItem*-Tabelle jetzt aber eine Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.
 
<a name="add-authentication">
## Hinzufügen von Authentifizierung zur App 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

<a name="next-steps">
## Nächste Schritte

Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern] [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. 


<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register 
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions 
[Hinzufügen von Authentifizierung zur App]: #add-authentication 
[Nächste Schritte]:#next-steps 

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ 
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[Erste Schritte mit Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/ 
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


