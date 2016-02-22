<properties
	pageTitle="Suchen nach nicht verwalteten Cloudanwendungen per Cloud App Discovery | Microsoft Azure"
	description="Bietet Informationen zum Suchen und Verwalten von Anwendungen mit Cloud App Discovery, zu den Vorteilen und zu der Funktionsweise."
	services="active-directory"
	keywords="Cloud App Discovery, Verwalten von Anwendungen"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markvi"/>

# Suchen nach nicht verwalteten Cloudanwendungen per Cloud App Discovery

## Übersicht
In modernen Unternehmen sind den IT-Abteilungen häufig nicht alle Cloudanwendungen bekannt, die die Mitarbeiter des Unternehmens für ihre Arbeit verwenden. Es ist verständlich, warum Administratoren sich Sorgen um den unberechtigten Zugriff auf Unternehmensdaten, mögliche Datenlecks und andere Sicherheitsrisiken machen. Dieses fehlende Bewusstsein kann die Erstellung eines Plans für den Umgang mit diesen Sicherheitsrisiken als sehr anspruchsvolle Aufgabe erscheinen lassen.

Cloud App Discovery ist ein Feature von Azure Active Directory (AD) Premium, mit dem Sie Cloudanwendungen ermitteln können, die von den Personen in Ihrer Organisation verwendet werden.

**Cloud App Discovery ermöglicht Ihnen Folgendes:**

- Suchen nach den verwendeten Cloudanwendungen und Messen der Nutzung anhand der Benutzeranzahl, dem Datenverkehrsvolumen oder der Anzahl von Webanforderungen in der Anwendung.
- Identifizieren der Benutzer, die eine Anwendung verwenden.
- Exportieren von Daten für eine Offlineanalyse.
- Unterstellen dieser Anwendungen unter IT-Kontrolle und Aktivieren des einmaligen Anmeldens für die Benutzerverwaltung.

## So funktioniert's
1. Agents für die Anwendungsnutzung werden auf den Computern des Benutzers installiert.
2. Die von den Agents erfassten Daten zur Anwendungsnutzung werden über einen sicheren, verschlüsselten Kanal an den Cloud App Discovery-Dienst gesendet.
3. Der Cloud App Discovery-Dienst wertet die Daten aus und erstellt Berichte.

![Cloud App Discovery-Diagramm](./media/active-directory-cloudappdiscovery/cad01.png)

Informationen zu den ersten Schritten mit Cloud App Discovery finden Sie unter [Erste Schritte mit Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).

## Verwandte Artikel
- [Überlegungen zu Sicherheit und Datenschutz in Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
- [Cloud App Discovery-Gruppenrichtlinie – Bereitstellungshandbuch](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
- [Cloud App Discovery System Center – Bereitstellungshandbuch](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
- [Cloud App Discovery-Registrierungseinstellungen für Proxyserver mit benutzerdefinierten Ports](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
- [Cloud App Discovery – Agent-Änderungsprotokoll](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
- [Cloud App Discovery – Häufig gestellte Fragen](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->