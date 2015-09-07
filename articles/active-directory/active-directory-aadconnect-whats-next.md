<properties 
	pageTitle="Verwalten von Azure AD Connect"
	description="Erfahren Sie, wie die Standardkonfiguration und Betriebsaufgaben für Azure AD Connect erweitert werden."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Verwalten von Azure AD Connect 



Anhand der Anweisungen in den folgenden weiterführenden Themen können Sie Azure Active Directory Connect entsprechend den Bedürfnissen und Anforderungen Ihrer Organisation anpassen.

## Zuweisen von Lizenzen für Benutzer von Azure AD Premium und Enterprise Mobility

Nachdem Ihre Benutzer in der Cloud synchronisiert wurden, müssen Sie ihnen nun eine Lizenz zuweisen, sodass sie Cloudanwendungen wie z. B. Office 365 verwenden können.

### So weisen Sie eine Azure AD Premium- oder Enterprise Mobility Suite-Lizenz zu
--------------------------------------------------------------------------------
1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich **Active Directory** aus.
3. Doppelklicken Sie auf der Seite "Active Directory" auf das Verzeichnis mit den Benutzern, die Sie aktivieren möchten.
4. Wählen Sie oben auf der Seite des Verzeichnisses die Option **Lizenzen** aus.
5. Wählen Sie auf der Seite "Lizenzen" die Option "Active Directory Premium" oder "Enterprise Mobility Suite" aus, und klicken Sie dann auf **Zuweisen**.
6. Wählen Sie im Dialogfeld die Benutzer aus, denen Sie Lizenzen zuweisen möchten, und klicken Sie dann auf das Häkchen, um die Änderungen zu speichern.


## Überprüfen der geplanten Synchronisierungsaufgabe
Sie können den Status einer Synchronisierung im Azure-Portal überprüfen.

### So überprüfen Sie die geplante Synchronisierungsaufgabe
--------------------------------------------------------------------------------

1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich **Active Directory** aus.
3. Doppelklicken Sie auf der Seite "Active Directory" auf das Verzeichnis mit den Benutzern, die Sie aktivieren möchten.
4. Wählen Sie oben auf der Seite des Verzeichnisses die Option **Verzeichnisintegration** aus.
5. Prüfen Sie unter "Integration mit lokalem Active Directory" die letzte Synchronisierungszeit.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## Starten einer geplanten Synchronisierungsaufgabe
Wenn Sie eine Synchronisierungsaufgabe ausführen möchten, können Sie dazu wieder den Azure AD Connect-Assistenten durchlaufen. Sie müssen Ihre Azure AD-Anmeldeinformationen angeben. Wählen Sie im Assistenten die Aufgabe **Synchronisierungsoptionen anpassen** aus, und klicken Sie in den weiteren Schritten des Assistenten auf "Weiter". Stellen Sie am Ende sicher, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, sobald die Anfangskonfiguration abgeschlossen wurde** aktiviert ist.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




## Weitere verfügbare Aufgaben in Azure AD Connect
Nach der ersten Installation von Azure AD Connect können Sie den Assistenten über die Azure AD Connect-Startseite oder die Desktopverknüpfung jederzeit erneut starten. Sie werden feststellen, dass beim erneuten Durchlaufen des Assistenten unter "Zusätzliche Aufgaben" einige neue Optionen zur Verfügung stehen.

Die folgende Tabelle enthält eine Zusammenfassung und jeweils eine kurze Beschreibung dieser Aufgaben.

![Join Rule](./media/active-directory-aadconnect-whats-next/addtasks.png)


Weitere Aufgabe | Beschreibung 
------------- | ------------- |
Ausgewähltes Szenario anzeigen |Hiermit können Sie Ihre aktuelle Azure AD Connect-Lösung anzeigen. Dazu gehören allgemeine Einstellungen, synchronisierte Verzeichnisse, Synchronisierungseinstellungen usw.
Synchronisierungsoptionen anpassen | Hiermit können Sie die aktuelle Konfiguration ändern, z. B. Hinzufügen weiterer Aktive Directory-Gesamtstrukturen zur Konfiguration oder Aktivieren von Synchronisierungsoptionen wie beispielsweise "Benutzer", "Gruppe", "Gerät" oder "Kennwort zurückschreiben".
Stagingmodus aktivieren | Hiermit können Sie Informationen bereitstellen, die später synchronisiert, jedoch nicht in Azure AD oder Active Directory exportiert werden. Auf diese Weise können Sie die Synchronisierungen in der Vorschau anzeigen, bevor sie ausgeführt werden.


 






## Zusätzliche Dokumentation
Zusätzliche Dokumentation zur Verwendung von Azure AD Connect:

- [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
- [Changing the Azure AD Connect default configuration](active-directory-aadconnect-whats-next-change-default-config.md) (in englischer Sprache)
- [Using the Azure AD Connect Synchronization Rules Editor](active-directory-aadconnect-whats-next-synch-rules-editor.md) (in englischer Sprache)
- [Using declarative provisioning](active-directory-aadconnect-whats-next-declarative-prov.md) (in englischer Sprache)

Ein Teil der Dokumentation für Azure AD Sync ist noch immer relevant und gilt für Azure AD Connect. Obwohl jede Anstrengung unternommen wird, diese Dokumentation an Azure.com zu übermitteln, befindet sich ein Teil noch immer in der MSDN-Bibliothek. Zusätzliche Dokumentation finden Sie unter [Azure AD Connect auf MSDN](active-directory-aadconnect.md) und [Azure AD Sync auf MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).


 

<!---HONumber=August15_HO9-->