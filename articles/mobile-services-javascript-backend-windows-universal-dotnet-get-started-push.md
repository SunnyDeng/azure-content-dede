<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen mit einem mobilen JavaScript-Back-End-Dienst" 
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

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .JavaScript-Back-End Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure Notification Hubs zum universellen Windows-App-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz in die ToDoList-Tabelle eingefügt wird, eine Pushbenachrichtigung vom JavaScript-Back-End an alle registrierten Windows Store- und Windows Phone Store-Apps. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio 2013 mit Update 3 enthaltenen Tools Unterstützung für Pushbenachrichtigungen von Mobile Services an eine universelle Windows-App hinzufügen. Mit denselben Schritten können Sie Pushbenachrichtigungen von mobilen Diensten an eine Windows Store- oder eine Windows Phone Store 8.1-App hinzufügen. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen in Mobile Services](mobile-services-javascript-backend-windows-phone-get-started-push.md).

> Wenn Sie kein Upgrade auf Visual Studio 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](mobile-services-javscript-backend-windows-store-dotnet-get-started-push.md) des Themas weitere Informationen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen](#register)
2. [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen](#update-service)
3. [Testen von Pushbenachrichtigungen in der App](#test)

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* [Visual Studio 2013 Express für Windows](http://go.microsoft.com/fwlink/?LinkId=257546) mit Update 3 oder eine höhere Version

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navigieren Sie zum Projektordner <code>\Services\MobileServices\your_service_name</code>, öffnen Sie die generierte Codedatei "push.register.cs" und überprüfen Sie die <strong>UploadChannel</strong>-Methode, welche die Kanal-URL des Geräts beim Notification Hub registriert.</p></li> 
<li><p>Öffnen Sie die gemeinsam genutzte Codedatei "App.xaml.cs". Beachten Sie, dass der Aufruf der neuen <strong>UploadChannel</strong>-Methode zum <strong>OnLaunched</strong>-Ereignishandler hinzugefügt wurde.</p> <p>Dies stellt sicher, dass versucht wird, das Gerät zu registrieren, sobald die App gestartet wird.</p></li>
<li><p>Führen Sie die vorigen Schritte erneut aus, um dem Windows Phone Store-App-Projekt Pushbenachrichtigungen hinzuzufügen, und entfernen Sie dann in der gemeinsam genutzten Datei "App.xaml.cs" den zusätzlichen Aufruf von <strong>UploadChannel</strong> sowie den übrigen bedingten <code>#if...#endif</code>-Wrapper.</p> <p>In beiden Projekten kann nun ein einziger Aufruf von <strong>UploadChannel</strong> verwendet werden.</p>
<p>Sie können den generierten Code auch vereinfachen, indem Sie die in <code>#if...#endif</code> gekapselten <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App verwendet wird.</p></li>
</ol>

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie den mobilen Dienst aktualisieren und für den Versand von Pushbenachrichtigungen anpassen. 

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Mit den folgenden Schritten wird das Insert-Skript aktualisiert, das in der TodoItem-Tabelle registriert ist. Sie können ähnlichen Code in jedem Serverskript oder an beliebiger anderer Stelle in Ihren Backend-Diensten implementieren. 

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Benachrichtigungs-Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] absolvieren, in dem die Verwendung von Tags zum Senden von Pushbenachrichtigungen von einem Mobile Service an nur einen authentifizierten Benutzer veranschaulicht wird.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr über die Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen über Mobile Services.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anweisungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [Verwenden eines .NET-Clients für Azure Mobile Services]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services in C#-Windows-Apps.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/

[Verwenden eines .NET-Clients für Azure Mobile Services]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/



<!--HONumber=42-->
