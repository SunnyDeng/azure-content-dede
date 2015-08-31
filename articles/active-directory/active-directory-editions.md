<properties
	pageTitle="Azure Active Directory-Editionen| Microsoft Azure"
	description="In diesem Thema werden die Features und Unterschiede der kostenlosen und kostenpflichtigen Editions von Azure Active Directory beschrieben."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="curtand"/>

# Azure Active Directory-Editionen

Azure Active Directory ist ein Dienst, der umfassende Funktionen zur Identitäts- und Zugriffsverwaltung in der Cloud bietet. Er kombiniert Verzeichnisdienste, eine erweiterte Identitätsgovernance, Zugriffsverwaltung für Anwendungen und eine standardbasierte Plattform für Entwickler mit einem reichhaltigen Funktionsangebot. Weitere Informationen [erhalten Sie in diesem Video](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/).

Zusätzlich zu der reichhaltigen Palette kostenloser Funktionen von Microsoft Azure Active Directory bieten die Premium und die Basic Edition von Azure Active Directory einen Satz erweiterter Funktionen, die auch den anspruchsvollen Identitäts- und Zugriffsverwaltungsanforderungen von Unternehmen genügen. Informationen zu den Preisoptionen für diese Editionen finden Sie unter [Azure Active Directory Preise](http://azure.microsoft.com/pricing/details/active-directory/). Beim Abschließen eines Azure-Abonnements erhalten Sie die Wahl zwischen den folgenden kostenlosen und kostenpflichtigen Editions von Azure Active Directory:

- **Free** – die Free Edition von Azure Active Directory ist in jedem Azure-Abonnement enthalten. Es gibt keine Lizenzen, und es müssen keine Komponenten installiert werden. Mit dieser Edition können Sie Benutzerkonten verwalten, eine Synchronisierung mit lokalen Verzeichnissen ausführen, sich mittels einmaligem Anmelden (SSO) bei Azure, Office 365 und Tausenden gängigen SaaS-Anwendungen wie Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox u. v. a. anmelden.
- **Basic** – Die Azure Active Directory Basic Edition bietet Sachbearbeitern in Unternehmen, die viel mit der Cloud arbeiten, Anwendungszugriffs- und Self-Service-Identitätsverwaltungsfeatures. Mit der Basic Edition von Azure Active Directory erhalten Sie alle Funktionen der Azure Active Directory Free Edition plus gruppenbasierter Zugriffsverwaltung, Self-Service-Kennwortrücksetzung für Cloud-Anwendungen, den Azure Active Directory-Anwendungsproxy (zur Veröffentlichung lokaler Webanwendungen mit Azure Active Directory), eine anpassbare Umgebung zum Starten von Cloud-Anwendungen für Unternehmen und Konsumenten sowie eine SLA auf Unternehmensebene mit einer Verfügbarkeitsgarantie von 99,9 %. Ein Administrator, der für die Azure Active Directory Basic Edition lizenziert ist, kann auch die Testversion von Azure Active Directory Premium aktivieren.
- **Premium** – mit der Premium Edition von Azure Active Directory erhalten Sie alle Funktionen der Free Edition und der Basic Edition von Azure Active Directory sowie zusätzlich die reichhaltige Palette der nachfolgend beschriebenen Identitätsverwaltungsfunktionen auf Unternehmensebene.

Wenn Sie sich noch heute für Active Directory Premium registrieren und Ihre Arbeit mit dieser Edition beginnen möchten, gehen Sie zu [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]Azure Active Directory Premium und Azure Active Directory Basic werden derzeit in China nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im [Azure Active Directory-Forum](http://feedback.azure.com/forums/169401-azure-active-directory).

## Funktionen der Azure Active Directory Basic Edition

Die Active Directory Basic Edition ist ein kostenpflichtiges Angebot von Azure Active Directory mit den folgenden Funktionen:

- **Unternehmensbranding** – Zur Verbesserung des Endbenutzererlebnisses können Sie den Anmelde- und Zugriffsbereichsseiten Ihres Unternehmens Firmenlogo und Farbschemas Ihres Unternehmens hinzufügen. Sie können auch lokalisierte Versionen des Logos für verschiedene Sprachen und Gebietsschemas hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md).
- **Gruppenbasierter Anwendungszugriff** – Mittels Benutzergruppen können Sie die Benutzerbereitstellung und die Zugriffszuweisung für Tausende von SaaS-Anwendungen gleichzeitig vornehmen. Diese Gruppen können Sie ausschließlich in der Cloud erstellen, Sie können aber auch vorhandene Gruppen aus Ihrem lokalen Active Directory-Verzeichnis synchronisieren und nutzen. Weitere Informationen finden Sie unter [Zuweisen des Zugriffs auf eine SaaS-Anwendung für eine Gruppe in Azure AD](https://msdn.microsoft.com/library/azure/dn621141.aspx).
- **Self-Service-Kennwortrücksetzung** – Azure hat Verzeichnisadministratoren schon immer das Zurücksetzen von Kennwörtern erlaubt. Mit Azure Active Directory Basic reduzieren Sie nun die Helpdesk-Anrufe Ihrer Benutzer, wenn diese ihr Kennwort vergessen haben, da Sie den Benutzern Ihres Verzeichnisses die Möglichkeit einräumen können, ihre Kennwörter selbst mit dem gleichen Anmeldeverfahren wie in Office 365 zurückzusetzen. Weitere Informationen finden Sie unter [Kennwortverwaltung in Azure AD](https://msdn.microsoft.com/library/azure/dn510386.aspx).
- **Unternehmens-SLA von 99,9 %** – Microsoft garantiert für den Azure Active Directory Basic-Dienst eine Verfügbarkeit von mindestens 99,9 %.
- [**Azure Active Directory-Anwendungsproxy**](https://msdn.microsoft.com/library/azure/dn768214.aspx) – Damit erhalten Ihre Mitarbeiter über Azure Active Directory einen sicheren Zugriff aus der Cloud auf lokale Anwendungen wie SharePoint und Exchange/OWA.

## Funktionen der Azure Active Directory Premium Edition

Die Active Directory Premium Edition ist ein kostenpflichtiges Angebot von Azure Active Directory mit dem vollen Funktionsumfang der Free Edition und der Basic Edition plus den folgenden Funktionen:

- **Self-Service-Gruppenverwaltung** – Azure Active Directory Premium vereinfacht die tägliche Verwaltung von Gruppen, indem es Benutzern folgende Aktionen erlaubt: Erstellen von Gruppen, Anfordern des Zugriffs auf andere Gruppen, Delegieren des Gruppenbesitzes, so dass andere Benutzer Anforderungen genehmigen können, und Verwalten der Zugehörigkeiten ihrer Gruppe.

    Weitere Informationen finden Sie unter [Self-Service-Gruppenverwaltung für Benutzer in Azure AD](https://msdn.microsoft.com/library/azure/dn641267.aspx).

- **Erweiterte Sicherheitsberichte und Warnungen** – In detaillierten Protokollen, die erweiterte Anomalien und inkonsistente Zugriffsmuster aufzeichnen, können Sie den Zugriff auf Ihre Cloud-Anwendungen überwachen und schützen. Die erweiterten Berichte verfügen über Machine-Learning-Fähigkeiten und verhelfen Ihnen zu neuen Erkenntnissen, mit denen Sie die Zugriffssicherheit verbessern und auf potenzielle Bedrohungen reagieren können.

    Weitere Informationen finden Sie unter [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md).

- **Multi-Factor Authentication** – Die nun in der Premium Edition enthaltene Multi-Factor Authentication hilft Ihnen beim Schutz des Zugriffs auf lokale Anwendungen (VPN, RADIUS usw.), Azure, Microsoft-Onlinedienste wie Office 365 und Dynamics CRM Online sowie Tausende von Clouddiensten von Drittanbietern, die bereits mit Azure Active Directory integriert sind. Aktivieren Sie einfach die Multi-Factor Authentication für Azure Active Directory-Identitäten, und Benutzer werden bei der nächsten Anmeldung aufgefordert, sich durch eine zusätzliche Verifizierung auszuweisen.

    Weitere Informationen finden Sie unter [Hinzufügen der Multi-Factor Authentication zu Azure Active Directory](https://msdn.microsoft.com/library/azure/dn249466.aspx).

- **Microsoft Identity Manager (MIM)** – Die Premium Edition beinhaltet die Möglichkeit, in Ihrem lokalen Netzwerk die Rechte zur Nutzung eines MIM-Servers (und zur Nutzung von CALs) zu erteilen, um beliebige Kombinationen von Hybrid-Identity-Lösungen zu unterstützen. Dies ist ein großartiges Feature, wenn Sie verschiedene lokale Verzeichnisse und Datenbanken verwenden, die Sie direkt mit Azure Active Directory synchronisieren möchten. Die Anzahl der FIM-Server, die Sie verwenden können, ist unbeschränkt, die Erteilung von MIM CALs basiert jedoch auf der Zuweisung einer Azure Active Directory Premium-Benutzerlizenz.

    Weitere Informationen finden Sie unter [Bereitstellen von MIM 2010 R2](https://www.microsoft.com/server-cloud/products/forefront-identity-manager/features.aspx).

- **Unternehmens-SLA von 99,9 %** – Microsoft garantiert für den Azure Active Directory Premium-Dienst eine Verfügbarkeit von mindestens 99,9 %.

    Weitere Informationen finden Sie unter [Active Directory Premium-SLA](http://azure.microsoft.com/support/legal/sla/).

- **Kennwortrücksetzung mit Zurückschreiben** – Die Self-Service-Kennwortrücksetzung kann zurück in die lokalen Verzeichnisse geschrieben werden.

- [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx): Überwachen Sie die Integrität Ihrer lokalen Active Directory-Infrastruktur, und rufen Sie Nutzungsanalysen ab.



## Vergleich der Editionen: Funktionen, die in allen Editionen gleich sind

- Das Angebot „Verzeichnis als Dienst“ ist für die Free Edition auf Objekte bis zu einem Volumen von 500K begrenzt. Diese Einschränkung gilt jedoch nicht für Office 365, Microsoft Intune oder andere Microsoft-Onlinedienste, die von Azure Active Directory als Verzeichnisdienst abhängig sind. Für die Basic und Premium Edition gilt keine Einschränkung des Objektvolumens.
- Benutzer- und Gruppenverwaltung mithilfe der Benutzeroberfläche oder der Windows PowerShell-Cmdlets
- Geräteregistrierung
- Zugriffsbereichsportal für Benutzerzugriff mit einmaligem Anmelden (SSO) auf SaaS- und benutzerdefinierte Anwendungen in Azure Active Directory Free und Basic Edition: Endbenutzer, denen Zugriff auf SaaS-Anwendungen zugewiesen wurde, können in ihrem Zugriffsbereich bis zu 10 dieser Anwendungen anzeigen und mit einmaligem Anmelden darauf zugreifen (sofern sie vom Administrator für SSO konfiguriert wurden). Mit der Free Edition können Administratoren SSO konfigurieren und Benutzerzugriff auf beliebig viele SaaS-Anwendungen zuweisen. Die Endbenutzer können in ihrem Zugriffsbereich jedoch jeweils nur 10 Anwendungen anzeigen. Für Azure Active Directory Premium gilt keine Anwendungseinschränkung.
- Benutzerbasierte Anwendungszugriffsverwaltung und Bereitstellung von Anwendungen
- Self-Service-Kennwortänderung für Cloudbenutzer
- Tool für die Verzeichnissynchronisierung: für die Synchronisierung des lokalen AD-Verzeichnisses mit Azure Active Directory
- Standardsicherheitsberichte

## Vergleich der Editionen: Funktionen der Premium und Basic Edition

- In der SLA garantierte Verfügbarkeit von 99,9 %
- Gruppenbasierte Anwendungszugriffsverwaltung und Bereitstellung von Anwendungen
- Anpassen von Firmenlogo und Farbschemas auf Anmelde- und Zugriffsbereichsseiten
- Self-Service-Kennwortrücksetzung für Cloudbenutzer
- Anwendungsproxy: sicherer Remotezugriff und einmaliges Anmelden für lokale Webanwendungen

## Vergleich der Editionen: nur in der Premium Edition enthaltene Funktionen

- Self-Service-Gruppenverwaltung für Cloudbenutzer
- Self-Service-Kennwortrücksetzung mit lokalem Zurückschreiben
- Microsoft Identity Manager (MIM)-Serverlizenzen – für die Synchronisierung der lokalen Datenbanken und/oder Verzeichnisse mit Azure Active Directory
- Erweiterte Machine-Learning-basierte Sicherheitsberichte zu Anomalien
- [Cloud-App-Ermittlung](http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery)
- Erweiterte Berichte zur Anwendungsnutzung
- Multi-Factor Authentication-Dienst für Cloudbenutzer
- Multi-Factor Authentication-Server für lokale Benutzer
- Azure Active Directory Connect Health

## Derzeit in öffentlicher Vorschau verfügbare Features

Die folgenden Features sind derzeit in öffentlicher Vorschau verfügbar und werden bald hinzugefügt:

- [Verwaltungseinheiten](https://msdn.microsoft.com/library/azure/dn832057.aspx): ein neuer Azure Active Directory-Container mit Ressourcen, die zum Delegieren administrativer Berechtigungen und Anwenden von Richtlinien für bestimmte Benutzergruppen verwendet werden können.
- [Hinzufügen eigener SaaS-Anwendungen](https://msdn.microsoft.com/library/azure/dn893637.aspx) zu Azure Active Directory.
- Kennwortrollover für Facebook, Twitter und LinkedIn; [hier finden Sie weitere Informationen](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)
- Dynamische Gruppenmitgliedschaft. [hier finden Sie weitere Informationen](https://msdn.microsoft.com/library/azure/dn913807.aspx)
- [Bedingter Zugriff](https://msdn.microsoft.com/library/azure/dn906877.aspx): Multi-Factor Authentication pro Anwendung.
- HR-Anwendungsintegration: Arbeitstag
- Privileged Identity Management: Privileged Identity Management bietet verbesserte Überwachung, um Vereinbarungen zum Servicelevel zu erfüllen und gesetzliche Bestimmungen einzuhalten.
- Self-Service-Anwendungsanforderungen: Administratoren können für Benutzer eine Liste mit SaaS-Anwendungen bereitstellen, damit Benutzer die gewünschten Anwendungen auswählen können. Die Anwendungen sind entweder sofort oder nach der Genehmigung verfügbar.
- Azure Reporting-API: Daten für alle Sicherheitsberichte von Azure Active Directory stehen für andere Überwachungstools oder SIEM-Tools zur Verfügung.


## Nächste Schritte

- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)

<!---HONumber=August15_HO8-->