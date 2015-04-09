<properties 
	pageTitle="Erste Schritte mit der Authentifizierung (Android) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Android-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

## Zusammenfassung

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">


<p>In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">10:42</span></div>
</div> 

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App.


##Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

<h2><a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services</h2>

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer</h2>

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. Öffnen Sie in Android Studio das Projekt, das Sie bei Ausführung des Lernprogramms [Erste Schritte mit Mobile Services] erstellt haben. 

4. Klicken Sie im Menü **Ausführen** auf **App ausführen**. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird. 

	 Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_ -Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

<h2><a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App</h2>

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Zwischenspeichern von Authentifizierungstoken auf dem Client

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Aktualisieren des Tokencaches

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. 

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Speichern von Authentifizierungstoken auf dem Client]: #cache-tokens
[Aktualisieren abgelaufener Token]: #refresh-tokens
[Nächste Schritte]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Einmaliges Anmelden für Windows Store-Apps mithilfe von Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started-android
[Hinzufügen von Mobile Services zu einer vorhandenen App]: /develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-android
[Autorisieren von Benutzern mit Skripts]: /develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=49-->