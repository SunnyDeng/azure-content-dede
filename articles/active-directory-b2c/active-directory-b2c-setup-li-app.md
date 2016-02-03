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

# Vorschau für Azure Active Directory B2C: Bereitstellen von Registrierung und Anmeldung für Kunden mit LinkedIn-Konten

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen einer LinkedIn-Anwendung

Um LinkedIn als Identitätsanbieter in Azure Active Directory (AD) B2C verwenden zu können, müssen Sie zuerst eine LinkedIn-Anwendung erstellen und die entsprechenden Parameter bereitstellen. Sie benötigen dazu ein LinkedIn-Konto. Wenn Sie kein LinkedIn-Konto haben, können Sie ein Konto unter [https://www.linkedin.com/](https://www.linkedin.com/) erstellen.

1. Navigieren Sie zur [LinkedIn-Entwickler-Website](https://www.developer.linkedin.com/), und melden Sie sich mit den Anmeldeinformationen für Ihr LinkedIn-Konto an.
2. Klicken Sie in der oberen Menüleiste auf **Meine Apps** und dann auf **Anwendung erstellen**.

    ![LinkedIn – Neue App](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Geben Sie im Formular **Neue Anwendung erstellen** die relevanten Informationen ein (**Firmenname**, **Name**, **Beschreibung**, **Anwendungslogo-URL**, **Anwendungsverwendung**, **Website-URL**, **E-Mail-Adresse (geschäftlich)** und **Telefon (geschäftlich)**).
4. Akzeptieren Sie die Nutzungsbedingungen für die LinkedIn-API, und klicken Sie auf **Absenden**.

    ![LinkedIn – Registrieren einer App](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Kopieren Sie die Werte von **Client-ID** und **Geheimer Clientschlüssel** (diese finden Sie im Abschnitt **Authentifizierungsschlüssel**). Sie benötigen beide Angaben, um LinkedIn als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. Hinweis: Der **geheime Clientschlüssel** ist eine wichtige Anmeldeinformation.

6. Geben Sie in das Feld **Autorisierte Umleitungs-URLs** (im Abschnitt **OAuth 2.0**) den Wert `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` ein. Ersetzen Sie dabei **{tenant}** durch den Namen Ihres Mandanten (z. B. „contoso.onmicrosoft.com“), und klicken Sie auf **Hinzufügen**. Klicken Sie dann auf **Aktualisieren**.

    ![LinkedIn – Einrichten einer App](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Konfigurieren von LinkedIn als Identitätsanbieter in Ihrem Mandanten

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf dem B2C-Featureblatt auf **Identitätsanbieter**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie einen aussagekräftigen Namen für die Konfiguration des Identitätsanbieters unter **Name** ein. Geben Sie z. B. "LI" ein.
5. Klicken Sie auf **Typ des Identitätsanbieters**, wählen Sie **LinkedIn** aus, und klicken Sie auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**, und geben Sie **Client-ID** und **Geheimer Clientschlüssel** der LinkeIn-Anwendung, die Sie zuvor erstellt haben, ein.
7. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration für LinkedIn zu speichern.

<!---HONumber=AcomDC_0114_2016-->