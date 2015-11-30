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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Nutzung der Twitter-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure App Service zur Verwendung von Twitter als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> auf, um ein neues Twitter-Konto zu erstellen.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrieren Ihrer Anwendung für Twitter


1. Melden Sie sich im [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Diese verwenden Sie zur Konfiguration der Twitter-App.

2. Navigieren Sie zur [Twitter Developers]-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und klicken Sie auf **Create New App**.

3. Geben Sie den **Namen** und eine **Beschreibung** für Ihre neue App ein. Fügen Sie die **URL** Ihrer Anwendung in den Wert für die **Website** ein. Fügen Sie dann für die **Rückruf-URL** die **Rückruf-URL** ein, die Sie zuvor kopiert haben. Dies ist Ihr Mobile App-Gateway mit dem angehängten Pfad _/.auth/login/twitter/callback_. Beispiel: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

    ![][0]
	

	> [AZURE.NOTE]Wenn anstelle der App Service-Authentifizierungs-/-Autorisierungsfunktion das App Service-Gateway verwendet wird, greift Ihre Umleitungs-URL stattdessen auf die Gateway-URL mit dem Pfad _/signin-twitter_ zurück.


3.  Lesen und akzeptieren Sie die Bedingungen am Ende der Seite. Klicken Sie dann auf **Create your Twitter application**. Daraufhin wird die Anwendung mit den angegebenen Details registriert.

4. Klicken Sie auf die Registerkarte **Einstellungen**, aktivieren Sie die Option **Allow this application to be used to sign in with Twitter**, und klicken Sie dann auf **Update Settings**.

5. Wählen Sie die Registerkarte **Keys and Access Tokens**. Notieren Sie die Werte für **Consumer Key (API Key)** und **Consumer secret (API Secret)**.

    > [AZURE.NOTE] Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.


## <a name="secrets"> </a>Hinzufügen von Twitter-Informationen zu Ihrer Anwendung


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Twitter**. Paste in the values you obtained earlier and click **Save**.


13. Wenn Sie sich wieder im [Azure-Verwaltungsportal] befinden, navigieren Sie zu Ihrer Anwendung. Klicken Sie auf **Einstellungen**, und dann auf **Authentifizierung/Autorisierung**.

14. Wenn die Authentifizierungs-/Autorisierungsfunktion nicht aktiviert ist, stellen Sie den Schalter auf **Ein**.

15. Klicken Sie auf **Twitter**. Fügen Sie die Werte für App-ID und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie dann auf **OK**.

    ![][1]
	
16. Standardmäßig erfolgt die Anmeldung über App Service, der jedoch den Zugriff auf die Inhalte Ihrer Website und APIs nicht einschränkt. Diese Aufgabe erfüllt der App Code. Wenn die Website vollständig von Twitter-Anmeldung geschützt werden soll, stellen Sie die **Auszuführende Aktion bei nicht authentifizierter Anforderung** in der Dropdownliste auf die Option **Twitter** ein. Dadurch müssen alle Anforderungen authentifiziert werden und nicht authentifizierte Anforderungen werden zur Twitter-Anmeldung umgeleitet.

17. Klicken Sie auf **Speichern**.

Sie können nun Twitter für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-Verwaltungsportal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=Nov15_HO4-->