<properties 
	pageTitle="Warum Sie ein globales Azure AD-Administratorkonto benötigen, um Azure AD Connect einzurichten" 
	description="Beschreibung für benutzerdefinierte Einstellungen – warum ein globales Administratorkonto erforderlich ist." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Warum Sie ein globales Azure AD-Administratorkonto benötigen, um Azure AD Connect einzurichten

In der folgenden Tabelle wird erläutert, warum für die Einrichtung von Azure AD Connect ein globales Azure AD-Administratorkonto erforderlich ist.

Unter den folgenden Bedingungen | Beschreibung 
------------- | ------------- |
Für Express-Einstellungen und DirSync-Upgrade | Aktivieren Sie (falls erforderlich) die Synchronisierung in Ihrem Azure AD-Verzeichnis, und erstellen Sie das Azure AD-Konto, das für fortlaufende Synchronisierungsvorgänge verwendet werden soll (das Azure AD Connector-Konto). 
Für benutzerdefinierte Einstellungen | Aktivieren Sie die Synchronisierung in Ihrem Azure AD-Verzeichnis, und erstellen Sie das Azure AD-Konto, das für fortlaufende Synchronisierungsvorgänge verwendet werden soll (das Azure AD Connector-Konto). Für das einmalige Anmelden mit der AD FS-Option können Sie die Verbundeigenschaften in Azure AD lesen und konfigurieren.



**Zusätzliche Ressourcen**


* [Weitere Informationen zu Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-account-summary.md)
* [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=August15_HO6-->