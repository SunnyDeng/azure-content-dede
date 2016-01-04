<properties
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Ihrer universellen Windows 8.1-App | Microsoft Azure"
	description="Erfahren Sie, wie Sie über einen mobilen .NET-Back-End-Dienst mithilfe von Azure Notification Hubs Pushbenachrichtigungen an Ihre universelle Windows 8.1-App senden können."
	services="mobile-services,notification-hubs"
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
	ms.date="11/11/2015"
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Übersicht
In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Backend Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure Notification Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom .NET-Backend an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Navigieren Sie zum Projektordner `\Services\MobileServices\your_service_name`, öffnen Sie die generierte Codedatei "push.register.js", und überprüfen Sie die Methode **UploadChannel**, welche die Kanal-URL des Geräts beim Benachrichtigungshub registriert.

&nbsp;&nbsp;7. Öffnen Sie die gemeinsam genutzte Codedatei "App.xaml.cs". Beachten Sie den Aufruf der neuen Methode **UploadChannel**, der dem Ereignishandler **OnLaunched**hinzugefügt wurde. Dies stellt sicher, dass versucht wird, das Gerät zu registrieren, sobald die App gestartet wird.

&nbsp;&nbsp;8. Führen Sie die vorherigen Schritte erneut aus, um dem Windows Phone Store-App-Projekt Pushbenachrichtigungen hinzuzufügen, und entfernen Sie dann in der gemeinsam genutzten Datei "App.xaml.cs" den zusätzlichen Aufruf von **UploadChannel** sowie den restlichen bedingten Wrapper `#if...#endif`. In beiden Projekten kann nun ein einziger Aufruf von **UploadChannel** verwendet werden.

> [AZURE.NOTE]Sie können den generierten Code auch vereinfachen, indem Sie die in `#if...#endif` umschlossenen [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx)-Definitionen zu einer einzigen nicht umschlossenen Definition zusammenfassen, die von beiden Versionen der App genutzt wird.

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen.

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird die bereits vorhandene Klasse "TodoItemController" so aktualisiert, dass sie eine Pushbenachrichtigung an alle registrierten Geräte sendet, wenn ein neues Element eingefügt wird. Sie können ähnlichen Code in jedem benutzerdefinierten [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) oder an beliebiger anderer Stelle in Ihren Back-End-Diensten implementieren.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Die restlichen Schritte in diesem Abschnitt sind optional. Sie dienen dazu, die App mit einem mobilen Dienst zu testen, der auf einem lokalen Computer ausgeführt wird. Wenn Sie planen, die Pushbenachrichtigungen mit dem in Azure gehosteten mobilen Dienst zu testen, können Sie diese Schritte überspringen und einfach zum letzten Abschnitt wechseln. Dies ist möglich, weil der Assistent zum Hinzufügen von Pushbenachrichtigungen die App bereits für die Verbindung mit dem in Azure gehosteten Dienst konfiguriert hat.

>[AZURE.NOTE]Verwenden Sie niemals einen in der Produktionsumgebung genutzten mobilen Dienst für Test- und Entwicklungszwecke. Veröffentlichen Sie das mobile Dienstprojekt zum Testen stets in einem getrennten Staging-Dienst.

&nbsp;&nbsp;5. Öffnen Sie die gemeinsam genutzte Projektdatei "App.xaml.cs", und suchen Sie alle Codezeilen, in denen eine neue Instanz der Klasse [MobileServiceClient] erzeugt wird, um auf den in Azure gehosteten mobilen Dienst zuzugreifen.

&nbsp;&nbsp;6. Kommentieren Sie diesen Code aus, und fügen Sie Code hinzu, der einen neuen [MobileServiceClient] mit demselben Namen erzeugt, im Konstruktor jedoch die URL des lokalen Computers verwendet, ähnlich dem folgenden Code:

	// This MobileServiceClient has been configured to communicate with your local
	// test project for debugging purposes.
	public static MobileServiceClient todolistClient = new MobileServiceClient(
		"http://localhost:4584"
	);

&nbsp;&nbsp;Bei Verwendung dieses [MobileServiceClient] verbindet sich die App nicht mit der in Azure gehosteten Version des Dienstes, sondern mit dem lokalen Dienst. Wenn Sie wieder umschalten und die App mit dem in Azure gehosteten mobilen Dienst ausführen möchten, wechseln Sie wieder zurück zu den ursprünglichen [MobileServiceClient]-Definitionen.

##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Hinzufügen von Mobile Services zu einer vorhandenen App][Get started with data] <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

* [Hinzufügen von Authentifizierung zur app][Get started with authentication] <br/> Erfahren Sie, wie sich Benutzer Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten authentifizieren können.

* [Was sind Notification Hubs?] <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Hier finden Sie die Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen.

* [Verwenden eines .NET-Clients für Azure Mobile Services] <br/>Erfahren Sie mehr über die Verwendung von Mobile Services in C#-Windows-Apps.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Was sind Notification Hubs?]: ../notification-hubs-overview.md

[Verwenden eines .NET-Clients für Azure Mobile Services]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=AcomDC_1203_2015-->