<properties 
	pageTitle="Erste Schritte mit Azure Multi-Factor Authentication und Active Directory-Verbunddiensten" 
	description="Auf dieser Seite zur Azure Multi-Factor Authentication werden die ersten Schritte mit Azure MFA und AD FS beschrieben." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Erste Schritte mit Azure Multi-Factor Authentication und Active Directory-Verbunddiensten



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Wenn Ihre Organisation über einen Verbund Ihres lokalen Active Directory mit Azure Active Directory über AD FS verfügt, sind die beiden folgenden Optionen für die Verwendung von Multi-Factor Authentication verfügbar.

- Sichern von Cloud-Ressourcen mit Azure Multi-Factor Authentication oder Active Directory-Verbunddiensten 
- Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server 

In der folgenden Tabelle sind die Authentifizierungsverfahren beim Sichern von Ressourcen mit Azure Multi-Factor Authentication und AD FS zusammengefasst.

|Authentifizierungsvorgang bei Browser-Apps|Authentifizierungsvorgang bei Nicht-Browser-Apps
:------------- | :------------- | :------------- |
Sichern von Azure AD-Ressourcen mit Azure Multi-Factor Authentication |<li>Die erste Authentifizierungsstufe wird lokal über AD FS ausgeführt.</li> <li>Die zweite Stufe ist eine telefonbasierte Methode mithilfe der Cloud-Authentifizierung.</li>|Endbenutzer können sich mit App-Kennwörtern anmelden.
Sichern von Azure AD-Ressourcen mit Active Directory-Verbunddiensten |<li>Die erste Stufe der Authentifizierung wird lokal mithilfe von AD FS durchgeführt.</li><li>Die zweite Stufe wird lokal unter Berücksichtigung des Anspruchs ausgeführt.</li>|Endbenutzer können sich mit App-Kennwörtern anmelden.

Sicherheitshinweise zu App-Kennwörtern für Verbundbenutzer:

- Das App-Kennwort wird über die Cloud-Authentifizierung überprüft, daher werden Verbunde umgangen. Der Verbund wird nur beim Einrichten des App-Kennworts aktiv verwendet.
- Lokale Einstellungen für die Clientzugriffssteuerung werden vom App-Kennwort nicht berücksichtigt.
- Wenn Sie App-Kennwörter verwenden, geht die Möglichkeit zum lokalen Protokollieren der Authentifizierung verloren.
- Das Deaktivieren oder Löschen von Konten in DirSync kann bis zu drei Stunden dauern, sodass das Deaktivieren bzw. Löschen des App-Kennworts in der Cloud-Identität verzögert wird.

Informationen zum Einrichten von Azure Multi-Factor Authentication oder Azure Multi-Factor Authentication-Server mit AD FS finden Sie hier:

- [Sichern von Cloudressourcen mit Azure Multi-Factor Authentication und AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Sichern von Cloud- und lokalen Ressourcen mithilfe von Azure Multi-Factor Authentication-Server mit AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=August15_HO6-->