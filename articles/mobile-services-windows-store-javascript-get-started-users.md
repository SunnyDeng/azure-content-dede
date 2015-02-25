<properties pageTitle="Erste Schritte mit der Authentifizierung (JavaScript) | Mobile Dev Center" description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Windows Store-JavaScript-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren.  In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.  

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]
4. [Speichern von Authentifizierungstoken auf dem Client]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

>[AZURE.NOTE]Dieses Lernprogramm demonstriert den von Mobile Services verwalteten Authentifizierungsfluss bei Verwendung einer Vielzahl von Identitätsanbietern. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Wenn Sie stattdessen Live Connect mit clientverwalteter Authentifizierung verwenden und eine Möglichkeit für einmaliges Anmelden in Ihrer Windows Phone App bereitstellen möchten, finden Sie weitere Informationen im Thema [Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]. Bei Verwendung der clientseitig verwalteten Authentifizierung hat die App Zugriff auf zusätzliche Benutzerdaten, die vom Identitätsanbieter verwaltet werden. Sie erhalten dieselben Benutzerdaten in Ihrem mobilen Dienst, indem Sie die **user.getIdentities()**-Funktion in Serverskripts aufrufen. Weitere Informationen finden Sie [in diesem Beitrag](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Optional) Führen Sie die unter <a href="/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung</a>. beschriebenen Schritte durch.</p>

    
	<p>Beachten Sie, dass dieser Schritt optional ist, da er nur für den Anmeldeanbieter für Microsoft-Konten relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Schließen Sie diesen Schritt ab, wenn Sie einen Identitätsanbieter für Microsoft Account verwenden möchten.</p>
</li>
</ol>
Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

##<a name="permissions"></a>Einschränken von Berechtigungen auf authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie im Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started/">Erste Schritte mit Mobile Services</a> erstellt haben.</p></li> 
<li><p>Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>Speichern von Authentifizierungstoken auf dem Client

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm, [Dienstseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts], verwenden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert, um die von Mobile Services zurückgegebenen Daten zu filtern. 


<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Speichern von Authentifizierungstoken auf dem Client]: #tokens
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]: /de-de/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/develop/mobile/how-to-guides/register-windows-store-app-package


<!--HONumber=42-->
