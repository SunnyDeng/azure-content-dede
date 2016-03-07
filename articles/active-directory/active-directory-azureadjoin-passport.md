<properties
	pageTitle="Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport | Microsoft Azure"
	description="Bietet eine Übersicht über Microsoft Passport und weiterführende Informationen zum Bereitstellen von Microsoft Passport."
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
	ms.date="11/19/2015"
	ms.author="femila"/>

# Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport

Die aktuellen Methoden der Authentifizierung mit Kennwörtern allein sind nicht ausreichend, um Benutzer zu schützen. Benutzer verwenden gleiche Kennwörter wiederholt und vergessen sie. Kennwörter können missbraucht werden, sind anfällig für Phishing, für Sicherheitslücken und sie sind leicht zu erraten. Sie sind außerdem schwer zu merken und anfällig für Angriffe wie "[Pass-the-Hash](https://technet.microsoft.com/dn785092.aspx)".

## Informationen zu Microsoft Passport
Microsoft Passport ist ein Authentifizierungsansatz für Unternehmen und Endkunden, der auf privaten/öffentlichen Schlüsseln oder Zertifikaten basiert und weit über den Gebrauch von Kennwörtern hinausgeht. Bei dieser Form der Authentifizierung werden Schlüsselpaar-Anmeldeinformationen verwendet, die Kennwörter ersetzen können und Verstößen, Diebstahl und Phishing gegenüber resistent sind.

 Mit Microsoft Passport können Benutzer sich bei einem Microsoft-Konto, einem Windows Server Active Directory-Konto, einem Microsoft Azure Active Directory-Konto (Azure AD) oder einem Nicht-Microsoft-Dienst authentifizieren, der die FIDO-Authentifizierung (Fast IDentity Online) unterstützt. Nach einer anfänglichen Überprüfung in zwei Schritten während der Microsoft Passport-Registrierung wird Microsoft Passport auf dem Gerät des Benutzers eingerichtet, und der Benutzer legt eine Geste fest, entweder Windows Hello oder eine PIN. Der Benutzer gibt die Geste zum Überprüfen der Identität an. Windows verwendet dann Microsoft Passport zum Authentifizieren des Benutzers und hilft ihm dabei, auf geschützte Ressourcen und Dienste zuzugreifen.

Der private Schlüssel wird ausschließlich über eine „Benutzergeste“ verfügbar gemacht, z. B. eine PIN, Biometrik oder ein Remotegerät wie eine Smartcard, die der Benutzer zum Anmelden am Gerät verwendet. Diese Informationen werden mit einem Zertifikat oder einem asymmetrischen Schlüsselpaar verknüpft. Der private Schlüssel ist für die Hardware bestätigt, falls das Gerät über einen TPM-Chip (Trusted Platform Module) verfügt. Der private Schlüssel bleibt immer im Gerät.

Der öffentliche Schlüssel wird mit Azure Active Directory und Windows Server Active Directory (für lokale Bereitstellungen) registriert. Die Identitätsanbieter überprüfen den Benutzer, indem sie den öffentlichen Schlüssel des Benutzers dem privaten Schlüssel zuordnen, und bieten Anmeldeinformationen über ein Einmalkennwort, PhoneFactor oder einen anderen Benachrichtigungsmechanismus.

## Gründe für den Einsatz von Microsoft Passport in Unternehmen

Durch Aktivieren von Microsoft Passport können Unternehmen ihre Ressourcen mithilfe folgender Funktionen noch besser sichern:

* Einrichten von Microsoft Passport mit einer Option, die Hardware bevorzugt. Dies bedeutet, dass die Schlüssel auf TPM 1.2 oder TPM 2.0 generiert werden. Wenn das TPM nicht verfügbar ist, wird der Schlüssel von der Software generiert.

* Definieren der Komplexität und Länge der PIN und der Verwendung von „Hello“ in Ihrer Organisation.

* Konfigurieren von Microsoft Passport zur Unterstützung Smartcard-ähnlicher Szenarien durch die Verwendung einer zertifikatbasierten Vertrauensstellung.

## Funktionsweise von Microsoft Passport
1. Schlüssel werden durch das TPM oder durch Software auf der Hardware generiert. Viele Geräte verfügen über einen vordefinierten TPM-Chip (Trusted Platform Module), der die Hardware durch die Integration kryptografischer Schlüssel in Geräten sichert. TPM 1.2 oder TPM 2.0 generiert Schlüssel oder Zertifikate, die über die generierten Schlüssel erstellt werden.

2. Das TPM bestätigt diese hardwaregebundenen Schlüssel.

3. Durch eine einzige Entsperrungsgeste wird das Gerät entsperrt. Diese Geste ermöglicht den Zugriff auf mehrere Ressourcen, wenn das Gerät in eine Domäne eingebunden oder mit Azure AD verknüpft ist.

## Funktionsweise des Microsoft Passport-Lebenszyklus

![Microsoft Passport-Lebenszyklus](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Das obige Diagramm veranschaulicht das private/öffentliche Schlüsselpaar und die Überprüfung durch den Identitätsanbieter. Jeder dieser Schritte wird hier ausführlich erläutert:

1. Der Benutzer weist seine Identität durch mehrere integrierte Überprüfungsmethoden (Gesten, physische Smartcards, mehrstufige Authentifizierung) nach und sendet diese Informationen an einen Identitätsanbieter wie Azure Active Directory oder lokales Active Directory.

2. Das Gerät erstellt dann den Schlüssel, bestätigt den Schlüssel, nimmt den öffentlichen Teil dieses Schlüssels, fügt Senderanweisungen hinzu, führt die Anmeldung durch und sendet den Schlüssel an den Identitätsanbieter, um ihn zu registrieren.

4. Sobald der öffentliche Teil des Schlüssels bei einem Identitätsanbieter registriert ist, fordert dieser das Gerät dazu auf, sich mit dem privaten Teil des Schlüssels anzumelden.

5. Der Identitätsanbieter überprüft dann das Authentifizierungstoken und stellt es aus, damit der Benutzer und das Gerät auf geschützte Ressourcen zugreifen können. Identitätsanbieter können plattformübergreifende Apps schreiben oder die Browserunterstützung (über JavaScript-/Webcrypto-APIs) nutzen, um Microsoft Passport-Anmeldeinformationen für ihre Benutzer zu erstellen und zu verwenden.

## Bereitstellungsanforderungen für Microsoft Passport
### Auf Unternehmensebene

* Das Unternehmen verfügt über ein Azure-Abonnement.

### Auf Benutzerebene

* Auf dem Computer des Benutzers wird Windows 10 Professional oder Enterprise ausgeführt.

Ausführliche Anweisungen zur Bereitstellung finden Sie unter [Microsoft Passport für die Arbeit in der Organisation aktivieren](active-directory-azureadjoin-passport-deployment.md).


## Zusätzliche Informationen

* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0224_2016-->