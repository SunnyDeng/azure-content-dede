<properties pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="authentication, Facebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey"></tags>

# Erste Schritte bei der Authentifizierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre Windows Phone-App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]
4.  [Speichern von Authentifizierungstoken auf dem Client][Speichern von Authentifizierungstoken auf dem Client]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  Öffnen Sie in Visual Studio das Client-App-Projekt, und stellen Sie sicher, dass die Instanz von \*\*MobileServiceClient\*\* in "App.xaml.cs" für die Verwendung der Cloud-URL zum mobilen Dienst konfiguriert ist.
2.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app][mobile-services-windows-phone-authenticate-app]]

## <a name="tokens"></a>Speichern der Autorisierungstoken auf dem Client

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token][mobile-services-windows-phone-authenticate-app-with-token]]

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern][Dienstweite Autorisierung von Mobile Services-Benutzern] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz][Mobile Services .NET – Erstellen einer konzeptionellen Referenz].

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Speichern von Authentifizierungstoken auf dem Client]: #tokens
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-windows-phone-authenticate-app]: ../includes/mobile-services-windows-phone-authenticate-app.md
  [mobile-services-windows-phone-authenticate-app-with-token]: ../includes/mobile-services-windows-phone-authenticate-app-with-token.md
  [Dienstweite Autorisierung von Mobile Services-Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
  [Mobile Services .NET – Erstellen einer konzeptionellen Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
