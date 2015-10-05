<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Multi-Factor Authentication | Microsoft Azure"
	description="Aktivieren der Multi-Factor Authentication in kundenorientierten Anwendungen, die mit Azure Active Directory B2C geschützt werden"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Aktivieren der Multi-Factor Authentication in kundenorientierten Anwendungen

Azure Active Directory (AD) B2C bietet eine direkte Integration in die [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md), damit Sie einfach eine zweite Sicherheitsebene zu Ihren Registrierungs- und Anmeldeerfahrungen in kundenorientierten Anwendungen hinzufügen können. Sie erreichen dies, ohne eine einzige Codezeile schreiben zu müssen. Derzeit werden Telefonanrufe und Textnachrichten als Überprüfungsoptionen unterstützt. Wenn Sie bereits Registrierungs- und Anmelderichtlinien erstellt haben (wie in [diesem Artikel]() erläutert), können Sie die Multi-Factor Authentication wie in den folgenden Abschnitten dargestellt aktivieren.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]Die Multi-Factor Authentication kann auch beim Erstellen der Registrierungs- und Anmelderichtlinien aktiviert werden und nicht nur durch Bearbeiten von vorhandenen Richtlinien.

Mithilfe dieses Features können Anwendungen Szenarien wie die folgenden behandeln:

- Für den Zugriff auf eine Anwendung ist keine Multi-Factor Authentication erforderlich, jedoch für den Zugriff auf eine andere Anwendung. Beispielsweise kann sich der Kunde bei der Anwendung einer Kfz-Versicherung mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks anmelden, er muss aber seine Telefonnummer bestätigen, bevor er auf die Anwendung für die Hausversicherung zugreifen kann, die im gleichen Verzeichnis registriert ist.
- Die Multi-Factor Authentication ist allgemein für den Zugriff auf eine Anwendung nicht erforderlich, aber für vertrauliche Unterbereiche dieser Anwendung. Beispielsweise kann sich der Kunde bei einer Onlinebanking-Anwendung mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks anmelden, um den Kontostand abzufragen. Um eine Überweisung zu tätigen, ist jedoch die Bestätigung der Telefonnummer erforderlich.

## Ändern der Registrierungsrichtlinie, um die Multi-Factor Authentication zu aktivieren

1. [Navigieren Sie zum B2C-Featureblatt im Azure-Vorschauportal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Registrierungsrichtlinien**.
3. Öffnen Sie die Registrierungsrichtlinie (z. B. "B2C\_1\_SiUp"), indem Sie darauf klicken.
4. Klicken Sie auf **Multi-Factor Authentication**, und legen Sie den **Status** auf **EIN** fest. Klicken Sie auf **OK**.
5. Klicken Sie oben auf dem Blatt auf **Speichern**. Fertig.

Mit dem Feature "Jetzt ausführen" für die Richtlinie können Sie die Benutzererfahrung überprüfen. Folgendes sollte angezeigt werden:

In Ihrem Verzeichnis wird ein Kundenkonto erstellt, bevor der Schritt für die Multi-Factor Authentication ausgeführt wird. Während dieses Schritts wird der Kunde aufgefordert, seine Telefonnummer anzugeben und zu bestätigen. Wenn die Überprüfung erfolgreich ist, wird die Telefonnummer zur späteren Verwendung an das Kundenkonto angefügt. Auch wenn der Kunde den Vorgang abbricht, kann er bei der nächsten Anmeldung aufgefordert werden, eine Telefonnummer erneut zu bestätigen (sofern die Multi-Factor Authentication aktiviert ist, siehe nächsten Abschnitt).

## Ändern der Anmelderichtlinie, um die Multi-Factor Authentication zu aktivieren

1. Navigieren Sie zum B2C-Featureblatt im [Azure-Vorschauportal](htts://portal.azure.com/). Informationen zur Vorgehensweise finden Sie [hier](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Anmelderichtlinien**.
3. Öffnen Sie die Anmelderichtlinie (z. B. "B2C\_1\_SiIn"), indem Sie darauf klicken. Klicken Sie oben auf dem Blatt auf **Bearbeiten**.
4. Klicken Sie auf **Multi-Factor Authentication**, und legen Sie den **Status** auf **EIN** fest. Klicken Sie auf **OK**.
5. Klicken Sie oben auf dem Blatt auf **Speichern**. Fertig.

Mit dem Feature "Jetzt ausführen" für die Richtlinie können Sie die Benutzererfahrung überprüfen. Folgendes sollte angezeigt werden:

Wenn sich der Kunde (mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks) anmeldet und eine bestätigte Telefonnummer an das Kundenkonto angefügt wurde, wird der aufgefordert, diese zu bestätigen. Wenn keine Telefonnummer angefügt ist, wird der Kunde aufgefordert, eine anzugeben und zu bestätigen. Bei erfolgreicher Bestätigung wird die Telefonnummer zur späteren Verwendung an das Kundenkonto angefügt.

<!---HONumber=Sept15_HO4-->