<properties 
	pageTitle="Azure Multi-Factor Authentication – Erste Schritte" 
	description="Entscheiden Sie sich für die am besten geeignete Multi-Factor Authentication-Sicherheitslösung, indem Sie sich fragen, was Sie sichern möchten und wo sich Ihre Benutzer befinden. Wählen Sie dann zwischen Cloud, MFA-Server und AD FS aus." 
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
	ms.topic="get-started-article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

#Auswählen der richtigen mehrstufigen Sicherheitslösung

Da es mehrere Arten von Azure Multi-Factor Authentication gibt, müssen Sie einige Fragen klären, um herauszufinden, welche Version für Sie die richtige ist. Fragen:

-	[Was möchte ich sichern?](#what-am-i-trying-to-secure)
-	[Wo befinden sich die Benutzer?](#where-are-the-users-located)

In den folgenden Abschnitten erhalten Sie Informationen, die Ihnen helfen, die Antworten auf diese Fragen zu geben.

## Was möchte ich sichern?

Um die richtige Multi-Factor Authentication-Lösung zu ermitteln, müssen Sie zunächst die Frage beantworten, was Sie über die zweite Methode zur Authentifizierung sichern möchten. Handelt es sich um eine Anwendung in Azure? Oder ist Sie es z. B. ein RAS-System? Die Antwort auf die Frage, wo Multi-Factor Authentication aktiviert werden muss, richtet sich danach, was Sie sichern möchten.



Was möchte ich sichern?| Multi-Factor Authentication in der Cloud|Multi-Factor Authentication-Server 
------------- | :-------------: | :-------------: |
Erstanbieter-Microsoft-Apps|* |* |
SaaS-Apps im Anwendungskatalog|* |* |
Über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen|* |* |
Nicht über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen | |* |
Remotezugriff, z. B. VPN, RDG| |* |



## Wo befinden sich die Benutzer?

Außerdem hängt die Entscheidung für die richtige Lösung – Multi-Factor Authentication in der Cloud oder lokal über den MFA-Server – davon ab, wo sich Ihre Benutzer befinden.



Benutzerstandort| Lösung
------------- | :------------- | 
Azure Active Directory| Multi-Factor Authentication in der Cloud|
Azure AD und lokales AD über einen Verbund mit AD FS| Die verfügbaren Optionen sind MFA in der Cloud und MFA-Server. 
Azure AD und lokales AD mit DirSync, Azure AD Sync, Azure AD Connect ohne Kennwortsynchronisierung|Die verfügbaren Optionen sind MFA in der Cloud und MFA-Server. 
Azure AD und lokales AD mit DirSync, Azure AD Sync, Azure AD Connect mit Kennwortsynchronisierung|Multi-Factor Authentication in der Cloud
Lokales Active Directory|Multi-Factor Authentication-Server

In der folgenden Tabelle finden Sie einen Vergleich der Funktionen von Multi-Factor Authentication in der Cloud und dem Multi-Factor Authentication-Server.

 | Multi-Factor Authentication in der Cloud | Multi-Factor Authentication-Server
------------- | :-------------: | :-------------: |
Benachrichtigung in der mobilen App als zweite Stufe | ● | ● |
Bestätigungscode in der mobilen App als zweite Stufe | ● | ●
Telefonanruf als zweite Stufe | ● | ● 
Unidirektionale SMS als zweite Stufe | ● | ●
Bidirektionale SMS als zweite Stufe | | ● 
Hardwaretoken als zweite Stufe | | ● 
App-Kennwörter für Clients, die MFA nicht unterstützen | ● |  
Administrative Kontrolle über Authentifizierungsmethoden | | ● 
PIN-Modus | | ●
Betrugswarnung | ● | ●
MFA-Berichte | ● | ● 
Einmalumgehung | ● | ● 
Benutzerdefinierte Begrüßungen für Telefonanrufe | ● | ● 
Benutzerdefinierte Anrufer-ID für Telefonanrufe | ● | ● 
Vertrauenswürdige IP-Adressen | ● | ● 
Aussetzen der Multi-Factor Authentication für gespeicherte Geräte (öffentliche Vorschau) | ● |  
Bedingter Zugriff | ● | ● 
Cache | ● | ● 

Sie haben nun ermittelt, ob Sie Multi-Factor Authentication in der Cloud oder den MFA-Server lokal verwenden möchten. Jetzt können Sie mit dem Einrichten und Verwenden von Azure Multi-Factor Authentication beginnen. **Wählen Sie das Symbol aus, das Ihrem Szenario entspricht.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

**Weitere Ressourcen**

* [Für Benutzer](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication bei MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=August15_HO6-->