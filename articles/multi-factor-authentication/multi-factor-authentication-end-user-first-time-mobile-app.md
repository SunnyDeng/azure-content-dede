<properties 
	pageTitle="Verwenden einer mobilen App als Kontaktmethode mit Azure MFA" 
	description="Auf dieser Seite erfahren Benutzer, wie sie die mobile App als primäre Kontaktmethode für Azure MFA verwenden." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# Verwenden einer mobilen App als Kontaktmethode mit Azure Multi-Factor Authentication

Wenn Sie die mobile App als primäre Kontaktmethode verwenden möchten, finden Sie weitere Informationen in diesem Artikel. Hier erfahren Sie, wie Sie Multi-Factor Authentication einrichten, um Ihre mobile App als primäre Kontaktmethode zu verwenden.

Die Azure Authenticator-App ist für [Windows Phone](http://www.windowsphone.com/de-de/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) und [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) verfügbar.

## So verwenden Sie eine mobile App als Kontaktmethode
<ol>
<li>Wählen Sie in der Dropdownliste "Mobile App" aus.</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>Wählen Sie "Benachrichtigung" oder "Einmalkennwort" aus, und klicken Sie auf "Einrichten".</li>
<li>Starten Sie die Azure Authenticator-App auf dem Mobiltelefon, auf dem die App installiert ist, und tippen Sie auf "Barcode einlesen".</li>


<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>Scannen Sie das Bild mit dem Barcode, das im Bildschirm "Mobile App konfigurieren" angezeigt wird. Klicken Sie auf "Fertig", um den Bildschirm mit dem Barcode zu schließen. Wenn der Barcode nicht gescannt werden kann, können Sie die Informationen manuell eingeben.</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>Die Aktivierung auf dem Mobiltelefon wird gestartet. Sobald der Vorgang abgeschlossen ist, klicken Sie auf "Nehmen Sie Kontakt mit mir auf". Dadurch wird eine Benachrichtigung oder ein Überprüfungscode an Ihr Mobiltelefon gesendet. Klicken Sie auf "Überprüfen".</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>Klicken Sie auf "Schließen". Die Überprüfung sollte jetzt erfolgreich abgeschlossen sein.</li>
<li>Jetzt sollten Sie Ihre Mobiltelefonnummer eingeben, falls Sie einmal keinen Zugang zu Ihrer mobilen App mehr haben. <li>Wählen Sie Ihr Land aus der Dropdownliste aus, und geben Sie im Feld neben dem Land Ihre Mobiltelefonnummer ein. Klicken Sie auf "Weiter".</li> <li>Ihre Kontaktmethode ist nun eingerichtet. Als Nächstes richten Sie App-Kennwörter für Nicht-Browser-Apps wie Outlook 2010 oder früher ein. Wenn Sie solche Apps nicht verwenden, klicken Sie auf **Fertig**. Fahren Sie andernfalls mit dem nächsten Schritt fort.</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>Wenn Sie solche Apps verwenden, kopieren Sie das angegebene App-Kennwort und fügen es in die Nicht-Browser-Anwendung ein. Die Schritte für bestimmte Anwendungen wie Outlook und Lync finden Sie unter "Ändern des Kennworts in Ihrer E-Mail in das App-Kennwort" und "Ändern des Kennworts in Ihrer Anwendung in das App-Kennwort".</li>
<li>Klicken Sie auf Done.</li>

<!---HONumber=July15_HO2-->