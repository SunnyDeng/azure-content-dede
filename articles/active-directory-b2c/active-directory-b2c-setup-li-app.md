<properties
	pageTitle="Vorschau für Azure Active Directory B2C: LinkedIn-Konfiguration | Microsoft Azure"
	description="Bereitstellen von Registrierung und Anmeldung für Kunden mit LinkedIn-Konten in mit Azure Active Directory B2C gesicherten Anwendungen"
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Bereitstellen von Registrierung und Anmeldung für Kunden mit LinkedIn-Konten

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen einer LinkedIn-Anwendung

Um LinkedIn als Identitätsanbieter in Azure Active Directory (Azure AD) B2C verwenden zu können, müssen Sie eine LinkedIn-Anwendung erstellen und mit den entsprechenden Parametern bereitstellen. Sie benötigen dazu ein LinkedIn-Konto. Wenn Sie über kein Konto verfügen, können Sie unter [https://www.linkedin.com/](https://www.linkedin.com/) eines erstellen.

1. Navigieren Sie zur [LinkedIn-Entwickler-Website](https://www.developer.linkedin.com/), und melden Sie sich mit den Anmeldeinformationen für Ihr LinkedIn-Konto an.
2. Klicken Sie in der oberen Menüleiste auf **My Apps** und dann auf **Create Application**.

    ![LinkedIn – Neue App](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Geben Sie im Formular **Neue Anwendung erstellen** die relevanten Informationen ein (**Firmenname**, **Name**, **Beschreibung**, **Anwendungslogo-URL**, **Anwendungsverwendung**, **Website-URL**, **E-Mail-Adresse (geschäftlich)** und **Telefon (geschäftlich)**).
4. Akzeptieren Sie die **LinkedIn API Terms of Use**, und klicken Sie auf **Submit**.

    ![LinkedIn – Registrieren einer App](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Kopieren Sie die Werte für **Client ID** und **Client Secret**. (Sie finden sie unter **Authentication Keys**.) Sie benötigen beide Angaben, um LinkedIn als Identitätsanbieter in Ihrem Mandanten zu konfigurieren.

	>[AZURE.NOTE] Der **geheime Clientschlüssel** ist eine wichtige Sicherheitsanmeldeinformation.

6. Geben Sie im Feld **Authorized Redirect URLs** (unter **OAuth 2.0**) den Wert `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` ein. Ersetzen Sie **{tenant}** durch den Namen Ihres Mandanten (z. B. „contoso.onmicrosoft.com“). Klicken Sie auf **Add** und dann auf **Update**.

    ![LinkedIn – Einrichten einer App](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Konfigurieren von LinkedIn als Identitätsanbieter in Ihrem Mandanten

1. Führen Sie diese Schritte aus, um im Azure-Portal [zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf dem Blatt „B2C-Funktionen“ auf **Identitätsanbieter**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie einen aussagekräftigen Namen für die Konfiguration des Identitätsanbieters unter **Name** ein. Geben Sie z. B. "LI" ein.
5. Klicken Sie auf **Typ des Identitätsanbieters**, wählen Sie **LinkedIn** aus, und klicken Sie auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**, und geben Sie die Client-ID und den geheimen Clientschlüssel der LinkedIn-Anwendung ein, die Sie zuvor erstellt haben.
7. Klicken Sie auf **OK** und dann auf **Erstellen**, um die LinkedIn-Konfiguration zu speichern.

<!---HONumber=AcomDC_0224_2016-->