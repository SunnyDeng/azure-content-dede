<properties 
	pageTitle="Registrieren für die Google-Authentifizierung | Microsoft Azure"
	description="Erfahren Sie, wie Sie Ihre Apps registrieren, um Google zur Authentifizierung mit Azure Mobile Services zu verwenden."
	services="mobile-services"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="glenga"/>

# Registrieren Ihrer App für die Google-Anmeldung mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Google zur Authentifizierung mit Azure Mobile Services zu verwenden.

>[AZURE.NOTE]Thema dieses Tutorials sind [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/), eine Lösung, die Sie beim Erstellen skalierbarer mobiler Anwendungen für beliebige Plattformen unterstützt. Mit Mobile Services ist es einfach, Daten zu synchronisieren, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden. Diese Seite unterstützt das Tutorial [Erste Schritte mit der Authentifizierung](mobile-services-ios-get-started-users.md), das zeigt, wie sich Benutzer bei Ihrer App anmelden. <br/>Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Tutorial [Erste Schritte mit Mobile Services](mobile-services-ios-get-started.md) abschließen.

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

3. Navigieren Sie zur [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303)-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project**, geben Sie einen **Project name** an, und klicken Sie auf **Create**.

4. Klicken Sie in der linken Navigationsleiste auf **API & Auth** und anschließend unter **Social APIs** auf **Google+ API** > **Enable API**.

5. Klicken Sie auf **API & Auth** > **Credentials** > **OAuth consent screen**, wählen Sie dann Ihre **Email address** aus, geben Sie einen Wert für **Product Name** ein, und klicken Sie auf **Save**.

6. Klicken Sie auf der Registerkarte **Credentials** auf **Add credentials** > **OAuth 2.0 client ID**, und wählen Sie dann **Web application**.

7. Geben Sie in **Authorized JavaScript Origins** die URL für Ihren mobilen Dienst ein, ersetzen Sie die generierte URL in **Authorized Redirect URI** durch die URL Ihres mobilen Diensts mit angehängtem Pfad `/login/google`, und klicken Sie dann auf **Create client ID**.

	>[AZURE.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix _signin-google_ für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-google`.&nbsp;
	
8. Notieren Sie sich auf dem nächsten Bildschirm die Werte für die Client-ID und den geheimen Clientschlüssel.

    > [AZURE.IMPORTANT]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

Sie können nun Ihren mobilen Dienst für die Verwendung der Google-Anmeldung zur Authentifizierung in Ihrer App konfigurieren.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=September15_HO1-->