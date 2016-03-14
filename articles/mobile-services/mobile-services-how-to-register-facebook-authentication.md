<properties
	pageTitle="Registrieren für die Facebook-Authentifizierung | Azure Mobile Services"
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
	ms.date="02/28/2016"
	ms.author="glenga"/>

# Registrieren Ihrer App für die Facebook-Authentifizierung mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]&nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Facebook zur Authentifizierung mit Azure Mobile Services zu verwenden. Diese Seite unterstützt das Lernprogramm [Erste Schritte mit der Authentifizierung](mobile-services-ios-get-started-users.md), das zeigt, wie sich Benutzer bei Ihrer App anmelden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services](mobile-services-ios-get-started.md) abschließen.

Sie benötigen ein Facebook-Konto mit verifizierter E-Mail-Adresse und einer Mobiltelefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285) auf, um ein neues Facebook-Konto zu erstellen.

1. Navigieren Sie zur Website für [Facebook-Entwickler](http://go.microsoft.com/fwlink/p/?LinkId=268285), und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.

2. (Optional) Wenn Sie sich noch nicht registriert haben, klicken Sie auf **My Apps**, klicken Sie auf **Register as a Developer**, akzeptieren Sie die Richtlinien, und folgen Sie dann den Anweisungen für die Registrierung.

3. Klicken Sie auf **My Apps** > **Add a New App** > **Website** > **Skip and Create App ID**.

4. Geben Sie in **Display Name** einen eindeutigen Anzeigenamen für Ihre App ein, wählen Sie unter **Category** eine Kategorie für die App aus, klicken Sie dann auf **Create App ID**, und schließen Sie die Schritte für die Sicherheitsüberprüfung ab. Dadurch gelangen Sie zum Entwickler-Dashboard für Ihre neue Facebook-App.

5. Klicken Sie im Feld **App Secret** auf **Show**, geben Sie bei Aufforderung Ihr Kennwort ein, und notieren Sie die Werte für **App ID** und **App Secret**. Ihre Anwendung wird später in Azure mit diesen Angaben konfiguriert.

	> [AZURE.NOTE] **Sicherheitshinweis** Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

5. Klicken Sie in der linken Navigationsleiste auf **Settings**, geben Sie unter **App Domains** die Domäne Ihres mobilen Diensts ein, geben Sie optional unter **Contact Email** einen E-Mail-Kontakt ein, klicken Sie auf **Add Platform**, und wählen Sie dann **Website** aus.

   	![][3]

6. Geben Sie die URL Ihres Mobile Service unter **Site URL** ein, und klicken Sie auf **Änderungen speichern**.

7. Klicken Sie auf **Anzeigen**, geben Sie falls erforderlich Ihr Kennwort an, und notieren Sie sich die Werte von **App-ID** und **App-Schlüssel**.

   	![][5]&nbsp;

    >[AZURE.IMPORTANT] Der App-Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.&nbsp;

8. Klicken Sie auf die Registerkarte **Erweitert**, geben Sie eines der folgenden URL-Formate in **Valid OAuth redirect URIs** ein, klicken Sie dann auf **Änderungen speichern**:

	+ **.NET-Back-End**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **JavaScript-Back-End**: `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]Stellen Sie sicher, dass Sie das richtige Umleitungs-URL-Pfadformat für Ihren Typ von Mobile Services-Back-End verwenden. Verwenden Sie dazu das *HTTPS*-Schema. Wenn die Angabe falsch ist, wird die Authentifizierung nicht erfolgreich ausgeführt.


12. Das zum Registrieren der Anwendung verwendete Facebook-Konto fungiert als Administrator der App. Zu diesem Zeitpunkt können sich nur Administratoren bei der Anwendung anmelden. Um andere Facebook-Konten zu authentifizieren, klicken Sie auf **App Review**, und aktivieren Sie **Make todolist-complete-nodejs public**, um den allgemeinen öffentlichen Zugriff mithilfe der Facebook-Authentifizierung zu aktivieren.

Sie können nun ein Facebook-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die App-ID und den App-Schlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0302_2016-->