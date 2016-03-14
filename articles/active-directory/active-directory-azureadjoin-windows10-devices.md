<properties
	pageTitle="Verwenden von Windows 10-Geräten an Ihrem Arbeitsplatz | Microsoft Azure"
	description="Enthält eine Momentaufnahme der Funktionen für Benutzer und das IT-Personal. Es wird veranschaulicht, wie ein Gerät in einem Unternehmen mit Windows 10 bereitgestellt und verwendet werden kann."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="femila"/>

# Verwenden von Windows 10-Geräten an Ihrem Arbeitsplatz

Unter Windows 10 werden drei Modelle für Unternehmen angeboten, damit Benutzer sicher und bequem auf geschäftliche Ressourcen zugreifen können.

- **Azure Active Directory Join** (Azure AD Join) für Mitarbeiter, die auf Ressourcen wie etwa Office 365 in erster Linie in der Cloud zugreifen. Azure AD Join ist eine Self-Service-Benutzeroberfläche für die Arbeitsbereitstellung und neu in Windows 10.
- **Domänenbeitritt** für Organisationen, die in lokale Apps und Ressourcen investiert haben. Der Domänenbeitritt lässt sich unter Windows 10 noch komfortabler verwenden, wenn eine Verbindung mit Azure AD besteht.
- **Neue, vereinfachte BYOD-Umgebung** für Benutzer, die Windows ein Geschäfts- oder Schulkonto hinzufügen möchten, um einfach auf Ressourcen auf persönlichen Geräten zugreifen zu können.

Die folgende Tabelle enthält eine Momentaufnahme der Funktionen für Benutzer und IT-Administratoren. Es wird veranschaulicht, wie ein Gerät in einem Unternehmen mit Windows 10 bereitgestellt und verwendet werden kann:

| | Domänenbeitritt | Azure AD Join | Persönliches Gerät |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows-Geräteanmeldung für Geschäfts-oder Schulkonten | Ja | Ja | Nein |
| Einmaliges Anmelden (Single Sign-On, SSO) für Benutzer bei Office 365 und Azure AD-Apps. SSO ermöglicht es Benutzern, sich nur einmal anzumelden, um auf Unternehmensressourcen zuzugreifen. | Ja | Ja | Ja |
| Einmalige Benutzeranmeldung (SSO) an Kerberos/NTLM-Apps | Ja | Eingeschränkt | Ja, über VPN |
| Strenge Autorisierung und bequeme Anmeldung bei Geschäfts- oder Schulkonten mit Microsoft Passport und Windows Hello | Ja | Ja | Ja |
| Zugriff auf Windows Store des Unternehmens per Geschäfts- oder Schulkonto (kein Microsoft-Konto) | Ja | Ja | Ja |
| Für Unternehmen geeignetes geräteübergreifendes Roaming von Benutzereinstellungen per Geschäfts- oder Schulkonten | Ja | Ja | Ja |
| Mögliche Einschränkung des Zugriffs auf Unternehmens-Apps auf Geräten, die mit Unternehmensrichtlinien für Geräte kompatibel sind | Ja | Ja | Ja |
| Geschäftliche Self-Service-Bereitstellung von Geräten für Benutzer von jedem Ort aus | Nein | Ja | Ja |
| Möglichkeit zum Verwalten von Geräten | Ja, per Gruppenrichtlinie/SCCM | Ja | Ja |

## Verwendung unternehmenseigener Geräte mit Azure AD Join und Domänenbeitritt unter Windows 10

Windows 10 stellt zwei Methoden bereit, mit denen unternehmenseigene Geräte auf geschäftliche Ressourcen zugreifen können:

- Azure AD Join
- Domänenbeitritt

 Beides können gültige Optionen sein, und die bessere Eignung richtet sich jeweils nach den Anforderungen des Unternehmens. Es gibt auch Fälle, in denen Unternehmen unter Umständen davon profitieren, beide Bereitstellungsmethoden zu aktivieren.

## Verwendung von Azure Active Directory Join

Azure AD Join ist eine neue Self-Service-Benutzeroberfläche für die Arbeitsbereitstellung unter Windows 10. Sie ist für Mitarbeiter gedacht, die auf geschäftliche Ressourcen, z. B. Office 365, vor allem in der Cloud zugreifen. Die ist eine einfache Möglichkeit, Computer, Tablets und Smartphones für das Unternehmen zu konfigurieren. Geräte werden per mobiler Geräteverwaltung verwaltet, indem einheitliche Steuerelemente über alle Windows-Plattformen hinweg verwendet werden.

**Verwenden Sie Azure AD Join beispielsweise aus den folgenden Gründen**:

- Sie möchten die Self-Service-Bereitstellung von Geräten für Mitarbeiter ermöglichen, die unterwegs sind.
- Sie stellen für Benutzer unternehmenseigene mobile Geräte bereit, z. B. Tablets und Smartphones.
- Sie möchten eine Gruppe von Benutzern nicht in Active Directory, sondern in Azure AD verwalten, z. B. Saisonkräfte, Subunternehmer oder Studenten.
- Sie möchten Beitrittsfunktionen für Mitarbeiter in entfernten Niederlassungen mit eingeschränkter lokaler Infrastruktur bereitstellen.
- Sie besitzen keine lokale Active Directory-Instanz.

Einige Unternehmen verwenden Azure AD Join als vorrangigen Weg zum Bereitstellen unternehmenseigener Geräte, vor allem beim Migrieren der meisten oder auch aller Ressourcen in die Cloud. Hybrid-Unternehmen mit Active Directory und Azure AD können je nach Benutzer oder Abteilung wählen, ob sie die eine oder die andere Methode bereitstellen.

Schulbezirke und Universitäten können beispielsweise das Personal in Active Directory und die Schüler bzw. Studenten in Azure AD verwalten. Einige Unternehmen ziehen es vielleicht vor, entfernte Büros oder die Vertriebsabteilung unter Azure AD zu verwalten. In einem Hybrid-Unternehmen können sowohl Azure AD Join- als auch Domänenbeitritt-Methoden verwendet werden. Azure AD Join kann eine hervorragende Erweiterung des Domänenbeitritts zum Bereitstellen von Geräten in einer Arbeitsumgebung darstellen.

**Falls der Zugriff auf Unternehmensressourcen hauptsächlich über die Cloud erfolgt, profitiert Ihr Unternehmen in den folgenden Fällen ggf. von zusätzlichen Vorteilen**:

- Sie können Abhängigkeiten von der lokalen Identitätsinfrastruktur beseitigen.
- Sie können Ihr Modell für die Gerätebereitstellung vereinfachen, indem Sie Lösungen für die Imageerstellung auslaufen lassen und die Self-Service-Konfiguration ermöglichen.
- Sie können die mobile Geräteverwaltung verwenden, um alle Geräte auf unterschiedlichen Plattformen zu verwalten.

Weitere Informationen zu Azure AD Join finden Sie unter [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## Verwendung (bzw. Weiternutzung) des Domänenbeitritts

In den letzten 15 Jahren haben zahlreiche Unternehmen den Domänenbeitritt verwendet, um Unternehmensgeräte zu verbinden. Er ermöglicht Benutzern die Anmeldung bei Geräten mit ihren Geschäfts- oder Schulkonten in Active Directory. Der Domänenbeitritt ermöglicht zudem der IT-Abteilung die zentrale und vollständige Verwaltung dieser Geräte. Unternehmen nutzen normalerweise Verfahren für die Imageerstellung, um Geräte bereitzustellen, und häufig System Center Configuration Manager (SCCM) oder die Gruppenrichtlinie für die Verwaltung.

**Für die Nutzung (oder Weiternutzung) des Domänenbeitritts in Ihrem Unternehmen gibt es die folgenden Gründe**:

- Sie haben auf den Geräten Win32-Apps bereitgestellt, für die NTLM/Kerberos verwendet wird.
- Sie benötigen die Gruppenrichtlinie oder SCCM/DCM, um Geräte zu verwalten.
- Sie möchten weiterhin Lösungen für die Imageerstellung verwenden, um Geräte für Ihre Mitarbeiter zu konfigurieren.

**Mit dem Domänenbeitritt unter Windows 10 kommen Sie auch in den Genuss der folgenden Vorteile, wenn die Verbindung mit Azure AD hergestellt wurde:**

- Strenge gerätegebundene Authentifizierung und bequeme Anmeldung bei Geschäfts- oder Schulkonten mit Microsoft Passport und Windows Hello
- Zugriff auf Windows Store des Unternehmens für Geräte, die Geschäfts- oder Schulkonten nutzen (kein Microsoft-Konto erforderlich)
- Für Unternehmen geeignetes Roaming von Benutzereinstellungen auf Geräten, die Geschäfts- oder Schulkonten nutzen (kein Microsoft-Konto erforderlich)
- Mögliche Einschränkung des Zugriffs auf Unternehmens-Apps auf Geräten, die mit Unternehmensrichtlinien für Geräte kompatibel sind

Weitere Informationen zu Azure AD Join finden Sie unter [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## Aktivieren der Verknüpfung von persönlichen Geräten für Geschäft oder Schule

Zur Unterstützung von BYOD in Unternehmen können Benutzer in Windows 10 ihrem Computer, Tablet oder Smartphone ein Geschäfts- oder Schulkonto hinzufügen. Nachdem der Benutzer ein Geschäfts- oder Schulkonto hinzugefügt hat, ist das Gerät in Azure AD und optional in dem vom Unternehmen konfigurierten System für die mobile Geräteverwaltung registriert. Im Verzeichnis werden diese Geräte als „Registriert“ bzw. „In Azure AD eingebunden“ angezeigt. IT-Administratoren können basierend auf diesen Informationen unterschiedliche Richtlinien anwenden und bei Bedarf bei Geräten im persönlichen Besitz vorsichtiger als bei unternehmenseigenen Geräten vorgehen.

Benutzer können ihrem persönlichen Gerät ganz einfach ein Geschäfts- oder Schulkonto hinzufügen. Dieser Schritt kann beim ersten Zugreifen auf eine Unternehmensanwendung erfolgen, oder Benutzer können das Konto manuell über das Menü „Einstellungen“ hinzufügen. Dieses Konto ermöglicht das einmalige Anmelden für den Zugriff auf Unternehmensressourcen.

Weitere Informationen zu Azure AD Join finden Sie unter [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md).

## Aktivieren des Domänenbeitritts oder von Azure AD Join

Für alle oben beschriebenen Methoden (Domänenbeitritt, Azure AD Join und Hinzufügen von Geschäfts- oder Schulkonten) ist auf der Windows 10-Benutzeroberfläche ein Einstiegspunkt vorhanden. Allerdings ist für alle Methoden ein IT-Administrator erforderlich, der die Funktion in der Infrastruktur aktiviert, bevor sie verwendet werden kann.

## Anforderungen für die Bereitstellung von Azure AD Join

Sie benötigen Folgendes, um Azure AD Join für eine Gruppe von Benutzern bereitzustellen:

- Ein Azure AD-Abonnement
- Ein Azure AD Premium-Abonnement, z. B. die automatische Registrierung für die mobile Geräteverwaltung, falls Sie weitere Funktionen benötigen
- Mobile Geräteverwaltung, z. B. ein Microsoft Intune-Abonnement, die mobile Geräteverwaltung für Office 365 oder ein Partneranbieter für die mobile Geräteverwaltung, der in Azure AD integriert werden kann (Weitere Informationen finden Sie am Ende dieses Artikels im Abschnitt mit den [häufig gestellten Fragen](#frequently-asked-questions)).

In Hybrid-Umgebungen wird empfohlen, Azure AD Connect bereitzustellen, um das lokale Verzeichnis auf Azure AD zu erweitern.

## Anforderungen für die Verwendung des Domänenbeitritts mit Azure AD

Der Domänenbeitritt funktioniert wie immer. Damit Sie von den Azure AD-Vorteilen profitieren können, benötigen Sie jedoch Folgendes:

- Ein Azure AD-Abonnement
- Eine Bereitstellung von Azure AD Connect, um das lokale Verzeichnis auf Azure AD zu erweitern
- Eine Richtlinie, die den in die Domäne eingebundenen Geräten den bedingten Zugriff auf Azure AD ermöglicht
- Eine Richtlinie, die den Zugriff auf in die Domäne eingebundene Geräte zulässt, falls Sie den Zugriff für bestimmte Geräte einschränken möchten
- System Center Configuration Manager Version 1509 für Technical Preview, um Regeln für die Erzwingung der Gerätekompatibilität zu aktivieren (Informationen finden Sie in der TechNet-Dokumentation und im TechNet-Blogbeitrag).

Weitere Informationen zum Domänenbeitritt unter Windows 10 finden Sie unter <link-to-DJ-in-Win10-deployment-guide>.

## Anforderungen für die Verwendung von BYOD und „Geschäfts- oder Schulkonto hinzufügen“

Sie benötigen Folgendes, um BYOD (Bring Your Own Device) für Geschäfts- oder Schulkonten zu ermöglichen:

- Ein Azure AD-Abonnement
- Ein Azure AD Premium-Abonnement, z. B. die automatische Registrierung für die mobile Geräteverwaltung, falls Sie weitere Funktionen benötigen

## Anforderungen für die Verwendung von Microsoft Passport

Sie benötigen Folgendes, um Microsoft Passport zu aktivieren:

- Eine Public Key-Infrastruktur (PKI) zur Unterstützung der zertifikatbasierten Authentifizierung mit Microsoft Passport.
- Ein Intune-Abonnement zur Unterstützung der zertifikatbasierten Authentifizierung per Microsoft Passport für Azure AD Join und Geschäfts- oder Schulkonten.
- System Center Configuration Manager Version 1509 für Technical Preview (siehe TechNet-Dokumentation und -Blogbeitrag) zur Unterstützung der zertifikatbasierten Authentifizierung per Microsoft Passport für den Domänenbeitritt.
- Eine Richtlinie zum Aktivieren von Microsoft Passport im Unternehmen.

Als Alternative zur Verwendung einer PKI können Sie das schlüsselbasierte Microsoft Passport-Verfahren aktivieren, indem Sie wie folgt vorgehen:

- Stellen Sie Windows Server 2016 „Production Preview 1“-Domänencontroller bereit (keine Domänen- oder Gesamtstruktur-Funktionsebene erforderlich; einige Domänencontroller zu Redundanzzwecken für jede Active Directory-Website sind ausreichend).
- Legen Sie die Richtlinie zum Aktivieren von Microsoft Passport im Unternehmen fest.

Weitere Informationen zu Microsoft Passport und Windows Hello unter Windows 10 finden Sie unter <link-to-MS-Passport-and-Windows-Hello-document>.

## Häufig gestellte Fragen
### Welche Partnerprodukte für die mobile Geräteverwaltung können in Azure AD integriert werden?

Die folgenden Produkte von Anbietern können für die einheitliche Registrierung und den bedingten Zugriff unter Windows 10 in Azure AD integriert werden:

- AirWatch von VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Lokale Verwaltung mobiler Geräte von SOTI

### Wie sieht es unter Windows 10 mit der Arbeitsbereichverknüpfung aus?
Die Arbeitsbereichsverknüpfung wurde unter Windows 8.1 zum Aktivieren von BYOD verwendet. Unter Windows 10 wird BYOD durch das Hinzufügen eines Geschäfts- oder Schulkontos aktiviert. Dies wurde weiter oben in diesem Artikel erläutert. Für Unternehmen, die die mobile Geräteverwaltung nicht in Azure AD integrieren, können Benutzer das Gerät über **Einstellungen** > **Konten** > **Arbeitsplatzzugriff** manuell bei der Verwaltung registrieren.


### Können Benutzer ihr Microsoft-Konto mit ihrem Domänenkonto unter Windows 10 verbinden?
Nicht unter Windows 10. Unter Windows 8.1 konnten Benutzer von Geräten, für die der Domänenbeitritt durchgeführt wurde, für ihr Microsoft-Konto (z. B. Hotmail, Live, Outlook, XBox usw.) eine Verbindung mit ihrem Domänenkonto herstellen, um bestimmte Funktionen wie das einmalige Anmelden (SSO) für Live-Dienste, die Nutzung des Windows Store und das geräteübergreifende Roaming von Benutzereinstellungen zu ermöglichen. Unter Windows 10 wurde die Funktion für die Verbindungsherstellung mit Microsoft-Konten entfernt. Benutzer können Microsoft-Konten als zusätzliche Konten hinzufügen, um das einmalige Anmelden für Verbraucherdienste wie etwa den Windows Store zu aktivieren. Dies ist unter **Einstellungen** > **Konten** > **Ihr Konto** möglich.

Für Benutzer, die ein Upgrade von Windows 8.1-Geräten mit Domänenbeitritt durchführen und deren Microsoft-Konten verbunden waren, wird das verbundene Microsoft-Konto automatisch der Liste mit den verwendeten zusätzlichen Konten hinzugefügt.


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->