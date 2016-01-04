<properties
	pageTitle="Vorschau für Azure Active Directory-Domänendienste: Bereitstellungsszenarios | Microsoft Azure"
	description="Bereitstellungsszenarios für Azure Active Directory-Domänendienste"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="maheshu"/>


# Bereitstellungsszenarios und Anwendungsfälle
In diesem Abschnitt betrachten wir einige Szenarios und Anwendungsfälle, die von der Verwendung von Azure Active Directory-Domänendiensten (AD) profitieren würden.

## Sichere und einfache Verwaltung von virtuellen Azure-Computern
Wenn Server und andere Infrastruktur das Ende ihrer Lebensdauer erreichen, verlagert Contoso viele Anwendungen in die Cloud, die derzeit noch lokal gehostet werden. Der aktuelle IT-Standard des Unternehmens gibt vor, dass Server, auf denen Unternehmensanwendungen gehostet werden, einer Domäne angehören und per Gruppenrichtlinie verwaltet werden müssen. Der IT-Administrator von Contoso zieht es vor, für unter Azure bereitgestellte virtuelle Computer den Beitritt zu einer Domäne durchzuführen, um die Verwaltung zu vereinfachen (Administratoren können sich also mit Unternehmensanmeldeinformationen anmelden). Contoso würde es vorziehen, Domänencontroller in Azure nicht bereitstellen, überwachen und verwalten zu müssen, um virtuelle Azure-Computer zu schützen.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Verwaltete Domänen, die von Azure AD-Domänendiensten bereitgestellt werden, unterstützen für Organisationseinheiten nur eine flache Struktur. Alle Computer, die einer Domäne beigetreten sind, befinden sich in einer einzelnen flachen Organisationseinheit, und hierarchische Strukturen für Organisationseinheiten werden nicht unterstützt.
- Azure AD-Domänendienste unterstützen jeweils eine einfache Gruppenrichtlinie in Form eines integrierten Gruppenrichtlinienobjekts (GPO) für Benutzer- und Computercontainer. Es ist nicht möglich, die Gruppenrichtlinie nach Organisationseinheit/Abteilung auszurichten, eine WMI-Filterung durchzuführen oder benutzerdefinierte Gruppenrichtlinienobjekte zu erstellen.
- Azure AD-Domänendienste unterstützen das grundlegende AD-Computerobjektschema. Sie können das Schema des Computerobjekts nicht erweitern.


## Verlagern einer lokalen Anwendung, die die LDAP-Bindungsauthentifizierung verwendet, auf Azure-Infrastrukturdienste
Contoso verfügt über eine lokale Anwendung, die vor vielen Jahren von einem Internetdienstanbieter gekauft wurde. Die Anwendung wurde vom Internetdienstanbieter in den Wartungsmodus versetzt, und das Anfordern von Änderungen an der Anwendung ist für Contoso gerade übermäßig teuer. Diese Anwendung verfügt über ein webbasiertes Front-End, mit dem Benutzeranmeldeinformationen per Web Form gesammelt werden, und anschließend werden Benutzer authentifiziert, indem eine LDAP-Bindung an das Active Directory des Unternehmens eingerichtet wird. Contoso möchte diese Anwendung zu den Azure-Infrastrukturdiensten migrieren. Hierbei ist es wünschenswert, dass die Anwendung funktioniert, ohne dass Änderungen erforderlich sind. Außerdem sollen sich Benutzer mit ihren vorhandenen Unternehmensanmeldeinformationen authentifizieren können, und es soll kein Schulungsaufwand zur Vermittlung einer anderen Vorgehensweise anfallen. Anders ausgedrückt: Für Endbenutzer soll nicht erkennbar bzw. relevant sein, wo die Anwendung ausgeführt wird, und die Migration sollte transparent sein.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf verwaltete Domänen, die von Azure AD-Domänendiensten bereitgestellt werden, wird nicht unterstützt.
- Benutzer können ihr Kennwort nicht direkt basierend auf der verwalteten Domäne ändern. Benutzer können ihr Kennwort entweder mithilfe des Self-Service-Verfahrens zum Ändern des Kennworts von Azure AD oder basierend auf dem lokalen Verzeichnis ändern. Diese Änderungen werden automatisch mit der verwalteten Domäne synchronisiert und sind darin verfügbar.


## Verlagern einer lokalen Anwendung, die den LDAP-Lesevorgang zum Zugreifen auf das Verzeichnis verwendet, auf Azure-Infrastrukturdienste
Contoso besitzt eine lokale Branchenanwendung, die vor fast zehn Jahren entwickelt wurde. Diese Anwendung ist verzeichnisorientiert und wurde für die Zusammenarbeit mit Windows Server AD entworfen. Die Anwendung nutzt LDAP (Lightweight Directory Access Protocol) zum Lesen von Informationen/Attributen der Benutzer über Active Directory. Von der Anwendung werden keine Attribute geändert, und es werden auch keine anderen Schreibvorgänge in das Verzeichnis durchgeführt. Contoso möchte diese Anwendung zu Azure-Infrastrukturdiensten migrieren und die ältere lokale Hardware aussondern, auf der diese Anwendung derzeit gehostet wird. Die Anwendung kann nicht für die Verwendung moderner Verzeichnis-APIs, z. B. die REST-basierte Azure AD Graph-API, umgeschrieben werden. Aus diesem Grund ist eine Verlagerungsoption („Lift-and-Shift“) hilfreich, bei der die Anwendung für die Ausführung in der Cloud migriert werden kann, ohne dass Code geändert oder die Anwendung umgeschrieben wird.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Stellen Sie sicher, dass es für die Anwendung nicht erforderlich ist, das Verzeichnis zu ändern oder in das Verzeichnis zu schreiben. Der LDAP-Schreibzugriff auf verwaltete Domänen, die von Azure AD-Domänendiensten bereitgestellt werden, wird nicht unterstützt.
- Stellen Sie sicher, dass die Anwendung kein benutzerdefiniertes/erweitertes Active Directory-Schema benötigt. Schemaerweiterungen werden in Azure AD-Domänendiensten nicht unterstützt.
- Stellen Sie sicher, dass für die Anwendung kein LDAPS-Zugriff erforderlich ist. LDAPS wird von Azure AD-Domänendiensten nicht unterstützt.


## Migrieren eines lokalen Diensts oder einer Daemonanwendung zu Azure-Infrastrukturdiensten
Contoso verfügt über eine benutzerdefinierte Softwaretresor-Anwendung mit einem Web-Front-End, einem SQL-Server und einem Back-End-FTP-Server. Das Unternehmen nutzt die integrierte Windows-Authentifizierung per Dienstkonto, um das Web-Front-End gegenüber dem FTP-Server zu authentifizieren. Diese Anwendung soll auf die Azure-Infrastrukturdienste umgestellt werden, und es soll vermieden werden, in der Cloud einen virtuellen Domänencontroller-Computer bereitzustellen, um die Identitätsanforderungen dieser Anwendung zu unterstützen. Der IT-Administrator von Contoso kann die Server, auf denen das Web-Front-End gehostet wird, den SQL-Server und den FTP-Server auf virtuellen Computern in Azure bereitstellen und für diese Komponenten den Beitritt zu einer Domäne der Azure AD-Domänendienste durchführen. Anschließend kann dasselbe Dienstkonto im Verzeichnis für die Authentifizierungszwecke der App verwendet werden.

Beachten Sie die folgenden wichtigen Punkte, wenn Sie dieses Szenario in Betracht ziehen:

- Stellen Sie sicher, dass die Anwendung für die Authentifizierung Benutzername und Kennwort verwendet. Die Authentifizierung per Zertifikat oder Smartcard wird von Azure AD-Domänendiensten nicht unterstützt.

<!---HONumber=AcomDC_1217_2015-->