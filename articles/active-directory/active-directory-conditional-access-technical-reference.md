
<properties
	pageTitle="Technische Referenz: Bedingter Zugriff auf Azure AD-Apps | Microsoft Azure"
	description="Mit der bedingten Zugriffssteuerung überprüft Azure Active Directory die besonderen Bedingungen, die Sie beim Authentifizieren des Benutzers und vor dem Gewähren des Zugriffs auf die Anwendung auswählen. Nachdem diese Bedingungen erfüllt sind, wird der Benutzer authentifiziert und erhält Zugriff auf die Anwendung."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/11/2016"
	ms.author="femila"/>

# Technische Referenz: Bedingter Zugriff auf Azure AD-Apps

## Dienste mit aktiviertem bedingtem Zugriff
Bedingte Zugriffsregeln werden über verschiedene Arten von Azure AD-Anwendungen hinweg unterstützt. Diese Liste enthält:

- Verbundanwendungen aus dem Azure AD-Anwendungskatalog
- Kennwort-SSO-Anwendungen aus dem Azure AD-Anwendungskatalog
- Beim Azure-Anwendungsproxy registrierte Anwendungen
- Entwickelte branchenspezifische und mehrinstanzenfähige Anwendungen, die bei Azure AD registriert sind
- Visual Studio Online
- Azure RemoteApp

## Aktivieren von Zugriffsregeln

Jede Regel kann auf Anwendungsbasis aktiviert oder deaktiviert werden. Wenn Regeln auf „ON“ festgelegt sind, sind sie aktiviert und werden für Benutzer durchgesetzt, die auf die Anwendung zugreifen. Wenn sie auf „OFF“ festgelegt sind, werden sie nicht verwendet und besitzen keine Auswirkungen auf den Anmeldevorgang der Benutzer.

## Anwenden von Regeln auf bestimmte Benutzer
Die Regeln können nach Sicherheitsgruppen auf bestimmte Gruppen von Benutzern festgelegt werden, indem Sie „Anwenden auf“ einstellen. „Anwenden auf“ kann auf „Alle Benutzer“ oder auf „Gruppen“ festgelegt werden. Bei der Einstellung „Alle Benutzer“ gelten die Regeln für alle Benutzer mit Zugriff auf die Anwendung. Die Option „Gruppen“ ermöglicht die Auswahl bestimmter Sicherheits- und Verteilergruppen, für die die Regeln durchgesetzt werden. Wenn Sie eine Regel erstmals bereitstellen, ist es üblich, sie zuerst auf eine begrenzte Anzahl von Benutzern anzuwenden, die Mitglieder einer Pilotgruppe sind. Nach der Fertigstellung kann die Regel auf „Alle Benutzer“ angewendet werden. Dadurch wird die Regel für alle Benutzer in der Organisation erzwungen. Mithilfe der Option „Ausgenommen“ können ausgewählte Gruppen auch von der Richtlinie ausgenommen werden. Dies gilt für alle Mitglieder der jeweiligen Gruppen, selbst dann, wenn sie in einer eingeschlossenen Gruppe angezeigt werden.

## Arbeitsnetzwerke

Regeln für den bedingten Zugriff, die ein Arbeitsnetzwerk verwenden, greifen auf vertrauenswürdige IP-Adressbereiche zurück, die in Azure AD konfiguriert wurden. Zu diesen Regeln gehören:

- Erfordert mehrstufige Authentifizierung, wenn nicht bei der Arbeit
- Zugriff blockieren, wenn nicht gearbeitet wird

Vertrauenswürdige IP-Adressbereiche können auf der [Konfigurationsseite für die mehrstufige Authentifizierung](../multi-factor-authentication/multi-factor-authentication-whats-next.md) konfiguriert werden. Die Richtlinie für den bedingten Zugriff verwendet die konfigurierten Bereiche für jede Authentifizierungsanfrage und Tokenausstellung zum Auswerten von Regeln. Der innerhalb des Unternehmensnetzwerks geltende Anspruch wird nicht verwendet, da er für längere Sitzungen, z. B. Aktualisierungstoken in mobilen Anwendungen, nicht verfügbar ist.

## Regeln pro Anwendung
Regeln werden für jede Anwendung konfiguriert, damit hochwertige Dienste abgesichert werden, ohne den Zugriff auf andere Dienste zu beeinträchtigen. Regeln für den bedingten Zugriff können auf der Registerkarte „Konfigurieren“ der Anwendung konfiguriert werden.

Die aktuell angebotenen Regeln sind:

- Erfordern von Multi-Factor Authentication
 - Alle Benutzer, auf die die Richtlinie angewendet wird, müssen mindestens einmal die mehrstufige Authentifizierung durchgeführt haben.
- Erfordert mehrstufige Authentifizierung, wenn nicht bei der Arbeit
 - Alle Benutzer, auf die die Richtlinie angewendet wird, müssen mindestens einmal die mehrstufige Authentifizierung durchgeführt haben, wenn sie von einem Remotestandort aus auf den Dienst zugreifen. Wenn sie von einem Unternehmensstandort an einen Remotestandort wechseln, müssen sie beim Zugriff auf den Dienst die mehrstufige Authentifizierung ausführen.
- Zugriff blockieren, wenn nicht gearbeitet wird 
 - Alle Benutzer, auf die die Richtlinie angewendet wird, werden beim Zugriff auf den Dienst von einem Remotestandort aus gesperrt. Dies gilt auch, wenn sie von einem Unternehmensstandort an einen Remotestandort wechseln. Wenn sie sich an einem Unternehmensstandort befinden, wird der Zugriff gewährt.

<!---HONumber=AcomDC_0218_2016-->