<properties 
	pageTitle="Registrieren für die Facebook-Authentifizierung – Mobile Services" 
	description="Erfahren Sie, wie Sie die Facebook-Authentifizierung mit Ihrer Azure Mobile Services-App verwenden." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Registrieren Ihrer App für die Facebook-Authentifizierung mit Mobile Services

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Facebook zur Authentifizierung mit Azure Mobile Services zu verwenden.

> [AZURE.NOTE]Thema dieses Lernprogramms sind [Azure Mobile Services], eine Lösung, die Sie beim Erstellen skalierbarer mobiler Anwendungen für beliebige Plattformen unterstützt. Mit Mobile Services ist es einfach, Daten zu synchronisieren, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden. Diese Seite unterstützt das Lernprogramm <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Erste Schritte mit der Authentifizierung</a>, das zeigt, wie sich Benutzer bei Ihrer App anmelden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Erste Schritte mit Mobile Services</a> abschließen.
	
Sie benötigen ein Facebook-Konto mit verifizierter E-Mail-Adresse und einer Mobiltelefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>￼ auf, um ein neues Facebook-Konto zu erstellen.

1. Navigieren Sie zur Website für <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook-Entwickler</a>￼, und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.

2. (Optional) Falls Sie sich noch nicht registriert haben, klicken Sie auf **Apps** und dann auf **Register as a Developer**, akzeptieren Sie die Richtlinie, und führen Sie die Registrierungsschritte durch.

   	![][0]

3. Klicken Sie auf **Apps** und dann auf **Create a New App**.

   	![][1]

4. Wählen Sie einen eindeutigen Namen für Ihre App, wählen Sie **Apps for Pages**, klicken Sie auf **Create App**, und beantworten Sie die Sicherheitsfrage.

   	![][2]

	Daraufhin wird die App bei Facebook registriert.

5. Klicken Sie auf **Settings**, und geben Sie unter **App Domains** die Domäne Ihres mobilen Diensts ein. Geben Sie unter **Contact Email** auch Ihre Kontakt-E-Mail ein, klicken Sie auf **Add Platform**, und wählen Sie **Website** aus.

   	![][3]

6. Geben Sie die URL Ihres Mobile Service unter **Site URL** ein, und klicken Sie auf **Änderungen speichern**.

	![][4]

7. Klicken Sie auf **Anzeigen**, geben Sie falls erforderlich Ihr Kennwort an, und notieren Sie sich die Werte von **App-ID** und **App-Schlüssel**.

   	![][5]

	> [AZURE.NOTE]**Sicherheitshinweis** Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.


8. Klicken Sie auf die Registerkarte **Advanced**, geben Sie in _Valid OAuth redirect URIs_ die URL Ihres mobilen Diensts gefolgt vom Pfad **/login/facebook** ein, und klicken Sie auf **Save Changes**.

	> [AZURE.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix _signin-facebook_ für Ihren mobilen Dienst als .NET-Dienst, z. B. <code>https://todolist.azure-mobile.net/signin-facebook</code>.
	
	![][7]

9. Das Facebook-Konto, für das die neue App definiert wurde, verfügt über Administratorrechte und über Administratorzugriff für die App. Zur Authentifizierung anderer Facebook-Konten benötigen diese Zugriff auf die App. Dieser Schritt gewährleistet den allgemeinen öffentlichen Zugriff, sodass die App andere Facebook-Konten authentifizieren kann. Klicken Sie auf **Status & Review**. Klicken Sie dann auf **Ja**, um den allgemeinen öffentlichen Zugriff zu aktivieren.

    ![][6]



Sie können nun ein Facebook-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die App-ID und den App-Schlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!--HONumber=54--> 