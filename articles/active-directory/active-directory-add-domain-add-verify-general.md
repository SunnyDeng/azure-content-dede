<properties
	pageTitle="Hinzufügen und Überprüfen eines benutzerdefinierten Domänennamens in Azure Active Directory | Microsoft Azure"
	description="Vorgehensweise zum Hinzufügen vorhandener Domänen zu Azure Active Directory im Rahmen der ersten Schritte in Azure AD. Richten Sie Ihre benutzerdefinierte Domäne so ein, dass Benutzerkontoinformationen mit Ihrer lokalen Identitätsinfrastruktur synchronisiert werden."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Hinzufügen und Überprüfen eines benutzerdefinierten Domänennamens in Azure Active Directory

Um einen benutzerdefinierten Domänennamen hinzuzufügen und ihn für die Verwendung mit Ihrem Azure Active Directory zu überprüfen, müssen Sie die folgenden Schritte ausführen.

1.  Führen Sie einen der folgenden Schritte aus:

    -   [Hinzufügen eines benutzerdefinierten Domänennamens, der im Verbund mit einem lokalen Active Directory verwendet wird](#add-a-custom-domain-name-that-will-be-federated)

    -   [Hinzufügen eines benutzerdefinierten Domänennamens, der nicht im Verbund mit einem lokalen Active Directory verwendet wird](#add-a-custom-domain-name-that-will-not-be-federated)

2.  Prüfen Sie den benutzerdefinierten Domänennamen.

    -   Fügen Sie die DNS-Einträge hinzu, anhand derer Azure AD bei der Domänennamen-Registrierungsstelle für Ihre Domäne überprüfen kann, dass Ihre Organisation den benutzerdefinierten Domänennamen besitzt.

    -   Überprüfen Sie die Domäne in Azure AD.

## Hinzufügen eines benutzerdefinierten Domänennamens, der im Verbund verwendet werden soll

Weitere Informationen zur lokalen Verzeichnisintegration mit Azure AD Connect finden Sie unter

**So fügen Sie Ihrem Azure AD-Verzeichnis einen benutzerdefinierten Domänennamen hinzu**

1.  Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) über ein Konto an, das globale Administratorberechtigungen in Azure AD besitzt.

2.  Wählen Sie Active Directory aus.

3.  Öffnen Sie Ihr Verzeichnis.

4.  Wählen Sie die Registerkarte **Domänen**.

5.  Wählen Sie in der Befehlsleiste **Hinzufügen** aus.

6.  Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein. Achten Sie darauf, dass die Erweiterung „.com“ mit angegeben wird.

7.  Aktivieren Sie das Kontrollkästchen **Ich habe vor, diese Domäne für die einmalige Anmeldung bei meinem lokalen Active Directory-Verzeichnis zu konfigurieren**.

8.  Klicken Sie auf **Hinzufügen**.

## Hinzufügen eines benutzerdefinierten Domänennamens, der nicht im Verbund verwendet werden soll

Die meisten benutzerdefinierten Domänennamen sind Domänen der zweiten Ebene, „contoso.com“

**So fügen Sie Ihrem Azure AD-Verzeichnis den benutzerdefinierten Domänennamen hinzu**

1.  Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) über ein Konto an, das globale Administratorberechtigungen in Azure AD besitzt.

2.  Wählen Sie Active Directory aus.

3.  Öffnen Sie Ihr Verzeichnis.

4.  Wählen Sie die Registerkarte **Domänen**.

5.  Wählen Sie in der Befehlsleiste **Hinzufügen** aus.

6.  Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein. Achten Sie darauf, dass die Erweiterung „.com“ mit angegeben wird.

7.  Stellen Sie sicher, dass das Kontrollkästchen **Ich habe vor, diese Domäne für die einmalige Anmeldung bei meinem lokalen Active Directory-Verzeichnis zu konfigurieren** deaktiviert ist.

8.  Wählen Sie **Hinzufügen**.

9.  Prüfen Sie, ob die Domäne Ihrem Verzeichnis hinzugefügt wurde.

## Überprüfen einer Domäne bei einer Domänennamen-Registrierungsstelle

Zum Überprüfen Ihrer Domäne erstellen Sie einen DNS-Eintrag bei der Domänennamen-Registrierungsstelle (oder dort, wo Ihr DNS gehostet wird), und Azure AD verwendet diesen Datensatz, um zu bestätigen, dass Sie die Domäne besitzen. [Anleitung für das Hinzufügen von DNS-Einträgen bei gängigen DNS-Registrierungsstellen](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

Wenn Sie die Domäne bereits bei einer Domänennamen-Registrierungsstelle registriert haben, sind die erforderlichen DNS-Einträge bereits vorhanden.

Wenn Sie eine benutzerdefinierte Domäne hinzugefügt haben, diese aber noch nicht überprüft wurde, wird der Status **Nicht überprüft** angezeigt.

## Überprüfen eines benutzerdefinierten Domänennamens, der nicht im Verbund mit einem lokalen Verzeichnis verwendet werden soll
Nachdem alle Einträge, die Sie für Ihre Domäne erstellt haben, erfolgreich über das DNS-System bei Ihrer Domänenregistrierungsstelle hinzugefügt wurden, führen Sie folgende Schritte aus:

1.  Klicken Sie in Azure Active Directory im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf **Domänen**.

2.  Suchen Sie in der Liste **Domänen** die Domäne, die Sie überprüfen möchten, und klicken Sie dann je nach verwendetem Portal entweder auf **Klicken Sie zum Überprüfen der Domäne** oder auf **Überprüfen**.

3.  Befolgen Sie die Anweisungen zum Abschließen des Überprüfungsprozesses.

    -   Bei erfolgreicher Domänenüberprüfung werden Sie benachrichtigt, dass Ihre Domäne zu Ihrem Konto hinzugefügt wurde.

    -   Wenn die Domänenüberprüfung fehlschlägt, benötigen die bei der Domänenregistrierungsstelle vorgenommenen Änderungen unter Umständen mehr Zeit für die Verteilung. Brechen Sie die Überprüfung ab, und versuchen Sie es später noch einmal.

Wenn seit den Änderungen an Ihrer Domäne mehr als 72 Stunden vergangen sind, melden Sie sich bei der Website der Domänenregistrierungsstelle an, und überprüfen Sie, ob Sie die Aliasinformationen korrekt eingegeben haben. Wenn die Angaben falsch eingegeben wurden, müssen Sie den fehlerhaften DNS-Eintrag entfernen und einen neuen mit den richtigen Informationen erstellen.

## Überprüfen Ihrer benutzerdefinierten Domäne für den Verbund mit Ihrem lokalen Verzeichnis

1.  Laden Sie Azure AD Connect herunter, und führen Sie es aus. Das Tool Azure AD Connect [fordert Sie zum Hinzufügen der von ihm bereitgestellten DNS-Einträge auf](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

## Domänennamen der dritten Ebene

Sie können Domänen der dritten Ebene, z. B. „europe.contoso.com“ mit Azure AD verwenden. So fügen Sie eine Domäne der dritten Ebene hinzu und verwenden diese:

1.  Fügen Sie die Domäne der zweiten Ebene, „contoso.com“, hinzu, und überprüfen Sie sie.

2.  Fügen Sie Azure AD Unterdomänen wie z. B. „europe.contoso.com“ hinzu. Wenn Sie eine Unterdomäne einer überprüften Domäne der zweiten Ebene hinzufügen, wird die Domäne der dritten Ebene automatisch von Azure AD überprüft. Sie müssen keine weiteren DNS-Einträge hinzufügen.

Diese Schritte können auch mithilfe von PowerShell und Graph ausgeführt werden.

Nachdem Sie Ihre Domäne überprüft haben, können Sie sie so konfigurieren, dass sie mit Ihren Konten funktioniert.

## Nächste Schritte

- [Verwenden von benutzerdefinierten Domänennamen zum Vereinfachen des Anmeldevorgangs für Benutzer](active-directory-add-domain.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Zuweisen von Benutzern zu einer benutzerdefinierten Domäne](active-directory-add-domain-add-users.md)
- [Ändern der DNS-Registrierungsstelle für Ihren benutzerdefinierten Domänennamen](active-directory-add-domain-change-registrar.md)
- [Löschen einer benutzerdefinierten Domäne in Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0302_2016-->