<properties
	pageTitle="Zuweisen von Benutzern zu einer benutzerdefinierten Domäne in Azure Active Directory | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine benutzerdefinierte Domäne in Azure Active Directory mit Benutzerkonten auffüllen."
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
	ms.date="02/03/2016"
	ms.author="curtand;jeffsta"/>

# Zuweisen von Benutzern zu einer benutzerdefinierten Domäne

Nachdem Sie Ihre benutzerdefinierte Domäne mit Azure Active Directory hinzugefügt haben, müssen Sie die Benutzerkonten für diese Domäne hinzufügen, damit Sie mit deren Authentifizierung beginnen können.

## Benutzer, die aus einem Verzeichnis in Ihrem Unternehmensnetzwerk synchronisiert werden

Wenn Sie bereits eine Verbindung zwischen Ihrem lokalen Active Directory und Azure Active Directory eingerichtet haben, kann die Synchronisierung Konten auffüllen. Weitere Informationen zum Synchronisieren von Azure Active Directory mit Ihrem lokalen Active Directory finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).

## Benutzer, die in der Cloud hinzugefügt und verwaltet werden

So ändern Sie die Domäne für ein vorhandenes Benutzerkonto

1.  Öffnen Sie das klassische Azure-Portal mit einem Konto, das ein globaler Administrator oder ein Benutzeradministrator ist.

2.  Öffnen Sie das Verzeichnis.

3.  Klicken Sie auf die Registerkarte **Benutzer**.

4.  Wählen Sie den Benutzer aus der Liste aus.

5.  Ändern Sie die Domäne für den Benutzer, und wählen Sie dann **Speichern** aus.

Sie können dies auch mit [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) oder der [Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) durchführen.

## Auswählen einer benutzerdefinierten Domäne beim Erstellen neuer Benutzer

1.  Öffnen Sie das klassische Azure-Portal mit einem Konto, das ein globaler Administrator oder ein Benutzeradministrator ist.

2.  Öffnen Sie das Verzeichnis.

3.  Klicken Sie auf die Registerkarte **Benutzer**.

4.  Wählen Sie in der Befehlsleiste **Hinzufügen** aus.

5.  Wenn Sie den Benutzernamen hinzufügen, wählen Sie die benutzerdefinierte Domäne aus der Domänenliste aus.

6.  Wählen Sie **Speichern** aus.

## Nächste Schritte

- [Verwenden von benutzerdefinierten Domänennamen zum Vereinfachen des Anmeldevorgangs für Benutzer](active-directory-add-domain.md)
- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Hinzufügen und Überprüfen eines benutzerdefinierten Domänennamens in Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Ändern der DNS-Registrierungsstelle für Ihren benutzerdefinierten Domänennamen](active-directory-add-domain-change-registrar.md)
- [Löschen einer benutzerdefinierten Domäne in Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->