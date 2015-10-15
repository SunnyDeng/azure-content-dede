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
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Registrieren der Anwendung

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Voraussetzung

Zum Erstellen einer Anwendung, die Registrierungen und Anmeldungen von Kunden akzeptiert, müssen Sie sie zunächst bei einem Azure Active Directory B2C-Verzeichnis registrierten. Die Schritte, mit denen Sie Ihr eigenes Verzeichnis erhalten, sind [hier](active-directory-b2c-get-started.md) beschrieben. Wenn Sie alle Schritte in diesem Artikel ausgeführt haben, sollte das B2C-Featureblatt an Ihr Startmenü angeheftet sein.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navigieren zum B2C-Featureblatt

Sie haben zwei Möglichkeiten, zum B2C-Featureblatt zu navigieren.

### 1. Direkt im Azure-Vorschauportal

Wenn das B2C-Featureblatt an Ihr Startmenü angeheftet ist, sehen Sie es, sobald Sie sich als globaler Administrator des B2C-Verzeichnisses beim [Azure-Vorschauportal](https://portal.azure.com/) anmelden.

Sie können auch direkt auf das B2C-Featureblatt zugreifen, indem Sie zu [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com) navigieren, wobei **{directory}** durch den Namen ersetzt werden muss, der zum Zeitpunkt der Erstellung des Verzeichnisses verwendet wurde (z. B. "contosob2c"). Melden Sie sich dann als globaler Administrator des B2C-Verzeichnisses an. Sie können ein Lesezeichen für diesen Link erstellen, um in Zukunft darauf zuzugreifen.

### 2. Zugriff über das Azure-Portal

Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an (dabei handelt es sich um das gleiche Geschäfts- oder Schulkonto bzw. das gleiche Microsoft-Konto, mit dem Sie sich bei Azure registriert haben). Navigieren Sie links zur Active Directory-Erweiterung, und klicken Sie auf das B2C-Verzeichnis. Klicken Sie auf der Registerkarte **Schnellstart** (die Registerkarte, die als Erstes geöffnet wird) unter **Verwalten** auf **B2C-Einstellungen verwalten**. Das B2C-Featureblatt in einem neuen Browserfenster oder die Registerkarte wird geöffnet.

Sie finden den Link **B2C-Einstellungen verwalten** außerdem auf der Registerkarte **Konfigurieren** (im Abschnitt **B2C-Verwaltung**).

## Registrieren einer Anwendung

1. Klicken Sie auf dem B2C-Featureblatt im Azure-Vorschauportal auf **Anwendungen**.
2. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
3. Der **Name** der Anwendung sollte die Funktion der Anwendung für Kunden beschreiben. Geben Sie z. B. "Contoso B2C-App" ein.
4. Wenn Sie eine webbasierte Anwendung entwickeln, stellen Sie den Schalter **Web-App/Web-API einschließen** auf **Ja**. Die **Antwort-URLs** sind Endpunkte, an denen von Azure AD B2C von Ihrer Anwendung angeforderte Token zurückgegeben werden. Geben Sie z. B. Folgendes ein: `https://localhost:44321/`. Wenn Ihre Anwendung eine serverseitige Komponente (API) umfasst, die geschützt werden muss, sollten Sie jetzt ein **Anwendungsgeheimnis** erstellen (und kopieren), indem Sie auf die Schaltfläche **Schlüssel erzeugen** klicken.

    > [AZURE.NOTE]
    Das **Anwendungsgeheimnis** ist eine wichtige Sicherheitsanmeldeinformation.

5. Wenn Sie eine mobile Anwendung entwickeln, stellen Sie den Schalter **Systemeigenen Client einschließen** auf **Ja**. Notieren Sie sich den standardmäßigen **Umleitungs-URI**, der automatisch für Sie erstellt wurde.
6. Klicken Sie auf **Erstellen**, um Ihre Anwendung zu registrieren.
7. Klicken Sie auf die soeben erstellte Anwendung, und notieren Sie sich die global eindeutige **Anwendungs-ID**, die Sie später in Ihrem Code verwenden.

## Erstellen einer Schnellstart-App

Nachdem Sie über eine bei Azure AD B2C registrierte Anwendung verfügen, können Sie zum Einstieg eines der Schnellstart-Lernprogramme ausführen. Hier sind einige Vorschläge:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!-----HONumber=Oct15_HO1-->