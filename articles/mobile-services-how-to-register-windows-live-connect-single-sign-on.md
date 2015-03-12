<properties 
	pageTitle="Registrieren für die einmalige Anmeldung - Azure Mobile Services" 
	description="Erfahren Sie mehr über die Registrierung für die Authentifizierung durch einmaliges Anmelden in Ihrer Azure Mobile Services-Anwendung." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registrieren Ihrer Windows Store-App für die einmalige Anmeldung mit Windows Live Connect

In diesem Thema erfahren Sie, wie Sie Ihre App im Windows Store registrieren, um Live Connect als Authentifizierungsanbieter für einmalige Anmeldung in Azure Mobile Services zu verwenden. Dieser Schritt wird ebenfalls für die Verwendung von Pushbenachrichtigungen benötigt.

> [AZURE.IMPORTANT] Sie müssen Ihre App nicht im Windows Store registrieren, um Microsoft-Konten für die Authentifizierung zu verwenden, bevor Sie Ihre App veröffentlichen. Falls Ihre Windows Store-App keine einmalige Anmeldung oder Pushbenachrichtigungen benötigt, können Sie die App einfach mit Live Connect für die Verwendung von Microsoft-Konten registrieren.  Weitere Informationen finden Sie unter [Registrieren Ihrer Windows Store-Apps zum Verwenden von Microsoft-Konten](/de-de/develop/mobile/how-to-guides/register-for-microsoft-authentication).

1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zur [Seite "App übermitteln"], melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

   	![][0]

2. Geben Sie einen Namen für Ihre Anwendung unter **App-Name** ein, klicken Sie auf **App-Namen reservieren** und dann auf **Speichern**.

   	![][1]

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie im Visual Studio 2012 Express für Windows 8 das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**. 

  	![][2]

   	Auf diese Weise wird der Assistent **App mit Windows Store verknüpfen** angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

6. Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

   	![][3]

   	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

9. Navigieren Sie zur Seite [My Applications] im Live Connect Developer Center, und klicken Sie in der Liste **Eigene Apps** auf Ihre App.

   	![][6] 

10. Klicken Sie auf **Einstellungen bearbeiten**, dann auf **API-Einstellungen**, und notieren Sie sich den Wert für **Geheimer Clientschlüssel**. 

   	![][7]

    > [AZURE.NOTE] Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

11. Geben Sie unter **Umleitungsdomäne** die URL Ihres mobilen Dienstes aus Schritt 8 ein, und klicken Sie auf **Speichern**.

Nun können Sie die Authentifizierung mit Live Connect in Ihre App integrieren. Mobile Services bietet die beiden folgenden Methoden zur Authentifizierung von Benutzern mit Live Connect an:

   - Einmaliges Anmelden für Windows Store-Apps. Mit dieser Methode müssen die Benutzer die Authentifizierung in Ihrer App per Live Connect nur einmal autorisieren. Anschließend werden die Anmeldeinformationen anhand der Benutzerpräferenzen von Windows verwaltet. Weitere Informationen finden Sie unter [Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect].

   - Einfache Authentifizierung. Diese Methode unterstützt eine Vielzahl verschiedener Authentifizierungsanbieter, allerdings müssen sich die Benutzer bei jedem Start der App erneut anmelden. Weitere Informationen finden Sie unter [Erste Schritte mit der Authentifizierung].

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-users-js/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
