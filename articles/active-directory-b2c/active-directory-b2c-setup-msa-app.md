<properties
	pageTitle="Vorschau für Azure Active Directory B2C: Konfiguration für Microsoft-Konto | Microsoft Azure"
	description="Bereitstellen von Registrierung und Anmeldung für Kunden mit Microsoft-Konten in mit Azure Active Directory B2C gesicherten Anwendungen"
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
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Vorschau für Azure Active Directory B2C: Bereitstellen von Registrierung und Anmeldung für Kunden mit Microsoft-Konten

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen einer Microsoft-Kontoanwendung

Um den Dienst „Microsoft-Konto“ als Identitätsanbieter in Azure Active Directory (AD) B2C verwenden zu können, müssen Sie zuerst eine Microsoft-Kontoanwendung erstellen und die entsprechenden Parameter bereitstellen. Sie benötigen dazu ein Microsoft-Konto. Wenn Sie kein Microsoft-Konto haben, können Sie es unter [https://www.live.com/](https://www.live.com/) erstellen.

1. Navigieren Sie zum [Microsoft-Konto – Developer Center](https://account.live.com/developers/applications), und melden Sie sich mit Ihren Anmeldeinformationen für das Microsoft-Konto an.
2. Klicken Sie auf **Anwendung erstellen**.

    ![MSA – Hinzufügen einer neuen App](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Geben Sie unter **Name der App** einen Namen für Ihre Anwendung an, und klicken Sie auf **Ich stimme zu**. Hinweis: Sie müssen hier die Nutzungsbedingungen für Microsoft-Dienste akzeptieren.

    ![MSA – Name der App](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Klicken Sie links im Navigationsbereich auf **API-Einstellungen**. Geben Sie eine gültige Adresse unter **Kontakt-E-Mail** ein.

    ![MSA – API-Einstellungen](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Geben Sie in das Feld **Umleitungs-URLs** den Wert `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` ein. Ersetzen Sie dabei **{tenant}** durch den Namen Ihres Mandanten (z. B. „contosob2c.onmicrosoft.com“). Klicken Sie unten auf der Seite auf **Speichern**.

    ![MSA – Umleitungs-URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. Klicken Sie links im Navigationsbereich auf **App-Einstellungen**. Kopieren Sie die Werte für **Client-ID** und **Geheimer Clientschlüssel**. Sie benötigen beide Angaben, um Microsoft-Konto als Identitätsanbieter in Ihrem Mandanten zu konfigurieren.

> [AZURE.NOTE]Der **geheime Clientschlüssel** ist eine wichtige Anmeldeinformation.

    ![MSA - Client secret](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Konfigurieren von Microsoft-Konto als Identitätsanbieter in Ihrem Mandanten

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf dem Blatt „B2C-Funktionen“ auf **Identitätsanbieter**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie einen aussagekräftigen Namen für die Konfiguration des Identitätsanbieters unter **Name** ein. Geben Sie z. B. „MSA“ ein.
5. Klicken Sie auf **Typ des Identitätsanbieters**, wählen Sie **Microsoft-Konto** aus, und klicken Sie auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**, und geben Sie die **Client-ID** und den **geheimen Clientschlüssel** der Microsoft-Kontoanwendung ein, die Sie zuvor erstellt haben.
7. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration für das Microsoft-Konto zu speichern.

<!---HONumber=AcomDC_0107_2016-->