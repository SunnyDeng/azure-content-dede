<properties 
	pageTitle="Erste Schritte mit der Authentifizierung (HTML 5) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer HTML-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="glenga"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer HTML-App wie z. B. PhoneGap oder Cordova. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Starten Sie im Ordner der App eine der folgenden Befehlsdateien aus dem **server**-Unterordner.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	>[AZURE.NOTE]Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

	Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

2. Öffnen Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in einem Webbrowser, um die App zu starten.

	Die Daten können nicht geladen werden. Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf den mobilen Dienst zugreift und die _TodoItem_-Tabelle nun eine Authentifizierung verlangt.

3. (Optional) Öffnen Sie den Skript-Debugger Ihres Webbrowsers und aktualisieren Sie die Seite. Vergewissern Sie sich, dass ein "Zugriff verweigert"-Fehler auftritt.

Anschließend werden Sie die App aktualisieren, sodass diese sich authentifiziert, bevor sie Ressourcen aus dem mobilen Dienst abfragt.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

>[AZURE.NOTE]Da die Anmeldung in einem Popup erfolgt, sollten Sie die <strong>login</strong>-Methode aus dem Click-Ereignis einer Schaltfläche heraus aufrufen. Viele Browser unterdrücken ansonsten das Anmeldefenster.

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

    Diese Funktionen erledigen den Authentifizierungsprozess. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die **login**-Methode übergebenen Wert auf einen der folgenden Werte: *microsoftaccount*, *facebook*, *twitter*, *google* oder *aad*.

	>[AZURE.IMPORTANT]In einer PhoneGap-App müssen Sie dem Projekt auch die folgenden Plug-Ins hinzufügen:<ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Wechseln Sie zum Browser, in dem Ihre App ausgeführt wird, und aktualisieren Sie die Seite.

	   Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

	>[AZURE.NOTE]Falls Sie Internet Explorer verwenden, kann es sein, dass Sie nach der Anmeldung den folgenden Fehler erhalten: <code>Fensteröffner nicht erreichbar. Möglicherweise befindet sich dieser in einer anderen Internet Explorer-Zone</code>. Dies liegt daran, dass das Popup in einer anderen Sicherheitszone (Internet) als Localhost (Intranet) ausgeführt wird. Dies betrifft nur Apps während der Entwicklung mit Localhost. Um dies zu vermeiden, öffnen Sie die Registerkarte <strong>Sicherheit</strong> in den <strong>Internet-Optionen</strong>, klicken Sie auf <strong>Lokales Intranet</strong>, anschließend auf <strong>Sites</strong> und deaktivieren Sie <strong>Intranetnetzwerk automatisch ermitteln</strong>. Vergessen Sie nicht, diese Einstellung nach Abschluss Ihrer Tests wiederherzustellen.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung von mobilen Diensten mit HTML/JavaScript finden Sie unter [Mobile Services HTML/JavaScript How-to Conceptual Reference]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: mobile-services-html-get-started.md
[Get started with data]: mobile-services-html-get-started-data.md
[Autorisieren von Benutzern mit Skripts]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=July15_HO1-->