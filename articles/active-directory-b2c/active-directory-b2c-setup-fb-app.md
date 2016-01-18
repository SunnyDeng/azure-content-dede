<properties
	pageTitle="Vorschau für Azure Active Directory B2C: Facebook-Konfiguration | Microsoft Azure"
	description="Bereitstellen von Registrierung und Anmeldung für Kunden mit Facebook-Konten in mit Azure Active Directory B2C gesicherten Anwendungen"
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

# Vorschau für Azure Active Directory B2C: Bereitstellen von Registrierung und Anmeldung für Kunden mit Facebook-Konten

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen einer Facebook-Anwendung

Um Facebook als Identitätsanbieter in Azure Active Directory (AD) B2C verwenden zu können, müssen Sie zuerst eine Facebook-Anwendung erstellen und die entsprechenden Parameter bereitstellen. Sie benötigen dazu ein Facebook-Konto. Wenn Sie kein Facebook-Konto haben, können Sie ein Konto unter [https://www.facebook.com/](https://www.facebook.com/) erstellen.

1. Navigieren Sie zur [Facebook-Entwickler-Website](https://developers.facebook.com/), und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.
2. Wenn Sie dies noch nicht getan haben, müssen Sie sich als Facebook-Entwickler registrieren. Dazu klicken sie in der oberen rechten Ecke der Seite auf **Register**, akzeptieren die Facebook-Richtlinien und führen die Registrierungsschritte aus.
3. Klicken Sie auf **My Apps** und dann auf **Add a new App**. Wählen Sie dann **Website** als Plattform aus, und klicken Sie auf **Skip and Create App ID**.

    ![FB – Hinzufügen einer neuen App](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB – Hinzufügen einer neuen App – Website](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB – Erstellen einer App-ID](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Geben Sie im Formular einen Wert für **Display name** an, wählen Sie einen Wert für **Category** aus, und klicken Sie auf **Create App ID**. Hinweis: Hierzu müssen Sie die Richtlinien für die Facebook-Plattform akzeptieren und eine Online-Sicherheitsüberprüfung durchführen.

    ![FB – Erstellen einer App-ID](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Klicken Sie in der links angezeigten Navigationsleiste auf **Settings**. Geben Sie eine gültige Adresse unter **Contact Email** ein.
6. Klicken Sie auf **+Add Platform**, und wählen Sie dann **Website** aus.

    ![FB – Einstellungen](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB – Einstellungen](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Geben Sie [https://login.microsoftonline.com/](https://login.microsoftonline.com/) im Feld **Site URL** ein, und klicken Sie dann auf **Save Changes**.
8. Kopieren Sie den Wert unter **App ID**. Klicken Sie auf **Show**, und kopieren Sie den Wert unter **App Secret**. Sie benötigen beide Angaben, um Facebook als Identitätsanbieter in Ihrem Mandanten zu konfigurieren.

> [AZURE.NOTE]Bei **App Secret** handelt es sich um eine wichtige Sicherheitsinformation für die Anmeldung.

    ![FB - Site URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Klicken Sie oben auf die Registerkarte **Advanced**, und geben Sie dann `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` im Feld **Valid OAuth redirect URIs** (im Abschnitt **Security**) ein. Ersetzen Sie dabei **{tenant}** durch den Namen Ihres Mandanten (z. B. „contosob2c.onmicrosoft.com“). Klicken Sie unten auf der Seite auf **Save Changes**.

    ![FB – URI für OAuth-Umleitung](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Damit Ihre Facebook-Anwendung von Azure AD B2C verwendet werden kann, müssen Sie sie öffentlich verfügbar machen. Dazu klicken Sie im linken Navigationsbereich auf **Status & Review** und reichen Ihre Anwendung zur Überprüfung ein (klicken Sie auf die Schaltfläche **Start a Submission**). Nachdem Ihre Anwendung von Facebook genehmigt wurde, können Sie sie öffentlich verfügbar machen, indem Sie den Schalter oben auf der Seite auf **YES** stellen. Klicken Sie dann auf **Confirm**.

    ![FB – Einreichen einer App](./media/active-directory-b2c-setup-fb-app/fb-app-submission.png)

    ![FB – Veröffentlichen einer App](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Konfigurieren von Facebook als Identitätsanbieter in Ihrem Mandanten

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf dem Blatt „B2C-Funktionen“ auf **Identitätsanbieter**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie einen aussagekräftigen Namen für die Konfiguration des Identitätsanbieters unter **Name** ein. Geben Sie z. B. "FB" ein.
5. Klicken Sie auf **Typ des Identitätsanbieters**, wählen Sie **Facebook** aus, und klicken Sie auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**, und geben Sie die **App-ID** und den **geheimen App-Schlüssel** der Facebook-Anwendung, die Sie zuvor erstellt haben, in den Feldern **Client-ID** und **Geheimer Clientschlüssel** ein.
7. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Facebook-Konfiguration zu speichern.

<!---HONumber=AcomDC_0107_2016-->