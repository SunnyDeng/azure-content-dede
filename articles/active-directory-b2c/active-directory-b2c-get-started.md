<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Erstellen eines Azure Active Directory B2C-Verzeichnisses | Microsoft Azure"
	description="Thema zum Erstellen eines Azure Active Directory B2C-Verzeichnisses"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Erstellen eines Azure AD B2C-Verzeichnisses

Für den Einstieg in Azure Active Directory (AD) B2C führen Sie die unten beschriebenen 3 Schritte aus.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Schritt 1: Registrieren für ein Azure-Abonnement

Wenn Sie bereits über ein Azure-Abonnement verfügen, fahren Sie mit dem nächsten Schritt fort. Andernfalls registrieren Sie sich für ein [Azure-Abonnement](sign-up-organization.md), um Zugriff auf Azure AD B2C zu erhalten.

> [AZURE.NOTE]Die Verwendung von Azure AD B2C-Vorschau ist derzeit kostenlos, jedoch eingeschränkt (bis zu 50.000 Benutzer pro Verzeichnis). Für den Zugriff auf das [Azure-Portal](http://manage.windowsazure.com/) ist ein Azure-Abonnement erforderlich.

## Schritt 2: Erstellen eines Azure AD B2C-Verzeichnisses

Führen Sie die folgenden Schritte aus, um ein neues Azure AD B2C-Verzeichnis zu erstellen. Derzeit können B2C-Features in Ihren vorhandenen Verzeichnissen nicht aktiviert werden, sofern vorhanden.

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an. Dies ist dasselbe Geschäfts-, Schul- oder Universitätskonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
2. Klicken Sie auf **Neu** > **App Services** > **Active Directory** > **Verzeichnis** > **Benutzerdefiniert erstellen**.

    ![Erstellen eines Verzeichnisses](./media/active-directory-b2c-get-started/new-directory.png)

3. Geben Sie **Name**, **Domänenname** und **Land oder Region** für das Verzeichnis an.
4. Aktivieren Sie die Option **Hierbei handelt es sich um ein B2C-Verzeichnis**.
5. Klicken Sie auf das Häkchen, um die Aktion abzuschließen.

    ![Erstellen eines B2C-Verzeichnisses](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Das Verzeichnis wird jetzt erstellt und in der Active Directory-Erweiterung aufgeführt. Außerdem werden Sie als globaler Administrator des Verzeichnisses festgelegt. Sie können bei Bedarf weitere globale Administratoren hinzufügen.

    > [AZURE.IMPORTANT]Es kann bis zu zwei Minuten dauern, bis das Verzeichnis erstellt ist. Wenn beim Erstellen des Verzeichnisses Probleme auftreten, finden Sie eine Anleitung in diesem [Artikel](active-directory-b2c-support-create-directory.md).

## Schritt 3: Navigieren zum B2C-Featureblatt im Azure-Vorschauportal

1. Navigieren Sie auf der linken Navigationsleiste zur Active Directory-Erweiterung.
2. Suchen Sie Ihr Verzeichnis auf der Registerkarte **Verzeichnis**, und klicken Sie darauf.
3. Klicken Sie auf die Registerkarte **Konfigurieren**.
4. Klicken Sie im Abschnitt **B2C-Verwaltung** auf den Link **B2C-Einstellungen verwalten**.

    ![Erstellen eines B2C-Verzeichnisses](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. Das [Azure-Vorschauportal](https://portal.azure.com/) mit dem B2C-Featureblatt wird in einer neuen Registerkarte oder einem neuen Fenster des Browsers geöffnet.
5. Heften Sie diese Blade (rechts oben) an Ihrem Startmenü an, um einfach darauf zugreifen zu können.

    ![B2C-Featureblatt](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]Im [Azure-Portal](https://manage.windowsazure.com/) können Sie Benutzer und Gruppen, die Konfiguration für die Self-Service-Kennwortzurücksetzung und Features des Unternehmensbrandings verwalten.

## Nächste Schritte

Fahren Sie mit dem [Registrieren einer Anwendung bei Azure AD B2C und dem Erstellen einer Schnellstartanwendung](active-directory-b2c-app-registration.md) fort.

<!---HONumber=Sept15_HO3-->