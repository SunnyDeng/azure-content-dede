<properties 
	pageTitle="Azure AD Connect – Hinweise zur Windows-Remoteverwaltung" 
	description="Azure AD Connect – Hinweise zur Windows-Remoteverwaltung für die Verwendung mit AD FS." 
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

# Azure AD Connect – Hinweise zur Windows-Remoteverwaltung


Überprüfen Sie bei Verwendung von Azure AD Connect für die Bereitstellung von Active Directory-Verbunddiensten oder des Webanwendungsproxys die unten aufgeführten Anforderungen und Hinweise, um die Konnektivität und eine erfolgreiche Konfiguration sicherzustellen:

- Wenn der Zielserver mit der Domäne verknüpft ist, stellen Sie sicher, dass die Windows-Remoteverwaltung aktiviert ist. 
	* Öffnen Sie ein PSH-Befehlsfenster mit erhöhten Rechten, und verwenden Sie den Befehl "Enable-PSRemoting –force". 

- Wenn der Zielserver kein mit der Domäne verknüpfter WAP-Computer ist, gibt es einige zusätzliche Anforderungen.
	- Auf dem Zielcomputer (WAP-Computer): 

- Stellen Sie sicher, dass der WinRM-Dienst (Windows-Remoteverwaltung/WS-Verwaltung) über das Dienste-Snap-In ausgeführt wird.

- Öffnen Sie ein PSH-Befehlsfenster mit erhöhten Rechten, und verwenden Sie den Befehl "Enable-PSRemoting –force".
	- Auf dem Computer, auf dem der Assistent ausgeführt wird (wenn der Zielcomputer nicht der Domäne beigetreten ist oder sich in einer nicht vertrauenswürdigen Domäne befindet): 

- Öffnen Sie ein PSH-Befehlsfenster mit erhöhten Rechten, und verwenden Sie den Befehl "Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate".
	- In Server Manager:
		- Fügen Sie den DMZ-WAP-Host zum Computerpool hinzu (Server-Manager -> "Verwalten" -> "Server hinzufügen"-> Registerkarte "DNS verwenden"). 
		- Server-Manager, Registerkarte "Alle Server": Klicken Sie mit der rechten Maustaste auf "WAP-Server", und wählen Sie "Verwalten als" aus. Geben Sie dann die Anmeldeinformationen für den lokalen WAP-Computer ein (nicht für die Domäne). 
		- Um die PSH-Remotekonnektivität zu überprüfen, klicken Sie auf der Registerkarte "Alle Server" mit der rechten Maustaste auf "WAP-Server", und wählen Sie "Windows PowerShell" aus. Daraufhin sollte sich eine PSH-Remotesitzung öffnen, um sicherzustellen, dass PowerShell-Remotesitzungen hergestellt werden können. 

**Zusätzliche Ressourcen**


* [Weitere Informationen zu Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-account-summary.md)
* [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->