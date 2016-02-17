<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Anforderungen in Bezug auf die Verzeichnissynchronisierung | Microsoft Azure"
	description="Identifizieren Sie, welche Anforderungen für die Synchronisierung aller Benutzer zwischen lokalen Speicherorten und Cloudspeicherorten für das Unternehmen gelten."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/02/2016"
	ms.author="billmath"/>

# Ermitteln der Anforderungen an die Verzeichnissynchronisierung
Bei der Synchronisierung geht es um das Bereitstellen einer Identität für Benutzer in der Cloud, und zwar basierend auf ihrer lokalen Identität. Benutzer müssen unabhängig davon, ob sie das synchronisierte Konto für die Authentifizierung oder die Verbundauthentifizierung verwenden, über eine Identität in der Cloud verfügen. Diese Identität muss regelmäßig verwaltet und aktualisiert werden. Die Updates können viele Formen annehmen – von der Änderung des Titels bis zur Änderung des Kennworts.

Beginnen Sie, indem Sie für das Unternehmen die lokale Identitätslösung und die Benutzeranforderungen auswerten. Diese Auswertung ist wichtig, um die technischen Anforderungen dafür zu definieren, wie Benutzeridentitäten in der Cloud erstellt und verwaltet werden. Für die Mehrzahl der Unternehmen wird Active Directory lokal verwendet und dient als lokales Verzeichnis, über das Benutzer synchronisiert werden. Dies ist aber nicht immer der Fall.

Beantworten Sie die folgenden Fragen:


- Haben Sie eine, keine oder mehrere Active Directory-Gesamtstrukturen?
 - Für wie viele Azure AD-Verzeichnisse wird die Synchronisierung durchgeführt?
 
    1. Nutzen Sie die Filterung?
    2. Haben Sie mehrere Azure AD Connect-Server geplant?
  
- Verwenden Sie derzeit lokal ein Synchronisierungstool?
  - Wenn ja: Ist für Ihre Benutzer ein virtuelles Verzeichnis bzw. eine Integration der Identitäten vorhanden?
- Sind lokal noch weitere Verzeichnisse vorhanden, die synchronisiert werden sollen (z. B. LDAP-Verzeichnis, HR-Datenbank usw.)?
  - Sollen GALSync-Vorgänge durchgeführt werden?
  - Welchen aktuellen Stand haben UPNs in Ihrem Unternehmen? 
  - Verwenden Sie ein anderes Verzeichnis, das für die Authentifizierung der Benutzer eingesetzt wird?
  - Wird in Ihrem Unternehmen Microsoft Exchange verwendet?
    - Ist eine Hybrid-Exchange-Bereitstellung geplant? 
   
Nachdem Sie nun Erkenntnisse zu den Synchronisierungsanforderungen für ihr Unternehmen gewonnen haben, müssen Sie die Anwendungen auswerten, von denen diese Verzeichnisdienste verwendet werden. Diese Auswertung ist wichtig, um die technischen Anforderungen zum Integrieren dieser Anwendungen in die Cloud zu definieren. Beantworten Sie die folgenden Fragen:

- Werden diese Anwendungen in die Cloud verschoben, und soll das Verzeichnis dafür genutzt werden?
- Gibt es spezielle Attribute, die mit der Cloud synchronisiert werden müssen, damit sie von den Anwendungen erfolgreich genutzt werden können?
- Müssen diese Anwendungen umgeschrieben werden, um die Cloudauthentifizierung nutzen zu können?
- Werden diese Anwendungen weiterhin lokal vorgehalten, während Benutzer mit der Cloud-Identität darauf zugreifen?

Außerdem müssen Sie die Sicherheitsanforderungen und Einschränkungen der Verzeichnissynchronisierung ermitteln. Diese Auswertung ist wichtig, um eine Liste mit den Anforderungen zu erhalten, die zum Erstellen und Verwalten von Benutzeridentitäten in der Cloud benötigt werden. Beantworten Sie die folgenden Fragen:

- Wo soll sich der Synchronisierungsserver befinden?
- Soll er Mitglied einer Domäne sein?
- Befindet sich der Server in einem eingeschränkten Netzwerk hinter einer Firewall, z. B. einer DMZ?
  - Können Sie die erforderlichen Firewallports öffnen, um die Synchronisierung zu unterstützen?
- Verfügen Sie für den Synchronisierungsserver über einen Plan für die Notfallwiederherstellung?
- Verfügen Sie über ein Konto mit den richtigen Berechtigungen für alle Gesamtstrukturen, für die eine Synchronisierung durchgeführt werden soll?
 - Falls Sie in Ihrem Unternehmen diese Frage nicht beantworten können, helfen Ihnen die Informationen im Abschnitt „Berechtigungen für die Kennwortsynchronisierung“ des Artikels [Installieren des Azure Active Directory-Synchronisierungsdiensts](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) weiter. Mit diesen Informationen können Sie ermitteln, ob bereits ein Konto mit diesen Berechtigungen vorhanden ist oder ob Sie ein Konto erstellen müssen.
- Kann der Synchronisierungsserver auf alle Gesamtstrukturen zugreifen, wenn bei Ihnen mehrere Gesamtstrukturen an der Synchronisierung beteiligt sind?
 
>[AZURE.NOTE]
Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. Unter [Bestimmen der Anforderungen an Reaktionen auf Vorfälle](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) sind die verfügbaren Optionen beschrieben. Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.

## Nächste Schritte
[Ermitteln der Anforderungen für die Multi-Factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0204_2016-->