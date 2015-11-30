<properties 
	pageTitle="Verwenden von Windows 10-Geräten an Ihrem Arbeitsplatz | Microsoft Azure" 
	description="Enthält eine Momentaufnahme der Funktionen für Benutzer und das IT-Personal. Es wird veranschaulicht, auf welche unterschiedlichen Arten ein Gerät in einem Unternehmen mit Windows 10 bereitgestellt und verwendet werden kann." 
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
	ms.date="11/16/2015" 
	ms.author="femila"/>

# Verwenden von Windows 10-Geräten an Ihrem Arbeitsplatz

Unter Windows 10 werden drei Modelle für Unternehmen angeboten, damit Benutzer sicher und bequem auf die geschäftlichen Ressourcen zugreifen können.

1. **Azure AD Join** ist ein neues Feature von Windows 10. Hierbei handelt es sich um eine Self-Service-Benutzeroberfläche für die Arbeitsbereitstellung für Benutzer, die vorrangig auf Ressourcen in der Cloud zugreifen, z. B. Office 365.
1. **Domänenbeitritt** (besser geeignet unter Windows 10 bei Verbindung mit Azure AD) für Unternehmen mit großen Investitionen in lokale Apps und Ressourcen.
1. **Neue, vereinfachte BYOD-Benutzeroberfläche**, mit der Benutzer Windows ein Geschäftskonto hinzufügen können, um den einfachen Zugriff auf geschäftliche Ressourcen auf persönlichen Geräten zu ermöglichen.

Die folgende Tabelle enthält eine Momentaufnahme der Funktionen für Benutzer und das IT-Personal. Es wird veranschaulicht, auf welche unterschiedlichen Arten ein Gerät in einem Unternehmen mit Windows 10 bereitgestellt und verwendet werden kann:

| | Domänenbeitritt | Azure AD Join | Persönliche Geräte |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows-Geräteanmeldung mit Geschäftskonten | Ja | Ja | Nein |
| Einmalige Benutzeranmeldung (SSO) an Office 365 und Azure AD-Apps,[1] einmaliges Anmelden oder die Möglichkeit, auf Unternehmensressourcen zuzugreifen, ohne nach der ersten Anmeldung die Anmeldedaten erneut eingeben zu müssen. | Ja | Ja | Ja |
| Einmalige Benutzeranmeldung (SSO) an Kerberos/NTLM-Apps | Ja | Eingeschränkt | Per VPN |
| Starke Authentifizierung und bequeme Anmeldung für das Geschäftskonto per Microsoft Passport und Windows Hello | Ja | Ja | Ja |
| Zugriff auf Windows Store des Unternehmens per Geschäftskonto (kein Microsoft-Konto) | Ja | Ja | Ja |
| Für Unternehmen geeignetes geräteübergreifendes Roaming von Benutzereinstellungen per Geschäftskonto | Ja | Ja | Ja |
| Einschränkung des Zugriffs auf Unternehmens-Apps über Geräte, die mit Unternehmensrichtlinien für Geräte kompatibel sind | Ja | Ja | Ja |
| Geschäftliche Self-Service-Bereitstellung von Geräten für Benutzer von jedem Ort aus | Nein | Ja | Ja |
| Möglichkeit zum Verwalten von Geräten | Ja, per Gruppenrichtlinie/SCCM | Ja | Ja |

##Unternehmenseigene Geräte: Azure AD Join und Domänenbeitritt

Windows 10 stellt zwei Modelle bereit, mit denen für unternehmenseigene Geräte der Zugriff auf geschäftliche Ressourcen erfolgen kann:

- Azure AD Join
- Domänenbeitritt

 Beides sind gültige Optionen, und die bessere Eignung richtet sich jeweils nach den Anforderungen des Unternehmens. Es gibt auch Fälle, in denen Unternehmen unter Umständen davon profitieren, beide Bereitstellungsmethoden zu aktivieren.

## Verwendung von Azure Active Directory Join

Azure AD Join ist eine neue Self-Service-Benutzeroberfläche für die Arbeitsbereitstellung unter Windows 10. Sie ist für Mitarbeiter gedacht, die vor allem auf geschäftliche Ressourcen in der Cloud zugreifen, z. B. Office 365. Die ist eine einfache Möglichkeit, PCs, Tablets und Smartphones für das Unternehmen zu konfigurieren. Geräte werden per mobiler Geräteverwaltung (MDM) verwaltet, indem einheitliche Steuerelemente über alle Windows-Plattformen hinweg verwendet werden.

**Verwenden Sie Azure AD Join beispielsweise aus den folgenden Gründen**:

1.	Sie möchten die Self-Service-Bereitstellung von Geräten für Mitarbeiter ermöglichen, die unterwegs sind.
2.	Sie stellen für Benutzer unternehmenseigene mobile Geräte bereit, z. B. Tablets und Smartphones.
3.	Sie möchten eine Gruppe von Benutzern nicht in AD, sondern in Azure AD verwalten, z. B. Saisonkräfte, Subunternehmer oder Studenten.
4.	Sie möchten Beitrittsfunktionen für Mitarbeiter in entfernten Niederlassungen mit eingeschränkter lokaler Infrastruktur bereitstellen.
5.	Sie haben kein lokales AD.

Einige Unternehmen verwenden Azure AD Join als vorrangigen Weg zum Bereitstellen unternehmenseigener Geräte, vor allem beim Migrieren der meisten oder auch aller Ressourcen in die Cloud. Hybrid-Unternehmen mit AD und Azure AD können wählen, ob sie je nach Benutzer oder Abteilung die eine oder die andere Methode bereitstellen. Schulbezirke und Universitäten können beispielsweise das Personal in AD und die Schüler bzw. Studenten in Azure AD verwalten, und einige Unternehmen ziehen es vielleicht vor, entfernte Büros oder die Vertriebsabteilung unter Azure AD zu verwalten. In einem Hybrid-Unternehmen können sowohl Azure AD Join- als auch Domänenbeitritt-Methoden verwendet werden. Azure AD Join kann eine hervorragende Erweiterung zum Bereitstellen von Geräten in einer Arbeitsumgebung darstellen.

**Falls der Zugriff auf Unternehmensressourcen größtenteils über die Cloud erfolgt, profitiert ein Unternehmen ggf. von folgenden zusätzlichen Vorteilen**:

- Unter Umständen können Abhängigkeiten von der lokalen Identitätsinfrastruktur beseitigt werden.
- Sie können Ihre Modell für die Gerätebereitstellung vereinfachen, indem Sie Lösungen für die Imageerstellung auslaufen lassen und die Self-Service-Konfiguration ermöglichen.
- Sie können die mobile Geräteverwaltung (MDM) verwenden, um alle Geräte auf den unterschiedlichen Plattformen zu verwalten.

Weitere Informationen zu Azure AD Join finden Sie unter [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## Verwendung des Domänenbeitritts (bzw. Weiternutzung)

Der Domänenbeitritt ist das herkömmliche Verfahren, mit dem Unternehmen in den letzten 15 oder mehr Jahren eine Verbindung für geschäftliche Geräte hergestellt haben. Benutzer konnten sich mit ihren AD-Geschäftskonten an den Geräten anmelden, und das IT-Personal konnte diese Geräte zentral und umfassend verwalten. Unternehmen nutzen normalerweise Verfahren für die Imageerstellung, um Geräte bereitzustellen, und meistens System Center Configuration Manager (SCCM) oder die Gruppenrichtlinie für die Verwaltung.

**Für die Nutzung des Domänenbeitritts in Ihrem Unternehmen gibt es die folgenden Gründe**:

- Sie haben auf den Geräten Win32-Apps bereitgestellt, für die NTLM/Kerberos verwendet wird.
- Sie benötigen die Gruppenrichtlinie oder SCCM/DCM, um Geräte zu verwalten.
- Sie möchten weiterhin Lösungen für die Imageerstellung verwenden, um Geräte für Ihre Mitarbeiter zu konfigurieren.

**Mit dem Domänenbeitritt unter Windows 10 kommen Sie auch in den Genuss der folgenden Vorteile, nachdem die Verbindung mit Azure AD hergestellt wurde**:

- Starke gerätegebundene Authentifizierung und bequeme Anmeldung für das Geschäftskonto per Microsoft Passport und Windows Hello.
- Zugriff auf Windows Store des Unternehmens per Geschäftskonto (kein Microsoft-Konto erforderlich).
- Für Unternehmen geeignetes geräteübergreifendes Roaming von Benutzereinstellungen per Geschäftskonto (kein Microsoft-Konto erforderlich).
- Mögliche Einschränkung des Zugriffs auf Unternehmens-Apps über Geräte, die mit Unternehmensrichtlinien für Geräte kompatibel sind.
- Weitere Informationen zu Azure AD Join finden Sie unter [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md).

##Aktivieren von Geräten im persönlichen Besitz für die Zusammenarbeit mit Geschäftskonten

Zur Unterstützung von BYOD in Unternehmen können Benutzer in Windows 10 ihrem PC, Tablet oder Smartphone ein Geschäfts- oder Schulkonto hinzufügen. Durch das Hinzufügen eines Geschäftskontos wird das Gerät unter Azure AD registriert und optional auch für die mobile Geräteverwaltung (MDM) registriert, die vom Unternehmen für Geräte konfiguriert wurde. Im Verzeichnis werden diese Geräte als „Registriert“ bzw. „In Azure AD eingebunden“ angezeigt. Das IT-Personal kann basierend auf diesen Informationen unterschiedliche Richtlinien anwenden und bei Bedarf bei Geräten im persönlichen Besitz vorsichtiger als bei unternehmenseigenen Geräten vorgehen.

Benutzer können ihrem persönlichen Gerät auf bequeme Weise ein Geschäftskonto hinzufügen: beim ersten Zugreifen auf die Geschäftsanwendung oder manuell über die „Einstellungen“. Dieses Geschäftskonto ermöglicht das einmalige Anmelden für den Zugriff auf Unternehmensressourcen.

Weitere Informationen zu Azure AD Join finden Sie unter [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md).

## Aktivieren des Domänenbeitritts oder von Azure AD Join 

Alle oben beschriebenen Methoden (Domänenbeitritt, Azure AD Join und Hinzufügen eines Geschäftskontos) verfügen über Einstiegspunkte für die Windows 10-Benutzeroberfläche. Aber bei allen Methoden muss das IT-Personal die Funktionen in der Infrastruktur aktivieren, damit die Benutzeroberfläche funktioniert.

##Azure AD Join

Sie benötigen Folgendes, um Azure AD Join für eine Gruppe von Benutzern bereitzustellen:
---------------------------------------------------------------------------
- Ein Azure AD-Abonnement.
- Für weitere Funktionen benötigen Sie ein Azure AD Premium-Abonnement, z. B. die automatische MDM-Registrierung.
- Ein Azure AD Premium-Abonnement.
- Mobile Geräteverwaltung (MDM) (Intune-Abonnement oder MDM für Office 365 oder Produkte beliebiger MDM-Drittanbieter, die in Azure AD integriert werden können; Details im Abschnitt „Häufig gestellte Fragen“).
- Wenn Sie ein Hybrid-Unternehmen sind, wird dringend die folgende Vorgehensweise empfohlen:
- Stellen Sie Azure AD Connect bereit, um das lokale Verzeichnis auf Azure AD zu erweitern.

##Domänenbeitritt

Der Domänenbeitritt funktioniert wie gewohnt. Sie benötigen aber Folgendes, um in den Genuss der Azure AD-Vorteile zu kommen:

- Ein Azure AD-Abonnement.
- Stellen Sie Azure AD Connect bereit, um das lokale Verzeichnis auf Azure AD zu erweitern.
- Legen Sie eine Richtlinie zum Herstellen einer Verbindung für Geräte, die einer Domäne beigetreten sind, mit Azure AD fest.
- Weitere Informationen zum Domänenbeitritt unter Windows 10 finden Sie unter <link-to-DJ-in-Win10-deployment-guide>.
- Für den bedingten Zugriff können Sie eine Richtlinie erstellen, die den Zugriff auf Geräte mit „Domänenbeitritt“ zulässt. Sie benötigen Folgendes, um Regeln aktivieren zu können, mit denen die Kompatibilität von Geräten erzwungen wird:
- System Center Configuration Manager Version 1509 für Technical Preview (siehe TechNet-Dokumentation und -Blogbeitrag).

##BYOD und Hinzufügen eines Geschäftskontos

Sie benötigen Folgendes, um BYOD für Geschäftskonten zu aktivieren:

- Ein Azure AD-Abonnement.
- Für weitere Funktionen benötigen Sie ein Azure AD Premium-Abonnement, z. B. die automatische MDM-Registrierung.
- Ein Azure AD Premium-Abonnement.
- Mobile Geräteverwaltung (MDM) (Intune-Abonnement oder MDM für Office 365 oder Produkte beliebiger MDM-Drittanbieter, die in Azure AD integriert werden können; Details im Abschnitt „Häufig gestellte Fragen“).

##Microsoft Passport

Sie benötigen Folgendes, um zusätzlich Microsoft Passport zu aktivieren:

- PKI-Infrastruktur zur Unterstützung der zertifikatbasierten Authentifizierung mit Microsoft Passport.
- Intune-Abonnement zur Unterstützung der zertifikatbasierten Authentifizierung per Microsoft Passport für Azure AD Join und Geschäftskonten.
- System Center Configuration Manager Version 1509 für Technical Preview (siehe TechNet-Dokumentation und -Blogbeitrag) zur Unterstützung der zertifikatbasierten Authentifizierung per Microsoft Passport für den Domänenbeitritt.
- Legen Sie die Richtlinie zum Aktivieren von Microsoft Passport im Unternehmen fest.

Als Alternative zur Verwendung einer PKI-Infrastruktur können Sie das schlüsselbasierte Microsoft Passport-Verfahren aktivieren, indem Sie wie folgt vorgehen:

- Stellen Sie Windows Server 2016 „Production Preview 1“-Domänencontroller bereit (keine Domänen- oder Gesamtstruktur-Funktionsebene erforderlich; einige Domänencontroller zu Redundanzwecken für jede AD-Website sind ausreichend).
- Legen Sie die Richtlinie zum Aktivieren von Microsoft Passport im Unternehmen fest.
- Weitere Informationen zu Microsoft Passport und Windows Hello unter Windows 10 finden Sie unter <link-to-MS-Passport-and-Windows-Hello-document>.

## Häufig gestellte Fragen

###Welche Produkte von MDM-Drittanbietern können in Azure AD integriert werden?

Die folgenden Produkte von Anbietern können für die einheitliche Registrierung und den bedingten Zugriff unter Windows 10 in Azure AD integriert werden:

- AirWatch von VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Lokales MDM von SOTI

###Wie sieht es unter Windows 10 mit der Arbeitsbereichverknüpfung aus?
Die Arbeitsbereichverknüpfung wurde unter Windows 8.1 zum Aktivieren von BYOD verwendet. Unter Windows 10 wird BYOD durch das Hinzufügen eines Geschäftskontos aktiviert. Dies wurde weiter oben in diesem Artikel erläutert. Für Unternehmen, die die mobile Geräteverwaltung (MDM) nicht in Azure AD integrieren, können Benutzer das Gerät über **Einstellungen** > **Konten** > **Arbeitsplatzzugriff** manuell bei der Verwaltung registrieren.

###Können Benutzer ihr Microsoft-Konto (MSA) mit ihrem Domänenkonto unter Windows 10 verbinden?
Nicht unter Windows 10. Unter Windows 8.1 konnten Benutzer von Geräten, für die der Domänenbeitritt durchgeführt wurde, für ihr MSA (z. B. Hotmail, Live, Outlook, XBox usw.) eine Verbindung mit ihrem Domänenkonto herstellen, um bestimmte Funktionen wie das einmalige Anmelden (SSO) für Live-Dienste, die Nutzung des Windows Store und das geräteübergreifende Roaming von Benutzereinstellungen zu ermöglichen. Unter Windows 10 wurde die Funktion für die MSA-Verbindungsherstellung entfernt. Benutzer können ein oder mehrere MSAs als zusätzliche Konten hinzufügen, um das einmalige Anmelden für Verbraucherdienste zu aktivieren, z. B. den Windows Store. Dies ist unter **Einstellungen** > **Konten** > **Ihr Konto** möglich.

Für Benutzer, die ein Upgrade von Windows 8.1-Geräten mit Domänenbeitritt durchführen und für die eine MSA-Verbindung bestanden hat, wird das verbundene MSA-Konto automatisch der Liste mit den verwendeten zusätzlichen Konten hinzugefügt.


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->