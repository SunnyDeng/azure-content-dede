<properties
	pageTitle="Azure Active Directory: Supportthema zur Verzeichniserstellung | Microsoft Azure"
	description="Erstellen eines Azure Active Directory-Verzeichnisses oder eines Azure Active Directory B2C-Verzeichnisses – Probleme und Lösungen"
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

# Erstellen eines Azure AD-Verzeichnisses oder eines Azure AD B2C-Verzeichnisses – Probleme und Lösungen

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen eines Azure AD-Verzeichnisses

Wenn Sie beim ersten Mal kein Azure AD-Verzeichnis erstellen können, versuchen Sie es noch mal. Wenn das Problem weiterhin besteht, wenden Sie sich an den Support.

## Erstellen eines Azure AD B2C-Verzeichnisses (Vorschau)

Es gibt bekannte Probleme, die beim [Erstellen eines Azure AD B2C-Verzeichnisses](active-directory-b2c-get-started) auftreten können.

- Wenn das Azure AD B2C-Verzeichnis nicht in Ihrer Liste der Verzeichnisse angezeigt wird, versuchen Sie es noch mal.
- Wenn das Azure AD B2C-Verzeichnis in Ihrer Liste der Verzeichnisse angezeigt wird, aber auch eine Fehlermeldung angezeigt wird, die besagt "Erstellung des B2C-Verzeichnisses 'contosob2c' konnte nicht abgeschlossen werden". Besuchen Sie den [Link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409), um weitere Anleitung zu erhalten, und führen Sie dann eine der folgenden Aktionen aus:
    - Navigieren Sie zum B2C-Features-Blatt im Azure-Vorschauportal über den folgenden Link [https://portal.azure.com/{Verzeichnis}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{Verzeichnis}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com). Ersetzen Sie hierbei **{Verzeichnis}** durch den Namen, der zum Zeitpunkt der Erstellung des Verzeichnisses verwendet wurde (z. B. "contosob2c"), und melden Sie sich als globaler Administrator an. Klicken Sie am oberen Rand des Blades auf **B2C-Features aktivieren**, und klicken Sie auf **OK**. Ihr Azure AD B2C-Verzeichnis sollte nun einsatzbereit sein.
	- Löschen Sie das soeben erstellte Verzeichnis, und versuchen Sie es noch mal.
- Wenn keine der oben aufgeführten Lösungen für Sie funktioniert, wenden Sie sich an den Support. In [diesem Artikel](active-directory-b2c-support.md) erhalten Sie Informationen zum Senden von Supportanfragen für Azure AD B2C.

<!---HONumber=Sept15_HO4-->