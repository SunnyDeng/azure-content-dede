<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Multi-Factor Authentication | Microsoft Azure"
	description="Aktivieren der Multi-Factor Authentication in kundenorientierten Anwendungen, die mit Azure Active Directory B2C geschützt werden"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Aktivieren der Multi-Factor Authentication in kundenorientierten Anwendungen

Azure Active Directory (Azure AD) B2C bietet eine direkte Integration in die [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md), damit Sie eine zweite Sicherheitsebene zu Registrierungs- und Anmeldeoberflächen in kundenorientierten Anwendungen hinzufügen können. Sie erreichen dies, ohne eine einzige Codezeile schreiben zu müssen. Derzeit wird eine Überprüfung per Telefonanruf und Textnachricht unterstützt. Sie können die Multi-Factor Authentication auch dann aktivieren, wenn Sie bereits Registrierungs- und Anmelderichtlinien erstellt haben.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
Die Multi-Factor Authentication kann auch beim Erstellen von Registrierungs- und Anmelderichtlinien aktiviert werden und nicht nur durch Bearbeiten von vorhandenen Richtlinien.

Mithilfe dieses Features können Anwendungen z. B. folgende Szenarios bewältigen:

- Für den Zugriff auf eine Anwendung ist keine Multi-Factor Authentication erforderlich, jedoch für den Zugriff auf eine andere Anwendung. Beispielsweise kann sich der Kunde bei der Anwendung einer Kfz-Versicherung mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks anmelden. Er muss jedoch seine Telefonnummer bestätigen, bevor er auf die Anwendung für die Hausversicherung zugreifen kann, die im selben Verzeichnis registriert ist.
- Die Multi-Factor Authentication ist allgemein für den Zugriff auf eine Anwendung nicht erforderlich, jedoch für vertrauliche Unterbereiche dieser Anwendung. Beispielsweise kann sich der Kunde bei einer Onlinebanking-Anwendung mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks anmelden, um den Kontostand abzufragen. Um eine Überweisung zu tätigen, ist jedoch die Bestätigung der Telefonnummer erforderlich.

## Ändern der Registrierungsrichtlinie zur Aktivierung der Multi-Factor Authentication

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Registrierungsrichtlinien**.
3. Klicken Sie auf die Registrierungsrichtlinie (z. B. „B2C\_1\_SiUp“), um sie zu öffnen.
4. Klicken Sie auf **Multi-Factor Authentication**, und legen Sie den **Status** auf **EIN** fest. Klicken Sie auf **OK**.
5. Klicken Sie oben auf dem Blatt auf **Speichern**.

Mit dem Feature "Jetzt ausführen" für die Richtlinie können Sie die Benutzererfahrung überprüfen. Überprüfen Sie Folgendes:

In Ihrem Verzeichnis wird ein Kundenkonto erstellt, bevor der Schritt für die Multi-Factor Authentication ausgeführt wird. Während dieses Schritts wird der Kunde aufgefordert, seine Telefonnummer anzugeben und zu bestätigen. Wenn die Überprüfung erfolgreich ist, wird die Telefonnummer zur späteren Verwendung an das Kundenkonto angefügt. Auch wenn der Kunde den Vorgang abbricht, kann er bei der nächsten Anmeldung aufgefordert werden, eine Telefonnummer erneut zu bestätigen (bei aktivierter Multi-Factor Authentication).

## Ändern der Anmelderichtlinie zur Aktivierung der Multi-Factor Authentication

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Anmelderichtlinien**.
3. Klicken Sie auf die Anmelderichtlinie (z. B. „B2C\_1\_SiIn“), um sie zu öffnen. Klicken Sie oben auf dem Blatt auf **Bearbeiten**.
4. Klicken Sie auf **Multi-Factor Authentication**, und legen Sie den **Status** auf **EIN** fest. Klicken Sie auf **OK**.
5. Klicken Sie oben auf dem Blatt auf **Speichern**.

Mit dem Feature "Jetzt ausführen" für die Richtlinie können Sie die Benutzererfahrung überprüfen. Überprüfen Sie Folgendes:

Wenn sich der Kunde (mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks) anmeldet und eine bestätigte Telefonnummer an das Kundenkonto angefügt wurde, wird der aufgefordert, diese zu bestätigen. Wenn keine Telefonnummer angefügt wurde, wird der Kunde aufgefordert, eine Telefonnummer anzugeben und zu bestätigen. Bei erfolgreicher Überprüfung wird die Telefonnummer zur späteren Verwendung an das Kundenkonto angefügt.

<!---HONumber=AcomDC_0224_2016-->