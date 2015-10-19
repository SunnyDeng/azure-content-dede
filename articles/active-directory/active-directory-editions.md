<properties
	pageTitle="Azure Active Directory-Editionen| Microsoft Azure"
	description="In diesem Thema werden die Features und Unterschiede der kostenlosen und kostenpflichtigen Editions von Azure Active Directory beschrieben."
	services="active-directory"
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
	ms.date="10/01/2015"
	ms.author="markvi"/>

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
- **Gruppenbasierter Anwendungszugriff** – Mittels Benutzergruppen können Sie die Benutzerbereitstellung und die Zugriffszuweisung für Tausende von SaaS-Anwendungen gleichzeitig vornehmen. Diese Gruppen können Sie ausschließlich in der Cloud erstellen, Sie können aber auch vorhandene Gruppen aus Ihrem lokalen Active Directory-Verzeichnis synchronisieren und nutzen. Weitere Informationen finden Sie unter [Zuweisen des Zugriffs auf eine SaaS-Anwendung für eine Gruppe in Azure AD](active-directory-accessmanagement-group-saasapps.md).
- **Self-Service-Kennwortrücksetzung** – Azure hat Verzeichnisadministratoren schon immer das Zurücksetzen von Kennwörtern erlaubt. Mit Azure Active Directory Basic reduzieren Sie nun die Helpdesk-Anrufe Ihrer Benutzer, wenn diese ihr Kennwort vergessen haben, da Sie den Benutzern Ihres Verzeichnisses die Möglichkeit einräumen können, ihre Kennwörter selbst mit dem gleichen Anmeldeverfahren wie in Office 365 zurückzusetzen. Weitere Informationen finden Sie unter [Verwalten von Kennwörtern von einem beliebigen Ort aus](active-directory-passwords.md).
- **Unternehmens-SLA von 99,9 %** – Microsoft garantiert für den Azure Active Directory Basic-Dienst eine Verfügbarkeit von mindestens 99,9 %.
- [**Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen**](active-directory-application-proxy-get-started.md) – Damit erhalten Ihre Mitarbeiter über Azure Active Directory einen sicheren Zugriff aus der Cloud auf lokale Anwendungen wie SharePoint und Exchange/OWA.

## Funktionen der Azure Active Directory Premium Edition

Die Active Directory Premium Edition ist ein kostenpflichtiges Angebot von Azure Active Directory mit dem vollen Funktionsumfang der Free Edition und der Basic Edition plus den folgenden Funktionen:

- **Self-Service-Gruppenverwaltung** – Azure Active Directory Premium vereinfacht die tägliche Verwaltung von Gruppen, indem es Benutzern folgende Aktionen erlaubt: Erstellen von Gruppen, Anfordern des Zugriffs auf andere Gruppen, Delegieren des Gruppenbesitzes, so dass andere Benutzer Anforderungen genehmigen können, und Verwalten der Zugehörigkeiten ihrer Gruppe.

    Weitere Informationen finden Sie unter [Self-Service-Gruppenverwaltung für Benutzer in Azure AD](https://msdn.microsoft.com/library/azure/dn641267.aspx).

- **Erweiterte Sicherheitsberichte und Warnungen** – In detaillierten Protokollen, die erweiterte Anomalien und inkonsistente Zugriffsmuster aufzeichnen, können Sie den Zugriff auf Ihre Cloud-Anwendungen überwachen und schützen. Die erweiterten Berichte verfügen über Machine-Learning-Fähigkeiten und verhelfen Ihnen zu neuen Erkenntnissen, mit denen Sie die Zugriffssicherheit verbessern und auf potenzielle Bedrohungen reagieren können.

    Weitere Informationen finden Sie unter [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md).

- **Multi-Factor Authentication** – Die nun in der Premium Edition enthaltene Multi-Factor Authentication hilft Ihnen beim Schutz des Zugriffs auf lokale Anwendungen (VPN, RADIUS usw.), Azure, Microsoft Online Services wie Office 365 und Dynamics CRM Online sowie Tausende von Clouddiensten von Drittanbietern, die bereits in Azure Active Directory integriert sind. Aktivieren Sie einfach die Multi-Factor Authentication für Azure Active Directory-Identitäten, und Benutzer werden bei der nächsten Anmeldung aufgefordert, sich durch eine zusätzliche Verifizierung auszuweisen.

    Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](multi-factor-authentication.md)

- **Microsoft Identity Manager** – Die Premium Edition beinhaltet die Möglichkeit, in Ihrem lokalen Netzwerk die Rechte zur Nutzung eines MIM-Servers (und zur Nutzung von CALs) zu erteilen, um beliebige Kombinationen von Hybrid-Identity-Lösungen zu unterstützen. Dies ist ein großartiges Feature, wenn Sie verschiedene lokale Verzeichnisse und Datenbanken verwenden, die Sie direkt mit Azure Active Directory synchronisieren möchten. Die Anzahl der MIM-Server, die Sie verwenden können, ist unbeschränkt, die Erteilung von MIM-CALs basiert jedoch auf der Zuweisung einer Azure Active Directory Premium-Benutzerlizenz.

    Weitere Informationen finden Sie unter [Microsoft Identity Manager](http://www.microsoft.com/de-DE/server-cloud/products/microsoft-identity-manager/default.aspx).

- **Unternehmens-SLA von 99,9 %** – Microsoft garantiert für den Azure Active Directory Premium-Dienst eine Verfügbarkeit von mindestens 99,9 %.

    Weitere Informationen finden Sie unter [Active Directory Premium-SLA](http://azure.microsoft.com/support/legal/sla/).

- **Kennwortrücksetzung mit Zurückschreiben** – Die Self-Service-Kennwortrücksetzung kann zurück in die lokalen Verzeichnisse geschrieben werden.

- [Azure Active Directory Connect Health](active-directory-aadconnect-health.md): Überwachen Sie die Integrität Ihrer lokalen Active Directory-Infrastruktur, und rufen Sie Nutzungsanalysen ab.



## Vergleich der Free, Basic und Premium Edition

<br> In folgender Edition verfügbar: ![Checkliste](./media/active-directory-editions/ic195031.png)


<table>
	<tr>
		<th>&#160;</th>
		<th>Merkmale </th>
		<th>Free Edition </th>
		<th>Basic Edition </th>
		<th>Premium Edition </th>
	</tr>
	<tr>
		<td rowspan="8">
		<p>Gemeinsame Features</p>
		</td>
		<td>
		<p>Verzeichnis als Dienst</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /><br />
		Bis zu 500 K Objekte [1]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /><br />
		Keine Einschränkung des Objektvolumens</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /><br />
		Keine Einschränkung des Objektvolumens</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Benutzer- und Gruppenverwaltung mithilfe der Benutzeroberfläche oder der Windows PowerShell-Cmdlets</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Geräteregistrierung</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Zugriffsbereichsportal für SSO-basierte Benutzerzugriffe auf SaaS und benutzerdefinierte Anwendungen</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /><br />
		Bis zu 10 Apps pro Benutzer [2]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /><br />
		Bis zu 10 Apps pro Benutzer [2]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /><br />
		Keine App-Begrenzung</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Benutzerbasierte Anwendungszugriffsverwaltung und Bereitstellung von Anwendungen</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Self-Service-Kennwortänderung für Cloudbenutzer</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Azure AD Connect – Für die Synchronisierung zwischen lokalen Verzeichnissen und Azure Active Directory</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Standardsicherheitsberichte</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td rowspan="5">
		<p>Features der Premium und Basic Edition</p>
		</td>
		<td>
		<p>In der SLA garantierte Verfügbarkeit von 99,9 %</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Gruppenbasierte Anwendungszugriffsverwaltung und Bereitstellung von Anwendungen</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Anpassen von Firmenlogo und Farbschemas auf Anmelde- und Zugriffsbereichsseiten</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Self-Service-Kennwortrücksetzung für Cloudbenutzer</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Anwendungsproxy: sicherer Remotezugriff und SSO für lokale Webanwendungen</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td rowspan="9">
		<p>Nur in der Premium Edition enthaltene Features</p>
		</td>
		<td>
		<p>Erweiterte Berichte zur Anwendungsnutzung</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Self-Service-Gruppenverwaltung für Cloudbenutzer</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Self-Service-Kennwortrücksetzung mit lokalem Zurückschreiben</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Microsoft Identity Manager (MIM)-Benutzerlizenzen – Für die lokale Identitäts- und Zugriffsverwaltung</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /> [3]</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Erweiterte Machine-Learning-basierte Sicherheitsberichte zu Anomalien</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>
		<a href="http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery"> Cloud App Discovery</a> </p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Multi-Factor Authentication-Dienst für Cloudbenutzer</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Multi-Factor Authentication-Server für lokale Benutzer</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>
		<a href="https://msdn.microsoft.com/de-DE/library/azure/dn906722.aspx">Azure Active Directory Connect Health</a>: Überwachen Sie die Integrität der lokalen Active Directory-Infrastruktur, und rufen Sie Nutzungsanalysen ab.</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Prüfliste" src="./media/active-directory-editions/ic195031.png" title="Prüfliste" xmlns="" /></p>
		</td>
	</tr>
</table>


[1] Die Objektbeschränkung auf 500 K gilt jedoch nicht für Office 365, Microsoft Intune oder andere Microsoft-Onlinedienste, die von Azure Active Directory als Verzeichnisdienst abhängig sind.

[2] In Azure Active Directory Free und Azure Active Directory Basic können Endbenutzer, denen Zugriff auf alle SaaS-Apps gewährt wurde, in ihrem Zugriffsbereich bis zu 10 Apps anzeigen und mittels SSO darauf zugreifen (sofern sie vom Administrator für SSO konfiguriert wurden). Mit der Free Edition können Administratoren SSO konfigurieren und Benutzerzugriff auf beliebig viele SaaS-Anwendungen zuweisen; die Endbenutzer können in ihrem Zugriffsbereich jedoch jeweils nur 10 Anwendungen anzeigen.

[3] Rechte an Microsoft Identity Manager Server-Software werden mit Windows Server-Lizenzen gewährt (beliebige Edition). Da Microsoft Identity Manager unter dem Betriebssystem Windows Server ausgeführt wird, kann Microsoft Identity Manager auf diesem Server installiert und verwendet werden, solange auf dem Server eine gültige, lizenzierte Kopie von Windows Server ausgeführt wird. Für Microsoft Identity Manager Server ist keine andere separate Lizenz erforderlich.



<br> <br>









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

<!---HONumber=Oct15_HO2-->