<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen mit einem mobilen JavaScript-Back-End-Dienst" 
	description="Erfahren Sie, wie Sie Azure Mobile Services und Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre universelle Windows-App zu senden." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="glenga"/>


# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit JavaScript-Backend Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungs-Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom JavaScript-Backend an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von Mobile Services an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen in Mobile Services](mobile-services-javascript-backend-windows-phone-get-started-push.md).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen](#register)
2. [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen](#update-service)
3. [Testen von Pushbenachrichtigungen in der App](#test)

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* [Visual Studio 2013 Express für Windows](http://go.microsoft.com/fwlink/?LinkId=257546) mit Update 3 oder eine höhere Version

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navigieren Sie zum Projektordner <code>\Services\MobileServices\your_service_name</code>, öffnen Sie die generierte Codedatei "push.register.js", und überprüfen Sie die <strong>UploadChannel</strong>-Methode, welche die Channel-URL des Geräts beim Benachrichtigungs-Hub registriert.</p></li> 
<li><p>Öffnen Sie die gemeinsam genutzte Codedatei "App.xaml.cs". Beachten Sie den Aufruf der neuen Methode <strong>UploadChannel</strong>, der dem Ereignishandler <strong>OnLaunched</strong>hinzugefügt wurde.</p> <p>Dies stellt sicher, dass versucht wird, das Gerät zu registrieren, sobald die App gestartet wird.</p></li>
<li><p>Führen Sie die vorigen Schritte erneut aus, um dem Windows Phone Store-App-Projekt Pushbenachrichtigungen hinzuzufügen, und entfernen Sie dann in der gemeinsam genutzten Datei "App.xaml.cs" den zusätzlichen Aufruf von <strong>UploadChannel</strong> sowie den restlichen bedingten Wrapper <code>#if...#endif</code>.</p> <p>In beiden Projekten kann nun ein einziger Aufruf von <strong>UploadChannel</strong> verwendet werden.</p>
<p>Sie können den generierten Code auch vereinfachen, indem Sie die in <code>#if...#endif</code> gekapselten <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App verwendet wird.</p></li>
</ol>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen.

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird das Insert-Skript aktualisiert, das in der TodoItem-Tabelle registriert ist. Sie können ähnlichen Code in jedem Serverskript oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren.

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Benachrichtigungs-Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen:

+ [Senden von Pushbenachrichtigungen an authentifizierte Benutzer](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an authentifizierte Benutzer gesendet werden.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten](../notification-hubs-windows-store-dotnet-send-breaking-news.md) <br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

+ [Senden plattformunabhängiger Benachrichtigungen an Abonnenten](../notification-hubs-aspnet-cross-platform-notify-users.md) <br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen von einem mobilen Dienst genutzt werden, ohne dass Sie im Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Azure Notification Hubs - Diagnoserichtlinien](../notification-hubs-diagnosing.md) <br/>Erfahren Sie, wie Sie Probleme mit Pushbenachrichtigungen beheben.

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

* [Was sind Notification Hubs?] <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Verwenden eines .NET-Clients für Azure Mobile Services] <br/>Erfahren Sie mehr über die Verwendung von Mobile Services in C#-Windows-Apps.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[Was sind Notification Hubs?]: ../notification-hubs-overview.md

[Verwenden eines .NET-Clients für Azure Mobile Services]: mobile-services-windows-dotnet-how-to-use-client-library.md
 

<!---HONumber=July15_HO4-->