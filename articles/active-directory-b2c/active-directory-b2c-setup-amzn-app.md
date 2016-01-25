<properties
	pageTitle="Vorschau für Azure Active Directory B2C: Amazon-Konfiguration | Microsoft Azure"
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

# Vorschau für Azure Active Directory B2C: Bereitstellen von Registrierung und Anmeldung für Kunden mit Amazon-Konten

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen einer Amazon-Anwendung

Um Amazon als Identitätsanbieter in Azure Active Directory (AD) B2C verwenden zu können, müssen Sie zuerst eine Amazon-Anwendung erstellen und die entsprechenden Parameter bereitstellen. Sie benötigen dazu ein Amazon-Konto. Wenn Sie kein Amazon-Konto haben, können Sie ein Konto unter [http://www.amazon.com/](http://www.amazon.com/) erstellen.

1. Klicken Sie auf [Amazon Developers Center](https://login.amazon.com/), und melden Sie sich mit den Anmeldeinformationen für Ihr Amazon-Konto an.
2. Wenn Sie dies nicht bereits getan haben, klicken Sie auf **Sign Up**, führen Sie die Schritte zur Registrierung von Entwicklern aus, und stimmen Sie den Richtlinien zu.
3. Klicken Sie auf **Register new application**.

    ![Amazon – Neue App](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Geben Sie Informationen zur Anwendung unter **Name**, **Description** und **Privacy Notice URL** ein, und klicken Sie auf **Save**.

    ![Amazon – Registrieren einer App](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Kopieren Sie im Abschnitt **Web Settings** die Werte für **Client ID** und **Client secret**. (Hierzu müssen Sie auf die Schaltfläche **Show secret** klicken.) Sie benötigen beide Angaben, um Amazon als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. Klicken Sie unten im Abschnitt auf **Edit**. Hinweis: Der **geheime Clientschlüssel** ist eine wichtige Anmeldeinformation.

    ![Amazon – Geheimer Clientschlüssel](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Geben Sie [https://login.microsoftonline.com](https://login.microsoftonline.com) im Feld **Zulässige JavaScript-Ursprünge** und `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` im Feld **Zulässige Rückgabe-URLs** ein. Ersetzen Sie dabei **{tenant}** durch den Namen Ihres Mandanten (z. B. „contoso.onmicrosoft.com“). Klicken Sie auf **Speichern**. Hinweis: Beim Wert für **{tenant}** muss die Groß-/Kleinschreibung beachtet werden.

    ![Amazon – URLs](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Konfigurieren von Amazon als Identitätsanbieter in Ihrem Mandanten

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf dem B2C-Featureblatt auf **Identitätsanbieter**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie einen aussagekräftigen Namen für die Konfiguration des Identitätsanbieters unter **Name** ein. Geben Sie z. B. "Amzn" ein.
5. Klicken Sie auf **Typ des Identitätsanbieters**, wählen Sie **Amazon** aus, und klicken Sie auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**, und geben Sie die **App-ID** und den **geheimen App-Schlüssel** der Amazon-Anwendung ein, die Sie zuvor erstellt haben.
7. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration für Amazon zu speichern.

<!---HONumber=AcomDC_0114_2016-->