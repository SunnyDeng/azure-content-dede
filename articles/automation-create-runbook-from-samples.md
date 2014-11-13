<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Erste Schritte mit Azure Automation" metaKeywords="" description="Erfahren Sie mehr &uuml;ber den Import und die Ausf&uuml;hrung eines Automatisierungs-Jobs in Azure." metaCanonical="" services="automation" documentationCenter="" title="Erste Schritte mit Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Erste Schritte mit Azure Automation

Microsoft Azure Automation bietet Entwicklern die Möglichkeit, die manuellen, langfristigen, Fehler verursachenden und häufig wiederholten Aufgaben zu automatisieren, die für gewöhnlich in einer Cloudumgebung ausgeführt werden. Mithilfe von Runbooks, bei denen es sich eigentlich um Windows PowerShell-Workflows handelt, können Sie in Ihrer Azure-Umgebung Ressourcen erstellen, überwachen, verwalten und bereitstellen. Weitere Informationen zu Automation finden Sie im [Automation Overview Guide][Automation Overview Guide] (Leitfaden Überblick über Automation, in englischer Sprache).

In diesem Lernprogramm wird erläutert, wie Sie das Beispiel-Runbook "Hello World" in Azure Automation importieren, das Runbook ausführen und anschließend dessen Ausgabe anzeigen.

> [WACOM.NOTE] Informationen zum Automatisieren von Azure-Vorgängen mithilfe der [Azure PowerShell-Cmdlets][Azure PowerShell-Cmdlets] finden Sie unter [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Authentifizieren bei Azure mit Azure Active Directory, in englischer Sprache).

## Beispiele und Hilfsprogramm-Runbooks

Das Azure Automation-Team hat eine Reihe von Runbook-Beispielen erstellt, die Sie bei den ersten Schritten mit Automation unterstützen sollen. Diese Beispiele umfassen grundlegende Automation-Konzepte und sollen Ihnen helfen, Ihre eigenen Runbooks zu schreiben.

Das Automation-Team hat darüber hinaus Hilfsprogramm-Runbooks erstellt, die Sie als Bausteine für umfangreichere Automation-Aufgaben verwenden können.

> [WACOM.NOTE] Es empfiehlt sich, kleine, modulare, wiederverwendbare Runbooks zu schreiben. Sie sollten außerdem unbedingt eigene Hilfsprogramm-Runbooks für häufig verwendete Szenarien erstellen, nachdem Sie sich mit Automation vertraut gemacht haben.

Sie können die Beispiel- und Hilfsprogramm-Runbooks des Automation-Teams im [Script Center][Script Center] aufrufen und herunterladen. Alternativ können Sie diese direkt aus dem [Runbook-Katalog][Runbook-Katalog] importieren.

## Die Automation-Community und Feedback

Runbooks der Community und anderer Microsoft-Teams werden auch im [Script Center][1] und im [Runbook-Katalog][Runbook-Katalog] veröffentlicht.

**Geben Sie uns Feedback!** Wenn Sie nach einer Automation-Runbook-Lösung oder einem Integrationsmodul suchen, senden Sie im Script Center eine Skriptanforderung. Wenn Sie eine Idee für ein neues Automation-Feature haben, senden Sie sie an die Seite [User Voice][User Voice].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Allgemeine Schritte für dieses Lernprogramm

1.  [Anmelden für die Azure Automation-Vorschau][Anmelden für die Azure Automation-Vorschau]
2.  [Importieren des Runbooks aus dem Runbook-Katalog][Importieren des Runbooks aus dem Runbook-Katalog]
3.  [Veröffentlichen des Runbooks][Veröffentlichen des Runbooks]
4.  [Starten des Runbooks][Starten des Runbooks]

## <a name="preview"></a>Anmelden für die Azure Automation-Vorschau

Um Azure Automation verwenden zu können, benötigen Sie ein aktives Azure-Abonnement mit aktiviertem Microsoft Azure Automation-Vorschaufeature.

-   Klicken Sie auf der Seite **Vorschaufeatures** auf **Jetzt testen**.

    ![Aktivieren der Vorschau][Aktivieren der Vorschau]

## <a name="automationaccount"></a>Create an Automation Account (Erstellen eines Automatisierungskontos)

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie im Verwaltungsportal auf **Create an Automation Account**.

    > [WACOM.NOTE] Wenn Sie bereits ein Automation-Konto erstellt haben, können Sie zu Schritt 4 springen.

    ![Konto erstellen][Konto erstellen]

3.  Geben Sie auf der Seite **Add a New Automation Account** einen Namen für das Konto ein, und klicken Sie auf das Häkchen.

    ![Neues Konto hinzufügen][Neues Konto hinzufügen]

## <a name="importrunbook"></a>Import Runbook from Runbook Gallery (Importieren des Runbooks aus dem Runbook-Katalog)

1.  Klicken Sie auf der Seite **Automation** auf das soeben erstellte neue Konto.

    ![Neues Konto][Neues Konto]

2.  Klicken Sie auf **RUNBOOKS**.

    ![Registerkarte "Runbooks"][Registerkarte "Runbooks"]

3.  Klicken Sie auf **Neu** \> **Runbook** \> **Aus Katalog**.

    ![Runbook-Katalog][2]

4.  Wählen Sie die Kategorie **Lernprogramm** und anschließend **Hello World for Azure Automation**. Klicken Sie auf die Schaltfläche mit dem Pfeil nach rechts.

    ![Importieren des Runbooks][Importieren des Runbooks]

5.  Überprüfen Sie die Inhalte des Runbooks, und klicken Sie anschließend auf die Schaltfläche mit dem Pfeil nach rechts.

    ![Runbook-Definition][Runbook-Definition]

6.  Überprüfen Sie die Details des Runbooks, und klicken Sie anschließend auf das Häkchen.

    ![Runbook-Details][Runbook-Details]

## <a name="publishrunbook"></a>Publish Runbook (Veröffentlichen des Runbooks)

1.  Wenn der Import des Runbooks abgeschlossen ist, klicken Sie auf **Write-HelloWorld**.

    ![Importiertes Runbook][Importiertes Runbook]

2.  Klicken Sie auf **AUTOR** und dann auf **ENTWURF**.

    Sie können den Inhalt eines Runbooks im Entwurfsmodus ändern. Bei diesem Runbook müssen Sie keine Änderungen vornehmen.

    ![Autor – Entwurf][Autor – Entwurf]

3.  Klicken Sie auf **VERÖFFENTLICHEN**, um das Runbook hochzustufen, sodass es in der Produktion eingesetzt werden kann.

    ![Publish][Publish]

4.  Wenn Sie zum Speichern und Veröffentlichen des Runbooks aufgefordert werden, klicken Sie auf **Ja**.

    ![Aufforderung zum Speichern und Veröffentlichen][Aufforderung zum Speichern und Veröffentlichen]

## <a name="startrunbook"></a>Start Runbook (Starten des Runbooks)

1.  Klicken Sie bei geöffnetem **Write-HelloWorld**-Runbook auf **START**.

    ![Veröffentlicht][Veröffentlicht]

2.  Geben Sie auf der Seite **Specify the runbook parameter values** einen **Namen** ein, der als Eingabeparameter für das Skript Write-HelloWorld.ps1 verwendet wird, und klicken Sie dann auf das Häkchen.

    ![Runbook-Parameter][Runbook-Parameter]

3.  Klicken Sie auf **JOBS**, um den Status des soeben gestarteten Runbook-Auftrags zu prüfen. Klicken Sie anschließend auf den Zeitstempel in der Spalte **AUFTRAGSANFANG**, um eine Zusammenfassung des Auftrags anzuzeigen.

    ![Runbook-Status][Runbook-Status]

4.  Auf der Seite **ZUSAMMENFASSUNG** sehen Sie die Zusammenfassung, die Eingabeparameter und die Ausgabe des Auftrags.

    ![Runbook-Zusammenfassung][Runbook-Zusammenfassung]

# Verwalten von Azure-Diensten mit einem Runbook

Das obige Beispiel zeigt ein einfaches Runbook, das keine Azure-Dienste verwaltet. Die [Azure-Cmdlets][Azure PowerShell-Cmdlets] erfordern eine Authentifizierung bei Azure. Eine Anleitung zum Konfigurieren Ihres Azure-Abonnements für die Verwaltung mit Azure Automation finden Sie unter [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Authentifizieren bei Azure mit Azure Active Directory, in englischer Sprache).

# Weitere Informationen

-   [Automation Overview (Übersicht über Automation, in englischer Sprache)][Automation Overview (Übersicht über Automation, in englischer Sprache)]
-   [Runbook Authoring Guide (Leitfaden zum Erstellen von Runbooks, in englischer Sprache)][Runbook Authoring Guide (Leitfaden zum Erstellen von Runbooks, in englischer Sprache)]
-   [Automation-Forum][Automation-Forum]
-   [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Authentifizieren bei Azure mit Azure Active Directory, in englischer Sprache)

  [Automation Overview Guide]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [Azure PowerShell-Cmdlets]: http://msdn.microsoft.com/de-de/library/jj156055.aspx
  [Azure Automation: Authenticating to Azure using Azure Active Directory]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Script Center]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [Runbook-Katalog]: http://aka.ms/runbookgallery
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [Anmelden für die Azure Automation-Vorschau]: #automationaccount
  [Importieren des Runbooks aus dem Runbook-Katalog]: #importrunbook
  [Veröffentlichen des Runbooks]: #publishrunbook
  [Starten des Runbooks]: #startrunbook
  [Aktivieren der Vorschau]: ./media/automation/automation_00_EnablePreview.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Konto erstellen]: ./media/automation/automation_01_CreateAccount.png
  [Neues Konto hinzufügen]: ./media/automation/automation_02_addnewautoacct.png
  [Neues Konto]: ./media/automation/automation_03_NewAutoAcct.png
  [Registerkarte "Runbooks"]: ./media/automation/automation_04_RunbooksTab.png
  [2]: ./media/automation/automation_05_ImportGallery.png
  [Importieren des Runbooks]: ./media/automation/automation_06_ImportRunbook.png
  [Runbook-Definition]: ./media/automation/automation_07_RunbookDefinition.png
  [Runbook-Details]: ./media/automation/automation_08_RunbookDetails.png
  [Importiertes Runbook]: ./media/automation/automation_07_ImportedRunbook.png
  [Autor – Entwurf]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [Aufforderung zum Speichern und Veröffentlichen]: ./media/automation/automation_09_SavePubPrompt.png
  [Veröffentlicht]: ./media/automation/automation_10_PublishStart.png
  [Runbook-Parameter]: ./media/automation/automation_11_RunbookParams.png
  [Runbook-Status]: ./media/automation/automation_12_RunbookStatus.png
  [Runbook-Zusammenfassung]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Automation Overview (Übersicht über Automation, in englischer Sprache)]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Runbook Authoring Guide (Leitfaden zum Erstellen von Runbooks, in englischer Sprache)]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Automation-Forum]: http://go.microsoft.com/fwlink/p/?LinkId=390561
