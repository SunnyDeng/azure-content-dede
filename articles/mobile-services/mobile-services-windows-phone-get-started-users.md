<properties 
	pageTitle="Erste Schritte mit der Authentifizierung (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Windows Phone-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">10:50</span></div>
</div>

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen außerdem zunächst das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

>[AZURE.NOTE]Dieses Lernprogramm demonstriert den von Mobile Services verwalteten Authentifizierungsfluss bei Verwendung einer Vielzahl von Identitätsanbietern. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Wenn Sie stattdessen Live Connect mit clientverwalteter Authentifizierung verwenden und eine Möglichkeit für einmaliges Anmelden in Ihrer Windows Phone App bereitstellen möchten, finden Sie weitere Informationen im Thema [Einmalige Anmeldung für Windows Phone-Apps mithilfe von Live Connect]. Durch die clientverwaltete Authentifizierung hat die App Zugriff auf weitere Benutzerdaten, die vom Identitätsanbieter verwaltet werden. Sie erhalten dieselben Benutzerdaten in Ihrem mobilen Dienst, indem Sie die **user.getIdentities()**-Funktion in Serverskripts aufrufen. Weitere Informationen finden Sie in [diesem Beitrag](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services


[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]


##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


<ol start="3">
<li>Öffnen Sie in Visual Studio 2012 Express für Windows&#160;Phone das Projekt, das Sie erstellt haben, als Sie das Lernprogramm <strong>Erste Schritte mit Mobile Services</strong> abgeschlossen haben.</li>

<li>Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die <em>TodoItem</em>-Tabelle nun eine Authentifizierung verlangt.</li></ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Speichern der Autorisierungstoken auf dem Client

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern](mobile-services-javascript-backend-service-side-authorization.md) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Einmalige Anmeldung für Windows Phone-Apps mithilfe von Live Connect]: mobile-services-windows-phone-single-sign-on.md
 

<!---HONumber=July15_HO2-->