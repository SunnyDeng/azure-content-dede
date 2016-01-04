<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrididentität – Definieren der Datenschutzstrategie | Microsoft Azure"
	description="Sie definieren die Datenschutzstrategie für Ihre Hybrididentitätslösung, um die geschäftlichen Anforderungen zu erfüllen, die Sie definiert haben."
	documentationCenter=""
	services="active-directory"
	authors="yuridio"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="yuridio"/>


# Definieren der Datenschutzstrategie für Ihre Hybrididentitätslösung

In dieser Aufgabe definieren Sie die Datenschutzstrategie für Ihre Hybrididentitätslösung, um die geschäftlichen Anforderungen zu erfüllen, die Sie hier definiert haben:

- [Bestimmen der Datenschutzanforderungen](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) 
- [Bestimmen der Content Management-Anforderungen](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Bestimmen der Zugriffssteuerungsanforderungen](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Bestimmen der Anforderungen an Reaktionen auf Vorfälle](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## Definieren von Datenschutzoptionen
Wie in [Bestimmen von Anforderungen an die Verzeichnissynchronisierung](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md) erläutert, können Sie Microsoft Azure AD mit Ihren lokalen Active Directory-Domänendiensten (AD DS, Active Directory Domain Services) synchronisieren. Diese Integration ermöglicht Unternehmen die Nutzung von Azure-AD, um die Anmeldeinformationen des Benutzers zu überprüfen bei dem Versuch, auf Unternehmensressourcen zugreifen. Dies ist für beide Szenarien möglich: lokale und in der Cloud befindliche ruhende Daten. Zugriff auf Daten in Azure AD erfordert die Benutzerauthentifizierung über einen Sicherheitstokendienst (Security Token Service, STS).

Nach der Authentifizierung wird der Benutzerprinzipalname (User Principal Name, UPN) aus dem Authentifizierungstoken gelesen, und die replizierte Partition und der Container werden gemäß der Domäne des Benutzers bestimmt. Informationen zu Vorhandensein, Aktivierungsstatus und Rolle des Benutzers verwendet das Autorisierungssystem, um festzustellen, ob der Benutzer in dieser Sitzung für den angeforderten Zugriff auf den Zielmandanten autorisiert ist. Bestimmte autorisierte Aktionen (insbesondere Benutzererstellung, Zurücksetzen des Kennworts) erstellen einen Überwachungspfad, den ein Mandantenadministrator zum Verwalten von Maßnahmen zum Einhalten der Compliance oder Ermittlungen verwenden kann.

Die Verschiebung von Daten aus Ihrem lokalen Datencenter in Azure Storage über eine Internetverbindung ist wegen Datenvolumen, Bandbreitenverfügbarkeit oder anderer Faktoren möglicherweise nicht immer sinnvoll. Der [Import-/Exportdienst von Azure Storage](../storage/storage-import-export-service.md) bietet eine hardwarebasierte Option, um große Mengen von Daten im BLOB-Speicher abzulegen und daraus abzurufen. Sie können [BitLocker-verschlüsselte](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) Festplattenlaufwerke direkt an ein Azure-Datencenter senden, wo Cloudbediener den Inhalt auf Ihr Speicherkonto hochladen, oder sie können Ihre Azure-Daten auf Ihre Datenträger herunterladen und sie Ihnen zurücksenden. Nur verschlüsselte Datenträger werden für diesen Prozess akzeptiert (mithilfe eines BitLocker-Schlüssels, der durch den Dienst selbst während der Einrichtung des Auftrags generiert wird) . Der BitLocker-Schlüssel wird für Azure separat bereitgestellt, es ist sozusagen eine gemeinsame Schlüsselnutzung „außer der Reihe“.

Da Datenübertragung in verschiedenen Szenarien stattfinden kann, ist es auch wichtig, zu wissen, dass Microsoft Azure [virtuelle Netzwerke](http://azure.microsoft.com/documentation/services/virtual-network/) verwendet, um den Datenverkehr eines Mandanten von dem der übrigen zu isolieren. Dies erfolgt mit Maßnahmen wie Firewalls auf Host- und Gastebene, IP-Paketfilter, Portblockierung und HTTPS-Endpunkte. Allerdings wird der größte Teil der internen Azure-Kommunikation, Infrastruktur-zu-Infrastruktur und Infrastruktur-zu-Kunde (lokal) inbegriffen, auch verschlüsselt. Ein anderes wichtiges Szenario ist die interne Kommunikation von Azure-Datencentern. Microsoft verwaltet Netzwerke, um sicherzustellen, dass keine VM die IP-Adresse einer anderen abhören oder annehmen kann. TLS/SSL wird beim Zugreifen auf Azure Storage oder SQL-Datenbanken bzw. beim Verbinden mit Cloud Services verwendet. In diesem Fall ist der Kundenadministrator für den Abruf eines TLS/SSL-Zertifikats und dessen Bereitstellung in der Mandanteninfrastruktur verantwortlich. Datenverkehr zwischen virtuellen Computern in der gleichen Bereitstellung oder Mandanten in einer einzelnen Bereitstellung über Microsoft Azure Virtual Network kann durch verschlüsselte Kommunikationsprotokolle wie z. B. HTTPS, SSL/TLS oder andere geschützt werden.

Je nachdem, wie Sie die Fragen in [Bestimmen der Datenschutzanforderungen](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) beantwortet haben, sollten Sie bestimmen können, wie Ihre Daten geschützt werden sollen, und wie die Hybrididentitätslösung Ihnen dabei hilft. Die Tabelle zeigt die von Azure unterstützten Optionen, die für jedes Datenschutzszenario verfügbar sind.


| Datenschutzoptionen | In der Cloud gespeichert | Lokal gespeichert | Während der Übertragung |
|---------------------------------|----------------------|---------------------|------------|
| BitLocker-Laufwerkverschlüsselung | X | X | |
| SQL Server zum Verschlüsseln von Datenbanken | X | X | |
| VM-zu-VM-Verschlüsselung | | | X |
| SSL/TLS | | | X |
| VPN | | | X |


>[AZURE.NOTE]Lesen Sie [Compliance nach Produkt](http://azure.microsoft.com/support/trust-center/services/) unter [Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/), um mehr zu erfahren über die Zertifizierungen, mit denen jeder Azure-Dienst kompatibel ist. Da die Optionen zum Schutz von Daten einem mehrstufigen Ansatz unterliegen, ist ein Vergleich dieser Optionen für diese Aufgabe nicht machbar. Stellen Sie sicher, dass Sie alle verfügbaren Optionen für die jeweilige Situation der Daten nutzen.

## Definieren von Content Management-Optionen
Ein Vorteil der Verwendung von Azure AD zum Verwalten einer Hybrididentitätsinfrastruktur ist, dass der Prozess aus der Perspektive des Endbenutzers vollständig transparent ist. Der Benutzer versucht, auf eine freigegebene Ressource zugreifen, die Ressource erfordert eine Authentifizierung, der Benutzer muss eine Authentifizierungsanfrage an Azure AD senden, um das Token abzurufen und auf die Ressource zuzugreifen. Dieser gesamte Prozess findet im Hintergrund ohne Benutzereingriff statt. Es ist auch möglich, eine Berechtigung für eine [Gruppe](active-directory-manage-groups.md#getting-started-with-access-management) von Benutzern zu gewähren, damit sie bestimmte gemeinsame Aktionen ausführen können.

Organisationen, die um den Datenschutz besorgt sind, fordern in der Regel eine Klassifizierung von Daten für ihre Lösung. Wenn ihre aktuelle lokale Infrastruktur bereits die Klassifizierung von Daten unterstützt, können sie Azure AD als Hauptrepository für die Identität des Benutzers nutzen. Ein gängiges, lokal für die Klassifizierung von Daten verwendetes Tool ist das[Toolkit zur Datenklassifizierung](https://msdn.microsoft.com/library/Hh204743.aspx) für Windows Server 2012 R2. Dieses Tool unterstützt Sie beim Ermitteln, Klassifizieren und Schützen von Daten auf Dateiservern in Ihrer privaten Cloud. Sie können hierfür auch die [Automatische Dateiklassifizierung](https://technet.microsoft.com/library/hh831672.aspx) in Windows Server 2012 einsetzen.

Wenn Ihre Organisation keine Datenklassifizierung einsetzt, aber vertrauliche Dateien schützen muss, ohne lokal neue Server hinzuzufügen, kann sie den [Azure Rights Management-Dienst](https://technet.microsoft.com/library/JJ585026.aspx) von Microsoft nutzen. Azure RMS verwendet Verschlüsselungs-, Identitäts- und Autorisierungsrichtlinien, um Ihre Dateien und E-Mail zu schützen, und ist geräteübergreifend einsetzbar – auf Telefonen, Tablets und PCs. Da Azure RMS ein Clouddienst ist, erübrigt es sich, explizit Vertrauensstellungen mit anderen Organisationen zu konfigurieren, bevor Sie geschützte Inhalte freigeben können. Wenn Sie bereits ein Office 365- oder Azure AD-Verzeichnis haben, wird automatisch die Zusammenarbeit zwischen Organisationen unterstützt. Sie können auch einfach die Verzeichnisattribute, die Azure RMS benötigt, um eine gemeinsame Identität für Ihre lokalen Active Directory-Konten zu unterstützen, mithilfe der Azure Active Directory-Synchronisierungsdienste (AAD-Synchronisierung) oder Azure AD Connect synchronisieren.

Ein wesentlicher Bestandteil des Content Managements ist, zu verstehen, wer auf welche Ressource zugreift. Darum ist eine umfassende Protokollierungsfunktion für die Identitätsverwaltungslösung wichtig. Azure AD bietet eine 30 Tage dauernde Protokollierung, einschließlich:

- Änderungen in der Rollenmitgliedschaft (z. B. Benutzer, die der globalen Administratorrolle hinzugefügt werden)
- Updates von Anmeldeinformationen (z. B. Kennwortänderungen)
- Domänenverwaltung (z. B. Überprüfen einer benutzerdefinierten Domäne, Entfernen einer Domäne)
- Hinzufügen oder Entfernen von Anwendungen
- Benutzerverwaltung (z. B. Hinzufügen, Entfernen, Aktualisieren eines Benutzers)
- Hinzufügen oder Entfernen von Lizenzen

>[AZURE.NOTE]Lesen Sie [Microsoft Azure Sicherheits- und Überwachungsprotokollverwaltung](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf), um weitere Informationen zu Protokollierungsfunktionen in Azure zu erhalten. Je nachdem, wie Sie die Fragen in [Bestimmen der Content Management-Anforderungen](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md) beantwortet haben, sollten Sie bestimmen können, wie der Inhalt in Ihrer Hybrididentitätslösung verwaltet werden soll. Obgleich alle Optionen, die in Tabelle 6 aufgeführt werden, in Azure AD integriert werden können, ist es wichtig, zu definieren, welche für Ihre geschäftlichen Anforderungen besser geeignet sind.

| Content Management-Optionen | Vorteile | Nachteile |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zentralisiert lokal (Active Directory Rights Management-Server) | Volle Kontrolle über die Serverinfrastruktur, verantwortlich für die Klassifizierung der Daten <br> Integrierte Funktion in Windows Server, keine Notwendigkeit zusätzlicher Lizenzen oder Abonnements <br> Kann mit Azure AD in einem hybriden Szenario integriert werden <br> Unterstützt Information Rights Management-Funktionen (IRM) in Microsoft Online-Diensten wie Exchange Online und SharePoint Online sowie Office 365 <br> Unterstützt lokale Microsoft-Serverprodukte wie Exchange Server, SharePoint Server und Dateiserver, auf denen Windows Server und Dateiklassifizierungsinfrastruktur (File Classification Infrastructure, FCI) ausgeführt werden. | Höherer Wartungsaufwand (mit Updates, Konfiguration und potenziellen Upgrades auf dem Laufenden bleiben), da die IT-Abteilung den Server besitzt <br> Lokale Serverinfrastruktur erforderlich <br> Keine systemeigene Nutzung der Azure-Funktionen systemintern |
| Zentralisiert in der Cloud (Azure RMS) | Einfacher zu verwalten im Vergleich zur lokalen Lösung <br> Kann mit AD DS in einem Hybridszenario integriert werden <br> Vollständig in Azure AD integriert <br> Kein lokaler Server zur Bereitstellung des Diensts erforderlich <br> Unterstützt lokale Microsoft-Serverprodukte wie Exchange Server, SharePoint Server und Dateiserver, auf denen Windows Server und Dateiklassifizierungsinfrastruktur (File Classification Infrastructure, FCI) ausgeführt werden <br> IT-Abteilung hat vollständige Kontrolle über Schlüssel ihrer Mandanten mit BYOK-Funktion. | Ihre Organisation muss ein Cloudabonnement besitzen, das RMS unterstützt <br> Ihre Organisation benötigt ein Azure AD-Verzeichnis zur Unterstützung der Benutzerauthentifizierung für RMS |
| Hybrid (Azure RMS integriert mit lokalem Active Directory Rights Management-Server) | Dieses Szenario bietet die Vorteile beider Alternativen, zentralisiert lokal und in der Cloud. | Ihre Organisation muss ein Cloudabonnement besitzen, das RMS unterstützt <br> Ihre Organisation benötigt ein Azure AD-Verzeichnis zur Unterstützung der Benutzerauthentifizierung für RMS <br> Verbindung zwischen Azure-Clouddienst und lokaler Infrastruktur ist erforderlich |

## Definieren von Zugriffssteuerungsoptionen
Durch die optimale Nutzung der in Azure AD verfügbaren Funktionen für Authentifizierung, Autorisierung und Zugriffssteuerung ermöglichen Sie Ihrem Unternehmen die Nutzung eines Repositorys für die zentrale Identität und Benutzern und Partnern einmaliges Anmelden (Single Sign-on, SSO), wie in der folgenden Abbildung dargestellt:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Zentralisierte Verwaltung und vollständige Integration in andere Verzeichnisse

Azure Active Directory bietet einmaliges Anmelden bei Tausenden von SaaS-Anwendungen und lokalen Webanwendungen. Lesen Sie bitte den Artikel [Einmaliges Anmelden mithilfe externer Identitätsanbieter](https://msdn.microsoft.com/library/azure/jj679342.aspx), um mehr über SSO-Drittanbieter zu erfahren, die von Microsoft getestet wurden. Diese Funktion ermöglicht Unternehmen, eine Vielzahl von B2B-Szenarien zu implementieren und gleichzeitig die Kontrolle über die Identitäts- und Zugriffsverwaltung zu behalten. Allerdings muss während des B2B-Entwurfsprozesses die Authentifizierungsmethode berücksichtigt werden, die vom Partner verwendet wird, und überprüft werden, ob diese Methode von Azure unterstützt wird. Zurzeit werden diese Methoden von Azure AD unterstützt:

- Security Assertion Markup Language (SAML)
- OAuth
- Kerberos
- Token
- Zertifikate


>[AZURE.NOTE]Lesen Sie [Azure Active Directory-Authentifizierungsprotokolle](https://msdn.microsoft.com/library/azure/dn151124.aspx), um weitere Details zu jedem Protokoll und seinen Funktionen in Azure zu erfahren. Mit Azure AD-Unterstützung können mobile Geschäftsanwendungen die gleiche mühelose Mobile Services-Authentifizierung nutzen, um Mitarbeitern zu ermöglichen, ihre mobilen Anwendungen mit ihren Active Directory-Unternehmensanmeldeinformationen anzumelden. Mit diesem Feature wird Azure AD als Identitätsanbieter in Mobile Services parallel mit anderen Identitätsanbietern unterstützt, die bereits unterstützt werden (einschließlich Microsoft-Konten, Facebook-ID, Google-ID und Twitter-ID). Wenn die lokalen Apps die Anmeldeinformationen des Benutzers verwenden, die sich im AD DS des Unternehmens befinden, sollte der Zugriff von Partnern und Benutzern aus der Cloud transparent sein. Sie können den bedingten Zugriff des Benutzers auf (cloudbasierte) Webanwendungen, Web-API, Microsoft-Clouddienste, Drittanbieter-SaaS-Anwendungen und systemeigene (mobile) Clientanwendungen verwalten und die Vorteile von Sicherheit, Überwachung, Berichterstattung an einem Ort nutzen. Es empfiehlt sich jedoch, dies in einer Nicht-Produktionsumgebung oder mit einer begrenzten Anzahl von Benutzern zu überprüfen.

>[AZURE.TIP]Es ist wichtig zu erwähnen, dass Azure AD nicht wie AD DS über eine Gruppenrichtlinien verfügt. Um Richtlinien für Geräte durchzusetzen, benötigen Sie eine Verwaltungslösung für mobile Geräte wie z. B. [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Sobald der Benutzer mithilfe von Azure AD authentifiziert ist, muss seine Zugriffsebene ausgewertet werden. Die Zugriffsebene des Benutzers für eine Ressource kann variieren. Während Azure AD eine zusätzliche Sicherheitsstufe durch Steuern des Zugriffs auf einige Ressourcen hinzufügen kann, müssen Sie auch bedenken, dass die Ressource selbst auch eine eigene, separate Zugriffssteuerungsliste haben kann, wie z. B. die Zugriffssteuerung für Dateien, die sich auf einem Dateiserver befinden. Die Abbildung unten fasst die Ebenen der Zugriffssteuerung zusammen, die Sie in einem Hybridszenario haben können:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Jede Interaktion in dem in Abbildung X gezeigten Diagramm stellt eine Zugriffssteuerungsszenerie dar, die von Azure AD abgedeckt werden kann. Im Folgenden werden die einzelnen Szenarien beschrieben:

1. Bedingter Zugriff auf lokal gehostete Anwendungen: Sie können registrierte Geräte mit Zugriffsrichtlinien für Anwendungen verwenden, die für die Verwendung von AD FS mit Windows Server 2012 R2 konfiguriert sind. Weitere Informationen zum Einrichten des bedingten Zugriffs für lokale Systeme finden Sie unter [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-on-premises-setup.md). 
2. Steuerung des Zugriffs auf das Azure-Verwaltungsportal: Azure verfügt auch über die Fähigkeit zum Steuern des Zugriffs auf das Verwaltungsportal mit rollenbasierter Zugriffssteuerung (RBAC, Role Based Access Control). Diese Methode ermöglicht dem Unternehmen, die Menge der Vorgänge zu beschränken, die eine einzelne Person ausführen kann, wenn sie auf das Azure-Verwaltungsportal zugreift. Durch Einsatz von RBAC zur Steuerung des Zugriffs auf das Portal können IT-Administratoren mithilfe folgender Ansätze zur Zugriffsverwaltung den Zugriff delegieren:

 - Gruppenbasierte Rollenzuweisung: Sie können Azure AD-Gruppen den Zugriff zuweisen, die von Ihrem lokalen Active Directory aus synchronisiert werden können. So können Sie die vorhandenen Investitionen nutzen, die Ihre Organisation in Tools und Prozesse zum Verwalten von Gruppen vorgenommen hat. Sie können auch das Feature zur delegierten Gruppenverwaltung von Azure AD Premium nutzen.
 - Nutzen Sie integrierte Rollen in Azure: Sie können diese drei Rollen verwenden – Besitzer, Mitwirkender und Leser, um sicherzustellen, dass Benutzer und Gruppen berechtigt sind, nur die Aufgaben auszuführen, die sie für ihre Arbeit benötigen. 
 - Präziser Zugriff auf Ressourcen: Sie können Benutzern und Gruppen Rollen für bestimmte Abonnements, Ressourcengruppen oder eine einzelne Azure-Ressource, z. B. eine Website oder eine Datenbank, zuweisen. Auf diese Weise können Sie sicherstellen, dass Benutzer Zugriff auf alle Ressourcen haben, die sie benötigen, und keinen Zugriff auf Ressourcen, die sie nicht verwalten müssen.

 >[AZURE.NOTE]Weitere Informationen zu dieser Funktion finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal](http://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/). Entwickler, die Anwendungen erstellen und die Zugriffssteuerung hierfür anpassen möchten, können auch die Anwendungsrollen aus Azure AD für die Autorisierung verwenden. Lesen Sie dieses [WebApp-RoleClaims-DotNet-Beispiel](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet), um zu erfahren, wie Sie Ihre App so erstellen, dass sie diese Funktion nutzt.

3. Bedingter Zugriff für Office 365-Anwendungen mit Microsoft Intune: IT-Administratoren können Geräterichtlinien für den bedingten Zugriff bereitstellen, um Unternehmensressourcen zu schützen, und gleichzeitig Information-Workern auf kompatiblen Geräten den Zugriff auf die Dienste zu gestatten. Weitere Informationen finden Sie unter [Geräterichtlinien für den bedingten Zugriff auf Office 365-Dienste](active-directory-conditional-access-device-policies.md).

4. Bedingter Zugriff für Saas-Apps: [Dieses Feature](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) ermöglicht Ihnen die anwendungsspezifische Konfiguration von Zugriffsregeln für die Multi-Factor Authentication und das Blockieren des Zugriffs für Benutzer, die nicht zu einem vertrauenswürdigen Netzwerk gehören. Die Regeln für die mehrstufige Authentifizierung können auf alle Benutzer angewendet werden, die einer Anwendung zugewiesen sind, oder nur auf Benutzer in angegebenen Sicherheitsgruppen. Benutzer können von der Pflicht zur mehrstufigen Authentifizierung ausgenommen werden, wenn sie von einer IP-Adresse innerhalb des Netzwerks der Organisation auf die Anwendung zugreifen.

Da die Optionen zur Zugriffssteuerung einem mehrstufigen Ansatz unterliegen, ist ein Vergleich dieser Optionen für diese Aufgabe nicht machbar. Stellen Sie sicher, dass Sie alle verfügbaren Optionen für jedes Szenario nutzen, das die Steuerung des Zugriffs auf Ihre Ressourcen erfordert.

## Definieren der Optionen für die Reaktion auf Vorfälle
Azure AD kann die IT-Abteilung dabei unterstützen, potenzielle Sicherheitsrisiken in der Umgebung durch Überwachung der Benutzeraktivität zu erkennen. Die IT-Abteilung kann die Azure AD-Funktion für Zugriffs- und Verwendungsberichte nutzen, um einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu erhalten. Mithilfe dieser Informationen kann ein IT-Administrator mögliche Sicherheitsrisiken besser bestimmen, um angemessen zu planen, wie diese Risiken eingedämmt werden können. Das [Azure AD Premium-Abonnement](articles/active-directory-get-started-premium.md) bietet eine Reihe von Sicherheitsberichten, die IT-Mitarbeitern ermöglichen, diese Informationen zu erhalten. [Azure AD-Berichte](active-directory-view-access-usage-reports.md) werden wie folgt kategorisiert:

- **Anomalieberichte**: Enthalten Anmeldeereignisse, die wir als anomal eingestuft haben. Unser Ziel ist, Sie auf solche Aktivitäten aufmerksam zu machen und Ihnen zu ermöglichen, eine Entscheidung zu treffen, ob ein Ereignis verdächtig ist. 
- **Integrierte Anwendungsberichte**: Bieten Einblicke, wie Cloudanwendungen in Ihrer Organisation verwendet werden. Azure Active Directory ermöglicht die Integration in Tausende von Cloudanwendungen. 
- **Fehlerberichte**: Enthalten Hinweise auf Fehler, die bei der Bereitstellung von Konten für externe Anwendungen auftreten können.
- **Benutzerspezifische Berichte**: Enthalten Geräte-/Anmeldeaktivitätsdaten für einen bestimmten Benutzer.
- **Aktivitätsprotokolle**: Enthalten eine Aufzeichnung aller überwachten Ereignisse in den letzten 24 Stunden, letzten 7 Tagen oder letzten 30 Tagen sowie von Änderungen an Gruppenaktivitäten sowie Kennwortzurücksetzungs- und Registrierungsaktivitäten.

>[AZURE.TIP]Ein weiterer Bericht, der auch das Team für die Reaktion auf Vorfälle bei der Bearbeitung eines Falls unterstützen kann, ist der Bericht über [Benutzer mit kompromittierten Anmeldeinformationen](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx). Dieser Bericht offenbart etwaige Übereinstimmungen zwischen dieser Liste kompromittierter Anmeldeinformationen und Ihrem Mandanten.

Andere wichtige, in Azure AD integrierte Berichte, die während einer Untersuchung zur Reaktion auf einen Vorfall verwendet werden können, sind:

- **Kennwortrücksetzungsaktivität**: Zeigt dem Administrator, wie intensiv die Kennwortrücksetzung in der Organisation praktiziert wird.
- **Kennwortrücksetzungs-Registrierungsaktivität**: Zeigt, welche Benutzer ihre Methoden zum Zurücksetzen des Kennworts registriert haben, und welche Methoden sie ausgewählt haben.
- **Gruppenaktivität**: Zeigt einen Verlauf der Änderungen der Gruppe (z. B. Hinzufügen oder Entfernen von Benutzern), die im Zugriffsbereich initiiert wurden.

Ergänzend zu den in Azure AD Premium verfügbaren Kernberichtsfunktionen, die bei der Untersuchung zur Reaktion auf einen Vorfall verwendet werden können, können IT-Mitarbeiter auch den Überwachungsbericht nutzen, um Informationen wie die folgenden zu erhalten:

- Änderungen in der Rollenmitgliedschaft (z. B. Benutzer, die der globalen Administratorrolle hinzugefügt werden)
- Updates von Anmeldeinformationen (z. B. Kennwortänderungen)
- Domänenverwaltung (z. B. Überprüfen einer benutzerdefinierten Domäne, Entfernen einer Domäne)
- Hinzufügen oder Entfernen von Anwendungen
- Benutzerverwaltung (z. B. Hinzufügen, Entfernen, Aktualisieren eines Benutzers)
- Hinzufügen oder Entfernen von Lizenzen

Da die Optionen zur Reaktion auf einen Vorfall einem mehrstufigen Ansatz unterliegen, ist ein Vergleich dieser Optionen für diese Aufgabe nicht machbar. Stellen Sie sicher, dass Sie alle verfügbaren Optionen für jedes Szenario nutzen, für das Sie die Berichtsfunktionen von Azure AD im Rahmen des Prozesses zur Reaktion auf Vorfälle Ihres Unternehmens nutzen müssen.

## Nächste Schritte
[Ermitteln der Aufgaben für die Hybrididentitätsverwaltung](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md)


## Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_1203_2015-->