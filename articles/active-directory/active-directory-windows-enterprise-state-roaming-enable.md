<properties
    pageTitle="Aktivieren von Enterprise State Roaming in Azure Active Directory | Microsoft Azure"
    description="Enthält häufig gestellte Fragen zu Enterprise State Roaming-Einstellungen auf Windows-Geräten. Beim Enterprise State Roaming profitieren Benutzer von einer einheitlichen Benutzeroberfläche auf allen Windows-Geräten und von einer Reduzierung der Zeit, die zum Konfigurieren eines neuen Geräts benötigt wird."
    services="active-directory"
    keywords="Enterprise State Roaming, Windows-Cloud, Aktivieren von Enterprise State Roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
    ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

 

# Aktivieren von Enterprise State Roaming in Azure Active Directory

Enterprise State Roaming ist für Organisationen mit einem Premium Azure Active Directory-Abonnement (Azure AD) verfügbar. Weitere Informationen zum Abrufen eines Azure AD-Abonnements finden Sie auf der [Azure AD-Produktseite](https://azure.microsoft.com/services/active-directory).

Wenn Sie Enterprise State Roaming aktivieren, werden Ihrer Organisation automatisch Lizenzen für ein kostenloses Azure Rights Management-Abonnement zugeteilt. Dieses kostenlose Abonnement ist ausschließlich auf das Ver- und Entschlüsseln der Einstellungsdaten von Unternehmen beschränkt. Sie müssen über ein kostenpflichtiges Abonnement verfügen, um die vollständigen Funktionen von Azure Rights Management nutzen zu können.

Führen Sie nach dem Erhalt eines Azure AD-Abonnements diese Schritte aus, um Enterprise State Roaming zu aktivieren:
 
1. Melden Sie sich am klassischen Azure-Portal an. 
2. Wählen Sie links die Option **ACTIVE DIRECTORY** und dann das Verzeichnis aus, für das Sie Enterprise State Roaming aktivieren möchten. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. Klicken Sie oben auf die Registerkarte **Konfigurieren**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	Führen Sie auf der Seite einen Bildlauf nach unten durch, und wählen Sie die Option **Benutzer können Einstellungen und App-Daten des Unternehmens synchronisieren**. Klicken Sie dann auf **Speichern**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Damit für ein Windows 10-Gerät das Roaming von Einstellungen mit dem Enterprise State Roaming-Dienst möglich ist, muss das Gerät mit einer Azure AD-Identität authentifiziert werden. Für Geräte, die Azure AD beigetreten sind, ist die primäre Anmeldung des Benutzers die Azure AD-Identität. Es ist also keine zusätzliche Konfiguration erforderlich. Für Geräte, für die eine herkömmliche lokale Active Directory-Instanz verwendet wird, müssen IT-Administratoren die lokale Active Directory-Instanz mit Azure AD verbinden, indem sie [Azure AD Connect](active-directory-aadconnect.md) nutzen. Anschließend muss die Gruppenrichtlinie konfiguriert werden, um zu erzwingen, dass Clientgeräte ihre Benutzerdaten automatisch mit Azure synchronisieren.

## Synchronisieren der Datenspeicherung
Enterprise State Roaming-Daten werden in einer oder mehreren [Azure-Regionen](https://azure.microsoft.com/regions/) gehostet, die am besten zum Länderwert passt, der in der Azure AD-Instanz festgelegt ist. Beispielsweise werden Kunden, deren Länderwert auf „France“ (Frankreich) festgelegt ist, in einer oder mehreren Azure-Regionen in Europa gehostet. Für Kunden, die ihren Länderwert in Azure AD auf „US“ festlegen, werden die Daten in einer oder mehreren Azure-Regionen in den USA gehostet. Der Länderwert wird im Rahmen der Azure AD-Domänenerstellung festgelegt und kann später nicht mehr geändert werden.

Wenn Sie ausführliche Informationen zum Speicherort der Daten benötigen, können Sie beim [Azure-Support](https://azure.microsoft.com/support/options/) ein Ticket erstellen.

## Verwalten von Enterprise State Roaming
Azure AD-Mandantenadministratoren können Enterprise State Roaming im klassischen Azure-Portal aktivieren und deaktivieren. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Mandantenadministratoren können für jeden Benutzer auch einen Bericht zum Synchronisierungsstatus anzeigen und die Einstellungssynchronisierung auf bestimmte Sicherheitsgruppen beschränken.

##Beibehaltung von Daten
Daten, die per Enterprise State Roaming mit Azure synchronisiert werden, werden unendlich lange beibehalten, es sei denn, es wird ein manueller Löschvorgang durchgeführt, oder die betreffenden Daten werden als veraltet eingestuft.
 
- **Manuelles Löschen von Daten:** Wenn der Azure AD-Administrator Einstellungsdaten manuell löschen möchte, kann er beim [Azure-Support](https://azure.microsoft.com/support/options/) ein Ticket erstellen.
 
 - **Löschen von Benutzern:** Wenn ein Benutzer in Azure AD gelöscht wird, wird das Benutzerkonto für einen Zeitraum von 30 Tagen deaktiviert. Nach 30 Tagen wird das Konto gelöscht, und die betreffenden Einstellungsdaten unterliegen der Löschung.
 - **Löschen von Mandanten (Verzeichnissen):** Das Löschen eines gesamten Verzeichnisses in Azure AD ist ein unmittelbarer Vorgang. Alle Einstellungsdaten, die dem jeweiligen Verzeichnis zugeordnet sind, unterliegen dann der Löschung. 
 - **Löschen von Einstellungen:** Wenn der Azure AD-Administrator die Einstellungsdaten eines bestimmten Benutzers sofort löschen möchte, kann er beim Azure-Support ein entsprechendes Ticket erstellen. 
- **Veraltete Daten:** Daten, auf die ein Jahr lang nicht zugegriffen wurde (Aufbewahrungsdauer), werden als veraltet eingestuft und können aus Azure gelöscht werden. Bei den veralteten Daten kann es sich um eine bestimmte Gruppe von Windows-/Anwendungseinstellungen oder um alle Einstellungen eines Benutzers handeln. Beispiel: 
 - Wenn keine Geräte auf eine bestimmte Sammlung mit Einstellungen zugreifen (z. B. wird eine Anwendung vom Gerät entfernt, oder eine Einstellungsgruppe wie „Design“ wird für alle Geräte eines Benutzers deaktiviert), gilt diese Sammlung nach Ablauf der Aufbewahrungsdauer als veraltet und kann gelöscht werden. 
 - Falls ein Benutzer die Einstellungssynchronisierung auf allen eigenen Geräten deaktiviert hat, wird nicht auf die Einstellungsdaten zugegriffen. Alle Einstellungsdaten für diesen Benutzer werden als veraltet eingestuft und können nach Ablauf der Aufbewahrungsdauer gelöscht werden. 
 - Wenn der Azure AD-Verzeichnisadministrator Enterprise State Roaming für das gesamte Verzeichnis deaktiviert hat, wird die Synchronisierung der Einstellungen für alle Benutzer im jeweiligen Verzeichnis beendet. Alle Einstellungsdaten aller Benutzer gelten dann als veraltet und können nach Ablauf der Aufbewahrungsdauer gelöscht werden. 

- **Wiederherstellen gelöschter Daten:** Die Richtlinie für die Datenaufbewahrung ist nicht konfigurierbar. Nachdem die Daten endgültig gelöscht wurden, können sie nicht wiederhergestellt werden. Beachten Sie hierbei aber, dass die Einstellungsdaten nur aus Azure gelöscht werden, nicht vom Gerät des Endbenutzers. Falls mit einem Gerät zu einem späteren Zeitpunkt wieder eine Verbindung mit dem Enterprise State Roaming-Dienst hergestellt wird, werden die Einstellungen wieder synchronisiert und in Azure gespeichert.

## Verwandte Themen
- [Übersicht über Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Roaming von Einstellungen und Daten – Häufig gestellte Fragen](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Gruppenrichtlinien- und MDM-Einstellungen für Einstellungssynchronisierung](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10-Roamingeinstellungen – Referenz](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->