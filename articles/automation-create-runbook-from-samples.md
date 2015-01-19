<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Erste Schritte mit Azure Automation" metaKeywords="" description="Erfahren Sie mehr über den Import und die Ausführung eines Automatisierungs-Jobs in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="bwren" />


# Erste Schritte mit Azure Automation

Microsoft Azure Automation bietet Entwicklern die Möglichkeit, die manuellen, langfristigen, Fehler verursachenden und häufig wiederholten Aufgaben zu automatisieren, die für gewöhnlich in einer Cloudumgebung ausgeführt werden. Mithilfe von Runbooks, bei denen es sich eigentlich um Windows PowerShell-Workflows handelt, können Sie in Ihrer Azure-Umgebung Ressourcen erstellen, überwachen, verwalten und bereitstellen. Weitere Informationen zu Automation finden Sie im Leitfaden [Überblick über Automation](http://go.microsoft.com/fwlink/p/?LinkId=392861). 

In diesem Lernprogramm wird erläutert, wie Sie das Beispiel-Runbook "Hello World" in Azure Automation importieren, das Runbook ausführen und anschließend dessen Ausgabe anzeigen.

>[WACOM.NOTE] Weitere Informationen zum Automatisieren von Azure-Vorgängen mithilfe der [Azure PowerShell-Cmdlets](http://msdn.microsoft.com/de-de/library/jj156055.aspx) finden Sie unter <a href="http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/">Azure Automation: Authentifizieren bei Azure mit Azure Active Directory</a>.

## Beispiele und Hilfsprogramm-Runbooks

Das Azure Automation-Team hat eine Reihe von Runbook-Beispielen erstellt, die Sie bei den ersten Schritten mit Automation unterstützen sollen.  Diese Beispiele umfassen grundlegende Automation-Konzepte und sollen Ihnen helfen, Ihre eigenen Runbooks zu schreiben.  

Das Automation-Team hat darüber hinaus Hilfsprogramm-Runbooks erstellt, die Sie als Bausteine für umfangreichere Automation-Aufgaben verwenden können.  

>[WACOM.NOTE] Es empfiehlt sich, kleine, modulare, wiederverwendbare Runbooks zu schreiben. Sie sollten außerdem unbedingt eigene Hilfsprogramm-Runbooks für häufig verwendete Szenarien erstellen, nachdem Sie sich mit Automation vertraut gemacht haben.  

Sie können die Beispiel- und Hilfsprogramm-Runbooks des Automation-Teams im [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029) aufrufen und herunterladen. Alternativ können Sie diese direkt aus dem [Runbook-Katalog](http://aka.ms/runbookgallery) importieren. 

## Die Automation-Community und Feedback

Runbooks der Community und anderer Microsoft-Teams werden auch im [Script Center](http://go.microsoft.com/fwlink/?LinkID=391681) und im [Runbook-Katalog](http://aka.ms/runbookgallery) veröffentlicht. 

<strong>Geben Sie uns Feedback!</strong>  Wenn Sie nach einer Automation-Runbook-Lösung oder einem Integrationsmodul suchen, senden Sie im Script Center eine Skriptanforderung. Wenn Sie eine Idee für ein neues Automation-Feature haben, senden Sie sie an die Seite [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Allgemeine Schritte für dieses Lernprogramm

1. [Erstellen eines Automation-Kontos](#automationaccount)
2. [Importieren des Runbooks aus dem Runbook-Katalog](#importrunbook)
3. [Veröffentlichen des Runbooks](#publishrunbook)
4. [Starten des Runbooks](#startrunbook)


## <a name="automationaccount"></a>Create an Automation Account (Erstellen eines Automatisierungskontos)

1.	Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.	Klicken Sie im Verwaltungsportal auf die Option zum **Erstellen eines Automation-Kontos**.

	>[WACOM.NOTE] Wenn Sie bereits ein Automation-Konto erstellt haben, können Sie zu Schritt 4 springen.

	![Create Account](./media/automation/automation_01_CreateAccount.png)

3.	Geben Sie auf der Seite zum **Hinzufügen eines neuen Automation-Kontos** einen Namen für das Konto ein, und klicken Sie auf das Häkchen.

	![Add New Account](./media/automation/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Importieren des Runbooks aus dem Runbook-Katalog
 
4.	Klicken Sie auf der Seite **Automation** auf das soeben erstellte neue Konto.
 
	![New Account](./media/automation/automation_03_NewAutoAcct.png)

5.	Klicken Sie auf **RUNBOOKS**.

	![Runbooks Tab](./media/automation/automation_04_RunbooksTab.png)
  
6.	Klicken Sie auf **Neu** > **Runbook** > **Aus Katalog**.

	![Runbook Gallery](./media/automation/automation_05_ImportGallery.png)

7.  Wählen Sie die Kategorie **Lernprogramm** und anschließend **Hello World for Azure Automation**. Klicken Sie auf die Schaltfläche mit dem Pfeil nach rechts.

	![Import Runbook](./media/automation/automation_06_ImportRunbook.png)

8.  Überprüfen Sie die Inhalte des Runbooks, und klicken Sie anschließend auf die Schaltfläche mit dem Pfeil nach rechts.

	![Runbook Definition](./media/automation/automation_07_RunbookDefinition.png)

8.	Überprüfen Sie die Details des Runbooks, und klicken Sie anschließend auf das Häkchen.

	![Runbook Details](./media/automation/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Veröffentlichen des Runbooks 

9.	Wenn der Import des Runbooks abgeschlossen ist, klicken Sie auf **Write-HelloWorld**.

	![Imported Runbook](./media/automation/automation_07_ImportedRunbook.png)

9.	Klicken Sie auf **AUTOR** und dann auf **ENTWURF**.  

	Sie können den Inhalt eines Runbooks im Entwurfsmodus ändern. Bei diesem Runbook müssen Sie keine Änderungen vornehmen.

	![Author Draft](./media/automation/automation_08_AuthorDraft.png)  
 
10.	Klicken Sie auf **VERÖFFENTLICHEN**, um das Runbook hochzustufen, sodass es in der Produktion eingesetzt werden kann.

	![Publish](./media/automation/automation_085_Publish.png)
   
11.	Wenn Sie zum Speichern und Veröffentlichen des Runbooks aufgefordert werden, klicken Sie auf **Ja**.
 
	![Save and Pub prompt](./media/automation/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Starten des Runbooks

12.	Klicken Sie bei geöffnetem **Write-HelloWorld-Runbook** auf **START**.

	![Published](./media/automation/automation_10_PublishStart.png)
 
13.	Geben Sie auf der Seite **Specify the runbook parameter values** einen **Namen** ein, der als Eingabeparameter für das Skript "Write-HelloWorld.ps1" verwendet wird, und klicken Sie dann auf das Häkchen.

	![Runbook Parameters](./media/automation/automation_11_RunbookParams.png)
  
14.	Klicken Sie auf **JOBS**, um den Status des soeben gestarteten Runbook-Auftrags zu prüfen. Klicken Sie anschließend auf den Zeitstempel in der Spalte **AUFTRAGSANFANG**, um eine Zusammenfassung des Auftrags anzuzeigen.

	![Runbook Status](./media/automation/automation_12_RunbookStatus.png)

15.	Auf der Seite **ZUSAMMENFASSUNG** sehen Sie die Zusammenfassung, die Eingabeparameter und die Ausgabe des Auftrags.
 
	![Runbook Summary](./media/automation/automation_13_RunbookSummary_callouts.png)


# Verwalten von Azure-Diensten mit einem Runbook 
Das obige Beispiel zeigt ein einfaches Runbook, das keine Azure-Dienste verwaltet. Die [Azure-Cmdlets](http://msdn.microsoft.com/de-de/library/jj156055.aspx) erfordern eine Authentifizierung bei Azure. Eine Anleitung zum Konfigurieren Ihres Azure-Abonnements für die Verwaltung mit Azure Automation finden Sie unter [Azure Automation: Authentifizieren bei Azure mit Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/).

# Siehe auch

- [Übersicht über Automation](http://go.microsoft.com/fwlink/p/?LinkId=392860)
- [Leitfaden zum Erstellen von Runbooks](http://go.microsoft.com/fwlink/p/?LinkID=301740)
- [Automation-Forum](http://go.microsoft.com/fwlink/p/?LinkId=390561)
- [Azure Automation: Authentifizieren bei Azure mit Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)

<!--HONumber=35.2-->
