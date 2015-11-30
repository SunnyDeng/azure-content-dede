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
	ms.date="11/15/2015" 
	ms.author="glenga"/>

# Registrieren Ihrer App für die Facebook-Authentifizierung mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Facebook zur Authentifizierung mit Azure Mobile Services zu verwenden.

>[AZURE.NOTE]Thema dieses Lernprogramms sind [Azure Mobile Services], eine Lösung, die Sie beim Erstellen skalierbarer mobiler Anwendungen für beliebige Plattformen unterstützt. Mit Mobile Services ist es einfach, Daten zu synchronisieren, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden. Diese Seite unterstützt das Lernprogramm [Erste Schritte mit der Authentifizierung](mobile-services-ios-get-started-users.md), das zeigt, wie sich Benutzer bei Ihrer App anmelden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services](mobile-services-ios-get-started.md) abschließen.
	
Sie benötigen ein Facebook-Konto mit verifizierter E-Mail-Adresse und einer Mobiltelefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285) auf, um ein neues Facebook-Konto zu erstellen.

1. Navigieren Sie zur Website für [Facebook-Entwickler](http://go.microsoft.com/fwlink/p/?LinkId=268285), und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.

2. (Optional) Wenn Sie sich noch nicht registriert haben, klicken Sie auf **My Apps**, klicken Sie auf **Register as a Developer**, akzeptieren Sie die Richtlinien, und folgen Sie dann den Anweisungen für die Registrierung.

3. Klicken Sie auf **My Apps** > **Add a New App** > **Advanced setup**.

4. Geben Sie in **Display name** einen eindeutigen Anzeigenamen für Ihre App ein, wählen Sie unter **Category** die Option **Apps for Pages** aus, klicken Sie auf **Create App ID**, und schließen Sie die Schritte für die Sicherheitsüberprüfung ab.

	Damit wird eine neue Facebook App ID erstellt.

5. Klicken Sie auf **Settings**, geben Sie unter **App Domains** die Domäne Ihres mobilen Dienstes ein , geben Sie optional unter **Contact Email** einen E-Mail-Kontakt ein, klicken Sie auf **Add Platform**, und wählen Sie dann **Website** aus.

   	![][3]

6. Geben Sie die URL Ihres Mobile Service unter **Site URL** ein, und klicken Sie auf **Änderungen speichern**.

7. Klicken Sie auf **Anzeigen**, geben Sie falls erforderlich Ihr Kennwort an, und notieren Sie sich die Werte von **App-ID** und **App-Schlüssel**.

   	![][5]&nbsp;
	
    >[AZURE.IMPORTANT]Der App-Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.&nbsp;

8. Klicken Sie auf die Registerkarte **Erweitert**, geben Sie eines der folgenden URL-Formate in **Valid OAuth redirect URIs** ein, klicken Sie dann auf **Änderungen speichern**:
 
	+ **.NET-Back-End**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **JavaScript-Back-End**:`https://<mobile_service>.azure-mobile.net/login/facebook` 

	 >[AZURE.NOTE]Stellen Sie sicher, dass Sie das richtige Umleitungs-URL-Pfadformat für Ihren Typ von Mobile Services-Back-End verwenden. Wenn die Angabe falsch ist, wird die Authentifizierung nicht erfolgreich ausgeführt.

       
9. Klicken Sie auf **Status & Review** > **Yes**, um den allgemeinen öffentlichen Zugriff auf Ihre App zu aktivieren.

	Das Facebook-Konto, mit dem die neue App registriert wurde, verfügt über Administratorrechte und über Administratorzugriff auf die App. Mit diesem Schritt wird der allgemeine öffentliche Zugriff gewährt, sodass die App andere Facebook-Konten für die Authentifizierung verwenden kann.


Sie können nun ein Facebook-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die App-ID und den App-Schlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=Nov15_HO4-->