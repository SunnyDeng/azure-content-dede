<properties
	pageTitle="Was ist Azure Active Directory?"
	description="Verwenden Sie Azure Active Directory, um Ihre vorhandenen lokalen Identitäten in die Cloud zu erweitern oder in Azure AD integrierte Anwendungen zu entwickeln."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# Was ist Azure Active Directory?


[Wie funktioniert Azure AD?](active-directory-works.md)<br> [Erste Schritte](active-directory-get-started.md)<br> [Nächste Schritte](active-directory-next-steps.md)<br> [Weitere Informationen](active-directory-learn-map.md)

Azure Active Directory (Azure AD) bietet Ihrem Unternehmen eine einfache Möglichkeit, von Ihren lokal erfolgten Investitionen so zu profitieren, dass Sie in den Genuss der Vorteile von Clouddiensten mit der Flexibilität und Sicherheit kommen, die Active Directory schon immer geboten hat. Es ist eine Cloudplattform, die Probleme im Cloudzeitalter löst, indem Ihre bereits vorhandene Identitätssysteminfrastruktur erweitert wird, ohne dass Sie Daten verschieben müssen. Sie können weiterhin von Ihren bereits erfolgten Investitionen und lokalen Funktionen profitieren, während Sie gleichzeitig Azure AD zum Realisieren der Identitäts- und Zugriffsverwaltung in der Cloud nutzen.

Mit Azure AD können Sie den Zugriff auf Anwendungen und Ressourcen zentral steuern, vorhandene Ressourcen (Clouddienste und lokale Anwendungen) mühelos ergänzen und von Ihnen entwickelte Anwendungen integrieren. Ihre Benutzer können Self-Service-Funktionen und einen Zugriff per einmaligem Anmelden bei allen ihren Anwendungen nutzen, sodass sie nicht wissen müssen, wo die jeweilige App zu finden ist, oder sich ein gesondertes Kennwort merken müssen. Ihr Unternehmen kann in der Cloud mit Geschäftspartnern, Lieferanten und Kunden zusammenarbeiten, die Sie zur Teilnahme an SharePoint-Websites einladen können.

Mit Azure AD können alles zentral verwalten und behalten die Kontrolle über Ihre Cloud- und lokale Umgebung. Es gibt keine Verdopplung des Administrationsaufwands, denn Azure AD setzt darauf auf, was bereits vorhanden ist. [Weitere Informationen](active-directory-aadconnect.md).




> [AZURE.NOTE]Um Azure Active Directory verwenden zu können, benötigen Sie ein Azure-Konto. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](http://azure.microsoft.com/pricing/free-trial/).


## Welche Möglichkeiten bietet Azure Active Directory mir?

Da es sich um einen umfassenden Dienst handelt, bietet Azure AD unterschiedliche Vorteile für die Personen mit verschiedenen Rollen innerhalb einer Organisation.

- Als Entscheidungsträger können mit Sie mit Azure AD eine mobile Belegschaft realisieren und die Leistungsfähigkeit von Cloudanwendungen mit der Gewissheit nutzen, dass die Governance-Anforderungen erfüllt werden.
- Als Dienstanbieter können Sie mit Azure AD problemlos die Identitäts- und Zugriffsanforderungen bewältigen und Ihre Dienste mit den vorhandenen Identitätslösungen Ihrer Kunden verbinden, während Microsoft Azure- und Office 365-Kunden weiter erreicht werden können. Azure AD kann auch alle Anforderungen an den Zugriff auf das Backoffice meistern. Sie können sicher sein, dass die richtigen Personen sowohl intern als auch extern über die benötigten Zugriffsrechte verfügen.
- Wenn Sie IT-Experte sind, nutzen Sie Azure AD, um Vorgänge mit "Cloudgeschwindigkeit" besser und transparenter zu steuern. Dank Azure AD wissen Sie, was Benutzer verwenden und sie durch Self-Service-Angebote leistungsstärker machen. <br> <br>

![][1]

##Neun wichtige Dinge, die Sie über Azure AD wissen sollten

### Active Directory-Kennwörter lassen sich von überall aus verwalten

Mit Azure Active Directory können Sie Benutzern ermöglichen, ihre Active Directory- oder Azure Active Directory-Kennwörter auf einem beliebigen Gerät und an einem beliebigen Ort zu verwalten. Ein Administrator kann Kennwort-und Benachrichtigungsrichtlinien verwalten, sodass ihm bei diesen Vorgängen zur Kennwortzurücksetzung unmittelbar eine detaillierte Überwachungsaktivität angezeigt wird. [Erfahren Sie mehr über die Azure AD-Kennwortverwaltung](http://aka.ms/ssproverview)

### Für den Zugriff auf Cloudressourcen lassen sich Regeln festlegen

Sie können Regeln und Richtlinien festlegen, die steuern, wer unter welchen Bedingungen Zugriff auf Cloudanwendungen und -ressourcen hat. Sie können z. B. die Multi-Factor Authentication (MFA) anfordern und den Zugriff auf Basis des Geräts oder des Speicherorts verwalten. [Weitere Informationen zu Azure MFA](multi-factor-authentication.md).

### Einmaliges Anmelden bei jeder beliebigen App

Azure Active Directory bietet sicheres einmaliges Anmelden bei Cloud- und lokalen Anwendungen, einschließlich Microsoft Office 365 und Tausenden von SaaS-Anwendungen, wie z. B. Salesforce, Workday, DocuSign, ServiceNow und Box. [Erfahren Sie mehr über SaaS-Apps](http://azure.microsoft.com/marketplace/active-directory/).

### Funktioniert mit jedem Gerät

Benutzer können Anwendungen in einem personalisierten webbasierten Zugriffsbereich, mobile Anwendungen, Office 365 oder benutzerdefinierte Unternehmensportale mit ihren vorhandenen Anmeldeinformationen starten und haben auf iOS-, Mac OS X-, Android- und Windows-Geräten stets die gleiche Arbeitsumgebung.

### Festlegen von Regeln für den externen Zugriff

Externe Benutzer können über den integrierten Anwendungsproxy das lokale Netzwerk hinter der Firewall sicher erreichen. Alle festgelegten Regeln und Richtlinien, einschließlich Multi-Factor Authentication, können den Zugriff auf Cloudanwendungen oder lokale Legacyanwendungen über den Anwendungsproxy erzwingen. Dabei ist nicht erforderlich, dass die Anwendungen umgeschrieben oder direkt im Internet verfügbar gemacht werden müssen. [Erfahren Sie mehr über den Azure AD-Anwendungsproxy](https://msdn.microsoft.com/library/azure/dn768219.aspx).

### Überwachen der Zugriffe Ihrer Benutzer

Schließlich gibt Ihnen Azure AD Informationen über die Abläufe in Ihrer Organisation an die Hand. Mithilfe der erweiterten Berichterstellung und durch Analysen erhalten Sie eindeutige Informationen über den Zugriff Ihrer Benutzer. Mithilfe der Anwendungsermittlung können Sie z. B. herausfinden, welche Anwendungen in Ihrer Organisation aktiv verwendet werden. [Erfahren Sie mehr über Azure AD Cloud App Discovery](https://appdiscovery.azure.com/).

### Erweiterte Berichterstellung, Überwachung und Analyse
Azure AD bietet eine Reihe täglicher Nutzungs- und Zugriffsberichte für Administratoren ([Azure AD-Berichte](active-directory-view-access-usage-reports.md)). Über die Reporting-API ([Azure AD Reporting-API](active-directory-reporting-api-getting-started.md)) stehen benutzerdefinierte Berichte und Detailanalysen von Daten zur Verfügung. Weitere Berichte sind in den kostenpflichtigen Editionen von Azure AD verfügbar, einschließlich der Überwachung privilegierter Aktionen ([Azure AD-Überwachung](active-directory-view-access-usage-reports.md)).

### Eine einfache und sichere Umgebung für alle Benutzer

Beispiele: Endbenutzer erhalten eine einfache Benutzeroberfläche. Zudem erhalten sie Profile, Anwendungen und Funktionen zum zentralen Verwalten des Zugriffs auf Ressourcen, ohne spezielle Schulungen zu erfordern. Multi-Factor Authentication, SaaS-Anwendungen, Hybridtools und Self-Service-Funktionen sind einsatzbereit.

- Administratoren haben für die umfassende Verwaltung Zugriff auf das Azure AD-Verwaltungsportal und Windows PowerShell.

- Entwickler verfügen über einen konsistenten Satz von RESTful-APIs und über den einfachen Zugriff auf die Veröffentlichung und Nutzung von Anwendungsschnittstellen.

### Auswählen der Ausführung

Azure AD gibt es in drei Ausführungen:

- Die Edition Free bietet lediglich einen Cloudverzeichnisdienst.
- Die Edition Basic bietet einen Cloudverzeichnisdienst und Zugriff auf SaaS-Apps.
- Die Edition Premium bietet eine umfassende, regelgesteuerte im Self-Service verwaltete Verzeichnisdienstlösung.

Um sich die hier beschriebenen Features einmal anzusehen, aktivieren Sie Ihre [kostenlose Azure-Testversion](http://azure.microsoft.com/trial/get-started-active-directory/).

[Erfahren Sie mehr über die Azure AD-Editionen](active-directory-editions.md)


## Zusätzliche Ressourcen

* [Als Organisation für Azure registrieren](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58-->