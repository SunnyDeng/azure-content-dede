<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/de-de/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/de-de/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")
[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET-Backend") | [JavaScript-Backend](/de-de/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript-Backend")

Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer iOS-App. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

Zum Abschließen dieses Lernprogramms sind XCode 4.5 und iOS 5.0 oder neuere Versionen erforderlich.

Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

Einschränken von Berechtigungen für authentifizierte Benutzer
-------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-ios-get-started) erstellt haben.

2.  Klicken Sie auf **Ausführen**, das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern. 

## Hinzufügen von Authentifizierung zur App??
[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]??

## Nächste Schritte??
Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern] [Autorisieren von Benutzern mit Skripts] werden Sie die von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellte Benutzer-ID verwenden, um die von Mobile Services zurückgegebenen Daten zu filtern.

[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: \#register 
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: \#permissions 
[Hinzufügen von Authentifizierung zur App]: \#add-authentication 
[Nächste Schritte]:\#next-steps???????

[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png 
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png????????

[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png 
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png 
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png??? 

[Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet 
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios 
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios 
[Erste Schritte mit Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios 
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios 
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios??
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png


[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Get started with data]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure Management Portal]: https://manage.windowsazure.com/
