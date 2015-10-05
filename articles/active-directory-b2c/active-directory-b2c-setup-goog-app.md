<properties
	pageTitle="Vorschau für Azure Active Directory B2C: Konfiguration für Google+ | Microsoft Azure"
	description="Bereitstellen von Registrierung und Anmeldung für Kunden mit Konten bei Google+ in mit Azure Active Directory B2C gesicherten Anwendungen"
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

# Vorschau für Azure Active Directory B2C: Bereitstellen von Registrierung und Anmeldung für Kunden mit Konten von Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen einer Google+-Anwendung

Um Google+ als Identitätsanbieter in Azure Active Directory (AD) B2C verwenden zu können, müssen Sie zuerst eine Google+-Anwendung erstellen und die entsprechenden Parameter bereitstellen. Sie benötigen dazu ein Konto von Google+. Wenn Sie kein solches Konto haben, können Sie ein Konto unter [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) erstellen.

1. Klicken Sie auf die [Google Developers Console](https://console.developers.google.com/), und melden Sie sich mit Ihren Anmeldeinformationen für Google+ an.
2. Klicken Sie auf **Create Project**, geben Sie einen Namen unter **Project name** ein, stimmen Sie den Nutzungsbedingungen zu, und klicken Sie dann auf **Create**.

    ![G+ – Erste Schritte](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ – Neues Projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Klicken Sie links im Navigationsbereich auf **APIs & Auth** und dann auf **Credentials**.
4. Klicken Sie oben auf die Registerkarte **OAuth consent screen**.

    ![G+ – Anmeldeinformationen](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Wählen Sie unter **Email address** eine gültige Adresse aus, oder geben Sie sie ein, geben Sie einen Wert für **Product name** ein, und klicken Sie auf **Save**.

    ![G+ – OAuth-Zustimmungsbildschirm](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Wählen Sie unter **Application type** die Option **Web application** aus.
7. Geben Sie unter **Name** einen Namen für die Anwendung ein, geben Sie [https://login.microsoftonline.com](https://login.microsoftonline.com) im Feld **Authorized redirect URIs** und [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) im Feld **Authorized redirect URIs** ein. Ersetzen Sie dabei **{directory}** durch den Namen Ihres Verzeichnisses (z. B. "contosob2c.onmicrosoft.com"). Klicken Sie auf **Erstellen**.

    > [AZURE.NOTE]Beim Wert für **{directory}** wird die Groß-/Kleinschreibung beachtet.

    ![G+ – Erstellen der Client-ID](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. Kopieren Sie die Werte für **Client ID** und **Client Secret**. Sie benötigen beide Angaben, um Google+ als Identitätsanbieter in Ihrem Verzeichnis zu konfigurieren.

    > [AZURE.NOTE]Der **geheime Clientschlüssel** ist eine wichtige Anmeldeinformation.

    ![G+ – Geheimer Clientschlüssel](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Konfigurieren von Google+ als Identitätsanbieter in Ihrem Verzeichnis

1. [Navigieren Sie zum B2C-Featureblatt im Azure-Vorschauportal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf dem B2C-Featureblatt auf **Identitätsanbieter für soziale Medien**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie einen aussagekräftigen Namen für die Konfiguration des Identitätsanbieters unter **Name** ein. Geben Sie z. B. "G+" ein.
5. Klicken Sie auf **Typ des Identitätsanbieters**, wählen Sie **Google** aus, und klicken Sie auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**, und geben Sie die **Client-ID** und den **geheimen App-Schlüssel** der Google+-Anwendung ein, die Sie zuvor erstellt haben.
7. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration für Google+ zu speichern.

<!---HONumber=Sept15_HO4-->