<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/23/2014" ms.author="ricksal"></tags>

# Erste Schritte bei der Authentifizierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services &uuml;ber Ihre App authentifizieren. In diesem Lernprogramm f&uuml;gen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identit&auml;tsanbieter, der von Mobile Services unterst&uuml;tzt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">10:42</span></div>

</div>

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]
4.  [Speichern von Authentifizierungstoken auf dem Client][Speichern von Authentifizierungstoken auf dem Client]
5.  [Aktualisieren abgelaufener Token][Aktualisieren abgelaufener Token]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

Dieses Lernprogramm benötigt Eclipse und Android 4.2 oder eine neuere Version.

## <a name="register"></a><span class="short-header">Registrieren Ihrer App</span>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a><span class="short-header">Einschränken von Berechtigungen</span>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit mobilen Diensten][Erste Schritte mit Mobile Services] erstellt haben.

2.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a><span class="short-header">Hinzufügen von Authentifizierung</span>Hinzufügen von Authentifizierung zur App

[WACOM.INCLUDE [mobile-services-android-authenticate-app][mobile-services-android-authenticate-app]]

## <a name="cache-tokens"></a>Zwischenspeichern von Authentifizierungstoken auf dem Client

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][mobile-services-android-authenticate-app-with-token]]

## <a name="refresh-tokens"></a>Aktualisieren des Tokencaches

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][mobile-services-android-authenticate-app-refresh-token]]

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Lernprogramm ansehen]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Speichern von Authentifizierungstoken auf dem Client]: #cache-tokens
  [Aktualisieren abgelaufener Token]: #refresh-tokens
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-android
