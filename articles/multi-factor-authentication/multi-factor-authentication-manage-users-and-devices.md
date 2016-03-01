<properties 
	pageTitle="Azure Multi-Factor Authentication-Berichte" 
	description="Hier wird das Ändern von Benutzereinstellungen beschrieben, z. B. wie Benutzer dazu gebracht werden, den Nachweis-Prozess noch einmal durchzuführen." 
	documentationCenter="" 
	services="multi-factor-authentication" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Verwalten von Benutzereinstellungen mit Azure Multi-Factor Authentication (MFA) in der Cloud

Als Administrator können Sie die folgenden Einstellungen für Benutzer und Geräte verwalten.

- [Ausgewählte Benutzer müssen Kontaktmethoden erneut bereitstellen.](#require-selected-users-to-provide-contact-methods-again)
- [Löschen vorhandener App-Kennwörter eines Benutzers](#delete-users-existing-app-passwords)
- [Wiederherstellen der MFA auf allen ausgesetzten Geräten für einen Benutzer (öffentliche Vorschau)](#restore-mfa-on-all-suspended-devices-for-a-user)






Dies ist hilfreich, wenn ein Computer oder Gerät verloren geht oder gestohlen wird, oder wenn Sie einen Benutzerzugriff entfernen müssen.


## Ausgewählte Benutzer müssen Kontaktmethoden erneut bereitstellen

Durch diese Einstellung wird der Benutzer gezwungen, die Registrierung bei der Anmeldung erneut durchzuführen. Denken Sie daran, dass Nicht-Browser-Apps weiterhin funktionieren, wenn der Benutzer über App-Kennwörter dafür verfügt. Sie können die App-Kennwörter eines Benutzers löschen, indem Sie zusätzlich **Löschen aller vorhandener App-Kennwörter, die von ausgewählten Benutzern generiert wurden** auswählen.

### Benutzer dazu bringen, Kontaktmethoden erneut bereitzustellen

<ol>
<li>Melden Sie sich am Azure-Verwaltungsportal an.</li>
<li>Klicken Sie im linken Bereich auf "Active Directory".</li>
<li>Klicken Sie unter "Directory" auf das Verzeichnis für den Benutzer, der seine Kontaktmethode erneut bereitstellen soll.</li>
<li>Klicken Sie oben auf "Benutzer".</li>
<li>Klicken Sie unten auf der Seite auf "Verwalten der Multi-Factor Authentication". Die Seite für die mehrstufige Authentifizierung wird geöffnet. <li>Suchen Sie den Benutzer, den Sie verwalten möchten, und aktivieren Sie das Kontrollkästchen neben seinem Namen. Sie müssen möglicherweise oben die Ansicht ändern.</li> <li>Hierdurch erscheint rechts der Link **Benutzereinstellungen verwalten**. Klicken Sie auf diese Schaltfläche.</li> <li>Aktivieren Sie das Kontrollkästchen in **Ausgewählte Benutzer müssen Kontaktmethoden erneut bereitstellen**.</li>

![Kontaktmethoden bereitstellen](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>Klicken Sie auf "Speichern".</li>
<li>Klicken Sie auf "Schließen".</li>

## Löschen Sie die vorhandenen App-Kennwörter des Benutzers.

Dies löscht alle App-Kennwörter, die ein Benutzer erstellt hat. Nicht-Browser-Apps, die diesen App-Kennwörtern zugeordnet waren, sind bis zur Erstellung eines neuen App-Kennworts nicht mehr funktionsfähig.

### Löschen vorhandener App-Kennwörter eines Benutzers

<ol>
<li>Melden Sie sich am Azure-Verwaltungsportal an.</li>
<li>Klicken Sie im linken Bereich auf "Active Directory".</li>
<li>Klicken Sie unter "Verzeichnis" auf das Verzeichnis für den Benutzer, dem Sie die App-Kennwörter löschen möchten.</li>
<li>Klicken Sie oben auf "Benutzer".</li>
<li>Klicken Sie unten auf der Seite auf "Verwalten der Multi-Factor Authentication". Die Seite für die mehrstufige Authentifizierung wird geöffnet. <li>Suchen Sie den Benutzer, den Sie verwalten möchten, und aktivieren Sie das Kontrollkästchen neben seinem Namen. Sie müssen möglicherweise oben die Ansicht ändern.</li> <li>Hierdurch erscheint rechts der Link **Benutzereinstellungen verwalten**. Klicken Sie auf diese Schaltfläche.</li> <li>Aktivieren Sie das Kontrollkästchen in **Löschen aller vorhandener App-Kennwörter, die von den ausgewählten Benutzern generiert wurden**.</li> ![App-Kennwörter löschen](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)<li>Klicken Sie auf "Speichern".</li> <li>Klicken Sie auf "Schließen".</li>





## Wiederherstellen der MFA auf allen ausgesetzten Geräten eines Benutzers

Administratoren haben die Möglichkeit, die Multi-Factor Authentication auf ihren Geräten und in den Browsern zurückzusetzen. Dies erfolgt durch die Wiederherstellung der Multi-Factor Authentication für die Geräte und Browser eines Benutzers. Dabei wird die Aussetzung von allen Geräten und Browsern des Benutzers entfernt.

### MFA auf allen ausgesetzten Geräten für einen Benutzer wiederherstellen

<ol>
<li>Melden Sie sich am Azure-Verwaltungsportal an.</li>
<li>Klicken Sie im linken Bereich auf "Active Directory".</li>
<li>Klicken Sie unter "Verzeichnis" auf das Verzeichnis für den Benutzer, für den die MFA wiederhergestellt werden soll.</li>
<li>Klicken Sie oben auf "Benutzer".</li>
<li>Klicken Sie unten auf der Seite auf "Verwalten der Multi-Factor Authentication". Die Seite für die mehrstufige Authentifizierung wird geöffnet. <li>Suchen Sie den Benutzer, den Sie verwalten möchten, und aktivieren Sie das Kontrollkästchen neben seinem Namen. Sie müssen möglicherweise oben die Ansicht ändern.</li> <li>Hierdurch erscheint rechts der Link **Benutzereinstellungen verwalten**. Klicken Sie auf diese Schaltfläche.</li> <li>Aktivieren Sie das Kontrollkästchen bei "Multi-Factor Authentication-Wiederherstellung auf allen ausgesetzten Geräten".</li> ![App-Kennwörter löschen](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png) <li>Klicken Sie auf "Speichern".</li> <li>Klicken Sie auf "Schließen".</li>

<!---HONumber=AcomDC_0218_2016-->