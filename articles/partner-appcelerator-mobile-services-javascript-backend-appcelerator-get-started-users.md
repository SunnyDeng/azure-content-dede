<properties pageTitle="Get started with authentication (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# Erste Schritte bei der Authentifizierung in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a>
    <a href="/de-de/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users" title="Appcelerator" class="current">Appcelerator</a>
</div>

In diesem Thema erfahren Sie, wie Sie Benutzer in Windows Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

Um dieses Lernprogramm durchführen zu können, benötigen Sie Appcelerator Titanium Studio 3.2.1 oder neuer und iOS 7.0 und/oder neuer sowie Android 4.3 oder neuer.

## <a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Mobile Services] geändert haben.

2.  Klicken Sie auf die Schaltfläche „Ausführen“, um das Projekt zu erstellen und die App im iPhone-Simulator zu starten. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die TodoItem-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1.  Öffnen Sie die Datei index.js und suchen Sie in der Tabelle „event Lister method“ nach `case 2:`.

    Sie können in Ihrer App entweder den Benutzer mit verfügbaren Identitätsanbietern auffordern oder automatisch einen bestimmten Identitätsanbieter vorschlagen.

2.  Wenn Sie alle verfügbaren Identitätsanbieter vorhalten möchten, verwenden Sie folgenden Code:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var appName = 'appctest';
        azureMobileServices.setAppName(appName);
        var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
        var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
        });
        dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        });
        dialog.show();

3.  Wenn Sie einen bestimmten Identitätsanbieter vorhalten möchten, verwenden Sie folgenden Code:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

> [WACOM.NOTE] Falls Sie einen anderen Identitätsanbieter als Google verwenden, ändern Sie den an **authorizeClient** oben übergebenen Wert auf einen der folgenden Werte: *microsoftaccount*, *facebook*, *twitter* oder *windowsazureactivedirectory*.

1.  Klicken Sie auf die Schaltfläche „Ausführen“, um das Projekt zu erstellen, starten Sie die App im iPhone- oder Android-Simulator, und klicken Sie dann auf die Option „Mit Anmeldung“, sodass die Anmeldung mit dem Identitätsanbieter erfolgt.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-get-started-users "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-android-get-started-users "Android"
  [HTML]: /de-de/documentation/articles/mobile-services-html-get-started-users "HTML"
  [Xamarin.iOS]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android"
  [Appcelerator]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users "Appcelerator"
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
