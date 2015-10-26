<properties
	pageTitle="Azure Active Directory-Domänendienste (Vorschau): Übersicht | Microsoft Azure"
	description="Übersicht über Azure AD-Domänendienste"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)*

## Übersicht
Mithilfe von Azure-Infrastrukturdiensten können Sie eine Vielzahl von Computinglösungen auf flexible Weise bereitstellen. Dank Azure Virtual Machines können Sie Ihre Produkte praktisch sofort bereitstellen und erhalten eine minutengenaue Abrechnung. Mit der Unterstützung für Windows, Linux, SQL Server, Oracle, IBM, SAP und BizTalk ist die Bereitstellung jeder Workload in jeder Sprache auf fast jedem Betriebssystem möglich. Diese Vorteile versetzen Sie in die Lage, lokal bereitgestellte Legacyanwendungen zu Azure zu migrieren, um Betriebskosten zu sparen.

Ein wichtiger Aspekt der Migration von lokalen Anwendung zu Azure ist der Umgang mit den Identitätsanforderungen dieser Anwendungen. Verzeichnisorientierte Anwendungen nutzen ggf. LDAP für den Lese- und Schreibzugriff auf das Unternehmensverzeichnis oder die integrierte Windows-Authentifizierung (Kerberos- oder NTLM-Authentifizierung), um Endbenutzer zu authentifizieren. Branchenanwendungen, die unter Windows Server ausgeführt werden, werden normalerweise auf Computern bereitgestellt, die einer Domäne beigetreten sind. So können sie per Gruppenrichtlinie auf sichere Weise verwaltet werden. Um lokale Anwendungen in die Cloud verlagern zu können („Lift-and-Shift“), müssen diese Abhängigkeiten von der Identitätsinfrastruktur des Unternehmens beseitigt werden.

Administratoren nutzen häufig eine der folgenden Lösungen, um die Identitätsanforderungen der unter Azure bereitgestellten Anwendungen zu erfüllen:

- Bereitstellen einer Standort-zu-Standort-VPN-Verbindung zwischen Workloads, die in Azure-Infrastrukturdiensten ausgeführt werden, und dem lokalen Unternehmensverzeichnis
- Erweitern der AD-Domäne/Gesamtstruktur-Infrastruktur des Unternehmens, indem Replikatdomänencontroller mithilfe von virtuellen Azure-Computern eingerichtet werden
- Bereitstellen einer eigenständigen Domäne in Azure, indem als virtuelle Azure-Computer bereitgestellte Domänencontroller verwendet werden

All diese Ansätze sind mit hohen Kosten und hohem Verwaltungsaufwand verbunden. Administratoren müssen Domänencontroller mithilfe von virtuellen Azure-Computern bereitstellen. Sie müssen diese virtuellen Computer dann verwalten, schützen, patchen, überwachen und sichern und die Problembehandlung dafür durchführen. Aufgrund der Abhängigkeit von VPN-Verbindungen vom lokalen Verzeichnis sind unter Azure bereitgestellte Workloads für vorübergehende Netzwerkprobleme oder -ausfälle anfällig. Dies führt für diese Anwendungen wiederum zu einer reduzierten Betriebszeit und Zuverlässigkeit.

Azure AD-Domänendienste sind für die Bereitstellung einer deutlich einfacheren Alternative ausgelegt.


## Einführung in Azure AD-Domänendienste
Azure AD-Domänendienste stellen verwaltete Domänendienste bereit, z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP, Kerberos/NTLM-Authentifizierung usw., die mit Windows Server Active Directory vollständig kompatibel sind. Mit Azure AD-Domänendiensten können Sie diese Domänendienste nutzen, ohne dass Sie Domänencontroller in der Cloud bereitstellen, verwalten und patchen müssen. Azure AD-Domänendienste können in Ihren vorhandenen Azure AD-Mandanten integriert werden, wodurch es Benutzern ermöglicht wird, sich mit ihren Unternehmensanmeldeinformationen anzumelden. Außerdem können Sie vorhandene Gruppen und Benutzerkonten verwenden, um den Zugriff auf Ressourcen zu schützen. So stellen Sie sicher, dass die Verlagerung von lokalen Ressourcen auf Azure-Infrastrukturdienste reibungsloser verläuft.

Azure AD-Domänendienste arbeiten unabhängig davon nahtlos, ob Ihr Azure AD-Mandant auf die Cloud beschränkt ist oder mit Ihrer lokalen Active Directory-Instanz synchronisiert ist.

### Azure AD-Domänendienste für Organisationen mit vollständiger Umstellung auf die Cloud
Ein auf die Cloud beschränkter Azure AD-Mandant (häufig als „verwaltete Mandanten“ bezeichnet) verfügt nicht über einen lokalen Identitätsfußabdruck. Anders ausgedrückt: Benutzer, ihre Kennwörter und Gruppenmitgliedschaften liegen als systemeigene Daten in der Cloud vor und werden also in Azure AD erstellt und verwaltet. Stellen Sie sich vor, dass Contoso ein auf die Cloud beschränkter Azure AD-Mandant ist. Wie in der folgenden Abbildung dargestellt, hat der Administrator von Contoso ein virtuelles Netzwerk unter den Azure-Infrastrukturdiensten konfiguriert. Anwendungen und Serverworkloads werden in diesem virtuellen Netzwerk auf virtuellen Azure-Computern bereitgestellt. Da es sich bei Contoso um einen auf die Cloud beschränkten Mandanten handelt, werden alle Benutzeridentitäten, ihre Anmeldeinformationen und Gruppenmitgliedschaften in Azure AD erstellt und verwaltet.

![Azure AD-Domänendienste – Übersicht](./media/active-directory-domain-services-overview/aadds-overview.png)

Der IT-Administrator von Contoso kann Azure AD-Domänendienste für den Azure AD-Mandanten aktivieren und Domänendienste in diesem virtuellen Netzwerk zur Verfügung stellen. Nach der entsprechenden Konfiguration wird über die Azure AD-Domänendienste eine verwaltete Domäne bereitgestellt und im virtuellen Netzwerk verfügbar gemacht. Alle Benutzerkonten, Gruppenmitgliedschaften und Benutzeranmeldeinformationen, die im Azure AD-Mandanten von Contoso verfügbar sind, sind auch in dieser neu erstellten Domäne verfügbar. Mit diesem Feature können Benutzer sich mit ihren Unternehmensanmeldeinformationen an der Domäne anmelden, z. B. wenn per Remotedesktop eine Remoteverbindung mit Computern hergestellt wird, die der Domäne beigetreten sind. Administratoren können den Zugriff auf Ressourcen in der Domäne mit einer bereits vorhandenen Gruppenmitgliedschaft bereitstellen. Anwendungen, die auf virtuellen Computern im virtuellen Netzwerk bereitgestellt werden, profitieren von Domänendiensten, z. B. Domänenbeitritt, LDAP-Lesevorgang, LDAP-Bindung, NTLM- und Kerberos-Authentifizierung, Gruppenrichtlinie usw.

Einige wichtige Aspekte der verwalteten Domäne, die von den Azure AD-Domänendiensten bereitgestellt wird, lauten:

- Der IT-Administrator von Contoso muss diese Domäne oder die Domänencontroller für diese verwaltete Domäne nicht verwalten, patchen oder überwachen.
- Es ist nicht erforderlich, die AD-Replikation für diese Domäne zu verwalten. Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen für den Azure AD-Mandanten von Contoso sind innerhalb dieser verwalteten Domäne automatisch verfügbar.
- Da die Domäne über die Azure AD-Domänendienste verwaltet wird, verfügt der IT-Administrator von Contoso in dieser Domäne nicht über Domänenadministrator- oder Unternehmensadministratorrechte.


### Azure Active Directory-Domänendienste für Hybridunternehmen
Unternehmen mit einer IT-Hybridinfrastruktur nutzen eine Mischung aus Cloudressourcen und lokalen Ressourcen. In diesen Unternehmen werden Identitätsinformationen im lokalen Verzeichnis mit dem Azure AD-Mandanten synchronisiert. Wenn Hybridunternehmen mehr lokale Anwendungen zur Cloud migrieren möchten, vor allem verzeichnisorientierte Legacyanwendungen, können Azure AD-Domänendienste hierfür sehr hilfreich sein.

Die Litware Corporation hat [Azure AD Connect](../active-directory/active-directory-aadconnect.md) bereitgestellt, um Identitätsinformationen aus dem lokalen Verzeichnis mit dem Azure AD-Mandanten zu synchronisieren. Dies schließt auch Benutzerkonten, Hashes von Anmeldeinformationen für die Authentifizierung (Kennwortsynchronisierung) und Gruppenmitgliedschaften ein. Beachten Sie, dass die **Kennwortsynchronisierung für Hybridunternehmen für die Verwendung von Azure AD-Domänendiensten obligatorisch ist**. Dies liegt daran, dass die Anmeldeinformationen der Benutzer in der von den Azure AD-Domänendiensten bereitgestellten verwalteten Domäne erforderlich sind, um diese Benutzer mithilfe von NTLM- oder Kerberos-Authentifizierungsverfahren authentifizieren zu können.

![Azure AD-Domänendienste für Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

In der obigen Abbildung ist zu sehen, wie Unternehmen mit IT-Hybridinfrastruktur, z. B. die Litware Corporation, Azure AD-Domänendienste verwenden können. Die Anwendungen und Serverworkloads von Litware, die Domänendienste erfordern, werden in einem virtuellen Netzwerk unter den Azure-Infrastrukturdiensten bereitgestellt. Der IT-Administrator von Litware kann Azure AD-Domänendienste für den Azure AD-Mandanten aktivieren und eine verwaltete Domäne in diesem virtuellen Netzwerk zur Verfügung stellen. Da Litware ein Unternehmen mit einer IT-Hybridinfrastruktur ist, werden Benutzerkonten, Gruppen und Anmeldeinformationen aus dem lokalen Verzeichnis mit dem Azure AD-Mandanten synchronisiert. Mit diesem Feature können Benutzer sich mit ihren Unternehmensanmeldeinformationen an der Domäne anmelden, z. B. wenn per Remotedesktop eine Remoteverbindung mit Computern hergestellt wird, die der Domäne beigetreten sind. Administratoren können den Zugriff auf Ressourcen in der Domäne mit einer bereits vorhandenen Gruppenmitgliedschaft bereitstellen. Anwendungen, die auf virtuellen Computern im virtuellen Netzwerk bereitgestellt werden, profitieren von Domänendiensten, z. B. Domänenbeitritt, LDAP-Lesevorgang, LDAP-Bindung, NTLM- und Kerberos-Authentifizierung, Gruppenrichtlinie usw.

Einige wichtige Aspekte der verwalteten Domäne, die von den Azure AD-Domänendiensten bereitgestellt wird, lauten:

- Es handelt sich um eine eigenständige verwaltete Domäne. Sie stellt keine Erweiterung der lokalen Domäne von Litware dar.
- Der IT-Administrator von Litware muss diese Domäne oder die Domänencontroller für diese verwaltete Domäne nicht verwalten, patchen oder überwachen.
- Es ist nicht erforderlich, die AD-Replikation für diese Domäne zu verwalten. Benutzerkonten, Gruppenmitgliedschaften und Anmeldeinformationen aus dem lokalen Verzeichnis von Litware werden über Azure AD Connect mit Azure AD synchronisiert. Diese Elemente sind innerhalb dieser verwalteten Domäne automatisch verfügbar.
- Da die Domäne über die Azure AD-Domänendienste verwaltet wird, verfügt der IT-Administrator von Litware in dieser Domäne nicht über Domänenadministrator- oder Unternehmensadministratorrechte.


## Szenarios und Anwendungsfälle
In diesem Abschnitt betrachten wir einige Szenarios und Anwendungsfälle, die von der Verwendung von Azure Active Directory-Domänendiensten profitieren würden.

### Sichere und einfache Verwaltung von virtuellen Azure-Computern
Wenn Server und andere Infrastruktur das Ende ihrer Lebensdauer erreichen, verlagert Contoso viele Anwendungen in die Cloud, die derzeit noch lokal gehostet werden. Der aktuelle IT-Standard des Unternehmens gibt vor, dass Server, auf denen Unternehmensanwendungen gehostet werden, einer Domäne angehören und per Gruppenrichtlinie verwaltet werden müssen. Der IT-Administrator von Contoso zieht es vor, für unter Azure bereitgestellte virtuelle Computer den Beitritt zu einer Domäne durchzuführen, um die Verwaltung zu vereinfachen (Administratoren können sich also mit Unternehmensanmeldeinformationen anmelden). Contoso würde es vorziehen, Domänencontroller in Azure nicht bereitstellen, überwachen und verwalten zu müssen, um virtuelle Azure-Computer zu schützen.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Verwaltete Domänen, die von Azure AD-Domänendiensten bereitgestellt werden, unterstützen für Organisationseinheiten nur eine flache Struktur. Alle Computer, die einer Domäne beigetreten sind, befinden sich in einer einzelnen flachen Organisationseinheit, und hierarchische Strukturen für Organisationseinheiten werden nicht unterstützt.
- Azure AD-Domänendienste unterstützen jeweils eine einfache Gruppenrichtlinie in Form eines integrierten Gruppenrichtlinienobjekts (GPO) für Benutzer- und Computercontainer. Es ist nicht möglich, die Gruppenrichtlinie nach Organisationseinheit/Abteilung auszurichten, eine WMI-Filterung durchzuführen oder benutzerdefinierte Gruppenrichtlinienobjekte zu erstellen.
- Azure AD-Domänendienste unterstützen das grundlegende AD-Computerobjektschema. Sie können das Schema des Computerobjekts nicht erweitern.


### Verlagern einer lokalen Anwendung, die die LDAP-Bindungsauthentifizierung verwendet, auf Azure-Infrastrukturdienste
Contoso verfügt über eine lokale Anwendung, die vor vielen Jahren von einem Internetdienstanbieter gekauft wurde. Die Anwendung wurde vom Internetdienstanbieter in den Wartungsmodus versetzt, und das Anfordern von Änderungen an der Anwendung ist für Contoso gerade übermäßig teuer. Diese Anwendung verfügt über ein webbasiertes Front-End, mit dem Benutzeranmeldeinformationen per Web Form gesammelt werden, und anschließend werden Benutzer authentifiziert, indem eine LDAP-Bindung an das Active Directory des Unternehmens eingerichtet wird. Contoso möchte diese Anwendung zu den Azure-Infrastrukturdiensten migrieren. Hierbei ist es wünschenswert, dass die Anwendung funktioniert, ohne dass Änderungen erforderlich sind. Außerdem sollen sich Benutzer mit ihren vorhandenen Unternehmensanmeldeinformationen authentifizieren können, und es soll kein Schulungsaufwand zur Vermittlung einer anderen Vorgehensweise anfallen. Anders ausgedrückt: Für Endbenutzer soll nicht erkennbar bzw. relevant sein, wo die Anwendung ausgeführt wird, und die Migration sollte transparent sein.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf verwaltete Domänen, die von Azure AD-Domänendiensten bereitgestellt werden, wird nicht unterstützt.
- Benutzer können ihr Kennwort nicht direkt basierend auf der verwalteten Domäne ändern. Benutzer können ihr Kennwort entweder mithilfe des Self-Service-Verfahrens zum Ändern des Kennworts von Azure AD oder basierend auf dem lokalen Verzeichnis ändern. Diese Änderungen werden automatisch mit der verwalteten Domäne synchronisiert und sind darin verfügbar.


### Verlagern einer lokalen Anwendung, die den LDAP-Lesevorgang zum Zugreifen auf das Verzeichnis verwendet, auf Azure-Infrastrukturdienste
Contoso besitzt eine lokale Branchenanwendung, die vor fast zehn Jahren entwickelt wurde. Diese Anwendung ist verzeichnisorientiert und wurde für die Zusammenarbeit mit Windows Server AD entworfen. Die Anwendung nutzt LDAP (Lightweight Directory Access Protocol) zum Lesen von Informationen/Attributen der Benutzer über Active Directory. Von der Anwendung werden keine Attribute geändert, und es werden auch keine anderen Schreibvorgänge in das Verzeichnis durchgeführt. Contoso möchte diese Anwendung zu Azure-Infrastrukturdiensten migrieren und die ältere lokale Hardware aussondern, auf der diese Anwendung derzeit gehostet wird. Die Anwendung kann nicht für die Verwendung moderner Verzeichnis-APIs, z. B. die REST-basierte Azure AD Graph-API, umgeschrieben werden. Aus diesem Grund ist eine Verlagerungsoption („Lift-and-Shift“) hilfreich, bei der die Anwendung für die Ausführung in der Cloud migriert werden kann, ohne dass Code geändert oder die Anwendung umgeschrieben wird.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf verwaltete Domänen, die von Azure AD-Domänendiensten bereitgestellt werden, wird nicht unterstützt.
- Stellen Sie sicher, dass die Anwendung kein benutzerdefiniertes/erweitertes Active Directory-Schema benötigt. Schemaerweiterungen werden in Azure AD-Domänendiensten nicht unterstützt.
- Stellen Sie sicher, dass für die Anwendung kein LDAPS-Zugriff erforderlich ist. LDAPS wird von Azure AD-Domänendiensten nicht unterstützt.


### Migrieren eines lokalen Diensts oder einer Daemonanwendung zu Azure-Infrastrukturdiensten
Contoso verfügt über eine benutzerdefinierte Softwaretresor-Anwendung mit einem Web-Front-End, einem SQL-Server und einem Back-End-FTP-Server. Das Unternehmen nutzt die integrierte Windows-Authentifizierung per Dienstkonto, um das Web-Front-End gegenüber dem FTP-Server zu authentifizieren. Diese Anwendung soll auf die Azure-Infrastrukturdienste umgestellt werden, und es soll vermieden werden, in der Cloud einen virtuellen Domänencontroller-Computer bereitzustellen, um die Identitätsanforderungen dieser Anwendung zu unterstützen. Der IT-Administrator von Contoso kann die Server, auf denen das Web-Front-End gehostet wird, den SQL-Server und den FTP-Server auf virtuellen Computern in Azure bereitstellen und für diese Komponenten den Beitritt zu einer Domäne der Azure AD-Domänendienste durchführen. Anschließend kann dasselbe Dienstkonto im Verzeichnis für die Authentifizierungszwecke der App verwendet werden.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Stellen Sie sicher, dass die Anwendung für die Authentifizierung Benutzername und Kennwort verwendet. Die Authentifizierung per Zertifikat oder Smartcard wird von Azure AD-Domänendiensten nicht unterstützt.


## Vorteile
Mit Azure AD-Domänendiensten kommen Sie in den Genuss der folgenden Vorteile:

-	**Einfachheit:** Sie können die Identitätsanforderungen von virtuellen Computern, die für Azure-Infrastrukturdienste bereitgestellt werden, mit wenigen einfachen Klicks erfüllen und müssen keine Identitätsinfrastruktur in Azure bereitstellen und verwalten oder eine Verbindung zurück zur lokalen Identitätsinfrastruktur einrichten.

-	**Integration:** Die Azure AD-Domänendienste sind tief in Ihren Azure AD-Mandanten integriert. Sie können Azure AD jetzt als integriertes cloudbasiertes Unternehmensverzeichnis nutzen, das sowohl die Anforderungen Ihrer modernen Anwendungen als auch von herkömmlichen verzeichnisorientierten Anwendungen erfüllt.

-	**Kompatibilität:** Da Azure AD-Domänendienste auf der bewährten professionellen Infrastruktur von Windows Server Active Directory aufbauen, besteht für Ihre Anwendungen ein höherer Grad an Kompatibilität mit Windows Server Active Directory-Features. Beachten Sie, dass nicht alle in Windows Server AD verfügbaren Features derzeit in Azure AD-Domänendiensten verfügbar sind. Die verfügbaren Features sind aber mit den entsprechenden Windows Server AD-Features kompatibel, die Sie in Ihrer lokalen Infrastruktur nutzen. Die Funktionen für LDAP, Kerberos, NTLM, Gruppenrichtlinie und Domänenbeitritt, die von den Azure AD-Domänendiensten bereitgestellt werden, stellen ein ausgereiftes Angebot dar, das über mehrere Windows Server-Versionen hinweg getestet und optimiert wurde.

-	**Kosteneffektivität:** Mit Azure AD-Domänendiensten können Sie den Infrastruktur- und Verwaltungsaufwand vermeiden, der mit der Verwaltung der Identitätsinfrastruktur zur Unterstützung herkömmlicher verzeichnisorientierter Anwendungen verbunden ist. Sie können diese Anwendungen auf die Azure-Infrastrukturdienste verlagern und von höheren Einsparungen bei den Betriebskosten profitieren.

<!---HONumber=Oct15_HO3-->