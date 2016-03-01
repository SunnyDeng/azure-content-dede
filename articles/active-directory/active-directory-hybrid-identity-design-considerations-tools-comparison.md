<properties
	pageTitle="Hybrid-Identität: Vergleich von Tools für die Verzeichnisintegration | Microsoft Azure"
	description="Diese Seite enthält umfassende Tabellen zum Vergleich der verschiedenen Tools für die Verzeichnisintegration, die für die Verzeichnisintegration verwendet werden können."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/23/2016"
	ms.author="billmath"/>

# Vergleich von Tools für die Verzeichnisintegration für Hybrid-Identitäten

Im Laufe der Jahre sind die Tools für die Verzeichnisintegration umfangreicher geworden und wurden weiterentwickelt. Das vorliegende Dokument bietet eine Übersicht über diese Tools und einen Vergleich der Features, die jedes der Tools bietet.

>[AZURE.NOTE] Azure AD Connect umfasst die Komponenten und Funktionen, die zuvor als DirSync und AAD Sync veröffentlicht wurden. Diese Tools werden nicht mehr einzeln veröffentlicht, und alle künftigen Verbesserungen werden in den Updates von Azure AD Connect enthalten sein, sodass Sie immer wissen, wo Sie die aktuellsten Funktionen bekommen.
>
>DirSync wird zurzeit noch unterstützt, soll jedoch in naher Zukunft als veraltet eingestuft werden. Sobald dies geschieht, wird DirSync nur noch für einen begrenzten Zeitraum unterstützt. Nach Ablauf dieses Zeitraums endet die Unterstützung für DirSync .


Verwenden Sie den folgenden Schlüssel für jede der Tabellen.

● = Jetzt verfügbar BV = Bald verfügbar ÖV = Öffentliche Vorschau

## Synchronisierung vom lokalen Standort zur Cloud

| Funktion | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste (AAD Sync) | Azure Active Directory-Synchronisierungstool (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Herstellen einer Verbindung mit einer einzelnen lokalen AD-Gesamtstruktur | ● | ● | ● | ● |● |
| Herstellen einer Verbindung mit mehreren lokalen AD-Gesamtstrukturen |● | ● | | ● |● |
| Herstellen einer Verbindung mit mehreren lokalen Exchange-Organisationen | ● | | | | |
| Herstellen einer Verbindung mit einem einzelnen lokalen LDAP-Verzeichnis | BV | | | ● |● |
| Herstellen einer Verbindung mit mehreren lokalen LDAP-Verzeichnissen |BV | | | ● |● |
| Herstellen einer Verbindung mit lokalen Active Directory- und LDAP-Verzeichnissen |BV | | | ● |● |
| Herstellen einer Verbindung mit benutzerdefinierten Systeme (z. B. SQL, Oracle, MySQL usw.) | BV | | | ● |● |
| Synchronisieren von benutzerdefinierten Attributen (Verzeichniserweiterungen) | ● | | | | |
|Verbinden mit lokaler HR-Instanz (d. h. SAP, Oracle eBusiness,PeopleSoft)| BV | | | ● |● |
|Unterstützt FIM-Synchronisierungsregeln und -Connectors für die Bereitstellung lokaler Systeme.| | | | ● |● |

## Synchronisierung von der Cloud zum lokalen Standort

| Funktion | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste | Azure Active Directory-Synchronisierungstool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Rückschreiben von Geräten | ● | | ● | ||
| Rückschreiben von Attributen (für Exchange-Hybridbereitstellung) | ● | ● | ● | ● |● |
| Rückschreiben von Benutzer- und Gruppenobjekten | ●| | | ||
| Rückschreiben von Kennwörtern (Self-Service-Kennwortzurücksetzung und Kennwortänderung) | ● | ● | | ||



## Unterstützung von Authentifizierungsfunktionen

| Funktion | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste | Azure Active Directory-Synchronisierungstool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Kennwortsynchronisierung für eine einzelne lokale Active Directory-Gesamtstruktur | ● | ● | ● | ||
| Kennwortsynchronisierung für mehrere lokale Active Directory-Gesamtstrukturen | ●| ● | | ||
| Einmaliges Anmelden mit Verbund | ● | ● | ● | ● |● |
| Rückschreiben von Kennwörtern (Self-Service-Kennwortzurücksetzung und Kennwortänderung) |● | ● | | ||



## Einrichtung und Installation

| Funktion | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste | Azure Active Directory-Synchronisierungstool (DirSync) | Microsoft Identity Manager 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Unterstützt die Installation auf einem Domänencontroller | ● | ● | ● | |
| Unterstützt die Installation mit SQL Express | ● | ● | ● | |
| Einfaches Upgrade von DirSync |● | | | |
| Lokalisierung der Administrator-UX in die Windows Server-Sprachen | ● | ● | ● | |
|Lokalisierung der Endbenutzer-UX in die Windows Server-Sprachen| | | |● |
| Unterstützung für Windows Server 2008 und Windows Server 2008 R2 | ● für Synchronisierung, nicht für den Verbund| ● | ● | ● |
| Unterstützung für Windows Server 2012 und Windows Server 2012 R2 | ● | ● | ● | ● |

## Filterung und Konfiguration

Funktion | Azure Active Directory Connect | Azure Active Directory-Synchronisierungsdienste | Azure Active Directory-Synchronisierungstool (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Microsoft Identity Manager 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filterung nach für Domänen und Organisationseinheiten | ● | ● | ● | ● | ● 
Filterung nach den Attributwerten eines Objekts | ● | ● | ● | ●| ● 
Zulassen eines minimalen Attributsatzes für die Synchronisierung (MinSync) | ● | ● | ||
Zulassen der Anwendung verschiedener Dienstvorlagen auf Attributflüsse |● | ● | ||
Zulassen der Entfernung von Attributen aus dem Attributfluss von AD nach Azure AD | ● | ● | | |
Zulassen einer erweiterten Anpassung des Attributflusses | ● | ● | | ● | ● 

## Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0224_2016-->