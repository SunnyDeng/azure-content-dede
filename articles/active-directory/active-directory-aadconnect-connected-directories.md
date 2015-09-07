<properties 
	pageTitle="Verbinden Sie Ihre Verzeichnisse mit Azure AD Connect"
	description="Beschreibung der benutzerdefinierten Einstellungen von Verzeichnissen, die mit Azure AD Connect verbunden wurden."
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



# Verbinden Sie Ihre Verzeichnisse mit Azure AD Connect

Für benutzerdefinierte Einstellungen ist kein Unternehmensadministratorkonto erforderlich, um eine Verbindung zu Ihren Active Directory-Gesamtstrukturen herzustellen. Der Assistent akzeptiert die Anmeldung mit einem Domänenkonto oder einem lokalen Benutzerkonto. Sie sollten allerdings beachten, dass das Konto als lokales AD-Connector-Konto verwendet wird. Das bedeutet, dass es zur Synchronisierung für Lese- und Schreibvorgänge für Verzeichnisinformationen verwendet wird.

Entsprechend müssen Sie für die folgenden Szenarien zusätzliche Berechtigungen zuweisen:

Szenario |Berechtigung
------------- | ------------- |
Kennwortsynchronisierung| <li>Verzeichnisänderungen replizieren</li> <li>Alle Verzeichnisänderungen replizieren</li>
Exchange-Hybridbereitstellung|Siehe [Office 365 Exchange Hybrid AAD Sync write-back attributes and permissions](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange) (in englischer Sprache).
Kennwortrückschreiben | <li>Kennwort ändern</li><li>Kennwort zurücksetzen</li>
Benutzer-, Gruppen- und Geräterückschreiben|Schreibberechtigungen für die Active Directory-Objekte und -Attribute, die Sie "zurückschreiben" möchten.
Einmaliges Anmelden und AD FS| Domänenadministratorberechtigungen in der Domäne, in der sich die Verbundserver befinden.





**Zusätzliche Ressourcen**

* [Weitere Informationen zu Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-account-summary.md)
* [Berechtigungen für die Kennwortsynchronisierung](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Berechtigungen für Exchange-Hybrid](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Berechtigungen für das Kennwortrückschreiben](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect auf MSDN](active-directory-aadconnect.md)
 

<!---HONumber=August15_HO9-->