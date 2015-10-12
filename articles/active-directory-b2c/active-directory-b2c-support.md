<properties
	pageTitle="Vorschau für Azure Active Directory B2C: Support | Microsoft Azure"
	description="Senden von Supportanfragen für Azure Active Directory B2C"
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
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Vorschau für Azure Active Directory B2C: Senden von Supportanfragen für Azure Active Directory B2C

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Führen Sie die folgenden Schritte aus, um Supportanfragen für Azure Active Directory (AD) B2C im Azure-Vorschauportal zu senden:

1. [Navigieren Sie zum B2C-Featureblatt im Azure-Vorschauportal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Wechseln Sie aus Ihrem B2C-Mandanten in einen anderen Mandanten, dem ein Azure-Abonnement zugeordnet ist. Dies ist normalerweise Ihr Mitarbeitermandant oder der Standardmandant, der bei der Registrierung für ein Azure-Abonnement erstellt wurde. Lesen Sie [diesen Artikel](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad), um weitere Informationen zum Zusammenhang zwischen Azure-Abonnements und Azure AD-Verzeichnissen zu erhalten.

    > [AZURE.IMPORTANT]Dieser Schritt ist erforderlich. Der Vorgang kann nicht abgeschlossen werden, wenn Sie diesen Schritt nicht ausführen.

    ![Support – Wechseln des Verzeichnisses](./media/active-directory-b2c-support/support-switch-dir.png)

3. Klicken Sie nach dem Wechsel des Verzeichnisses auf **Hilfe und Support**.

    ![Support – Support](./media/active-directory-b2c-support/support-support.png)

4. Führen Sie die Schritte in [diesem Artikel](http://blogs.msdn.com/b/mast/archive/2013/10/24/windows-azure-technical-support-for-msdn-technet-or-mpn-users-and-partners.aspx) aus, um eine Supportanfrage für Azure AD B2C zu senden. Verwenden Sie dabei folgende Angaben:

    - Geben Sie für **Anforderungstyp** den Wert **Technischen** ein.
	- **Ressource** ist **Active Directory**.
	- Wählen Sie auf dem Blatt **Problemkategorie** die Option **B2C-Vorschau** als **Problemtyp** sowie den entsprechenden Wert für **Kategorie** aus.
	- Beschreiben Sie auf dem Blatt **Beschreibung** das Problem ausführlicher. Geben Sie im Textfeld **Ressource** den Namen Ihres B2C-Mandanten an, z. B. "contosob2c.onmicrosoft.com".

5. Wenn Ihre Supportanfrage gesendet wurde, können Sie sie überwachen, indem Sie im Startmenü auf **Hilfe und Support** und dann auf **Supportanfragen verwalten** klicken.

## Bekanntes Problem: Senden von Supportanfragen im Kontext eines B2C-Mandanten

Wenn Sie den oben beschriebenen Schritt 2 nicht durchführen und versuchen, eine Supportanfrage im Kontext Ihres B2C-Mandanten zu erstellen, wird der folgende Fehler angezeigt.

> [AZURE.IMPORTANT]Versuchen Sie nicht, sich in Ihrem B2C-Mandanten für ein neues Azure-Abonnement zu registrieren.

![Support – Kein Abonnement](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=Oct15_HO1-->