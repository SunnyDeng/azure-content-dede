<properties
	pageTitle="Hinzufügen eines Git-Artefaktrepositorys zu Ihrem DevTest Lab | Microsoft Azure"
	description="Fügen Sie ein GitHub- oder Visual Studio Team Services-Git-Repository für Ihre benutzerdefinierten Elemente in Ihrem Lab hinzu."
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Hinzufügen eines Git-Artefaktrepositorys zu Ihrem DevTest Lab

## Übersicht

Standardmäßig enthält ein DevTest Lab Artefakte aus dem offiziellen Azure DevTest Lab-Artefaktrepository. Sie können Ihrem Lab ein Git-Artefaktrepository hinzufügen, das die von Ihrem Team erstellten Artefakte enthalten soll. Das Repository kann auf [GitHub](https://github.com) oder bei [Visual Studio Team Services (VSTS)](https://visualstudio.com) gehostet werden.

- Anweisungen zum Erstellen eines GitHub-Repositorys finden Sie unter [GitHub Bootcamp](https://help.github.com/categories/bootcamp/) (in englischer Sprache).
- Anweisungen zum Erstellen eines Team Services-Projekts mit einem Git-Repository finden Sie unter [Connect to Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) (in englischer Sprache).

Der folgende Screenshot zeigt ein mögliches Beispiel für ein Repository mit Artefakten in GitHub: ![Beispiel: GitHub-Artefaktrepository](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Hinzufügen eines GitHub-Artefaktrepositorys zu Ihrem Lab

Um Ihrem Lab ein GitHub-Artefaktrepository hinzuzufügen, rufen Sie zunächst die HTTPS-Klon-URL und das persönliche Zugriffstoken aus dem Artefaktrepository ab und geben diese Informationen dann im Lab ein.

### Abrufen der Klon-URL für das GitHub-Repository und des persönlichen Zugriffstokens

1. Speichern Sie auf der Startseite des GitHub-Repositorys, das die Teamartefakte enthält, die **HTTPS-Klon-URL** zur späteren Verwendung.

1. Tippen Sie auf das Profilbild in der oberen rechten Ecke, und wählen Sie **Settings** aus.

1. Tippen Sie im Menü **Personal settings** auf der linken Seite auf **Personal access tokens**.

1. Tippen Sie auf **Generate new token**.

1. Geben Sie auf der Seite **New personal access token** unter **Token description** eine Tokenbeschreibung ein, akzeptieren Sie die Standardelemente unter **Select scopes**, und wählen Sie dann **Generate Token** aus.

1. Speichern Sie das generierte Token, da Sie es später benötigen.

1. Sie können GitHub nun schließen.

###Verbinden Ihres Labs mit dem GitHub-Repository

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Tippen Sie auf dem Blatt des Labs auf **Einstellungen**.

1. Tippen Sie auf dem Blatt **Einstellung** für das Lab auf **Artifacts Repository**.

1. Gehen Sie auf dem Blatt **Artifacts Repository** folgendermaßen vor:

    1. Geben Sie einen **Namen** für das Repository ein.
    1. Geben Sie die gespeicherte **Git-Klon-URL** ein.
    2. Geben Sie den **Ordnerpfad** im Artefaktrepository ein, der die Artefakte enthält.
    3. Geben Sie das gespeicherte **Persönliche Zugriffstoken** für das Artefaktrepository ein.
    4. Tippen Sie auf **Speichern**.

Die Artefakte im Repository werden jetzt auf dem Blatt **Artefakte hinzufügen** aufgeführt.

## Hinzufügen eines Visual Studio Git-Artefaktrepositorys zu Ihrem Lab

Um Ihrem Lab ein Visual Studio Git-Artefaktrepository hinzuzufügen, rufen Sie zunächst die HTTPS-Klon-URL und das persönliche Zugriffstoken aus dem Artefaktrepository ab und geben diese Informationen dann im Lab ein.

### Visual Studio-Webseite des Artefaktprojekts

1. Öffnen Sie die Startseite Ihrer Teamauflistung (z. B. `https://contoso-web-team.visualstudio.com`), und tippen Sie dann auf das Artefaktprojekt.

2. Tippen Sie auf der Startseite des Projekts auf **Code**.

1. Um die Klon-URL für das Projekt anzuzeigen, tippen Sie auf der Seite **Code** auf **Klonen**.

1. Speichern Sie die URL, da Sie sie später in diesem Lernprogramm benötigen.

1. Um ein persönliches Zugriffstoken zu erstellen, tippen Sie im Dropdownmenü Ihres Benutzerkontos auf **Mein Profil**.

1. Klicken Sie auf der Seite mit den Profilinformationen auf **Sicherheit**.

1. Tippen Sie auf der Registerkarte **Sicherheit** auf **Hinzufügen**.

1. Gehen Sie auf der Seite **Persönliches Zugriffstoken erstellen** folgendermaßen vor:

    1. Geben Sie eine **Beschreibung** für das Token ein.
    2. Wählen Sie **180 Tage** in der Liste **Läuft ab in** aus.
    3. Wählen Sie **Alle verfügbaren Konten** in der Liste **Konten** aus.
    4. Wählen Sie die Option **Alle Bereiche** aus.
    5. Wählen Sie **Token erstellen** aus.

1. Nach Abschluss wird das neue Token in der Liste **Persönliche Zugriffstoken** angezeigt. Tippen Sie auf **Token kopieren**, und speichern Sie dann den Tokenwert, da er in Kürze verwendet wird.

### Im DevTest Lab

1. Tippen Sie auf dem Blatt des Labs auf **Einstellungen**.

    ![Einstellungen auswählen](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. Tippen Sie auf dem Blatt **Einstellungen** auf **Artifacts Repository**.

1. Gehen Sie auf dem Blatt **Artifacts Repository** folgendermaßen vor:

    1. Geben Sie unter **Name** einen Anzeigenamen für das Repository ein.
    1. Geben Sie die gespeicherte **Git-Klon-URL** ein.
    2. Geben Sie den **Ordnerpfad** im Artefaktrepository ein, der die Artefakte enthält.
    3. Geben Sie das gespeicherte **Persönliche Zugriffstoken** für das Artefaktrepository ein.
    4. Tippen Sie auf **Speichern**.

<!---HONumber=AcomDC_0204_2016-->