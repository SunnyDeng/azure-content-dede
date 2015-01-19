<properties urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Erste Schritte mit der Authentifizierung (HTML 5) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie, wie Sie Mobile Services zum Authentifizieren von Benutzern der HTML-Anwendung über eine Vielzahl an Identitätsanbietern wie Google, Facebook, Twitter und Microsoft nutzen können." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer HTML- oder PhoneGap-App.  In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.  

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Starten Sie im Verzeichnis der App eine der folgenden Befehlsdateien aus dem **server**-Unterordner.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	>[WACOM.NOTE]Bei einem Windows-Computer geben Sie "R" ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

	Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

2. Öffnen Sie die URL <a href="http://localhost:8000/" target="_blank">"http://localhost:8000/"</a> in einem Webbrowser, um die App zu starten. 

	Das Laden der Daten schlägt fehl. Dies liegt daran, dass die App versucht als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

3. (Optional) Öffnen Sie den Skript-Debugger Ihres Webbrowsers und aktualisieren Sie die Seite. Vergewissern Sie sich, dass ein "Zugriff verweigert"-Fehler auftritt. 

Anschließend werden Sie die App aktualisieren, sodass diese sich authentifiziert, bevor sie Ressourcen aus dem mobilen Dienst abfragt.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

>[WACOM.NOTE]Da die Anmeldung in einem Popup erfolgt, sollten Sie die <strong>login-Methode</strong> aus dem Click-Ereignis einer Schaltfläche heraus aufrufen. Viele Browser unterdrücken ansonsten das Anmeldefenster.

1. Öffnen Sie die Projektdatei index.html, suchen Sie das H1-Element und fügen Sie im Anschluss den folgenden Codeausschnitt ein:

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	Mit diesem Code können Sie sich von der Seite aus bei einem mobilen Dienst anmelden.

2. Suchen Sie am Ende der Datei app.js nach der Codezeile, in der die refreshTodoItems-Funktion aufgerufen wird und ersetzen Sie die Zeile durch den folgenden Code: 
	
		function refreshAuthDisplay() {
			var isLoggedIn = client.currentUser !== null;
			$("#logged-in").toggle(isLoggedIn);
			$("#logged-out").toggle(!isLoggedIn);

			if (isLoggedIn) {
				$("#login-name").text(client.currentUser.userId);
				refreshTodoItems();
			}
		}

		function logIn() {
			client.login("facebook").then(refreshAuthDisplay, function(error){
				alert(error);
			});
		}

		function logOut() {
			client.logout();
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');
		}

		// On page init, fetch the data and set up event handlers
		$(function () {
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');		    
			$("#logged-out button").click(logIn);
			$("#logged-in button").click(logOut);
		});

    Diese Funktionen erledigen den Authentifizierungsprozess. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die Methode <strong>"login"</strong> oben übergebenen Wert auf einen der folgenden Werte: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em>oder <em>aad</em>.

	>[WACOM.NOTE]In einer PhoneGap-App müssen Sie dem Projekt auch die folgenden Plug-Ins hinzufügen:
	><ul><li><code>phonegap-Plug-In: "https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git" hinzufügen</code></li>
	><li><code>phonegap-Plug-In: "https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git" hinzufügen</code></li></ul>

9. Wechseln Sie zum Browser, in dem Ihre App ausgeführt wird, und aktualisieren Sie die Seite. 

	   Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

	>[WACOM.NOTE]Falls Sie Internet Explorer verwenden, kann es sein, dass Sie nach der Anmeldung den folgenden Fehler erhalten: <code>Fensteröffner nicht erreichbar. Möglicherweise befindet sich dieser in einer anderen Internet Explorer-Zone</code>. Dies liegt daran, dass das Popup in einer anderen Sicherheitszone (Internet) als Localhost (Intranet) ausgeführt wird. Dies betrifft nur Apps während der Entwicklung mit Localhost. Um dieses Problem zu umgehen, öffnen Sie die Registerkarte <strong>"Sicherheit"</strong> in den <strong>"Internetoptionen",</strong>klicken Sie auf <strong>"Lokales Intranet",</strong>klicken Sie auf <strong>"Websites"</strong>, und deaktivieren Sie <strong>"Intranetnetzwerk automatisch ermitteln"</strong>. Vergessen Sie nicht, diese Einstellung nach Abschluss Ihrer Tests wiederherzustellen.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm, [Autorisieren von Benutzern mit Skripts], werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung von Mobile Services mit HTML/JavaScript finden Sie unter [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz].

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-html-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-html-get-started-data
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-html-authorize-users-in-scripts

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library

<!--HONumber=35.2-->
