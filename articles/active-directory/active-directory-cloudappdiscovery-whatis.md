<properties 
	pageTitle="Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?" 
	description="Dieses Thema beschreibt den Cloud App Discovery-Dienst und Gründe für die Nutzung des Diensts." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?

In modernen Unternehmen sind den IT-Abteilungen häufig nicht alle Cloudanwendungen bekannt, die die Unternehmensbenutzer verwenden, um ihre Arbeit zu erledigen. Aus diesem Grund machen sich Administratoren häufig Sorgen in Bezug auf nicht autorisierte Zugriffe auf Unternehmensdaten, mögliche Datenlecks und andere Sicherheitsrisiken, die durch solche Anwendungen entstehen können. Da sie nicht wissen, welche und wie viele Apps verwendet werden, scheint bereits die Erstellung eines Plans zum Umgang mit diesen Risiken eine schier unlösbare Aufgabe.

Mit Cloud App Discovery können Sie dieses Problem beheben. Cloud App Discovery ist ein Premium-Feature von Azure Active Directory, mit dem Sie Cloudanwendungen ermitteln können, die von den Mitarbeitern in Ihrer Organisation verwendet werden.


**Cloud App Discovery ermöglicht Ihnen Folgendes:**

* Ermitteln von verwendeten Anwendungen und Messen der Nutzung anhand der Benutzeranzahl, dem Datenverkehrsvolumen oder der Anzahl von Webanforderungen in der Anwendung. 
* Identifizieren der Benutzer, die eine Anwendung verwenden. 
* Exportieren der Daten zur zusätzlichen Offlineanalyse. 
* Priorisieren von Anwendungen, die der Kontrolle durch die IT-Abteilung unterliegen sollen, und einfaches Integrieren von Anwendungen, um das einmalige Anmelden und die Benutzerverwaltung zu ermöglichen. 

Bei Cloud App Discovery erfolgt der Datenabruf über Agents, die auf Computern in Ihrer Umgebung installiert sind. Die von den Agents erfassten App-Nutzungsdaten werden über einen sicheren, verschlüsselten Kanal an den Cloud App Discovery-Dienst gesendet. Der Cloud App Discovery-Dienst wertet die Daten aus und generiert Berichte, die Sie zum Analysieren Ihrer Umgebung verwenden können.


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##Nächste Schritte


* Weitere Informationen zur Funktionsweise von Cloud App Discovery finden Sie unter [Erste Schritte mit Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) 
* Nähere Details zu Sicherheit und Datenschutz finden Sie unter [Überlegungen zu Sicherheit und Datenschutz in Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) 
* Informationen zur Bereitstellung des Cloud App Discovery-Agents in einer Unternehmensumgebung mit folgenden Komponenten: 
 * Active Directory-Gruppenrichtlinienverwaltung, siehe [Cloud App Discovery-Gruppenrichtlinien – Bereitstellungsleitfaden](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) 
 * Microsoft System Center Configuration Manager, siehe [Cloud App Discovery mit System Center – Bereitstellungshandbuch](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) 
 * Proxyserver mit benutzerdefinierten Ports, siehe [Cloud App Discovery-Registrierungseinstellungen für Proxyserver mit benutzerdefinierten Ports](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) 





**Weitere Ressourcen**


* [Cloud App Discovery – Agent-Änderungsprotokoll](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery – häufig gestellte Fragen](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=Oct15_HO3-->