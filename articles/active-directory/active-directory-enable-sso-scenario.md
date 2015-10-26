<properties 
    pageTitle="Kann ich Azure AD verwenden, um einmaliges Anmelden (SSO) für meine gesamten Anwendungen zu aktivieren | Microsoft Azure" 
    description="Erfahren Sie, wie Sie mit Azure Active Directory den Geltungsbereich einer Identität und deren Verwaltung erweitern können." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# Kann ich Azure AD verwenden, um einmaliges Anmelden (SSO) für meine gesamten Anwendungen zu aktivieren
Dieser Artikel richtet sich an IT-Entscheidungsträger, die sich über den geschäftlichen Nutzen informieren möchten, den Azure Active Directory für Anwendungen der Organisation bietet.

## Ausführen von Apps in einer cloudbasierten Umgebung  

In der heutigen cloudbasierten Zeit finden Sie Tausende von Apps für jede Aufgabe. IT-Abteilungen kennen häufig nicht einmal die SaaS-Apps, die in ihren Organisationen verwendet werden. In vielen Fällen ist die Verwendung der App bekannt, aber die Einrichtung von Governance und Sicherheit ist teuer und zeitaufwändig. Viele der Anwendungen unterstützen die Integration in Identitätssysteme der Unternehmen nicht. In anderen Fällen werden einmalige Integrationen aufgrund der Kosten und der Komplexität vermieden.

Daraus ergibt sich:

- Viele Organisationen haben Bedenken in Bezug auf nicht autorisierten Zugriff auf Unternehmensdaten, mögliche Datenverluste und andere Sicherheitsrisiken der Anwendung, die durch die Einführung nicht verwalteter Anwendungen entstehen. Da sie nicht wissen, welche und wie viele Apps verwendet werden, scheint bereits die Erstellung eines Plans zum Umgang mit diesen Risiken eine schier unlösbare Aufgabe.
- Administratoren müssen alle IAM-Dienstanbieter einzeln verwalten, die von den verwalteten Apps in Ihre Umgebung eingeführt werden. Dies umfasst Aufgaben wie das Erstellen und Löschen von Benutzern und Gruppen sowie das Gewähren oder Aufheben von Zugriff auf diese Apps.
- Die Benutzer müssen sich für den Zugriff auf die Apps, mit denen sie arbeiten müssen, verschiedene Anmeldeinformationen merken. Vergessene Kennwörter haben für viele Organisationen einen großen Einfluss auf die Betriebskosten. Gemeinsam ist all diesen Problemen, dass sie sich negativ auf die Produktivität Ihrer Benutzer und die Betriebskosten auswirken.  
 
## Wie hilft Azure Active Directory?
Azure Active Directory hilft Ihnen, diese Probleme zu beheben, indem Sie einfach die vorhandene Identitätsinfrastruktur auf die Cloud erweitern können. Dadurch erreichen Sie Folgendes:

- Erweitern der Reichweite Ihrer mobilen Benutzer für Apps 
- Erweitern der Reichweite der Zugriffsverwaltung für Cloud-Apps 
- Erkennen der Apps, auf die Ihre Benutzer zugreifen


### Erweitern der Reichweite Ihrer Identität für Apps 

Standardmäßig bietet Azure Active Directory SSO für Cloud-Apps, die in Azure und anderen Microsoft-Onlinediensten wie Office 365, CRM Online und Intune gehostet werden.

Darüber hinaus kann Azure Active Directory der Identitätsbroker für einmaliges Anmelden bei Apps in anderen öffentlichen Clouds wie Google, Amazon, IBM und anderen sein. <br> Wenn eine App allgemein bekannt oder beliebt ist, wird sie vorab in den App-Katalog von Azure Active Directory integriert. Heute finden Sie etwa 2500 vorab integrierte Apps im App-Katalog, und die Anzahl wächst kontinuierlich. <br> Das Konfigurieren von SSO für eine der vorab integrierten Apps im App-Katalog ist mit wenigen Klicks möglich. Was geschieht, wenn meine App noch nicht im App-Katalog aufgeführt ist? Alle öffentlichen Apps können mithilfe eines Assistenten in Azure Active Directory integriert werden, und auf diese Weise kann SSO für sie aktiviert werden. Weitere Informationen finden Sie unter [Bereitstellen des einmaligen Anmeldens mithilfe von Azure Active Directory für neu erworbenen SaaS-Apps](active-directory-single-sign-on-newly-acquired-saas-apps.md) und [Integrieren von Azure Active Directory-SSO in vorhandenen Apps](active-directory-sso-integrate-existing-apps.md).<br> Welche Apps werden von Ihrer Organisation entwickelt? Wir bieten Ihnen die entsprechende Dokumentation, damit die Entwickler die von Ihrer Organisation entwickelten Apps einfach in Azure Active Directory integrieren können. Weitere Informationen finden Sie unter [Azure AD und Anwendungen: Leitfaden für Entwickler](active-directory-applications-guiding-developers-for-lob-applications.md).

Um Ihnen Unterstützung für vorab integrierte Apps, öffentliche Apps, die nicht im App-Katalog enthalten sind, und die Apps, die von Ihrer Organisation entwickelt wurden, zu bieten, kann mit Azure Active Directory SSO für alle Cloud-Apps bereitgestellt werden.

Der Nutzen von Azure Active Directory geht über Cloud-Apps hinaus. Mit Azure Active Directory können Sie die Reichweite von Azure-Identitäten erweitern, indem Sie über SSO sicheren Remotezugriff auf lokale Apps bereitstellen. Dies bedeutet, dass für Remotezugriff keine Technologien wie VPNs oder andere herkömmliche Remotezugriffsinfrastrukturen erforderlich sind, um auf lokale Web-Apps zuzugreifen. Azure Active Directory bietet diese Funktionalität über App-Proxyfunktionen.

Das herkömmliche SSO-Modell basiert auf einer Zuordnung von zwei einzelnen Konten in zwei Identitätsrepositorys. Mit Azure Active Directory können Sie auch ein einzelnes Konto einem freigegebenen Unternehmenskonto wie dem Twitter-Konto Ihres Unternehmens zuordnen. Durch das Implementieren von SSO für Ihre freigegebenen Unternehmenskonten müssen die Kontoanmeldeinformationen nicht tatsächlich für Ihre Benutzer freigegeben werden, was den Schutz dieser Konten deutlich verbessert. Weitere Informationen dazu finden Sie unter [Freigeben von Konten in Azure AD](active-directory-sharing-accounts.md).

Durch die Erweiterung der Reichweite Ihrer Identitäten erhalten Sie mit einem Kennwort Zugriff auf Tausende Apps.



### Erweitern der Reichweite der Zugriffsverwaltung für Cloud-Apps

Das Verwalten des Zugriffs auf Ihre Cloud-Apps ist teuer, wenn Sie es manuell App für App durchführen müssen. Mit Azure Active Directory können Sie den Zugriff auf Ihre Cloud-Apps basierend auf Gruppen über eine zentrale Stelle, dem Azure-Portal, verwalten. Sie können einzelnen Benutzern oder sogar Gruppen Zugriff gewähren. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Apps](active-directory-managing-access-to-apps.md).

Einige Apps wie Salesforce, Box, Google Apps und Concur bieten Automatisierungsschnittstellen für die Erstellung und Entfernung (oder Deaktivierung) von Konten. Wenn ein Anbieter eine solche Schnittstelle anbietet, wird sie von Azure Active Directory genutzt. Anders ausgedrückt, unterstützt Azure Active Directory die automatisierte Benutzerbereitstellung für Apps, die eine entsprechende Automatisierungsschnittstelle bieten.

Mit der automatisierten Benutzerbereitstellung:

- Wenn einem Benutzer Zugriff auf eine zugehörige App gewährt wurde, wird von Azure Active Directory automatisch der App das erforderliche SSO-Konto bereitgestellt.
- Wenn das Konto eines Benutzers in Azure Active Directory gelöscht wird, wird das Konto in der App entweder deaktiviert oder gelöscht. Das Konfigurieren der automatisierten Benutzerbereitstellung bietet Ihnen die folgenden Vorteile:
- 	Es reduziert die Betriebskosten, da administrative Aufgaben automatisiert werden, die andernfalls von Ihrem IT-Personal durchgeführt werden müssten.
- Es verbessert die Sicherheit Ihrer Umgebung, da das Risiko verringert wird, dass nicht erforderlicher Zugriff auf Apps möglich ist.

Weitere Informationen zur automatisierten Benutzerbereitstellung finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](active-directory-saas-app-provisioning.md).


### Erkennen der Apps, auf die Ihre Benutzer zugreifen

Mit SSO und Kontobereitstellung bietet Azure Active Directory leistungsfähige Mechanismen, um zu verbessern, wie Ihre Benutzer und Administratoren mit den Ihnen bekannten Apps in Ihrer Umgebung arbeiten können. In modernen Unternehmen sind jedoch den IT-Abteilungen häufig nicht alle verwendeten Cloud-Apps. Mit Cloud App Discovery bietet Azure Active Directory auch eine Lösung zum Erkennen dieser Apps. Cloud App Discovery ermöglicht Ihnen Folgendes:

- Ermitteln von verwendeten Apps und Messen der Nutzung anhand der Benutzeranzahl, dem Datenverkehrsvolumen oder der Anzahl von Webanforderungen in der App.
- Identifizieren der Benutzer, die eine App verwenden.
- Exportieren der Daten zur zusätzlichen Offlineanalyse.
- Priorisieren von Apps, die der Kontrolle durch die IT-Abteilung unterliegen sollen, und einfaches Integrieren von Apps, um das einmalige Anmelden und die Benutzerverwaltung zu ermöglichen.

Weitere Informationen zu Cloud App Discovery finden Sie unter [Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?](active-directory-cloudappdiscovery-whatis.md)


## Wie fange ich an?

Wenn Sie Azure AD noch nicht nutzen und ein IT-Administrator sind:

- [Probieren Sie es aus!](https://azure.microsoft.com/trial/get-started-active-directory) – Registrieren Sie sich noch heute über diesen Link für eine kostenlose 30-Tage-Testversion, und stellen Sie Ihre erste Cloudlösung in weniger als 5 Minuten bereit.

<!---HONumber=Oct15_HO3-->