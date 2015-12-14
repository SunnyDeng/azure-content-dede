<properties 
	pageTitle="Beheben von Problemen mit der Azure Multi-Factor Authentication | Microsoft Azure" 
	description="In diesem Dokument erhalten Benutzer Informationen zur Vorgehensweise, wenn bei Multi-Factor Authentication ein Problem auftreten sollte." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Beheben von Problemen mit Azure Multi-Factor Authentication
Die folgende Informationen sollen Ihnen helfen, einige der häufigsten Probleme, die auftreten können, in den Griff zu bekommen.


- [Korrelations-ID-Fehler](#correlation-id-errors)
- [Ich habe mein Telefon verloren, oder es wurde mir gestohlen](#i-have-lost-my-phone-or-it-was-stolen?)
- [Ich möchte meine Telefonnummer ändern](#i-want-to-change-my-phone-number)
- [Ich empfange keinen Code auf meinem Telefon](#i-am-not-receiving-a-code-on-my-phone)
- [App-Kennwörter funktionieren nicht](#app-passwords-are-not-working)
- [Wie entferne ich Azure Authenticator von meinem alten Gerät, und wie wechsle ich zu einem neuen?](#how-do-i-clean-up-azure-authenticator-from-my-old-device-and-move-to-a-new-one)

##Korrelations-ID-Fehler
Wenn Sie die nachstehenden Schritte zur Problembehandlung ausgeführt haben und weiterhin Fehler auftreten, können Sie in den [Azure AD-Foren](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) eine Frage stellen, [die Microsoft Knowledge Base (KB) durchsuchen](https://www.microsoft.com/de-DE/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) oder [sich an den Support wenden](https://support.microsoft.com/de-DE). Wir bemühen uns, möglichst schnell eine Lösung für Ihr Problem zu finden.

Halten Sie die folgenden Informationen bereit, wenn Sie sich an den Support wenden:

 - **Allgemeine Beschreibung des Fehlers** – Welche Fehlermeldung wird dem Benutzer angezeigt? Wenn keine Fehlermeldung angezeigt wird, beschreiben Sie das beobachtete unerwartete Verhalten so genau wie möglich.
 - **Seite** – Auf welcher Seite haben Sie sich befunden, als der Fehler aufgetreten ist (einschließlich URL)?
 - **ErrorCode**: Der Fehlercode, den Sie erhalten.
 - **SessionId**: Die spezifische Sitzungs-ID, die Sie erhalten.
 - **Korrelations-ID**: Der generierte Code der Korrelations-ID bei Anzeige des Fehlers.
 - **Zeitstempel**: Genaues Datum samt Uhrzeit und Zeitzone des Auftretens des Fehlers.
 
![Korrelations-ID](./media/multi-factor-authentication-end-user-manage/correlation.png)

 - **Benutzer-ID** – Wie lautet die ID des Benutzers, der den Fehler beobachtet hat (z. B. user@contoso.com)?)?
 - **Informationen zum Benutzer** – Handelt es sich um einen Verbundbenutzer, um einen Benutzer mit Kennworthashsynchronisierung, oder um einen Benutzer, der ausschließlich über die Cloud zugreift? Verfügte der Benutzer über eine Azure AD Premium-, Enterprise Mobility- oder Azure AD Basic-Lizenz? Verwendet der Benutzer Office 365? usw.

Diese Informationen helfen uns dabei, Ihr Problem so schnell wie möglich zu beheben.

## Ich habe mein Telefon verloren, oder es wurde mir gestohlen
Wenn Ihr Telefon verloren gegangen ist oder gestohlen wurde, wird empfohlen, dass Sie Ihren Administrator bitten, Ihre [App-Kennwörter](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) zurückzusetzen und alle [gespeicherten Geräte](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-suspended-devices-for-a-user) zu löschen.

Sie haben zwei Optionen, um sich wieder bei Ihrem Konto anzumelden. Die erste ist, dass wenn Sie eine alternative Authentifizierungstelefonnummer eingerichtet haben, Sie diese für die Anmeldung bei Ihrem Konto verwenden und dann die Sicherheitseinstellungen ändern.

Wenn Sie eine sekundäre Authentifizierungstelefonnummer angegeben haben, können Sie sich damit anmelden. ![Einrichtung](./media/multi-factor-authentication-end-user-manage/altphone.png) Auf dem Bildschirmfoto oben sehen Sie, dass zwei Telefonnummern eingerichtet wurden. Eine endet mit 67 und die zweite mit 30.
  
Um sich mit der alternativen Telefonnummer anzumelden, gehen Sie wie üblich vor, und wählen Sie **Andere Überprüfungsoption verwenden** aus. ![Andere Überprüfung](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Wählen Sie dann Ihre andere Telefonnummer aus. Wählen Sie in diesem Fall **Rufen Sie mich unter der Rufnummer +XXXXXXXXX30 an** aus.

![Alternatives Telefon](./media/multi-factor-authentication-end-user-manage/altphone2.png)

>[AZURE.IMPORTANT]Konfigurieren Sie unbedingt eine sekundäre Authentifizierungstelefonnummer. Da sich Ihre primäre Telefonnummer und ihre mobile App wahrscheinlich auf demselben Telefon befinden, ist die sekundäre Telefonnummer die einzige Möglichkeit, sich wieder bei Ihrem Konto anzumelden, sollte Ihr Telefon verloren gehen oder gestohlen werden.

Wenn Sie keine sekundäre Authentifizierungstelefonnummer konfiguriert haben, müssen Sie den Administrator bitten, Ihre Einstellungen zu löschen, sodass Sie bei der nächsten Anmeldung aufgefordert werden, die [Multi-Factor Authentication erneut einzurichten](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

## Ich möchte meine Telefonnummer ändern
Je nachdem, wie Sie Multi-Factor Authentication verwenden, gibt es mehrere Stellen, an denen Sie Einstellungen wie Ihre Telefonnummer ändern können. Verwenden Sie die folgende Tabelle, mit deren Hilfe Sie die für Sie geeignetste Option bestimmen können.

Verwendung von Multi-Factor Authentication|Beschreibung
:------------- | :------------- | 
[Ich verwende sie mit Office 365.](#changing-your-settings-with-office-365)| Dies bedeutet, dass Sie die Einstellungen über das Office 365-Portal ändern sollten.
[Nicht bekannt](#changing-your-settings-with-the-myapps-portal)|Dies bedeutet, dass Sie sich bei [http://myapps.microsoft.com](http://myapps.microsoft.com) anmelden und dort die Einstellungen ändern sollten.
[Ich verwende sie mit Microsoft Azure.](#changing-your-settings-with-microsoft-azure)| Dies bedeutet, dass Sie die Einstellungen über das Azure-Portal ändern sollten.


 
### Ändern der Einstellungen mit Office 365


Wenn Sie Multi-Factor Authentication mit Office 365 verwenden, sollten Sie die Einstellungen für die zusätzliche Sicherheitsüberprüfung über das Office 365-Portal verwalten.

#### So ändern Sie Ihre Einstellungen im Office 365-Portal

1. Melden Sie sich beim [Office 365-Portal](https://login.microsoftonline.com/) an.
2. Klicken Sie in der oberen rechten Ecke auf das Widget, und wählen Sie die Office 365-Einstellungen.
3. Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung".
4. Wählen Sie auf der rechten Seite **Aktualisieren meiner Telefonnummern für die Kontosicherheit** aus. ![O365](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können. ![O365](./media/multi-factor-authentication-end-user-manage/o365b.png)


### Ändern Ihrer Einstellungen im Portal "Meine Apps"

Wenn Sie nicht sicher sind, wie Sie die mehrstufige Authentifizierung verwenden, können Sie Ihre Einstellungen jederzeit über das Portal "Meine Apps" ändern.

#### So ändern Sie Ihre Einstellungen im Portal "Meine Apps"

1. Melden Sie sich an bei [https://myapps.microsoft.com](https://myapps.microsoft.com).	
2. Wählen Sie oben das Profil aus.
3. Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung". ![MyApps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können.

![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

### Ändern der Einstellungen mit Microsoft Azure

Wenn Sie Multi-Factor Authentication mit Azure verwenden, können Sie Ihre Einstellungen über das Azure-Portal ändern.

#### So greifen Sie auf die Einstellungen für die zusätzliche Sicherheitsüberprüfung im Azure-Portal zu


1. Melden Sie sich beim Azure-Portal an.
2. Klicken Sie oben im Azure-Portal auf Ihren Benutzernamen. Eine Dropdownliste wird angezeigt.
3. Wählen Sie in der Dropdownliste "Zusätzliche Sicherheitsüberprüfung" aus. ![Azure](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können. ![Proofup](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

##Ich empfange keinen Code auf meinem Telefon

Zunächst müssen Sie Folgendes sicherstellen:

- Wenn Sie das Empfangen von Überprüfungscodes per Textnachricht auf Ihrem Mobiltelefon ausgewählt haben, vergewissern Sie sich, dass Ihr Tarif und Gerät die Übermittlung von Textnachrichten unterstützen. Übertragungsgeschwindigkeit und Verfügbarkeit können je nach Region und Dienstanbieter variieren. Stellen Sie auch sicher, dass das Mobilfunksignal stark genug ist, um diese Codes zu empfangen.
- Wenn Sie das Empfangen einer Überprüfung über die mobile App gewählt haben, stellen Sie sicher, dass das Mobilfunksignal stark genug ist. Beachten Sie auch, dass Übertragungsgeschwindigkeit und Verfügbarkeit je nach Region und Dienstanbieter variieren können. 

Bei Smartphones wird empfohlen, die [Azure Authenticator-App](multi-factor-authentication-azure-authenticator) zu nutzen.

Sie können zwischen dem Empfang von Überprüfungscodes per Textnachrichten und per mobiler App wechseln, indem Sie beim Anmelden **Andere Überprüfungsoption verwenden** auswählen.

![Andere Überprüfung](./media/multi-factor-authentication-end-user-manage/differentverification.png)


Mitunter ist die Übermittlung einer dieser Dienste zuverlässiger als der andere.

Wenn Sie mehrere Überprüfungscodes erhalten haben, funktioniert immer nur der neueste.

Falls Sie zuvor einen Ersatzanschluss konfiguriert haben, wird empfohlen, den Vorgang zu wiederholen, indem Sie dieses Telefon auswählen, wenn Sie auf der Anmeldeseite dazu aufgefordert werden. Wenn Sie keine andere Methode konfiguriert haben, bitten Sie Ihren Administrator, Ihre Einstellungen zu löschen, damit Sie bei der nächsten Anmeldung aufgefordert werden, die [Multi-Factor Authentication erneut einzurichten](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

##App-Kennwörter funktionieren nicht
Stellen Sie zunächst sicher, dass Sie das App-Kennwort richtig eingegeben haben. Wenn es weiterhin nicht funktioniert, versuchen Sie, sich anzumelden, und [erstellen Sie ein neues App-Kennwort](multi-factor-authentication-end-user-app-passwords.md). Wenn dies nicht funktioniert, bitten Sie den Administrator, [Ihre vorhandenen App-Kennwörter zu löschen](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords). Erstellen Sie anschließend ein neues, und nutzen Sie dieses.

##Wie entferne ich Azure Authenticator von meinem alten Gerät, und wie wechsle ich zu einem neuen?
Wenn Sie die App von Ihrem Gerät deinstallieren oder das Gerät auf die Werkseinstellungen zurücksetzen, wird die Aktivierung im Back-End nicht entfernt. Befolgen Sie die Schritte unter [Wechseln zu einem neuen Gerät](multi-factor-authentication-azure-authenticator.md#how-to-move-to-the-new-azure-authenticator-app).

<!---HONumber=AcomDC_1203_2015-->