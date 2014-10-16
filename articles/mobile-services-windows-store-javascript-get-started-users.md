<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/de-de/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/de-de/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/" title=".NET backend">.NET backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-store-get-started/) abschließen.

<div class="dev-callout"><b>Hinweis</b>

<p>Dieses Lernprogramm zeigt Ihnen die grundlegende Methode von Mobile Services zur Authentifizierung von Benutzern mithilfe einer Vielzahl von Identitätsanbietern. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Allerdings müssen Sie sich mit dieser Methode auch bei jedem Start Ihrer App anmelden. Wenn Sie stattdessen Live Connect für eine einmalige Anmeldung in Ihrer Windows Store App verwenden möchten, finden Sie hierzu weitere Information unter <a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js">Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect</a>.</p>
</div>

<a name="register"></a>
Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>(Optional) Führen Sie die unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung](/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/) beschriebenen Schritte durch.</p>

    <div class="dev-callout"><b>Hinweis</b>

    <p>Dieser Schritt ist optional, da er nur für Anmeldeanbieter für Microsoft Account relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Schließen Sie diesen Schritt ab, wenn Sie einen Identitätsanbieter für Microsoft Account verwenden möchten.</p>
	</div>
</li>
</ol>

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

<a name="permissions"></a>
Einschränken von Berechtigungen für authentifizierte Benutzer
-------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started/) abgeschlossen haben.</p>

<li><p>Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>

    <p>Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf den mobilen Dienst zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

<a name="add-authentication"></a>
Hinzufügen von Authentifizierung zur App
----------------------------------------

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

<a name="next-steps">
Nächste Schritte
----------------

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern](/de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /de-de/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Get started with Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Get started with authentication]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Authorize users with scripts]: /de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Azure Management Portal]: https://manage.windowsazure.com/
[Register your Windows Store app package for Microsoft authentication]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package