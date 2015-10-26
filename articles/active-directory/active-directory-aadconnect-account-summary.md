<properties
	pageTitle="Weitere Informationen zu den Anmeldeinformationen und Berechtigungen von Azure AD Connect | Microsoft Azure"
	description="Beschreibung der benutzerdefinierten Einstellungen von Azure AD Connect-Anmeldeinformationen und -Berechtigungen."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>



# Weitere Informationen zu den Anmeldeinformationen und Berechtigungen von Azure AD Connect


Der Azure AD Connect-Assistent bietet zwei unterschiedliche Pfade mit verschiedenen Berechtigungsanforderungen:

* Für die Express-Einstellungen benötigen Sie mehr Berechtigungen, um die Konfiguration einfach einrichten zu können, ohne Benutzern erstellen oder Berechtigungen einzeln konfigurieren zu müssen.

* Für die benutzerdefinierten Einstellungen stehen mehr Auswahlmöglichkeiten und Optionen zur Verfügung. Es gibt aber einige Situationen, in denen Sie sicherstellen müssen, dass Sie selbst über die richtigen Berechtigungen verfügen.


## Erfasste Anmeldeinformationen und deren Verwendungszweck bei einem Express-Setup

Seite des Assistenten | Erfasste Anmeldeinformationen | Erforderliche Berechtigungen| Verwendung
------------- | ------------- |------------- |------------- |
Herstellen einer Verbindung mit Azure AD| Azure AD-Verzeichnisanmeldeinformationen | Globale Administratorrolle in Azure AD | <li>Aktivieren der Synchronisierung im Azure AD-Verzeichnis.</li> <li>Erstellen des Azure AD-Kontos, das für die fortlaufenden Synchronisierungsvorgänge in Azure AD verwendet wird.</li>
Herstellen einer Verbindung mit AD DS | Lokale Active Directory-Anmeldeinformationen | Mitglied der Gruppe "Unternehmensadministratoren" in Active Directory| Wird als lokales AD-Connector-Konto verwendet. Das bedeutet, dass es zur Synchronisierung für Lese- und Schreibvorgänge für Verzeichnisinformationen verwendet wird.
–|Anmeldeinformationen des Benutzers, der den Assistenten ausführt| Administrator des lokalen Servers|Der Assistent erstellt das AD-Konto, das als Anmeldekonto für den Synchronisierungsdienst auf dem lokalen Computer dient.

<br> <br>


## Erfasste Anmeldeinformationen und deren Verwendungszweck bei einem benutzerdefinierten Setup


Seite des Assistenten | Erfasste Anmeldeinformationen | Erforderliche Berechtigungen| Verwendung
------------- | ------------- |------------- |-------------
Nicht verfügbar|Anmeldeinformationen des Benutzers, der den Assistenten ausführt|Administrator des lokalen Servers| <li>Standardmäßig erstellt der Assistent das Active Directory-Konto, das als Anmeldekonto für den Synchronisierungsdienst dient, auf dem lokalen Computer.</li><li>Sie erstellen das Anmeldekonto für den Synchronisierungsdienst nur dann, wenn der Administrator kein bestimmtes Konto angibt.</li> <li>Bei dem Konto handelt es sich um ein lokales Benutzerkonto, es sei denn, es befindet sich auf einem DC. In diesem Fall handelt es sich um Domänenbenutzerkonto.</li>
Seite "Synchronisierungsdienste installieren", Option "Dienstkonto" | Anmeldeinformationen für Active Directory- oder lokale Konten | Lokaler Benutzer|Wenn der Administrator ein Konto angibt, wird dieses Konto als Anmeldekonto für den Synchronisierungsdienst verwendet.
Herstellen einer Verbindung mit Azure AD|Azure AD-Verzeichnisanmeldeinformationen| Globale Administratorrolle in Azure AD|Der Assistent erstellt das AD-Konto, das als Anmeldekonto für den Synchronisierungsdienst auf dem lokalen Computer dient.
Verzeichnisse verbinden|Lokale Active Directory-Anmeldeinformationen für jede Gesamtstruktur, die mit Azure AD verbunden wird. |<li>Die vom Assistenten geforderte Mindestberechtigung ist "Domänenbenutzer".</li> <li>Jedoch muss das angegebene Konto die erforderlichen Berechtigungen für das gewünschte Szenario aufweisen.</li><li>Wenn Sie beabsichtigen, die Kennwortsynchronisierung zu Azure AD zu konfigurieren, stellen Sie sicher, dass dieses Konto über die folgenden Berechtigungen verfügt: – Verzeichnisänderungen replizieren – Alle Verzeichnisänderungen replizieren</li> <li>Wenn Sie beabsichtigen, die Synchronisierung so zu konfigurieren, dass Informationen von Azure AD in Ihr lokales AD "zurückgeschrieben" werden, stellen Sie sicher, dass das Konto über Schreibberechtigungen für Verzeichnisobjekte und -Attribute verfügt, die zurückgeschrieben werden sollen.</li> <li>Wenn Sie beabsichtigen, AD FS für die Anmeldung zu konfigurieren, stellen Sie sicher, dass das Konto mit den AD-Anmeldeinformationen, die Sie für die Gesamtstruktur bereitstellen, in der sich der AD FS-Server befindet, über Domänenadministratorberechtigungen verfügt.</li><li>In der folgenden Tabelle ist eine Liste mit zusätzlichen Anforderungen für Ihr Szenario aufgeführt.</li>|<li>Dies ist das Konto, das für das lokale Konto des Active Directory Verwaltungs-Agent-Kontos (AD MA-Konto, AD Management Agent-Konto) verwendet wird. Es wird zum Lesen und Schreiben der Objekte und Attribute im lokalen AD für den fortlaufenden Synchronisierungsvorgang verwendet.</li>
AD FS-Server|Für jeden Server in der Liste sammelt der Assistent Anmeldeinformationen, wenn die Anmeldeinformationen des Benutzers, der den Assistenten ausführt, nicht für die Verbindung ausreichen.|Domänenadministrator|Installieren und Konfigurieren der AD FS-Server-Rolle.|
Webanwendungsproxy-Server |Für jeden Server in der Liste sammelt der Assistent Anmeldeinformationen, wenn die Anmeldeinformationen des Benutzers, der den Assistenten ausführt, nicht für die Verbindung ausreichen.|Lokaler Administrator auf dem Zielcomputer.|Installieren und Konfigurieren der WAP-Server-Rolle.
Anmeldeinformationen der Proxyvertrauensstellung |Anmeldeinformationen der Verbunddienstvertrauensstellung (die Anmeldeinformationen, die der Proxy zur Registrierung für ein Zertifikat für die Vertrauensstellung vom FS verwendet) |Domänenkonto, dessen Benutzer ein lokaler Administrator des AD FS-Servers ist|Anfängliche Registrierung des vertrauenswürdigen FS-WAP-Zertifikats
Seite "AD FS-Dienstkonto", Option "Domänenbenutzerkonto verwenden"|Anmeldeinformationen für das Active Directory-Benutzerkonto|Domänenbenutzer|Das AD-Benutzerkonto, dessen Anmeldeinformationen bereitgestellt wurden, wird als das Anmeldekonto des AD FS-Diensts verwendet.



<br> <br>
## Für ein bestimmtes Szenario erforderliche Berechtigungen

Szenario |Berechtigung
------------- | ------------- |
Kennwortsynchronisierung| <li>Verzeichnisänderungen replizieren</li> <li>Alle Verzeichnisänderungen replizieren</li>
Exchange-Hybridbereitstellung|Siehe [Office 365 Exchange Hybrid AAD Sync write-back attributes and permissions](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange) (in englischer Sprache).
Kennwortrückschreiben | <li>Kennwort ändern</li><li>Kennwort zurücksetzen</li>
Benutzer-, Gruppen- und Geräterückschreiben|Schreibberechtigungen für die Active Directory-Objekte und -Attribute, die Sie "zurückschreiben" möchten.
Einmaliges Anmelden und AD FS| Domänenadministratorberechtigungen in der Domäne, in der sich die Verbundserver befinden.
SQL Server-Berechtigungen|Das Azure AD Connect Sync-Dienstkonto erfordert Dbo-Berechtigungen in SQL. Diese Berechtigungen werden während der Installation von Azure AD Connect festgelegt. Änderungen werden nicht unterstützt.

<br> <br>
## Zusammenfassung der Konten, die von Azure AD Connect erstellt werden



Erstelltes Konto |Zugewiesene Berechtigungen | Verwendung
------------- | ------------- |------------- |
Azure AD-Konto für die Synchronisierung|Dedizierte Rolle zur Verzeichnissynchronisierung|Fortlaufende Synchronisierungsvorgänge (Azure AD MA-Konto)
Express-Einstellungen: AD-Konto, das für die Synchronisierung verwendet wird|Lese-/Schreibberechtigungen für das Verzeichnis, die für die Synchronisierung und Kennwortsynchronisierung erforderlich sind|Fortlaufende Synchronisierungsvorgänge (Azure AD MA-Konto)
Express-Einstellungen: Anmeldekonto für den Synchronisierungsdienst | Anmeldeinformationen des Benutzers, der den Assistenten ausführt|Anmeldekonto für den Synchronisierungsdienst
Benutzerdefinierte Einstellungen: Anmeldekonto für den Synchronisierungsdienst |Nicht verfügbar|Anmeldekonto für den Synchronisierungsdienst
AD FS: GMSA-Konto (aadcsvc$)|Domänenbenutzer|Anmeldekonto für den AD FS-Dienst


**Zusätzliche Ressourcen**



* [Berechtigungen für die Kennwortsynchronisierung](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Berechtigungen für Exchange-Hybrid](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Berechtigungen für das Kennwortrückschreiben](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect auf MSDN](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->