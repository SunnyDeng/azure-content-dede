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
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Erstellen eines Azure AD B2C-Mandanten

Zum Einstieg in Microsoft Azure Active Directory (Azure AD) B2C führen Sie die drei Schritte aus, die in diesem Artikel beschrieben werden.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Schritt 1: Registrieren für ein Azure-Abonnement

Wenn Sie bereits über ein Azure-Abonnement verfügen, überspringen Sie diesen Schritt. Andernfalls registrieren Sie sich für ein [Azure-Abonnement](../active-directory/sign-up-organization.md), um Zugriff auf Azure AD B2C zu erhalten.

> [AZURE.NOTE]
Derzeit können Sie die Vorschauversion von Azure AD B2C kostenlos nutzen. Die Nutzung ist jedoch auf 50.000 Benutzer pro Mandant beschränkt. Für den Zugriff auf das [klassische Azure-Portal](http://manage.windowsazure.com/) ist ein Azure-Abonnement erforderlich.

## Schritt 2: Erstellen eines Azure AD B2C-Mandanten

Führen Sie die folgenden Schritte aus, um einen neuen Azure AD B2C-Mandanten zu erstellen. Derzeit können B2C-Features in Ihren vorhandenen Verzeichnissen nicht aktiviert werden, sofern vorhanden.

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an. Dies ist dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
2. Klicken Sie auf **Neu** > **App Services** > **Active Directory** > **Verzeichnis** > **Benutzerdefiniert erstellen**.

    ![Screenshot der ersten Schritte zum Erstellen eines Mandanten](./media/active-directory-b2c-get-started/new-directory.png)

3. Wählen Sie **Name**, **Domänenname** und **Land oder Region** für den Mandanten aus.
4. Aktivieren Sie die Option **Hierbei handelt es sich um ein B2C-Verzeichnis**.
5. Klicken Sie auf das Häkchen, um die Aktion abzuschließen.

    ![Screenshot des Kontrollkästchens zum Erstellen eines B2C-Verzeichnisses](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Der Mandant wird jetzt erstellt und in der Active Directory-Erweiterung aufgeführt. Außerdem werden Sie als globaler Administrator des Mandanten festgelegt. Sie können bei Bedarf weitere globale Administratoren hinzufügen.

    > [AZURE.IMPORTANT]
    Es kann bis zu zwei Minuten dauern, bis der Mandant erstellt ist. Wenn beim Erstellen des Mandanten Probleme auftreten, finden Sie eine Anleitung unter [Erstellen eines Azure AD-Mandanten oder eines Azure AD B2C-Mandanten – Probleme und Lösungen](active-directory-b2c-support-create-directory.md).

## Schritt 3: Navigieren zum Blatt „B2C-Funktionen“ im Azure-Portal

1. Navigieren Sie auf der Navigationsleiste links zur Active Directory-Erweiterung.
2. Suchen Sie Ihren Mandanten auf der Registerkarte **Verzeichnis**, und klicken Sie darauf.
3. Klicken Sie auf die Registerkarte **Konfigurieren**.
4. Klicken Sie im Abschnitt **B2C-Verwaltung** auf den Link **B2C-Einstellungen verwalten**.

    ![Screenshot der Verzeichniskonfiguration für B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Das Azure-Portal mit dem Blatt „B2C-Funktionen“ wird auf einer neuen Registerkarte oder in einem neuen Fenster des Browsers geöffnet.

    > [AZURE.IMPORTANT]
    Es gibt ein bekanntes Problem, durch das diese Seite nicht ordnungsgemäß geladen wird (für eine kleine Anzahl an Mandanten). Durch Aktualisieren des Browsers sollte es behoben sein. Wenden Sie sich andernfalls an den Azure-Support.

6. Heften Sie dieses Blatt an Ihr Startmenü an, um auf einfache Weise darauf zugreifen zu können. (Das Tool zum Anheften ist oben rechts auf dem Featureblatt rot gekennzeichnet.)

    ![Screenshot des Blatts „B2C-Funktionen“](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Im [klassischen Azure-Portal](https://manage.windowsazure.com/) können Sie Benutzer und Gruppen, die Konfiguration für die Self-Service-Kennwortzurücksetzung und die Features des Unternehmensbrandings für Ihren Mandanten verwalten.

## Nächste Schritte

Informationen zum Registrieren einer Anwendung in Azure AD B2C und Erstellen einer Schnellstartanwendung finden Sie unter [Azure Active Directory B2C-Vorschau: Registrieren der Anwendung](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0302_2016-->