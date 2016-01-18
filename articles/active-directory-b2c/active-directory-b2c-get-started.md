<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Erstellen eines Azure Active Directory B2C-Mandanten | Microsoft Azure"
	description="Thema zum Erstellen eines Azure Active Directory B2C-Mandanten"
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
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Erstellen eines Azure AD B2C-Mandanten

Für den Einstieg in Azure Active Directory (AD) B2C führen Sie die unten beschriebenen 3 Schritte aus.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Schritt 1: Registrieren für ein Azure-Abonnement

Wenn Sie bereits über ein Azure-Abonnement verfügen, fahren Sie mit dem nächsten Schritt fort. Andernfalls registrieren Sie sich für ein [Azure-Abonnement](sign-up-organization.md), um Zugriff auf Azure AD B2C zu erhalten.

> [AZURE.NOTE]Die Verwendung der Azure AD B2C-Vorschau ist derzeit kostenlos, jedoch eingeschränkt (bis zu 50.000 Benutzer pro Mandant). Für den Zugriff auf das [klassische Azure-Portal](http://manage.windowsazure.com/) ist ein Azure-Abonnement erforderlich.

## Schritt 2: Erstellen eines Azure AD B2C-Mandanten

Führen Sie die folgenden Schritte aus, um einen neuen Azure AD B2C-Mandanten zu erstellen. Derzeit können B2C-Features in Ihren vorhandenen Verzeichnissen nicht aktiviert werden, sofern vorhanden.

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an. Dies ist dasselbe Geschäfts-, Schul- oder Universitätskonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
2. Klicken Sie auf **Neu** > **App Services** > **Active Directory** > **Verzeichnis** > **Benutzerdefiniert erstellen**.

    ![Erstellen eines Mandanten](./media/active-directory-b2c-get-started/new-directory.png)

3. Geben Sie **Name**, **Domänenname** und **Land oder Region** für den Mandanten an.
4. Aktivieren Sie die Option **Hierbei handelt es sich um ein B2C-Verzeichnis**.
5. Klicken Sie auf das Häkchen, um die Aktion abzuschließen.

    ![Erstellen eines B2C-Mandanten](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Der Mandant wird jetzt erstellt und in der Active Directory-Erweiterung aufgeführt. Außerdem werden Sie als globaler Administrator des Mandanten festgelegt. Sie können bei Bedarf weitere globale Administratoren hinzufügen.

    > [AZURE.IMPORTANT]Es kann bis zu zwei Minuten dauern, bis der Mandant erstellt ist. Wenn beim Erstellen des Mandanten Probleme auftreten, finden Sie eine Anleitung in diesem [Artikel](active-directory-b2c-support-create-directory.md).

## Schritt 3: Navigieren zum Blatt „B2C-Funktionen“ im Azure-Portal

1. Navigieren Sie auf der linken Navigationsleiste zur Active Directory-Erweiterung.
2. Suchen Sie Ihren Mandanten auf der Registerkarte **Verzeichnis**, und klicken Sie darauf.
3. Klicken Sie auf die Registerkarte **Konfigurieren**.
4. Klicken Sie im Abschnitt **B2C-Verwaltung** auf den Link **B2C-Einstellungen verwalten**.

    ![Erstellen eines B2C-Mandanten](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. Das Azure-Portal mit dem Blatt „B2C-Funktionen“ wird in einer neuen Registerkarte oder einem neuen Fenster des Browsers geöffnet.

    > [AZURE.IMPORTANT]Es gibt ein bekanntes Problem, durch das diese Seite nicht ordnungsgemäß geladen wird (für eine kleine Anzahl an Mandanten). Durch Aktualisieren des Browsers sollte es behoben sein. Wenn dies nicht der Fall ist, wenden Sie sich an den Support.

5. Heften Sie diese Blade (rechts oben) an Ihrem Startmenü an, um einfach darauf zugreifen zu können.

    ![Blatt „B2C-Funktionen“](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]Im [klassischen Azure-Portal](https://manage.windowsazure.com/) können Sie Benutzer und Gruppen, die Konfiguration für die Self-Service-Kennwortzurücksetzung und die Features des Unternehmensbrandings für Ihren Mandanten verwalten.

## Nächste Schritte

Fahren Sie mit dem [Registrieren einer Anwendung bei Azure AD B2C und dem Erstellen einer Schnellstartanwendung](active-directory-b2c-app-registration.md) fort.

<!---HONumber=AcomDC_0107_2016-->