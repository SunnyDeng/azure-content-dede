<properties
	pageTitle="Hinzufügen von Authentifizierung zu Ihrer universellen Windows 8.1-App | Microsoft Azure"
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer universellen Windows Phone 8.1-App über verschiedene Identitätsanbieter wie Google, Facebook, Twitter oder Microsoft zu authentifizieren."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App
[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen für die entsprechende Mobile Apps-Version dieses Themas finden Sie unter [Hinzufügen von Authentifizierung zu Ihrer Windows-App](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md).

## Übersicht

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre universelle Windows-App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md) abschließen.

>[AZURE.NOTE]In diesem Lernprogramm erfahren Sie, wie Sie die servergesteuerte Authentifizierung für Benutzer in Windows Store- und Windows Phone Store 8.1-Apps verwenden können. Weitere Informationen zur clientgesteuerten Authentifizierung finden Sie unter [Logging in with Google, Microsoft and Facebook SDKs to Azure Mobile Services](https://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/) ("Anmelden bei Azure Mobile Services mit Google-, Microsoft- und Facebook-SDKs", in englischer Sprache).

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Windows Store-Projekt für die TodoList-App und dann auf **Als Startprojekt festlegen**.

&nbsp;&nbsp;7. Öffnen Sie im gemeinsam genutzten Projekt die Projektdatei "App.xaml.cs", suchen Sie die Definition des [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx), und stellen Sie sicher, dass diese für den Zugriff auf den in Azure gehosteten mobilen Dienst konfiguriert ist.

>[AZURE.NOTE]Wenn Sie die Tools von Visual Studio verwenden, um die App mit einem mobilen Dienst zu verbinden, werden zwei Gruppen von **MobileServiceClient**-Definitionen erzeugt, und zwar eine pro Plattform. Dies ist ein guter Moment, den generierten Code zu vereinfachen, indem Sie die in `#if...#endif` gekapselten **MobileServiceClient**-Definitionen zu einer einzigen ungekapselten Definition zusammenfassen, die von beiden Versionen der App genutzt wird. Dies ist nicht erforderlich, wenn Sie die Schnellstart-App aus dem [klassischen Azure-Portal] heruntergeladen haben.

&nbsp;&nbsp;8. Drücken Sie F5, um die Windows Store-App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

&nbsp;&nbsp;Der Fehler wird ausgegeben, weil die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]Wenn Sie Ihre Windows Store-App-Paketinformationen mit Mobile Services registriert haben, sollten Sie die <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>-Methode aufrufen, indem Sie für *useSingleSignOn* den Wert **true** angeben. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird.

##<a name="tokens"></a>Speichern der Autorisierungstoken auf dem Client

Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, bei der der Client bei jedem Starten der App sowohl den Identitätsanbieter als auch den mobilen Dienst kontaktieren muss. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollten. Ein besserer Ansatz ist es daher, den von Mobile Services zurückgegebenen Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

>[AZURE.NOTE]Den von Mobile Services zurückgegebenen Authentifizierungstoken können Sie unabhängig davon, ob Sie die clientverwaltete oder die dienstverwaltete Authentifizierung verwenden, zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern][Authorize users with scripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

##Weitere Informationen

+ [Verbesserte Benutzerfunktion](https://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> Sie können zusätzliche Benutzerdaten, die vom Identitätsanbieter verwaltet werden, in Ihrem mobilen Dienst abrufen, indem Sie die Methode **ServiceUser.GetIdentitiesAsync()** in einem .NET-Back-End aufrufen.

+ [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]<br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit einem .NET-Client.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[klassischen Azure-Portal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0309_2016-->