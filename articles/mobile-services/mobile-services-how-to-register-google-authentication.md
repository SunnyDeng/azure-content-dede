<properties 
	pageTitle="Registrieren für die Google-Authentifizierung – Mobile Services" 
	description="Erfahren Sie, wie Sie Ihre Apps registrieren, um Google zur Authentifizierung mit Azure Mobile Services zu verwenden." 
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

# Registrieren Ihrer App für die Google-Anmeldung mit Mobile Services

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Google zur Authentifizierung mit Azure Mobile Services zu verwenden.

>[AZURE.NOTE]Thema dieses Lernprogramms sind [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/), eine Lösung, die Sie beim Erstellen skalierbarer mobiler Anwendungen für beliebige Plattformen unterstützt. Mit Mobile Services ist es einfach, Daten zu synchronisieren, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden. Diese Seite unterstützt das Lernprogramm <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Erste Schritte mit der Authentifizierung</a>, das zeigt, wie sich Benutzer bei Ihrer App anmelden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Erste Schritte mit Mobile Services</a> abschließen.

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

1. Navigieren Sie zur <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google APIs</a>-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project**, geben Sie einen **Project name** an, und klicken Sie auf **Create**.

   	![][1]

2. Klicken Sie auf **Consent screen**, wählen Sie Ihre **Email Address** aus, geben Sie einen **Product Name** ein, und klicken Sie dann auf **Save**.

3. Klicken Sie auf **API & Auth**, auf **Credentials** und dann auf **Create new Client ID**.

   	![][2]

4. Wählen Sie **Web application**, geben Sie in **Authorized JavaScript Origins** die URL Ihres mobilen Diensts ein, ersetzen Sie die generierte URL in **Authorized Redirect URI** durch die URL des mobilen Diensts, fügen Sie den Pfad _/login/google_ an die URL an, und klicken Sie dann auf **Create client ID**.

	>[AZURE.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix _signin-google_ für Ihren mobilen Dienst als .NET-Dienst, z. B. <code>https://todolist.azure-mobile.net/signin-google</code>.

   	![][3]

5. Notieren Sie unter **Client-ID für Webanwendungen** die Werte für **Client-ID** und **Geheimer Clientschlüssel**.

   	![][4]

    > [AZURE.IMPORTANT]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

Sie können nun ein Google-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die Werte für Client-ID und geheimen Clientschlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54--> 