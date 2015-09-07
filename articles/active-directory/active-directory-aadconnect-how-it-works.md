<properties 
	pageTitle="Funktionsweise von Azure AD Connect"
	description="Weitere Informationen zur Funktionsweise von Azure AD Connect."
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

# Funktionsweise von Azure AD Connect



Azure Active Directory Connect besteht aus drei Hauptbestandteilen. Dies sind die Synchronisierungsdienste, die optionalen Active Directory-Verbunddienste und die Überwachung mithilfe von [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Synchronisierung – Dieser Teil umfasst die Komponenten und Funktionen, die zuvor als Dirsync und AAD Sync veröffentlicht wurden. Dieser Teil ist für das Erstellen von Benutzern und Gruppen zuständig. Er stellt ebenfalls sicher, dass Benutzer- und Gruppeninformationen in Ihrer lokalen Umgebung denen in der Cloud entsprechen.
- AD FS – Dies ist eine optionale Komponente von Azure AD Connect und kann zum Einrichten einer Hybridumgebung mithilfe einer lokalen AD FS-Infrastruktur verwendet werden. Dieser Teil kann von Organisationen verwendet werden, um sich mit komplexen Bereitstellungen zu befassen, z. B. Domänenbeitritts-SSO, Erzwingen von AD-Anmelderichtlinien und Smartcard- bzw. Drittanbieter-MFA. Weitere Informationen zum Konfigurieren von SSO finden Sie unter [DirSync mit Single Sign-On](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Überwachung der Integrität – Für komplexe Bereitstellungen mithilfe von AD FS bietet Azure AD Connect Health eine stabile Überwachung Ihrer Verbundserver und einen zentralen Speicherort im Azure-Portal, um diese Aktivität anzuzeigen. Weitere Informationen finden Sie unter [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


## Azure AD Connect unterstützende Komponenten

Folgendes ist eine Liste der erforderlichen und unterstützenden Komponenten, die Azure AD Connect auf dem Server installiert, auf dem Azure AD Connect eingerichtet ist. Diese Liste ist für eine einfache Expressinstallation. Wenn Sie einen anderen SQL Server auf der Installationsseite für Synchronisierungsdienste verwenden möchten, werden die unten aufgeführten SQL Server 2012-Komponenten nicht installiert.

- Azure AD Connector für Azure AD Connect
- Microsoft SQL Server 2012 – Befehlszeilenprogramme
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory-Modul für Windows PowerShell
- Microsoft Online Services-Anmeldeassistent für IT-Experten
- Microsoft Visual C++ 2013 Redistributionspaket




 

<!---HONumber=August15_HO9-->