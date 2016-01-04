<properties 
	pageTitle="Registrieren für die Twitter-Authentifizierung | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Twitter-Authentifizierung mit Ihrer Azure Mobile Services-Anwendung verwenden." 
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
	ms.date="11/30/2015" 
	ms.author="glenga"/>

#Registrieren Ihrer Apps für die Twitter-Anmeldung mit Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Twitter zur Authentifizierung mit Azure Mobile Services zu verwenden.

>[AZURE.NOTE]Thema dieses Lernprogramms sind [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/), eine Lösung, die Sie beim Erstellen skalierbarer mobiler Anwendungen für beliebige Plattformen unterstützt. Mit Mobile Services ist es einfach, Daten zu synchronisieren, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden. Diese Seite unterstützt das Tutorial [Hinzufügen von Authentifizierung zur App](mobile-services-ios-get-started-users.md), das zeigt, wie sich Benutzer bei Ihrer App anmelden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services](mobile-services-ios-get-started.md) abschließen.

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> auf, um ein neues Twitter-Konto zu erstellen.

1. Navigieren Sie zur [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300)-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und klicken Sie auf **Create new app**.

2. Geben Sie die Werte für **Name**, **Beschreibung** und **Website** für Ihre App ein, geben Sie dann eines der folgenden URL-Formate in **Callback URL** ein.

	+ **.NET-Back-End**: `https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **JavaScript-Back-End**: `https://<mobile_service>.azure-mobile.net/login/twitter`

	 >[AZURE.NOTE]Stellen Sie sicher, dass Sie das richtige Umleitungs-URL-Pfadformat für Ihren Typ von Mobile Services-Back-End verwenden. Wenn die Angabe falsch ist, wird die Authentifizierung nicht erfolgreich ausgeführt.&nbsp;

   	![][2]

3.  Lesen und bestätigen Sie die Nutzungsbedingungen am Ende der Seite, und klicken Sie auf **Twitter-Anwendung erstellen**.

   	Daraufhin wird die Anwendung mit den angegebenen Details registriert.

6. Klicken Sie in Ihrem App-Dashboard auf die Registerkarte **Schlüssel und Zugriffstoken**, und notieren Sie die Werte für **Verbraucherschlüssel** und **Verbrauchergeheimnis**.

    > [AZURE.NOTE] Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

7. Klicken Sie auf die Registerkarte **Einstellungen**, scrollen Sie nach unten, stellen Sie sicher, dass das Kontrollkästchen **Anwendung für die Anmeldung bei Twitter freigeben** aktiviert ist, und klicken Sie auf **Einstellungen aktualisieren**.

Sie können nun ein Twitter-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die consumer key und consumer secret für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_1210_2015-->