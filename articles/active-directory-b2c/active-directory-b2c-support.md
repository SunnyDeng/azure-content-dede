<properties
	pageTitle="Vorschau für Azure Active Directory B2C: Support | Microsoft Azure"
	description="Senden von Supportanfragen für Azure Active Directory B2C"
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
	ms.date="02/16/2016"
	ms.author="swkrish"/>

# Vorschau für Azure Active Directory B2C: Senden von Supportanfragen für Azure Active Directory B2C

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Führen Sie die folgenden Schritte aus, um Supportanfragen für Azure Active Directory (AD) B2C im Azure-Portal zu senden:

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Wechseln Sie aus Ihrem B2C-Mandanten in einen anderen Mandanten, dem ein Azure-Abonnement zugeordnet ist. Dies ist normalerweise Ihr Mitarbeitermandant oder der Standardmandant, der bei der Registrierung für ein Azure-Abonnement erstellt wurde. Lesen Sie [diesen Artikel](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad), um weitere Informationen zum Zusammenhang zwischen Azure-Abonnements und Azure AD-Mandanten zu erhalten.

    ![Support: Mandantenwechsel](./media/active-directory-b2c-support/support-switch-dir.png)

3. Klicken Sie nach dem Wechsel von Mandanten auf **Hilfe und Support**.

    ![Support: Hilfe und Support](./media/active-directory-b2c-support/support-support.png)

4. Klicken Sie auf **Neue Supportanfrage**.

    ![Support: Neu](./media/active-directory-b2c-support/support-new.png)

5. Verwenden Sie diese Informationen auf dem Blatt **Grundlagen**, und klicken Sie auf **Weiter**.

    - Wählen Sie für **Problemtyp** den Eintrag **Technisch** aus.
	- Wählen Sie das entsprechende **Abonnement**.
    - Wählen Sie unter **Dienst** die Option **Active Directory**.
    - Wählen Sie den entsprechenden **Supportplan**. Wenn Sie keinen besitzen, können Sie sich [hier](https://azure.microsoft.com/de-DE/support/plans/) für einen Supportplan registrieren.

    ![Support: Grundlagen](./media/active-directory-b2c-support/support-basics.png)

6. Verwenden Sie diese Informationen auf dem Blatt **Problem**, und klicken Sie auf **Weiter**.

    - Wählen Sie den entsprechenden **Schweregrad**.
    - Der **Problemtyp** lautet **B2C-Vorschau**
    - Wählen Sie die entsprechende **Kategorie**.
	- Beschreiben Sie Ihr Problem im Feld **Details**. Geben Sie Details wie den Namen des B2C-Mandanten, eine Beschreibung des Problems, Fehlermeldungen, Korrelations-IDs (falls verfügbar) usw. an.
    - Geben Sie im Feld **Zeitrahmen** mit Datum und Uhrzeit (einschließlich Zeitzone) an, wann das Problem aufgetreten ist.
    - Laden Sie unter **Dateiupload** alle Screenshots und Dateien hoch, die Ihrer Meinung nach bei der Behebung des Problems hilfreich sein könnten.

    ![Support: Problem](./media/active-directory-b2c-support/support-problem.png)

7. Fügen Sie auf dem Blatt **Kontakt** Ihre Kontaktinformationen hinzu. Klicken Sie auf **Erstellen**. *Hinweis: Während der Vorschau steht der Support für Azure AD B2C nur auf Englisch zur Verfügung.*

    ![Support: Kontakt](./media/active-directory-b2c-support/support-contact.png)

8. Wenn Ihre Supportanfrage gesendet wurde, können Sie sie überwachen, indem Sie im Startmenü auf **Hilfe und Support** und dann auf **Supportanfragen verwalten** klicken.

## Bekanntes Problem: Senden von Supportanfragen im Kontext eines B2C-Mandanten

Wenn Sie den oben beschriebenen Schritt 2 nicht durchführen und versuchen, eine Supportanfrage im Kontext Ihres B2C-Mandanten zu erstellen, wird der folgende Fehler angezeigt.

> [AZURE.IMPORTANT]
Versuchen Sie nicht, sich in Ihrem B2C-Mandanten für ein neues Azure-Abonnement zu registrieren.

![Support – Kein Abonnement](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0218_2016-->