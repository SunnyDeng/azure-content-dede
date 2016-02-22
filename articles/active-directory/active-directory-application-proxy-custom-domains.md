<properties
	pageTitle="Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy | Microsoft Azure"
	description="In diesem Artikel wird erläutert, wie Sie mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy arbeiten."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy
> [AZURE.NOTE] Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Durch das Verwenden von Standarddomänen können Sie dieselbe URL als interne und externe URL für den Zugriff auf eine Anwendung festlegen, sodass Ihre Benutzer sich nur eine URL für den Zugriff merken müssen – unabhängig davon, von wo sie auf die Anwendung zugreifen. Außerdem können Sie im Zugriffsbereich eine zentrale Verknüpfung für die Anwendung erstellen. Wenn Sie die von Azure AD-Anwendungsproxy bereitgestellte Standarddomäne verwenden, ist keine weitere Konfiguration zum Aktivieren Ihrer Domäne erforderlich. Falls Sie eine benutzerdefinierte Domäne einsetzen, müssen Sie verschiedene Punkte berücksichtigen, damit der Anwendungsproxy Ihre Domäne erkennt und die zugehörigen Zertifikate überprüft.

## Auswählen Ihrer benutzerdefinierten Domäne

1. Veröffentlichen Sie Ihre Anwendung, indem Sie die Anweisungen im Abschnitt [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md) befolgen.
2. Sobald die Anwendung in der Liste der Anwendungen angezeigt wird, wählen Sie sie aus, und klicken Sie auf **Konfigurieren**.
3. Geben Sie unter **Externe URL** Ihre benutzerdefinierte Domäne ein.
4. Wenn Sie eine externe HTTPS-URL verwenden, werden Sie aufgefordert, ein Zertifikat hochzuladen, damit Azure die Gültigkeit der Anwendungs-URL überprüfen kann. Sie haben auch die Möglichkeit, ein Platzhalterzertifikat hochzuladen, das der externen URL der Anwendung entspricht. Diese Domäne muss in der Liste der [von Azure verifizierten Domänen](https://msdn.microsoft.com/library/azure/jj151788.aspx) enthalten sein. Azure muss über ein Zertifikat für die Domänen-URL der Anwendung oder ein Platzhalterzertifikat verfügen, das der externen URL der Anwendung entspricht.
5. Stellen Sie sicher, dass ein DNS-Eintrag hinzugefügt wird, der die interne URL an die Anwendung weiterleitet. Auf diese Weise können Sie dieselbe URL für den internen und den externen Zugriff und eine einzige Verknüpfung mit der Anwendung in der Liste der Benutzeranwendungen verwenden.

## Häufig gestellte Fragen zur Arbeit mit benutzerdefinierten Domänen

F: Kann ich ein bereits hochgeladenes Zertifikat auswählen, ohne es erneut hochzuladen? A: Bereits hochgeladene Zertifikate werden automatisch an eine Anwendung gebunden, und es stimmt genau ein Zertifikat mit dem Hostnamen der Anwendung überein. ... F: Wie füge ich ein Zertifikat hinzu, und in welchem Format sollte das exportierte Zertifikat hochgeladen werden? A: Das Zertifikat sollte von der Seite für die Anwendungskonfiguration hochgeladen werden. Beim Zertifikat sollte es sich um eine PFX-Datei handeln. ... F: Können ECC-Zertifikate verwendet werden? A: Es besteht keine explizite Einschränkung für Signaturmethoden. ... F: Können SAN-Zertifikate verwendet werden? A: Ja. ... F: Können Platzhalterzertifikate verwendet werden? A: Ja. ... F: Können für jede Anwendung unterschiedliche Zertifikate verwendet werden? Ja, es sei denn, zwei Anwendungen verwenden denselben externen Host. ... F: Wenn ich eine neue Domäne registriere, kann ich diese Domäne verwenden? A: Ja, die Liste mit den Domänen wird aus der Liste des Mandanten mit den überprüften Domänen eingefügt. ... F: Was passiert, wenn ein Zertifikat abläuft? A: Auf der Seite für die Anwendungskonfiguration wird im Abschnitt für die Zertifikate eine Warnung angezeigt. Wenn ein Benutzer versucht, auf die Anwendung zuzugreifen, wird eine Sicherheitswarnung angezeigt. ... F: Wie kann ich vorgehen, wenn ich ein Zertifikat für eine bestimmte App ersetzen möchte? A: Laden Sie ein neues Zertifikat über die Seite für die Anwendungskonfiguration hoch. ... F: Kann ich ein Zertifikat löschen und ersetzen? A: Wenn Sie ein neues Zertifikat hochladen und das alte Zertifikat nicht von einer anderen Anwendung verwendet wird, wird es automatisch gelöscht. ... F: Was passiert, wenn ein Zertifikat widerrufen wird? A: Sperrprüfungen werden für Zertifikate nicht durchgeführt. Wenn ein Benutzer versucht, auf die Anwendung zuzugreifen, wird je nach verwendetem Browser unter Umständen eine Sicherheitswarnung angezeigt. ... F: Kann ich ein selbstsigniertes Zertifikat verwenden? A: Ja, selbstsignierte Zertifikate sind zulässig. Beachten Sie, dass bei Verwendung einer privaten Zertifizierungsstelle der Verteilungspunkt für die Zertifikatssperrliste (CDP) für das Zertifikat öffentlich sein sollte. ... F: Gibt es eine Ansicht, über die alle Zertifikate für einen Mandanten angezeigt werden können? A: Diese Funktion wird in der aktuellen Version nicht unterstützt.


## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)
- [Arbeiten mit Ansprüche unterstützenden Apps](active-directory-application-proxy-claims-aware-apps.md) - [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)

## Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen
- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
- [Als Organisation für Azure registrieren](sign-up-organization.md)
- [Azure-Identität](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->