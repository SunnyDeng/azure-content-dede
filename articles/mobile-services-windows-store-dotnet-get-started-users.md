<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn="" Gailey" />

# Erste Schritte bei der Authentifizierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services &uuml;ber Ihre App authentifizieren. In diesem Lernprogramm f&uuml;gen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identit&auml;tsanbieter, der von Mobile Services unterst&uuml;tzt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.</p>
<p>Sie k&ouml;nnen sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">10:04:00</span></div>

</div>

> [WACOM.NOTE]Bei Verwendung der JavaScript-Clientbibliothek von Mobile Services wird die Authentifizierung für Windows Phone Store 8.1-Apps derzeit nicht unterstützt. Wenn Sie über ein Projekt für eine universelle Windows-App verfügen, das den JavaScript-Client nutzt, sind Sie derzeit nicht in der Lage, Benutzer in Windows Phone zu authentifizieren.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]
4.  [Speichern von Authentifizierungstoken auf dem Client][Speichern von Authentifizierungstoken auf dem Client]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

> [WACOM.NOTE]Dieses Lernprogramm veranschaulicht den von Mobile Services verwalteten Authentifizierungsfluss anhand verschiedener Identitätsanbieter. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Wenn Sie stattdessen Live Connect mit clientverwalteter Authentifizierung verwenden und eine Möglichkeit für einmaliges Anmelden in Ihrer Windows Phone App bereitstellen möchten, finden Sie weitere Informationen im Thema [Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect][Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]. Durch die clientverwaltete Authentifizierung hat die App Zugriff auf weitere Benutzerdaten, die vom Identitätsanbieter verwaltet werden. Sie erhalten dieselben Benutzerdaten in Ihrem mobilen Dienst, indem Sie die **user.getIdentities()**-Funktion in Serverskripts aufrufen. Weitere Informationen finden Sie in [diesem Beitrag][diesem Beitrag].

## <a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Optional) Führen Sie die unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung][Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung] beschriebenen Schritte durch.

    <div class="dev-callout"><b>Hinweis</b>
    <p>Dieser Schritt ist optional, da er nur f&uuml;r Anmeldeanbieter f&uuml;r Microsoft Account relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen f&uuml;r Microsoft Account f&uuml;r eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Schlie&szlig;en Sie diesen Schritt ab, wenn Sie einen Identit&auml;tsanbieter f&uuml;r Microsoft Account verwenden m&ouml;chten.</p>
    </div>

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Öffnen Sie im Visual Studio 2012 Express für Windows 8 das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services][1] erstellt haben.

2.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Speichern des Autorisierungstokens auf dem Client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern][Dienstweite Autorisierung von Mobile Services-Benutzern] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz][Mobile Services .NET – Erstellen einer konzeptionellen Referenz].

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Speichern von Authentifizierungstoken auf dem Client]: #tokens
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
  [Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [diesem Beitrag]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /de-de/documentation/articles/mobile-services-windows-store-get-started
  [mobile-services-windows-dotnet-authenticate-app]: ../includes/mobile-services-windows-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Dienstweite Autorisierung von Mobile Services-Benutzern]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Mobile Services .NET – Erstellen einer konzeptionellen Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
