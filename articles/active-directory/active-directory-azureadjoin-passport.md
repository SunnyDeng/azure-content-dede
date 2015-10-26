<properties 
	pageTitle="Übersicht über Microsoft Passport und Details zu dieser neuen, zertifikatbasierten Authentifizierung | Microsoft Azure" 
	description="In diesem Thema wird erklärt, wie Benutzer Azure AD Join während Ihres Eindrucks beim ersten Ausführen einrichten können." 
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
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport

Die aktuellen Methoden der Authentifizierung mit Kennwörtern allein sind nicht ausreichend, um Benutzer zu schützen. Benutzer verwenden gleiche Kennwörter wiederholt und vergessen sie. Kennwörter können missbraucht werden, sind anfällig für Phishing, für Sicherheitslücken und sie sind leicht zu erraten. Sie sind außerdem schwer zu merken und anfällig für Angriffe wie "Pass-the-Hash". Bei "Pass-the-Hash" handelt es sich um ein Hacker-Verfahren, mit dem ein Angreifer sich mit dem zugrunde liegenden NTLM und/oder LanMan-Hash eines Benutzerkennworts bei einem Remoteserver oder -dienst authentifizieren kann, ohne dass das zugeordnete Klartextkennwort erforderlich ist. Dies ist normalerweise der Fall. Weitere Informationen zu "Pass-the-Hash" finden Sie unter [Pass-the-Hash](https://technet.microsoft.com/en-us/dn785092.aspx).

## Was ist Microsoft Passport?
Microsoft Passport ist ein neuer privater/öffentlicher Schlüssel oder ein zertifikatbasierter Authentifizierungsansatz für Unternehmen und Verbraucher, der über Kennwörter hinausgeht. Diese Form der Authentifizierung stützt sich auf Anmeldeinformationen mit einem Schlüsselpaar, das Kennwörter ersetzen kann und widerstandsfähig gegenüber Sicherheitsverstößen, Diebstählen und Phishing ist. Mit Microsoft Passport können sich Benutzer für ein Microsoft-Konto, ein Active Directory-Konto, ein Microsoft Azure Active Directory (AD)-Konto oder einen nicht-Microsoft-Dienst authentifizieren, der die Authentifizierung mit Fast ID Online (FIDO) unterstützt. Nach einer anfänglichen Überprüfung in zwei Schritten während der Microsoft Passport-Registrierung wird Microsoft Passport auf dem Gerät des Benutzers eingerichtet, und der Benutzer legt eine Geste fest, entweder Windows Hello oder eine PIN. Der Benutzer stellt die Geste zur Überprüfung seiner Identität bereit. Windows verwendet dann Microsoft Passport zum Authentifizieren des Benutzers und hilft ihm dabei, auf geschützte Ressourcen und Dienste zuzugreifen.

Der private Schlüssel wird ausschließlich über eine "Benutzergeste" verfügbar gemacht, z. B. eine PIN, Biometrik, ein Remotegerät wie eine Smartcard, die der Benutzer zum Anmelden am Gerät verwendet, und diese Informationen werden mit einem Zertifikat oder einem asymmetrischen Schlüsselpaar verknüpft. Dieser private Schlüssel ist für die Hardware bescheinigt, falls das Gerät über einen Trusted Platform Module (TPM)-Chip verfügt. Der private Schlüssel bleibt immer im Gerät.

Der öffentliche Schlüssel wird mit Azure Active Directory und Windows Server Active Directory (für lokale Bereitstellungen) registriert. Die Identitätsanbieter validieren den Benutzer durch die Zuordnung des öffentlichen Schlüssels des Benutzers auf den privaten Schlüssel und bieten Anmeldeinformationen über ein Einmalkennwort, PhoneFactor oder einen anderen Benachrichtigungsmechanismus.
## Warum Unternehmen Microsoft Passport übernehmen sollten
Durch Aktivieren von Microsoft Passport können Unternehmen ihre Ressourcen mithilfe folgender Funktionen noch besser sichern:

* Einrichten von Microsoft Passport mit einer Option, die Hardware bevorzugt. Das bedeutet, dass Schlüssel je nach Verfügbarkeit auf TPM 1.2 oder TPM 2.0 generiert werden. Falls TPM nicht verfügbar ist, werden die Schlüssel durch Software generiert. 

* Definieren der Komplexität und Länge der PIN, und ob die „Hello“-Verwendung in Ihrer Organisation aktiviert ist.

* Konfigurieren von Microsoft Passport zur Unterstützung Smartcard-ähnlicher Szenarien, die zertifikatbasierte Authentifizierung verwenden.

## Funktionsweise
1. Schlüssel werden auf der Hardware generiert. Viele Computer haben einen vordefinierten Trusted Platform Module (TPM)-Chip, der die Hardware durch die Integration von kryptografischen Schlüsseln in Geräten sichert. Das TPM 1.2 oder TPM 2.0 dient zum Generieren von Schlüsseln oder Zertifikaten, die aus den generierten Schlüsseln ausgeschlüsselt werden.

2. Diese an Hardware gebundenen Schlüssel werden durch das TPM bestätigt.

3. Das Entsperren mit einer einzelnen Geste entsperrt das Gerät. Die Geste erhält Zugriff auf mehrere Ressourcen, wenn das Gerät mit einer Domäne oder Azure AD verknüpft ist.

## Microsoft Passport-Lebenszyklus
Microsoft Passport-Lebenszyklus![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png) Das obige Diagramm veranschaulicht das private/öffentliche Schlüsselpaar und die Validierung durch den Identitätsanbieter. Jeder dieser Schritte wird nachfolgend ausführlich erläutert:

1. Der Benutzer weist seine Identität durch mehrere integrierte Überprüfungsmethoden (Gesten, physische Smartcards, mehrstufige Authentifizierung) nach, und sendet diese Informationen an den Identitätsanbieter, z. B. Azure Active Directory oder Active Directory.

2.  Das Gerät erstellt dann den Schlüssel, bestätigt den Schlüssel, nimmt den öffentlichen Teil dieses Schlüssels, fügt Sendeanweisungen hinzu, führt die Anmeldung durch und sendet den Schlüssel an den Identitätsanbieter, um ihn zu registrieren.

3. Sobald der öffentliche Teil des Schlüssels bei einem Identitätsanbieter registriert ist, fordert er das Gerät dazu auf, sich mit dem privaten Teil des Schlüssels anzumelden. Der Identitätsanbieter überprüft dann das Authentifizierungstoken und stellt es aus, damit der Benutzer auf geschützte Ressourcen zugreifen kann.

4. Sobald der öffentliche Teil des Schlüssels beim Identitätsanbieter registriert ist, fordert er das Gerät dazu auf, sich mit dem privaten Teil des Schlüssels anzumelden.

5. Der Identitätsanbieter überprüft dann das Authentifizierungstoken und stellt es aus, damit der Benutzer und das Gerät auf geschützte Ressourcen zugreifen kann. Identitätsanbieter können plattformübergreifende Apps schreiben oder die Browserunterstützung über JS/Webcrypto-APIs erstellen und Microsoft Passport-Anmeldeinformationen für ihre Benutzer verwenden.

## Anforderungen für die Bereitstellung
Auf Unternehmensebene
---------------------------
* Azure-Abonnement

Auf Benutzerebene
-------------------------------------------------------------
* Computer müssen die Windows 10 Professional- oder Enterprise-SKU ausführen.

## Zusätzliche Informationen

* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)
* [Verwalten der Identitätsüberprüfung mit Microsoft Passport](https://technet.microsoft.com/library/mt219735(v=vs.85).aspx)

<!---HONumber=Oct15_HO3-->