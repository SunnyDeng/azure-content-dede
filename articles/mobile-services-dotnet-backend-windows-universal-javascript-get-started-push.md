<properties pageTitle="Erste Schritte mit Pushbenachrichtigungen mit einem mobilen .NET-Back-End-Dienst" metaKeywords="" description="Erfahren Sie, wie Sie Azure Mobile Services und Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre universelle Windows-App zu senden." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />


# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Back-End Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungs-Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom .NET-Back-End an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

>[WACOM.NOTE]In diesem Thema wird erläutert, wie Sie mit den in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von mobilen Diensten an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) des Themas weitere Informationen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen](#register)
2. [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen](#update-service)
3. [Aktivieren von Pushbenachrichtigungen für lokale Tests](#local-testing)
4. [Testen von Pushbenachrichtigungen in der App](#test)

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein Aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 oder eine höhere Version). <br/>Dazu ist eine kostenlose Testversion verfügbar. 

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navigieren Sie zum Projektordner <code>\services\mobileServices\scripts</code>, kopieren Sie die erzeugte Skriptdatei &lt;<em>your_service_name</em>&gt;.push.register.js in den freigegebenen Ordner <code>\js</code>, und löschen Sie diese Datei dann sowohl aus dem Windows-App- als auch aus dem Windows Phone-App-Projekt.</p></li> 
<li><p>Öffnen Sie die Skriptdatei im freigegebenen Projektordner <code>\js</code>, suchen Sie den Ereignis-Listener <em>activated</em>, der die Channel-URL des Geräts beim Benachrichtigungshub registriert, und löschen Sie die Promise-Funktion<strong>done</strong>.</p>
<p>In diesem Lernprogramm werden Benachrichtigungen nicht beim Aufruf einer benutzerdefinierten API, sondern beim Einfügen eines neuen Elements gesendet.</p></li>
<li><p>Öffnen Sie im Windows-App-Projekt die Datei "default.html", und ändern Sie den im Verweis auf die Skriptdatei angegebenen Pfad zum gemeinsam genutzten Projektordner <code>\js</code>, sodass er folgendermaßen lautet:</p><pre><code>&lt;script src="/js/your_service_name.push.register.js""&gt;&lt;/script&gt;</code></pre></li>
<li><p>Wiederholen Sie diesen Schritt für das Windows Phone-App-Projekt.</p>
<p>Nun wird in beiden Projekten eine gemeinsame Version des Skripts für die Pushregistrierung verwendet.</p></li>
</ol>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen. 

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird die bereits vorhandene TodoItemController-Klasse so aktualisiert, dass sie eine Pushbenachrichtigung an alle registrierten Geräte sendet, wenn ein neues Element eingefügt wird. Sie können ähnlichen Code in jedem benutzerdefinierten [ApiController], [TableController] oder an beliebiger anderer Stelle in Back-End-Diensten implementieren. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Die restlichen Schritte in diesem Abschnitt sind optional. Sie dienen dazu, die App mit einem mobilen Dienst zu testen, der auf einem lokalen Computer ausgeführt wird. Wenn Sie planen, die Pushbenachrichtigungen mit dem in Azure gehosteten mobilen Dienst zu testen, können Sie diese Schritte überspringen und einfach zum letzten Abschnitt wechseln. Dies ist so, weil der Assistent zum Hinzufügen von Pushbenachrichtigungen die App bereits für die Verbindung mit dem in Azure gehosteten Dienst konfiguriert hat.  

>[WACOM.NOTE]Verwenden Sie niemals einen in der Produktionsumgebung genutzten mobilen Dienst für Test- und Entwicklungszwecke. Veröffentlichen Sie das mobile Dienstprojekt zum Testen stets in einem getrennten Staging-Dienst.

<ol start="5">
<li><p>Navigieren Sie zum Projektordner <code>\services\mobileServices\settings</code>, kopieren Sie die erzeugte Skriptdatei &lt;<em>Name_Ihres_Diensts</em>&gt;.js" in den gemeinsam genutzten Projektordner <code>\js</code>, und löschen Sie dann diese Datei aus dem Windows-App- und aus dem Windows Phone-App-Projekt. Löschen Sie die Datei außerdem auch aus dem Ordner <code>\services\mobileServices\scripts</code> aller App-Projekte, sofern sie dort vorhanden ist.</p></li> 
<li><p>Öffnen Sie die Skriptdatei im gemeinsam genutzten Projektordner <code>\js</code>, und kommentieren Sie den vorhandenen Code aus, in dem das <a href="http://msdn.microsoft.com/de-de/library/azure/jj554219.aspx">MobileServiceClient-Objekt</a> für den Zugriff auf den in Azure gehosteten mobilen Dienst definiert wird.</p></li>
<li><p>Fügen Sie eine neue <strong>MobileServiceClient</strong>-Objektdefinition mit demselben Namen hinzu, die jedoch im Konstruktor die URL des lokalen Computers verwendet, ähnlich dem folgenden Code:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>Bei Verwendung dieses <strong>MobileServiceClient</strong>-Objekts verbindet sich die App nicht mit der in Azure gehosteten Version des Dienstes, sondern mit dem lokalen Dienst. Wenn Sie wieder umschalten und die App mit dem in Azure gehosteten mobilen Dienst ausführen möchten, wechseln Sie wieder zurück zu den ursprünglichen <strong>MobileServiceClient</strong>-Objektdefinitionen.</p></li>
</ol>

##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Benachrichtigungshubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr zum Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr darüber, wie sich Benutzer Ihrer App mit verschiedenen Kontotypen mit Mobile Services authentifizieren können.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Notification Hubs arbeiten, um Benachrichtigungen auf allen großen Clientplattformen an Ihre Apps zu senden.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services in HTML- und JavaScript-Apps.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/

[So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library
