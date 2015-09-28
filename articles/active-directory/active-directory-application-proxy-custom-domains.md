<properties
	pageTitle="Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy"
	description="In diesem Artikel wird erläutert, wie Sie mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy arbeiten."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>

# Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy
> [AZURE.NOTE]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Durch das Verwenden von Standarddomänen können Sie eine einzige URL als interne und externe URL für den Zugriff auf eine Anwendung festlegen, sodass Ihre Benutzer sich nur eine URL für den Zugriff merken müssen – unabhängig davon, von wo sie auf die Anwendung zugreifen. Außerdem können Sie im Zugriffsbereich eine einzige Verknüpfung für die Anwendung erstellen. Wenn Sie die von Azure AD-Anwendungsproxy bereitgestellte Standarddomäne verwenden, ist keine weitere Konfiguration zum Aktivieren Ihrer Domäne erforderlich. Falls Sie eine benutzerdefinierte Domäne einsetzen, müssen Sie verschiedene Punkte berücksichtigen, damit der Anwendungsproxy Ihre Domäne erkennt und die zugehörigen Zertifikate überprüft.

## Auswählen Ihrer benutzerdefinierten Domäne

1. Veröffentlichen Sie Ihre Anwendung, indem Sie den Anweisungen im Abschnitt "Veröffentlichen von Anwendungen mit dem Anwendungsproxy" folgen.
2. Sobald die Anwendung in der Liste der Anwendungen angezeigt wird, wählen Sie sie aus, und klicken Sie auf **Konfigurieren**.
3. Geben Sie unter **Externe URL** Ihre benutzerdefinierte Domäne ein.
4. Wenn Sie eine externe HTTPS-URL verwenden, werden Sie aufgefordert, ein Zertifikat hochzuladen, damit Azure die Gültigkeit der Anwendungs-URL überprüfen kann. Sie haben auch die Möglichkeit, ein Platzhalterzertifikat hochzuladen, das der externen URL der Anwendung entspricht. Diese Domäne muss in der Liste der [von Azure verifizierten Domänen](https://msdn.microsoft.com/library/azure/jj151788.aspx) enthalten sein. Azure muss über ein Zertifikat für die Domänen-URL der Anwendung oder ein Platzhalterzertifikat verfügen, das der externen URL der Anwendung entspricht.
5. Stellen Sie sicher, dass ein DNS-Eintrag hinzugefügt wird, der die interne URL an die Anwendung weiterleitet. Auf diese Weise können Sie dieselbe URL für den internen und den externen Zugriff und eine einzige Verknüpfung mit der Anwendung in der Liste der Benutzeranwendungen verwenden.

## Häufig gestellte Fragen zur Arbeit mit benutzerdefinierten Domänen

F: Kann ich ein bereits hochgeladenes Zertifikat auswählen, ohne es erneut hochzuladen? <br> A: Bereits hochgeladene Zertifikate sind automatisch an eine Anwendung gebunden, und es gibt genau ein Zertifikat, das dem Anwendungshostnamen entspricht. <br> …<br> F: Wie füge ich ein Zertifikat hinzu, und welches Format sollte das Zertifikat aufweisen, das hochgeladen wird? <br> A: Das Zertifikat sollte von der Seite für die Anwendungskonfiguration hochgeladen werden. Das Zertifikat sollte als PFX-Datei vorliegen. <br> …<br> F: Können ECC-Zertifikate verwendet werden? <br> A: Es gilt keine explizite Beschränkung in Bezug auf die Signaturmethoden. <br> …<br> F: Können SAN-Zertifikate verwendet werden? <br> A: Ja.<br> …<br> F: Können Platzhalterzertifikate verwendet werden? <br> A: Ja. <br> …<br> F: Können für jede Anwendung unterschiedliche Zertifikate verwendet werden? <br> A: Ja, es sei denn, zwei Anwendungen verwenden denselben externen Host. <br> …..<br> F: Wenn ich eine neue Domäne registriere, kann ich diese Domäne verwenden? <br> A: Ja, die Liste der Domänen wird aus der Liste der verifizierten Domänen für den Mandanten aufgefüllt. <br> …<br> F: Was geschieht, wenn ein Zertifikat abläuft? <br> A: Auf der Seite für die Anwendungskonfiguration wird im Abschnitt für die Zertifikate eine Warnung angezeigt. Wenn ein Benutzer versucht, auf die Anwendung zuzugreifen, erscheint eine Sicherheitswarnung. <br> …<br> F: Wie muss ich vorgehen, um ein Zertifikat für eine Anwendung zu ersetzen? <br> A: Laden Sie über die Seite für die Anwendungskonfiguration ein neues Zertifikat hoch. <br> …<br> F: Kann ich ein Zertifikat löschen und ersetzen? <br> A: Wenn Sie ein neues Zertifikat hochladen und das alte Zertifikat von keiner anderen Anwendung genutzt wird, wird es automatisch gelöscht. <br> …<br> F: Was geschieht, wenn ein Zertifikat widerrufen wird? <br> A: Es wird nicht geprüft, ob Zertifikate widerrufen wurden. Wenn ein Benutzer versucht, auf die Anwendung zuzugreifen, wird je nach verwendetem Browser möglicherweise eine Sicherheitswarnung angezeigt.<br> …<br> F: Kann ich ein selbstsigniertes Zertifikat verwenden? <br> A: Ja, selbstsignierte Zertifikate sind zulässig. Beachten Sie, dass bei Verwendung einer privaten Zertifizierungsstelle der Verteilungspunkt für die Zertifikatssperrliste (CDP) für das Zertifikat öffentlich sein sollte. <br> ...<br> F: Gibt es eine Ansicht, über die alle Zertifikate für einen Mandanten angezeigt werden können? <br> A: Diese Funktion wird in der aktuellen Version nicht unterstützt.<br>



## Zusätzliche Ressourcen

* [Als Organisation für Azure registrieren](..sign-up-organization.md)
* [Azure-Identität](..fundamentals-identity.md)

<!---HONumber=Sept15_HO3-->