<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/23/2014" ms.author="krisragh"></tags>

# Erste Schritte bei der Authentifizierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]
4.  [Speichern von Authentifizierungstoken in der App][Speichern von Authentifizierungstoken in der App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  <p>
    Öffnen Sie in Xcode das Schnellstartprojekt der Client-App.
2.  Klicken Sie auf **Ausführen**, das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[WACOM.INCLUDE [mobile-services-ios-authenticate-app][mobile-services-ios-authenticate-app]]

## <a name="store-authentication"></a>Speichern von Authentifizierungstoken in der App

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token][mobile-services-ios-authenticate-app-with-token]]

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern][Dienstweite Autorisierung von Mobile Services-Benutzern] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Speichern von Authentifizierungstoken in der App]: #store-authentication
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-ios-authenticate-app]: ../includes/mobile-services-ios-authenticate-app.md
  [mobile-services-ios-authenticate-app-with-token]: ../includes/mobile-services-ios-authenticate-app-with-token.md
  [Dienstweite Autorisierung von Mobile Services-Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts
