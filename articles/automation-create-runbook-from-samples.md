<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Erste Schritte mit Azure Automation

Microsoft Azure Automation bietet Entwicklern die Möglichkeit, die manuellen, langfristigen, Fehler verursachenden und häufig wiederholten Aufgaben zu automatisieren, die für gewöhnlich in einer Cloudumgebung ausgeführt werden. Mithilfe von Runbooks, bei denen es sich eigentlich um Windows PowerShell-Workflows handelt, können Sie in Ihrer Azure-Umgebung Ressourcen erstellen, überwachen, verwalten und bereitstellen. Weitere Informationen zu Automation finden Sie im [Automation Overview Guide][Automation Overview Guide] (Leitfaden Überblick über Automation, in englischer Sprache).

In diesem Lernprogramm wird erläutert, wie Sie das Beispiel-Runbook "Hello World" in Azure Automation importieren, das Runbook ausführen und anschließend dessen Ausgabe anzeigen.

> [WACOM.NOTE] Informationen zum Automatisieren von Azure-Vorgängen mithilfe der [Azure PowerShell-Cmdlets][Azure PowerShell-Cmdlets] finden Sie unter [Azure Automation: Authenticating to Azure using Azure Active Directory][Azure Automation: Authenticating to Azure using Azure Active Directory] (Authentifizieren bei Azure mit Azure Active Directory, in englischer Sprache).

## Beispiele und Hilfsprogramm-Runbooks

Das Azure Automation-Team hat eine Reihe von Runbook-Beispielen erstellt, die Sie bei den ersten Schritten mit Automation unterstützen sollen. Diese Beispiele umfassen grundlegende Automation-Konzepte und sollen Ihnen helfen, Ihre eigenen Runbooks zu schreiben.

Das Automation-Team hat darüber hinaus Hilfsprogramm-Runbooks erstellt, die Sie als Bausteine für umfangreichere Automation-Aufgaben verwenden können.

> [WACOM.NOTE] Es empfiehlt sich, kleine, modulare, wiederverwendbare Runbooks zu schreiben. Sie sollten außerdem unbedingt eigene Hilfsprogramm-Runbooks für häufig verwendete Szenarien erstellen, nachdem Sie sich mit Automation vertraut gemacht haben.

Die Beispiel- und Hilfsprogramm-Runbooks des Automation-Teams können Sie im [Script Center][Script Center] aufrufen und herunterladen.

## Die Automation-Community und Feedback

Runbooks der Community und anderer Microsoft-Teams werden auch im [Script Center][1] veröffentlicht.

**Geben Sie uns Feedback!** Wenn Sie nach einer Automation-Runbook-Lösung oder einem Integrationsmodul suchen, senden Sie im Script Center eine Skriptanforderung. Wenn Sie eine Idee für ein neues Automation-Feature haben, senden Sie sie an die Seite [User Voice][User Voice].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Allgemeine Schritte für dieses Lernprogramm

1.  [Anmelden für die Azure Automation-Vorschau][Anmelden für die Azure Automation-Vorschau]
2.  [Herunterladen eines Beispiel-Runbooks][Herunterladen eines Beispiel-Runbooks]
3.  [Importieren und Ausführen des Beispiel-Runbooks und Anzeigen der Ausgabe][Importieren und Ausführen des Beispiel-Runbooks und Anzeigen der Ausgabe]

## <a name="preview"></a>Anmelden für die Azure Automation-Vorschau

Um Azure Automation verwenden zu können, benötigen Sie ein aktives Azure-Abonnement mit aktiviertem Microsoft Azure Automation-Vorschaufeature.

-   Klicken Sie auf der Seite **Vorschaufeatures** auf **Jetzt testen**.

    ![Aktivieren der Vorschau][Aktivieren der Vorschau]

## <a name="download-sample"></a>Herunterladen eines Beispiel-Runbooks aus dem Script Center

1.  Rufen Sie das [Script Center][Script Center] auf, und klicken Sie dann auf **Hello World for Azure Automation**.

2.  Klicken Sie auf den Namen **Write-HelloWorld.ps1** neben **Herunterladen**, und speichern Sie die Datei auf dem Computer.

## <a name="import-sample"></a>Importieren und Ausführen des Beispiel-Runbooks und Anzeigen der Ausgabe

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie im Verwaltungsportal auf **Create an Automation Account**.

    > [WACOM.NOTE] Wenn Sie bereits ein Automation-Konto erstellt haben, können Sie zu Schritt 4 springen.

    ![Konto erstellen][Konto erstellen]

3.  Geben Sie auf der Seite **Add a New Automation Account** einen Namen für das Konto ein, und klicken Sie auf das Häkchen.

    ![Neues Konto hinzufügen][Neues Konto hinzufügen]

4.  Klicken Sie auf der Seite **Automation** auf das soeben erstellte neue Konto.

    ![Neues Konto][Neues Konto]

5.  Klicken Sie auf **RUNBOOKS**.

    ![Registerkarte "Runbooks"][Registerkarte "Runbooks"]

6.  Klicken Sie auf **IMPORTIEREN**.

    ![Importieren][Importieren]

7.  Navigieren Sie zum heruntergeladenen Skript **Write-HelloWorld.ps1**, und klicken Sie auf das Häkchen.

    ![Durchsuchen][Durchsuchen]

8.  Klicken Sie auf **Write-HelloWorld**.

    ![Importiertes Runbook][Importiertes Runbook]

9.  Klicken Sie auf **AUTOR** und dann auf **ENTWURF**. Bei diesem Runbook müssen Sie keine Änderungen vornehmen.

    Sie sehen jetzt den Inhalt der Datei **Write-HelloWorld.ps1**. Sie können den Inhalt eines Runbooks im Entwurfsmodus ändern.

    ![Autor – Entwurf][Autor – Entwurf]

10. Klicken Sie auf **VERÖFFENTLICHEN**, um das Runbook hochzustufen, sodass es in der Produktion eingesetzt werden kann.

    ![Publish][Publish]

11. Wenn Sie zum Speichern und Veröffentlichen des Runbooks aufgefordert werden, klicken Sie auf **Ja**.

    ![Aufforderung zum Speichern und Veröffentlichen][Aufforderung zum Speichern und Veröffentlichen]

12. Klicken Sie auf **VERÖFFENTLICHT** und dann auf **START**.

    ![Veröffentlicht][Veröffentlicht]

13. Geben Sie auf der Seite **Specify the runbook parameter values** einen **Namen** ein, der als Eingabeparameter für das Skript Write-HelloWorld.ps1 verwendet wird, und klicken Sie dann auf das Häkchen.

    ![Runbook-Parameter][Runbook-Parameter]

14. Klicken Sie auf **JOBS**, um den Status des soeben gestarteten Runbook-Auftrags zu prüfen. Klicken Sie anschließend auf den Zeitstempel in der Spalte **AUFTRAGSANFANG**, um eine Zusammenfassung des Auftrags anzuzeigen.

    ![Runbook-Status][Runbook-Status]

15. Auf der Seite **ZUSAMMENFASSUNG** sehen Sie die Zusammenfassung, die Eingabeparameter und die Ausgabe des Auftrags.

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
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [create-account-note]: ../includes/create-account-note.md
  [Anmelden für die Azure Automation-Vorschau]: #preview
  [Herunterladen eines Beispiel-Runbooks]: #download-sample
  [Importieren und Ausführen des Beispiel-Runbooks und Anzeigen der Ausgabe]: #import-sample
  [Aktivieren der Vorschau]: ./media/automation/automation_00_EnablePreview.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Konto erstellen]: ./media/automation/automation_01_CreateAccount.png
  [Neues Konto hinzufügen]: ./media/automation/automation_02_addnewautoacct.png
  [Neues Konto]: ./media/automation/automation_03_NewAutoAcct.png
  [Registerkarte "Runbooks"]: ./media/automation/automation_04_RunbooksTab.png
  [Importieren]: ./media/automation/automation_05_Import.png
  [Durchsuchen]: ./media/automation/automation_06_Browse.png
  [Importiertes Runbook]: ./media/automation/automation_07_ImportedRunbook.png
  [Autor – Entwurf]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [Aufforderung zum Speichern und Veröffentlichen]: ./media/automation/automation_09_SavePubPrompt.png
  [Veröffentlicht]: ./media/automation/automation_10_PublishStart.png
  [Runbook-Parameter]: ./media/automation/automation_11_RunbookParams.png
  [Runbook-Status]: ./media/automation/automation_12_RunbookStatus.png
  [Runbook-Zusammenfassung]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Automation-Forum]: http://go.microsoft.com/fwlink/p/?LinkId=390561
