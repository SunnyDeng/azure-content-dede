<properties
	pageTitle="Azure AD Connect-Synchronisierung: Die Synchronisierung verstehen und anpassen | Microsoft Azure"
	description="Erläutert die Funktionsweise und Anpassung der Azure AD Connect-Synchronisierung."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung
Die Azure Active Directory Connect-Synchronisierungsdienste (Azure AD Connect-Synchronisierung) sind eine Hauptkomponente von Azure AD Connect. Diese Dienste wickeln alle Vorgänge ab, die mit dem Synchronisieren von Identitätsdaten zwischen Ihrer lokalen Umgebung und Azure AD in der Cloud zusammenhängen. Azure AD Connect Sync ist der Nachfolger von DirSync, Azure AD Sync und Forefront Identity Manager mit konfiguriertem Azure Active Directory-Connector.

Dieses Thema ist das zentrale Thema für **Azure AD Connect Sync** (auch als **„Synchronisierungsmodul“ bezeichnet**) und enthält eine Liste mit Links zu allen anderen verwandten Themen. Links zu Azure AD Connect finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).

## Azure AD Connect Sync-Themen

| Thema | Inhalt und Relevanz |
| ----- | ----- |
| **Azure AD Connect Sync-Grundlagen** ||
| [Grundlagen der Architektur](active-directory-aadconnectsync-understanding-architecture.md) | Für Benutzer bestimmt, die sich noch nicht mit dem Synchronisierungsmodul auskennen und sich über die Architektur und die verwendeten Ausdrücke informieren möchten. |
| [Technische Konzepte ](active-directory-aadconnectsync-technical-concepts.md) | Eine Kurzversion des Themas zur Architektur mit einer kurzen Erklärung der verwendeten Ausdrücke. |
| [Topologien für Azure AD Connect](active-directory-aadconnect-topologies.md) | Beschreibt die verschiedenen Topologien und Szenarien, die vom Synchronisierungsmodul unterstützt werden. |
| **Benutzerdefinierte Konfiguration** ||
| [Grundlegendes zur Standardkonfiguration](active-directory-aadconnectsync-understanding-default-configuration.md)| Beschreibt die Standardregeln und die Standardkonfiguration. Außerdem wird beschrieben, wie die Regeln zusammenarbeiten, damit die Standardszenarien funktionieren. |
| [Grundlegendes zu Benutzern und Kontakten](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Ist die Fortsetzung des vorherigen Themas und beschreibt, wie die Konfiguration für Benutzer und Kontakte zusammen funktioniert, vor allem in einer Umgebung mit mehreren Gesamtstrukturen. |
| [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Es wird ausführlich beschrieben, wie das Konfigurationsmodell funktioniert und wie die Syntax für die Ausdruckssprache verwendet wird. |
| [Bewährte Methoden zum Ändern der Standardkonfiguration](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Wenn Sie die Details der obigen Themen kennen und Änderungen an der Standardkonfiguration vornehmen möchten, damit Ihr Szenario funktioniert bzw. Ihre Anforderungen erfüllt werden, helfen Ihnen die Informationen in diesem Thema weiter. |
| [Konfigurieren der Filterung](active-directory-aadconnectsync-configure-filtering.md) | Beschreibt die verschiedenen Optionen zum Begrenzen der Objekte für die Synchronisierung mit Azure AD und enthält eine Schritt-für-Schritt-Anleitung für deren Konfiguration. |
| **Features** ||
| [Implementieren der Kennwortsynchronisierung](active-directory-aadconnectsync-implement-password-synchronization.md) | Es wird beschrieben, wie die Synchronisierung von Kennwörtern funktioniert und wie die Implementierung sowie der Betrieb und die Problembehandlung durchgeführt werden. |
| [Verhindern von versehentlichen Löschungen](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Beschreibt das Feature zum *Verhindern von versehentlichen Löschungen* und dessen Konfiguration. |
| **Vorgänge** ||
| [Operative Aufgaben und Überlegungen](active-directory-aadconnectsync-operations.md) | Es werden operative Aspekte beschrieben, z. B. die Notfallwiederherstellung. |
| **Weitere Informationen und Referenzen** ||
| [Ports](active-directory-aadconnect-ports.md) | Enthält eine Liste der Ports, die Sie zwischen dem Synchronisierungsmodul und Ihren lokalen Verzeichnissen und Azure AD geöffnet sein müssen. |
| [Mit Azure Active Directory synchronisierte Attribute](active-directory-aadconnectsync-attributes-synchronized.md) | Enthält eine Liste aller Attribute, die zwischen lokalem AD und Azure AD synchronisiert werden. |
| [Funktionsreferenz](active-directory-aadconnectsync-functions-reference.md) | Enthält eine Liste mit allen Funktionen, die für die deklarative Bereitstellung verfügbar sind. |

## Zusätzliche Ressourcen

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->