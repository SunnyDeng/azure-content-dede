<properties
	pageTitle="Geräterichtlinien für den bedingten Zugriff auf Office 365-Dienste | Microsoft Azure"
	description="In diesem Artikel werden Informationen dazu bereitgestellt, wie mithilfe von gerätebasierten Bedingungen der Zugriff auf Office 365-Dienste gesteuert wird. Während Information-Worker (IW) bei der Arbeit oder in der Schule über ihre persönlichen Geräte auf Office 365-Dienste wie z. B. Exchange und SharePoint Online zugreifen möchten, hat der IT-Administrator vor allem die Sicherheit beim Zugriff im Blick. IT-Administratoren können Geräterichtlinien für den bedingten Zugriff bereitstellen, um Unternehmensressourcen zu schützen, und gleichzeitig Information-Workern auf kompatiblen Geräten den Zugriff auf die Dienste gestatten."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>
# Geräterichtlinien für den bedingten Zugriff auf Office 365-Dienste

Mit dem Konzept des "bedingten Zugriff" sind viele Aspekte verknüpft, beispielsweise die mehrstufige Authentifizierung, authentifizierte Geräte, kompatible Geräte usw. In diesem Thema werden vor allem gerätebasierte Bedingungen behandelt, die zum Steuern des Zugriffs auf Office 365-Dienste verwendet werden. Während Information-Worker (IW) bei der Arbeit oder in der Schule über ihre persönlichen Geräte auf Office 365-Dienste wie z. B. Exchange und SharePoint Online zugreifen möchten, hat der IT-Administrator vor allem die Sicherheit beim Zugriff im Blick. IT-Administratoren können Geräterichtlinien für den bedingten Zugriff bereitstellen, um Unternehmensressourcen zu schützen, und gleichzeitig Information-Workern auf kompatiblen Geräten den Zugriff auf die Dienste gestatten. Richtlinien für den bedingten Zugriff auf Office 365 können im Microsoft Intune-Portal für den bedingten Zugriff konfiguriert werden.

Azure Active Directory erzwingt Richtlinien für den bedingten Zugriff, um den Zugriff auf Office 365-Dienste zu sichern. Ein Mandantenadministrator kann eine Richtlinie für den bedingten Zugriff erstellen, mit der ein Benutzer daran gehindert wird, von einem nicht kompatiblen Gerät auf einen Office 365-Dienst zuzugreifen. Der Benutzer muss zunächst die Geräterichtlinien des Unternehmens erfüllen, bevor ihm der Zugriff auf den Dienst erteilt werden kann. Alternativ kann der Administrator auch eine Richtlinie erstellen, die von den Benutzern lediglich eine Registrierung ihrer Geräte verlangt, um Zugriff auf einen Office 365-Dienst zu erhalten. Richtlinien können auf alle Benutzer einer Organisation angewendet oder auf wenige Zielgruppen beschränkt und im Laufe der Zeit auf weitere Zielgruppen ausgedehnt werden.

Eine Voraussetzung für die Umsetzung von Geräterichtlinien ist, dass Benutzer ihre Geräte beim Azure Active Directory-Geräteregistrierungsdienst registrieren müssen. Sie haben die Möglichkeit, die Multi-Factor Authentication (MFA) zum Registrieren von Geräten beim Azure Active Directory-Geräteregistrierungsdienst zu aktivieren. MFA wird für den Azure Active Directory-Geräteregistrierungsdienst empfohlen. Wenn MFA aktiviert ist, müssen Benutzer, die ihre Geräte beim Azure Active Directory-Geräteregistrierungsdienst registrieren, eine zweistufige Authentifizierung durchlaufen.

##Wie funktioniert die Richtlinie für den bedingten Zugriff?

Wenn ein Benutzer über eine unterstützte Geräteplattform Zugriff auf einen Office 365-Dienst anfordert, authentifiziert Azure Active Directory den Benutzer und das Gerät, auf dem der Benutzer die Anforderung startet. Es wird nur dann Zugriff auf den Dienst gewährt, wenn der Benutzer die für den Dienst festgelegte Richtlinie erfüllt. Benutzer, deren Gerät nicht registriert ist, erhalten Anweisungen, wie sie eine Registrierung durchführen und zum Zugriff auf Office 365-Unternehmensdienste berechtigt werden können. Benutzer von iOS- und Android-Geräten müssen ihre Geräte über das Unternehmensportal registrieren. Wenn ein Benutzer sein Gerät anmeldet, wird das Gerät in Azure Active Directory registriert und für Geräteverwaltung und Compliance angemeldet. Kunden müssen den Azure Active Directory-Geräteregistrierungsdienst in Verbindung mit Microsoft Intune verwenden, um die Verwaltung mobiler Geräte für den Office 365-Dienst zu ermöglichen. Die Geräteregistrierung ist eine Voraussetzung dafür, dass Benutzer auf Office 365-Dienste zugreifen können, wenn Geräterichtlinien erzwungen werden.

Wenn ein Benutzer sein Gerät erfolgreich registriert, wird das Gerät als vertrauenswürdig eingestuft. Azure Active Directory bietet die einmalige Anmeldung (SSO) für den Zugriff auf Unternehmensanwendungen und erzwingt die Richtlinie für den bedingten Zugriff auf einen Dienst. Dies gilt nicht nur für den ersten Benutzerzugriff, sondern jedes Mal, wenn der Benutzer eine Erneuerung des Zugriffs anfordert. Dem Benutzer wird der Zugriff auf Dienste verweigert, wenn Anmeldeinformationen geändert werden, das Gerät verloren geht/gestohlen wird oder die Richtlinie beim Anfordern der Erneuerung des Zugriffs nicht erfüllt wird.

## Überlegungen zur Bereitstellung
Für die Geräteregistrierung muss der Azure Active Directory-Geräteregistrierungsdienst verwendet werden.

Wenn Benutzer lokal authentifiziert werden sollen, sind hierzu die Active Directory-Verbunddienste (AD FS) (1.0 und höher) erforderlich. MFA für die Arbeitsbereichverknüpfung führt zu einem Fehler, wenn der Identitätsanbieter MFA nicht unterstützt. AD FS 2.0 beispielsweise bietet keine Unterstützung für die mehrstufige Authentifizierung. Der Mandantenadministrator muss sicherstellen, dass die lokale AD FS-Umgebung MFA-fähig ist und eine gültige MFA-Methode aktiviert wurde, bevor MFA im Azure Active Directory-Geräteregistrierungsdienst aktiviert wird. AD FS unter Windows Server 2012 R2 stellt z. B. MFA-Funktionen bereit. Sie müssen außerdem eine zusätzliche gültige Authentifizierungsmethode (MFA) auf dem AD FS-Server aktivieren, bevor Sie MFA im Azure Active Directory-Geräteregistrierungsdienst aktivieren. Weitere Informationen zu unterstützten MFA-Methoden in AD FS finden Sie unter "Konfigurieren zusätzlicher Authentifizierungsmethoden für AD FS".

## Häufig gestellte Fragen (FAQ)

F: Wie lautet die standardmäßige Ausschlussrichtlinie für nicht unterstützte Geräteplattformen?

A: Derzeit werden Richtlinien für den bedingten Zugriff selektiv für Benutzer von iOS- und Android-Geräten erzwungen. Anwendungen auf anderen Geräteplattformen sind standardmäßig von der Richtlinie für den bedingten Zugriff für iOS- und Android-Geräte ausgenommen. Der Mandantenadministrator kann die globale Richtlinie jedoch außer Kraft setzen, um Benutzern auf nicht unterstützten Plattformen den Zugriff zu verweigern. Es ist geplant, die Richtlinie für den bedingten Zugriff auf Benutzer auf anderen Plattformen (einschließlich Windows) auszuweiten.

F: Wann wird die Richtlinie für den bedingten Zugriff auf Office 365-Dienste auf browserbasierte Apps (z. B. OWA, browserbasiertes SharePoint) ausgeweitet?

Zurzeit ist der bedingte Zugriff auf Office 365-Dienste auf Geräteanwendungen beschränkt. Es ist geplant, die Richtlinie für den bedingten Zugriff auf Benutzer auszuweiten, die über Browser auf die Dienste zugreifen.

<!---HONumber=AcomDC_1125_2015-->