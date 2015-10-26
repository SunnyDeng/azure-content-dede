<properties
	pageTitle="Konfigurieren der Twitter-Authentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Twitter-Authentifizierung für Ihre App Services-Anwendung konfigurieren."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Twitter-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure Mobile Apps zur Verwendung von Twitter als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> auf, um ein neues Twitter-Konto zu erstellen.

## <a name="register"> </a>Registrieren Ihrer Anwendung für Twitter


1. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Mobile App. Kopieren Sie die **URL**. Diese verwenden Sie zur Konfiguration der Twitter-App.

2. Klicken Sie auf **Einstellungen**, **Benutzerauthentifizierung**, und klicken Sie dann auf **Twitter**. Kopieren Sie die **Rückruf-URL**. Diese verwenden Sie zur Konfiguration der Twitter-App.

3. Navigieren Sie zur [Twitter Developers]-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und klicken Sie auf **Create New App**.

4. Geben Sie den **Namen** und eine **Beschreibung** für Ihre neue App ein. Fügen Sie die **Mobile App-URL** in den Wert für die **Website** ein. Fügen Sie dann für die **Rückruf-URL** die **Rückruf-URL** ein, die Sie zuvor kopiert haben. Dies ist Ihr Mobile App-Gateway mit dem Pfad _/signin-twitter_. Beispiel: `https://contosogateway.azurewebsites.net/signin-twitter`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

    ![][0]

3.  Lesen und akzeptieren Sie die Bedingungen am Ende der Seite. Klicken Sie dann auf **Create your Twitter application**. Daraufhin wird die Anwendung mit den angegebenen Details registriert.

4. Klicken Sie auf die Registerkarte **Settings**, aktivieren Sie die Option **Allow this application to be used to sign in with Twitter**, und klicken Sie dann auf **Update Settings**.

5. Wählen Sie die Registerkarte **Keys and Access Tokens**. Notieren Sie die Werte für **Consumer Key (API Key)** und **Consumer secret (API Secret)**.

    > [AZURE.NOTE]Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.


## <a name="secrets"> </a>Hinzufügen von Twitter-Informationen zu Ihrer mobilen App

1. Fügen Sie zurück im [Azure-Verwaltungsportal] im Blatt mit den Twitter-Einstellungen für Ihre Mobile App die Werte für den App-Schlüssel und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

    ![][1]

Sie können nun Twitter für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-Verwaltungsportal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=Oct15_HO3-->