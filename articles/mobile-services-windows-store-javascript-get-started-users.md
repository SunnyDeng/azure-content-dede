<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Erste Schritte bei der Authentifizierung in Mobile Services

> [AZURE.SELECTOR-LIST (Plattform | Backend )]
-   [(iOS | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
-   [(iOS | JavaScript)](/de-de/documentation/articles/mobile-services-ios-get-started-users/)
-   [(Windows C# | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
-   [(Windows C# | Javascript)](/de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
-   [(Windows JavaScript | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
-   [(Windows JavaScript | Javascript)](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
-   [(Windows Phone | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
-   [(Windows Phone | Javascript)](/de-de/documentation/articles/mobile-services-windows-phone-get-started-users/)
-   [(Android | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
-   [(Android | Javascript)](/de-de/documentation/articles/mobile-services-android-get-started-users/)
-   [(Xamarin iOS | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
-   [(Xamarin iOS | Javascript)](/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
-   [(HTML | Javascript)](/de-de/documentation/articles/mobile-services-html-get-started-users/)
-   [(Xamarin Android | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
-   [(Xamarin Android | Javascript)](/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
-   [(Appcelerator | Javascript)](/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App]
4.  [Speichern von Authentifizierungstoken auf dem Client]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

> [WACOM.NOTE]Dieses Lernprogramm veranschaulicht den von Mobile Services verwalteten Authentifizierungsfluss anhand verschiedener Identitätsanbieter. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Wenn Sie stattdessen Live Connect mit clientverwalteter Authentifizierung verwenden und eine Möglichkeit für einmaliges Anmelden in Ihrer Windows Phone App bereitstellen möchten, finden Sie weitere Informationen im Thema [Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]. Durch die clientverwaltete Authentifizierung hat die App Zugriff auf weitere Benutzerdaten, die vom Identitätsanbieter verwaltet werden. Sie erhalten dieselben Benutzerdaten in Ihrem mobilen Dienst, indem Sie die **user.getIdentities()**-Funktion in Serverskripts aufrufen. Weitere Informationen finden Sie in [diesem Beitrag](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)

1.  (Optional) Führen Sie die unter <a href="/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung</a> beschriebenen Schritte durch.

    <div class="dev-callout"><b>Hinweis</b>
    <p>Dieser Schritt ist optional, da er nur f&uuml;r Anmeldeanbieter f&uuml;r Microsoft Account relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen f&uuml;r Microsoft Account f&uuml;r eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Schlie&szlig;en Sie diesen Schritt ab, wenn Sie einen Identit&auml;tsanbieter f&uuml;r Microsoft Account verwenden m&ouml;chten.</p>
    </div>

Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

1.  Öffnen Sie im Visual Studio 2012 Express für Windows 8 das Projekt, das Sie beim Abschluss des Lernprogramms <a href="/de-de/develop/mobile/tutorials/get-started/">Erste Schritte mit Mobile Services</a> erstellt haben.

2.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="tokens"></a>Speichern der Autorisierungstoken auf dem Client

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern][Dienstweite Autorisierung von Mobile Services-Benutzern] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.



  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Speichern von Authentifizierungstoken auf dem Client]: #tokens
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
  [Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]: /de-de/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [diesem Beitrag]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Dienstweite Autorisierung von Mobile Services-Benutzern]: /de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
