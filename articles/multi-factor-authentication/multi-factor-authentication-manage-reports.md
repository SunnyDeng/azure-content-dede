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
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Berichte in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication bietet verschiedene Berichte, die Sie und Ihre Organisation verwenden können. Auf diese Berichte können Sie über das Multi-Factor Authentication-Verwaltungsportal zugreifen. Folgendes ist eine Liste der verfügbaren Berichte.

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

1. Melden Sie sich an bei [http://azure.microsoft.com](http://azure.microsoft.com).
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Wählen Sie oben "Anbieter für mehrstufige Authentifizierung" aus. Hierdurch wird eine Liste der Anbieter für mehrstufige Authentifizierung angezeigt.
4. Wenn Sie mehr als einen Anbieter für mehrstufige Authentifizierung haben, wählen Sie denjenigen aus, bei dem Sie den Betrugswarnbericht anzeigen möchten, und klicken Sie am unteren Rand der Seite auf "Verwalten". Wenn Sie nur einen Anbieter haben, klicken Sie direkt auf "Verwalten". Dadurch wird das Multi-Factor Authentication-Verwaltungsportal von Azure geöffnet.
5. Im Azure Multi-Factor Authentication-Verwaltungsportal sehen Sie auf der linken Seite "Einen Bericht anzeigen". Von hier aus können Sie die oben beschriebenen Berichte auswählen.


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Weitere Ressourcen**

* [Für Benutzer](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication bei MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=August15_HO9-->