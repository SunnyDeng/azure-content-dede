<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Registrierung einer Anwendung | Microsoft Azure"
	description="Registrieren Ihrer Anwendung bei Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C-Vorschau: Registrieren der Anwendung

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Voraussetzung

Zum Erstellen einer Anwendung, die Registrierungen und Anmeldungen von Kunden akzeptiert, müssen Sie die Anwendung zunächst bei einem Azure Active Directory B2C-Mandanten registrieren. Erstellen Sie einen eigenen Mandanten mithilfe der unter [Erstellen eines Azure AD B2C-Mandanten](active-directory-b2c-get-started.md) beschriebenen Schritte. Nachdem Sie alle Schritte in diesem Artikel ausgeführt haben, ist das Blatt „B2C-Funktionen“ an Ihr Startmenü angeheftet.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navigieren zum Blatt „B2C-Funktionen“

Sie können im Azure-Portal oder im klassischen Azure-Portal zum Blatt „B2C-Funktionen“ navigieren.

### 1\. Zugreifen über das Azure-Portal

Wenn das Blatt „B2C-Funktionen“ an Ihr Startmenü angeheftet ist, sehen Sie das Blatt, sobald Sie sich als globaler Administrator des B2C-Mandanten beim [Azure-Portal](https://portal.azure.com/) anmelden.

Sie können auf das Blatt auch zugreifen, indem Sie im [Azure-Portal](https://portal.azure.com/) im linken Navigationsbereich auf **Durchsuchen** und dann auf **Azure AD B2C** klicken.

Sie können auch direkt auf das Blatt zugreifen, indem Sie zu [https://portal.azure.com/{Mandant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) navigieren, wobei **{Mandant}** durch den Namen ersetzt werden muss, der zum Zeitpunkt der Erstellung des Mandanten verwendet wurde (z. B. „contosob2c“). Sie können ein Lesezeichen für diesen Link erstellen, um in Zukunft darauf zuzugreifen.

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a user from any tenant cannot access it.

### 2\. Zugreifen über das klassische Azure-Portal

Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an. (Dies ist dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.) Navigieren Sie links zur Active Directory-Erweiterung, und klicken Sie auf Ihren B2C-Mandanten. Klicken Sie auf der Registerkarte **Schnellstart** (die Registerkarte, die als Erstes geöffnet wird) unter **Verwalten** auf **B2C-Einstellungen verwalten**. Das Blatt „B2C-Funktionen“ wird in einem neuen Browserfenster oder auf einer neuen Registerkarte geöffnet.

Sie finden den Link **B2C-Einstellungen verwalten** außerdem auf der Registerkarte **Konfigurieren** im Abschnitt **B2C-Verwaltung**.

## Registrieren einer Anwendung

1. Klicken Sie auf dem Blatt „B2C-Funktionen“ im Azure-Portal auf **Anwendungen**.
2. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
3. Geben Sie einen **Namen** für die Anwendung ein, der die Funktion der Anwendung für Kunden beschreibt. Sie könnten z. B. „Contoso B2C-App“ eingeben.
4. Wenn Sie eine webbasierte Anwendung entwickeln, stellen Sie den Schalter **Web-App/Web-API einschließen** auf **Ja**. Die **Antwort-URLs** sind Endpunkte, an denen Azure AD B2C von Ihrer Anwendung angeforderte Token zurückgibt. Geben Sie z. B. Folgendes ein: `https://localhost:44321/`. Wenn Ihre Anwendung eine serverseitige Komponente (API) umfasst, die geschützt werden muss, sollten Sie jetzt ein **Anwendungsgeheimnis** erstellen (und kopieren), indem Sie auf die Schaltfläche **Schlüssel erzeugen** klicken.

    > [AZURE.NOTE]
    Das **Anwendungsgeheimnis** ist eine wichtige Sicherheitsanmeldeinformation.

5. Wenn Sie eine mobile Anwendung entwickeln, stellen Sie den Schalter **Systemeigenen Client einschließen** auf **Ja**. Notieren Sie sich den standardmäßigen **Umleitungs-URI**, der automatisch für Sie erstellt wurde.
6. Klicken Sie auf **Erstellen**, um Ihre Anwendung zu registrieren.
7. Klicken Sie auf die soeben erstellte Anwendung, und notieren Sie sich die global eindeutige **Anwendungs-ID**, die Sie später in Ihrem Code verwenden.

## Erstellen einer Schnellstart-App

Nachdem Sie nun über eine bei Azure AD B2C registrierte Anwendung verfügen, können Sie zum Einstieg eines der Schnellstarttutorials ausführen. Hier sind einige Vorschläge:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0302_2016-->