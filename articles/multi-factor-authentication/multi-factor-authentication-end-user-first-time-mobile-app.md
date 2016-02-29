<properties 
	pageTitle="Verwenden einer mobilen App als Kontaktmethode mit Azure MFA" 
	description="Auf dieser Seite erfahren Benutzer, wie sie die mobile App als primäre Kontaktmethode für Azure MFA verwenden." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Verwenden einer mobilen App als Kontaktmethode mit Azure Multi-Factor Authentication

Wenn Sie die mobile App als primäre Kontaktmethode verwenden möchten, finden Sie weitere Informationen in diesem Artikel. Hier erfahren Sie, wie Sie Multi-Factor Authentication einrichten, um Ihre mobile App als primäre Kontaktmethode zu verwenden.

Die Azure Authenticator-App ist für [Windows Phone](http://www.windowsphone.com/de-DE/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) und [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) verfügbar.

## So verwenden Sie eine mobile App als Kontaktmethode


- Wählen Sie in der Dropdownliste "Mobile App" aus.


![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Wählen Sie "Benachrichtigung" oder "Einmalkennwort" aus, und klicken Sie auf "Einrichten".
- Starten Sie die Azure Authenticator-App auf dem Mobiltelefon, auf dem die App installiert ist, und tippen Sie auf "Barcode einlesen". Informationen zum Hinzufügen eines Kontos, das bereits über Azure MFA oder ein Drittanbieterkonto verfügt, finden Sie unter [Manuelles Hinzufügen eines Kontos](#adding-an-account-manually).

![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Scannen Sie das Bild mit dem Barcode, das im Bildschirm "Mobile App konfigurieren" angezeigt wird. Klicken Sie auf "Fertig", um den Bildschirm mit dem Barcode zu schließen. Wenn der Barcode nicht gescannt werden kann, können Sie die Informationen manuell eingeben.

![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Die Aktivierung auf dem Mobiltelefon wird gestartet. Sobald der Vorgang abgeschlossen ist, klicken Sie auf "Nehmen Sie Kontakt mit mir auf". Dadurch wird eine Benachrichtigung oder ein Überprüfungscode an Ihr Mobiltelefon gesendet. Klicken Sie auf "Überprüfen".

![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Klicken Sie auf "Schließen". Die Überprüfung sollte jetzt erfolgreich abgeschlossen sein.
- Jetzt sollten Sie Ihre Mobiltelefonnummer eingeben, falls Sie einmal keinen Zugang zu Ihrer mobilen App mehr haben.
- Wählen Sie Ihr Land aus der Dropdownliste aus, und geben Sie im Feld neben dem Land Ihre Mobiltelefonnummer ein. Klicken Sie auf Weiter.
- Ihre Kontaktmethode ist nun eingerichtet. Als Nächstes richten Sie App-Kennwörter für Nicht-Browser-Apps wie Outlook 2010 oder früher ein. Wenn Sie solche Apps nicht verwenden, klicken Sie auf **Fertig**. Fahren Sie andernfalls mit dem nächsten Schritt fort.

![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Wenn Sie solche Apps verwenden, kopieren Sie das angegebene App-Kennwort und fügen es in die Nicht-Browser-Anwendung ein. Die Schritte für bestimmte Anwendungen wie Outlook und Lync finden Sie unter "Ändern des Kennworts in Ihrer E-Mail in das App-Kennwort" und "Ändern des Kennworts in Ihrer Anwendung in das App-Kennwort".
- Klicken Sie auf Done.


## Manuelles Hinzufügen eines Kontos
Wenn Sie ein Konto manuell hinzufügen möchten, wählen Sie die Schaltfläche zum manuellen Eingeben des Kontos aus.

![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

Wenn Sie ein Konto besitzen, das bereits über Azure MFA verfügt, geben Sie den Code und die URL ein, die auf der Seite mit dem Barcode bereitgestellt werden. Diese werden in der mobilen App in den Feldern für Code und URL eingegeben. Dadurch wird die Aktivierung gestartet.

![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Sobald der Vorgang abgeschlossen ist, klicken Sie auf die Schaltfläche zur Kontaktaufnahme. Dadurch wird eine Benachrichtigung oder ein Überprüfungscode an Ihr Mobiltelefon gesendet. Klicken Sie auf "Überprüfen". Befolgen Sie zum Abschluss die oben aufgeführten Schritte, beginnend mit Schritt 6.

Wenn Sie ein Drittanbieterkonto mit der mobilen App verwenden, geben Sie den Kontonamen und den Sicherheitsschlüssel in die entsprechenden Textfelder ein, und aktivieren Sie anschließend das Konto. Sobald Sie das Konto überprüft haben, befolgen Sie die oben aufgeführten Schritte, beginnend mit Schritt 6.


![Einrichtung](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]Wenn Sie zum Hinzufügen eines Geschäftskontos aufgefordert werden, gilt dies für die Einbindung in den Arbeitsplatz und nicht für die mehrstufige Authentifizierung. Sie können dies ignorieren.
 

<!---HONumber=AcomDC_0218_2016-->