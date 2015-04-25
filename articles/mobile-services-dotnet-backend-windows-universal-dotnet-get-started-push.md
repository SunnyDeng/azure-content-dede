<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen mit einem mobilen .NET-Back-End-Dienst" 
	description="Erfahren Sie, wie Sie Azure Mobile Services und Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre universelle Windows-App zu senden." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zu einer Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Back-End Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure Notification Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom .NET-Back-End an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mit den in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von mobilen Diensten an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen in Mobile Services](mobile-services-dotnet-backend-windows-phone-get-started-push.md).

> Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) des Themas weitere Informationen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen](#register)
2. [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen](#update-service)
3. [Aktivieren von Pushbenachrichtigungen für lokale Tests](#local-testing)
4. [Testen von Pushbenachrichtigungen in der App](#test)

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 oder eine höhere Version). <br/>Dazu ist eine kostenlose Testversion verfügbar. 

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navigieren Sie zum <code>\Services\MobileServices\your_service_name</code> Projektordner, öffnen Sie die generierte Codedatei push.register.cs, und überprüfen Sie die Methode <strong>UploadChannel</strong>, welche die Kanal-ID des Geräts beim Notification Hub registriert.</p></li> 
<li><p>Öffnen Sie die gemeinsam genutzte Codedatei App.xaml.cs. Beachten Sie den Aufruf der neuen Methode <strong>UploadChannel</strong>, der dem Ereignishandler <strong>OnLaunched</strong> wurde.</p> <p>Dies stellt sicher, dass versucht wird, das Gerät zu registrieren, sobald die App gestartet wird.</p></li>
<li><p>Führen Sie die vorigen Schritte erneut aus, um dem Windows Phone Store-App-Projekt Pushbenachrichtigungen hinzuzufügen, und entfernen Sie dann in der gemeinsam genutzten Datei App.xaml.cs den zusätzlichen Aufruf von <strong>UploadChannel</strong> sowie den restlichen <code>#if...#endif</code> bedingten Wrapper.</p> <p>In beiden Projekten kann nun ein einziger Aufruf von <strong>UploadChannel</strong> verwendet werden.</p>

> [AZURE.NOTE] Sie können den generierten Code auch vereinfachen, indem Sie die in <code>#if...#endif</code> gekapselten [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx)-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App genutzt wird.

</li>
</ol>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen. 

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird die bereits vorhandene TodoItemController-Klasse so aktualisiert, dass sie eine Pushbenachrichtigung an alle registrierten Geräte sendet, wenn ein neues Element eingefügt wird. Sie können ähnlichen Code in jedem benutzerdefinierten [ApiController], [TableController] oder an beliebiger anderer Stelle in Ihren Back-End-Diensten implementieren. 

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Die restlichen Schritte in diesem Abschnitt sind optional. Sie dienen dazu, die App mit einem mobilen Dienst zu testen, der auf einem lokalen Computer ausgeführt wird. Wenn Sie planen, die Pushbenachrichtigungen mit dem in Azure gehosteten mobilen Dienst zu testen, können Sie diese Schritte überspringen und einfach zum letzten Abschnitt wechseln. Dies ist möglich, weil der Assistent zum Hinzufügen von Pushbenachrichtigungen die App bereits für die Verbindung mit dem in Azure gehosteten Dienst konfiguriert hat. 

>[AZURE.NOTE]Verwenden Sie niemals einen in der Produktionsumgebung genutzten mobilen Dienst für Test- und Entwicklungszwecke. Veröffentlichen Sie das mobile Dienstprojekt zum Testen stets in einem getrennten Staging-Dienst.

<ol start="5">
<li><p>Öffnen Sie die gemeinsam genutzte Projektdatei App.xaml.cs, und suchen Sie alle Codezeilen, in denen eine neue Instanz der Klasse <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> erzeugt wird, um auf den in Azure gehosteten mobilen Dienst zuzugreifen.</p></li>
<li><p>Kommentieren Sie diesen Code aus, und fügen Sie Code hinzu, der einen neuen <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> mit demselben Namen erzeugt, im Konstruktor jedoch die URL des lokalen Computers verwendet, ähnlich dem folgenden Code:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>Bei Verwendung dieses <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> verbindet sich die App nicht mit der in Azure gehosteten Version des Dienstes, sondern mit dem lokalen Dienst. Wenn Sie wieder umschalten und die App mit dem in Azure gehosteten mobilen Dienst ausführen möchten, wechseln Sie wieder zurück zu den ursprünglichen <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>-Definitionen.</p></li>
</ol>

##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr über die Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen über Mobile Services.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Notification Hubs arbeiten, um Benachrichtigungen auf allen großen Clientplattformen an Ihre Apps zu senden.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anweisungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [So verwenden Sie einen .NET-Client für mobile Dienste in Azure]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services in C#-Windows-Apps.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/

[So verwenden Sie einen .NET-Client für mobile Dienste in Azure]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/



<!--HONumber=42-->
