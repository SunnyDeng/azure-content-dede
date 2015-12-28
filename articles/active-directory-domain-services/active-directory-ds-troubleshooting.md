<properties
	pageTitle="Azure Active Directory-Domänendienste (Vorschau): Handbuch zur Problembehandlung | Microsoft Azure"
	description="Handbuch zur Problembehandlung für Azure AD-Domänendienste"
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

# Azure AD-Domänendienste *(Vorschau)* – Handbuch zur Problembehandlung
Dieser Artikel enthält Tipps zur Behandlung von Problemen, die beim Einrichten oder Verwalten von Azure Active Directory-Domänendiensten auftreten können.


### Benutzer können sich nicht an der verwalteten Domäne der Azure AD-Domänendienste anmelden
Falls sich ein oder mehrere Benutzer innerhalb Ihres Azure AD-Mandanten nicht an der neu erstellten verwalteten Domäne anmelden können, können Sie die folgenden Problembehandlungsschritte ausführen:

- Stellen Sie sicher, dass Sie die [Aktivierung der Kennwortsynchronisierung](active-directory-ds-getting-started-password-sync.md) in Übereinstimmung mit den Schritten durchgeführt haben, die im Leitfaden zu den ersten Schritten angegeben sind.

- Stellen Sie sicher, dass das betreffende Benutzerkonto kein externes Konto des Azure AD-Mandanten ist. Beispiele für externe Konten sind Microsoft-Konten (z. B. „joe@live.com“) oder Benutzerkonten aus einem externen Azure AD-Verzeichnis. Da Azure AD-Domänendienste nicht über Anmeldeinformationen für diese Benutzerkonten verfügen, können sich diese Benutzer nicht an der verwalteten Domäne anmelden.

- Stellen Sie sicher, dass das UPN-Präfix des betreffenden Benutzerkontos (d. h. der erste Teil des Benutzerprinzipalnamens) im Azure AD-Mandanten weniger als 20 Zeichen lang ist. Beispielsweise ist beim UPN „joereallylongnameuser@contoso.com“ das Präfix („joereallylongnameuser“) länger als 20 Zeichen, und dieses Konto wird nicht in der verwalteten Azure Active Directory-Domänendienste-Domäne verfügbar sein.

- **Synchronisierte Konten:** Falls die betroffenen Benutzerkonten über ein lokales Verzeichnis synchronisiert werden, müssen Sie sicherstellen, dass die folgenden Schritte eingehalten werden:
    - Sie haben die neueste empfohlene Version von Azure AD Connect bereitgestellt bzw. das Update darauf durchgeführt.
    - Sie haben den Registrierungsschlüssel erstellt, der für die Synchronisierung von Legacyanmeldeinformationen mit Azure AD erforderlich ist.
    - Nach dem Erstellen des oben erwähnten Registrierungsschlüssels auf dem Server, auf dem Azure AD Connect ausgeführt wird, haben Sie für Azure AD die Durchführung einer vollständigen Synchronisierung gemäß den Anweisungen im Dokument erzwungen.
    - Je nach Größe Ihres Verzeichnisses kann es einige Zeit dauern, bis die Benutzerkonten und Anmeldeinformationshashes in den Azure AD-Domänendiensten verfügbar sind. Warten Sie lange genug, bevor Sie versuchen, die Authentifizierung erneut durchzuführen (abhängig von der Größe Ihres Verzeichnisses – für große Verzeichnisse zwischen einigen Stunden und ein bis zwei Tagen).

- **Nur-Cloudkonten**: Wenn das betroffene Benutzerkonto nur für die Cloud gilt, müssen Sie sicherstellen, dass der Benutzer sein Kennwort geändert hat, nachdem Sie die Azure AD-Domänendienste aktiviert haben. Dieser Schritt führt dazu, dass die Anmeldeinformationshashes für die Azure AD-Domänendienste generiert werden.


### So erreichen Sie uns
Falls Sie Probleme mit Ihrer verwalteten Domäne haben, sollten Sie überprüfen, ob sich das Problem mit den Schritten in diesem Leitfaden zur Problembehandlung beheben lässt. Falls sich die Probleme nicht beseitigen lassen, können Sie sich gern an uns wenden:

- [Azure Active Directory User Voice-Kanal](http://feedback.azure.com/forums/169401-azure-active-directory). Stellen Sie Ihrer Frage das Kürzel **„AADDS“** voran, um uns zu erreichen.
- Sie können uns auch eine E-Mail an [Azure AD-Domänendienste – Feedback](mailto:aaddsfb@microsoft.com) senden.

<!---HONumber=AcomDC_1217_2015-->