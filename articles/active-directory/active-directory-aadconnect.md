<properties 
	pageTitle="Aktivieren Sie Ihr Verzeichnis für das Hybrid-Management mit Azure AD Connect." 
	description="Nachfolgend finden Sie einen Überblick über Azure AD Connect sowie eine Beschreibung des Einsatzes dieser Technologie." 
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
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Aktivieren Sie Ihr Verzeichnis für das Hybrid-Management mit Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Worum handelt es sich?" class="current">Worum handelt es sich?</a> <a href="../active-directory-aadconnect-how-it-works/" title="Funktionsweise">Funktionsweise</a> <a href="../active-directory-aadconnect-get-started/" title="Erste Schritte">Erste Schritte</a> <a href="../active-directory-aadconnect-whats-next/" title="Nächste Schritte">Nächste Schritte</a> <a href="../active-directory-aadconnect-learn-more/" title="Weitere Informationen">Weitere Informationen</a>
</div>


Heutzutage möchten Benutzer auf Anwendungen sowohl lokal als auch in der Cloud zugreifen können. Dieser Zugriff soll von jedem beliebigen Gerät aus erfolgen, z. B. einem Laptop, Smartphone oder Tablet. Da die vollständige Migration in die Cloud nicht immer möglich ist, müssen Unternehmen eine Lösung finden, wie sie Benutzern die benötigten Anwendungen zur Verfügung stellen können.

<center>![Was ist Azure&#160;AD Connect?](./media/active-directory-aadconnect/arch.png)</center>

Die Einführung von Azure Active Directory vereinfacht den Zugriff auf diese Anwendungen und die Migration in die Cloud. Azure AD Connect bietet die folgenden Vorteile:

- Die Benutzer können sich mit einer einzigen Identität sowohl in der Cloud als auch lokal anmelden. Das Merken mehrerer Benutzerpasswörter und -konten entfällt, und Administratoren entsteht kein Mehraufwand durch die Verwaltung mehrerer Konten.
- Azure AD Connect stellt das einzige Tool samt Anleitungen zum Verbinden Ihrer lokalen Verzeichnisse mit Azure Active Directory dar. Nach der Installation implementiert und konfiguriert der Assistent alle Komponenten, die erforderlich sind, um Ihre Verzeichnisintegration einzurichten und auszuführen, einschließlich Synchronisierungsdiensten, Kennwortsynchronisierung oder Active Directory-Verbunddiensten (AD FS) sowie Voraussetzungen wie dem Azure AD PowerShell-Modul.



<center>![Was ist Azure&#160;AD Connect?](./media/active-directory-aadconnect/azuread.png)</center>




## Gründe für die Verwendung von Azure AD Connect 

Die Integration Ihrer lokalen Verzeichnisse in Azure AD steigert die Produktivität Ihrer Benutzer, da für den Zugriff auf die Cloud und lokale Ressourcen nur eine Identität benötigt wird. Mit dieser Integration können Benutzer und Organisationen die folgenden Vorteile nutzen:
	
* Organisationen können für Benutzer eine Hybrid-Identität zum Zugriff auf lokale und cloudbasierte Dienste bereitstellen, die mithilfe von Windows Server Active Directory die Verbindung zu Azure Active Directory ermöglicht. 
* Administratoren können basierend auf Anwendungsressourcen, Geräte- und Benutzeridentität, Netzwerkadressen und Multi-Factor Authentication Richtlinien für den bedingten Zugriff erstellen.
* Benutzer können diese allgemeine Identität für Konten von Azure AD bis Office 365, Intune, SaaS-Anwendungen und Anwendungen von Drittanbietern nutzen.  
* Entwickler können Anwendungen erstellen, die das allgemeine Identitätsmodell nutzen, und Anwendungen in das lokale Active Directory oder Azure für cloudbasierte Anwendungen integrieren.

Connect von Azure AD erleichtert diese Integration und vereinfacht die Verwaltung Ihrer lokalen und cloudbasierten Identitätsinfrastruktur.



----------------------------------------------------------------------------------------------------------
## Azure AD Connect herunterladen

Für Ihre ersten Schritte mit Azure AD Connect können Sie die neuste Version über den folgenden Link herunterladen: [Download Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) (in englischer Sprache)

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->