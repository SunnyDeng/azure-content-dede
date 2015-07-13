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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Twitter-Anmeldung

In diesem Thema wird veranschaulicht, wie Sie Azure App Services zur Verwendung von Twitter als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> auf, um ein neues Twitter-Konto zu erstellen.

## <a name="register"> </a>Registrieren Ihrer Anwendung für Twitter

1. Navigieren Sie zur [Twitter Developers]-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und klicken Sie auf **Create New App**.

2. Geben Sie Werte für **Name**, **Description**, und **Website** für Ihre App ein. Geben Sie anschließend im Feld **Callback URL** die URL für Ihr Gateway mit angehängtem Pfad _/signin-twitter_ ein. Beispiel: `https://contosogateway.azurewebsites.net/signin-twitter`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

    ![][0]

3.  Lesen und akzeptieren Sie die Bedingungen am Ende der Seite. Klicken Sie dann auf **Create your Twitter application**. Daraufhin wird die Anwendung mit den angegebenen Details registriert.

4. Klicken Sie auf die Registerkarte **Settings**, aktivieren Sie die Option **Allow this application to be used to sign in with Twitter**, und klicken Sie dann auf **Update Settings**.

5. Wählen Sie die Registerkarte **Keys and Access Tokens**. Notieren Sie die Werte für **Consumer Key (API Key)** und **Consumer secret (API Secret)**.

    > [AZURE.NOTE]Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.


## <a name="secrets"> </a>Hinzufügen von Twitter-Informationen zu Ihrer mobilen App

6. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrem App Service-Gateway.

7. Wählen Sie unter **Einstellungen** die Option **Identität**, und wählen Sie dann **Twitter**. Fügen Sie die Werte für App-ID und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

    ![][1]

Sie können nun Twitter für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-Verwaltungsportal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=62-->