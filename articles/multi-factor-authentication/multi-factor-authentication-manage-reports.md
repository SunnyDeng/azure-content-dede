<properties 
	pageTitle="Azure Multi-Factor Authentication-Berichte" 
	description="Beschreibt, wie Sie das Berichte-Feature für Multi-Factor Authentication verwenden." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/11/2016" 
	ms.author="billmath"/>

# Berichte in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication bietet verschiedene Berichte, die Sie und Ihre Organisation verwenden können. Auf diese Berichte können Sie über das Multi-Factor Authentication-Verwaltungsportal zugreifen. Dazu benötigen Sie einen Azure MFA-Anbieter bzw. eine Azure MFA-, Azure AD Premium- oder Enterprise Mobility Suite-Lizenz. Folgendes ist eine Liste der verfügbaren Berichte.

Sie können auf die Berichte über das Azure-Verwaltungsportal zugreifen.

Name| Beschreibung
:------------- | :------------- | 
Verwendung | Die Nutzungsberichte geben Informationen zur Gesamtnutzung, Benutzerzusammenfassungen und Benutzerdetails an.
Serverstatus|Dieser Bericht zeigt den Status von Multi-Factor Authentication-Servern an, die mit Ihrem Konto verknüpft sind.
Verlauf – gesperrte Benutzer|Dieser Bericht zeigt den Verlauf von Anforderungen zum Sperren oder Entsperren von Benutzern an.
Verlauf – Umgangene Benutzer|Dieser Bericht zeigt den Verlauf von Anforderungen an, die mehrstufige Authentifizierung für die Telefonnummer des Benutzers zu umgehen.
Betrugswarnung|Dieser Bericht zeigt den Verlauf von Betrugswarnungen an, die im angegebenen Zeitraum übermittelt wurden.
In Warteschlange|Hier werden Berichte aufgelistet, die zur Verarbeitung und aufgrund ihres Status in der Warteschlange sind. Nach Abschluss des Berichts wird ein Link zum Herunterladen oder Anzeigen des Berichts bereitgestellt.

## Anzeigen von Berichten

1.	Melden Sie sich unter http://azure.microsoft.com an.
2.	Wählen Sie im linken Bereich "Active Directory" aus.
3.	Wählen Sie eine der folgenden Optionen:
	- **Option 1**: Klicken Sie auf die Registerkarte mit den Multi-Factor Authentication-Anbietern. Wählen Sie Ihren MFA-Anbieter, und klicken Sie auf die Schaltfläche „Verwalten“ am unteren Rand.
	- **Option 2**: Wählen Sie Ihr Verzeichnis aus und klicken Sie auf die Registerkarte „Konfigurieren“. Klicken Sie im Abschnitt „Multi-Factor Authentication“ auf „Diensteinstellungen verwalten“. Klicken Sie am unteren Rand der Seite mit den Einstellungen für den MFA-Dienst auf den Link „Portal aufrufen“.
4.	Im Azure Multi-Factor Authentication-Verwaltungsportal sehen Sie auf der linken Seite den Bereich „Einen Bericht anzeigen“. Von hier aus können Sie die oben beschriebenen Berichte auswählen.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Weitere Ressourcen**

* [Für Benutzer](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication bei MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=AcomDC_0114_2016-->