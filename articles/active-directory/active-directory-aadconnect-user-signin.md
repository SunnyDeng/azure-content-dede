<properties
	pageTitle="Azure AD Connect – Benutzeranmeldung | Microsoft Azure"
	description="Azure AD Connect-Benutzeranmeldung für benutzerdefinierte Einstellungen."
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



# Azure AD Connect-Optionen für die Benutzeranmeldung

Mit Azure AD Connect können sich Ihre Benutzer sowohl bei Cloud- als auch bei lokalen Ressourcen mit denselben Kennwörtern anmelden. Es gibt mehrere Möglichkeiten, diese Funktion zu aktivieren.


### Kennwortsynchronisierung
Mit der Kennwortsynchronisierung werden Benutzerkennworthashes aus Ihrem lokalen Active Directory mit Azure AD synchronisiert. Werden Kennwörter geändert oder lokal zurückgesetzt, werden die neuen Kennwörter sofort mit Azure AD synchronisiert, damit Ihre Benutzer für Cloudressourcen dasselbe Kennwort verwenden können wie in der lokalen Umgebung. Die Kennwörter werden weder an Azure AD übermittelt noch als Klartext in Azure AD gespeichert. Die Kennwortsynchronisierung kann mit der Kennwortrückschreibung kombiniert werden, um es den Benutzern zu ermöglichen, ihre Kennwörter in Azure AD selbst zurückzusetzen.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Weitere Informationen zur Kennwortsynchronisierung](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Verbund mit einem neuen oder vorhandenen AD FS in einer Windows Server 2012 R2-Farm
Mit der Verbundanmeldung können sich Ihre Benutzer bei Azure AD-basierten Dienste mit ihren lokalen Kennwörtern anmelden. Während sie in ihrem Unternehmensnetzwerk angemeldet sind, können sie auf Cloudressourcen zugreifen, ohne ihre Kennwörter erneut eingeben zu müssen. Mit der Verbundoption für AD FS können Sie eine neue Windows Server 2012 R2-Farm bereitstellen oder eine Farm angeben. Wenn Sie eine vorhandene Farm angeben, konfiguriert Azure AD Connect die Vertrauensstellung zwischen der Farm und Azure AD, sodass sich Ihre Benutzer anmelden können.

<center>! [Cloud] (. / media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Voraussetzungen für den Verbund mit AD FS unter Windows Server 2012 R2
Wenn Sie eine neue Farm bereitstellen:

- Ein Windows Server 2012 R2-Server für den Verbundserver.
- Ein Windows Server 2012 R2-Server für den Webanwendungsproxy.
- Eine PFX-Datei mit einem SSL-Zertifikat für den vorgesehenen Verbunddienstnamen, z. B. fs.contoso.com.

Wenn Sie eine neue Farm bereitstellen oder eine vorhandene Farm verwenden:

- Lokale Administratorrechte auf Ihren Verbundservern.
- Lokale Administratorrechte für jeden Arbeitsgruppenserver (keiner Domäne angehörend), auf dem Sie die Webanwendungsproxy-Rolle bereitstellen möchten.
- Der Computer, auf dem Sie den Assistenten ausführen, muss eine Verbindung mit allen weiteren Computern herstellen können, auf denen Sie AD FS oder den Webanwendungsproxy über die Windows-Remoteverwaltung installieren möchten.

#### Anmelden mit einer früheren Version von AD FS oder einer Drittanbieterlösung
Wenn Sie die Cloudanmeldung bereits mit einer früheren Version von AD FS (z. B. AD FS 2.0) oder einer Drittanbieterlösung konfiguriert haben, können Sie die Konfiguration der Benutzeranmeldung über Azure AD Connect überspringen. Auf diese Weise profitieren Sie von den neuesten Synchronisierungsfunktionen und anderen Features Azure AD Connect und können gleichzeitig Ihre vorhandene Lösung für die Anmeldung nutzen.

### Auswählen einer Benutzeranmeldemethode für Ihre Organisation
Für die meisten Organisationen, die lediglich die Benutzeranmeldung für Office 365, SaaS-Anwendungen und andere Azure AD-basierte Ressourcen aktivieren möchten, empfiehlt sich Standardoption für die Kennwortsynchronisierung. Einige Organisationen benötigen jedoch eine Option zur Verbundanmeldung, z. B. AD FS. Diese umfassen:

- Ihre Organisation hat bereits AD FS oder eine Verbundlösung eines Drittanbieters bereitgestellt.
- Ihre Sicherheitsrichtlinie unterbindet das Synchronisieren von Kennworthashes mit der Cloud.
- Ihren Benutzern soll es ermöglicht werden, per SSO bequem (und ohne zusätzliche Kennwortabfrage) über in der Domäne enthaltene Computer im Unternehmensnetzwerk auf Cloudressourcen zuzugreifen.
- Sie benötigen einige spezifische Funktionen, die AD FS bietet.
	- Lokale mehrstufige Authentifizierung mit einer Drittanbieterlösung oder mit Smartcards (Informationen über Drittanbieterlösungen für die mehrstufige Authentifizierung für AD FS unter Windows Server 2012 R2)
	- Funktionen für die Active Directory-Integration, z. B. die vorläufige Kontosperrung oder die AD-Kennwort- und Arbeitsstundenrichtlinie
	- Bedingte Zugriff auf lokale und Cloudressourcen mithilfe der Geräteregistrierung, Azure AD-Einbindung oder Intune-MDM-Richtlinien

<!---HONumber=Oct15_HO3-->