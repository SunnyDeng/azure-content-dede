<properties
    pageTitle="Mein erstes PowerShell-Runbook in Azure Automation | Microsoft Azure"
    description="Tutorial, in dem Sie sich mit dem Erstellen, Testen und Veröffentlichen eines einfachen PowerShell-Runbooks vertraut machen können."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn"/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/23/2016"
    ms.author="magoedte;sngun"/>

# Mein erstes PowerShell-Runbook

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Dieses Tutorial führt Sie durch die Erstellung eines [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) in Azure Automation. Wir beginnen mit einem einfachen Runbook, das wir testen und veröffentlichen. Dabei erläutern wir, wie Sie den Status des Runbookauftrags nachverfolgen. Anschließend ändern wir das Runbook, um damit tatsächlich Azure-Ressourcen zu verwalten. Im vorliegenden Fall soll ein virtueller Azure-Computer gestartet werden. Danach fügen wir Runbookparameter hinzu, um die Stabilität des Runbooks zu erhöhen.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

-	Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich <a href="/pricing/free-trial/" target="_blank">[für eine kostenlose Testversion registrieren](https://azure.microsoft.com/pricing/free-trial/).
-	[Automation-Konto](automation-configuring.md) zum Speichern des Runbooks.
-	Einen virtuellen Azure-Computer. Da dieser Computer gestartet und beendet wird, darf er sich nicht in der Produktionsumgebung befinden.
-	[Azure Active Directory-Benutzer und Automation-Anmeldeinformationsobjekt](automation-configuring.md) für die Authentifizierung gegenüber Azure-Ressourcen. Dieser Benutzer muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.

## Schritt 1: Erstellen eines neuen Runbooks

Wir erstellen zunächst ein einfaches Runbook, das den Text *Hello World* ausgibt.

1.	Öffnen Sie im Azure-Portal Ihr Automation-Konto. Die Seite des Automation-Kontos bietet einen kurzen Überblick über die Ressourcen des Kontos. Sie sollten bereits über einige Objekte verfügen. Bei den meisten handelt es sich um die Module, die automatisch in einem neuen Automation-Konto enthalten sind. Darunter müsste sich auch das in den [Voraussetzungen](#prerequisites) erwähnte Anmeldeinformationsobjekt befinden.
2.	Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen. ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3.	Erstellen Sie ein neues Runbook, indem Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen** klicken.
4.	Nennen Sie das Runbook *MyFirstRunbook-PowerShell*.
5.	Da wir hier ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) erstellen, wählen Sie als **Runbooktyp** die Option **PowerShell** aus. ![Runbooktyp](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6.	Klicken Sie auf **Erstellen**, um das Runbook zu erstellen und den Text-Editor zu öffnen.

## Schritt 2 – Hinzufügen von Code zum Runbook

Sie können entweder direkt Code in das Runbook eingeben, oder Sie wählen Cmdlets, Runbooks und Objekte aus dem Bibliotheksteuerelement aus und fügen diese mit entsprechenden Parametern zum Runbook hinzu. In dieser exemplarischen Vorgehensweise erfolgt eine direkte Eingabe in das Runbook.

1.	Das Runbook ist derzeit leer. Geben Sie Folgendes ein: *Write-Output "Hello World."*. ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2.	Klicken Sie auf **Speichern**, um das Runbook zu speichern. ![Schaltfläche „Speichern“](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## Schritt 3: Testen des Runbooks

Bevor wir das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, möchten wir uns vergewissern, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die **Entwurfsversion** des Runbooks aus und sehen sich interaktiv die Ausgabe an.

1.	Klicken Sie auf **Testbereich**, um den Testbereich zu öffnen. ![Testbereich](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2.	Klicken Sie auf **Starten**, um den Test zu starten. Andere Optionen dürften nicht zur Verfügung stehen.
3.	Ein [Runbookauftrag](automation-runbook-execution.md) wird erstellt, und der dazugehörige Status wird angezeigt. Der Auftrag besitzt zunächst den Status *In der Warteschlange*, um anzugeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet...* und anschließend zu *Wird ausgeführt...*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  
4.	Nach Abschluss des Runbookauftrags wird die Ausgabe angezeigt. In unserem Fall: *Hello World*. ![Ausgabe des Testbereichs](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5.	Schließen Sie den Testbereich, um zum Zeichenbereich zurückzukehren.

## Schritt 4: Veröffentlichen und Starten des Runbooks

Das soeben erstellte Runbook befindet sich immer noch im Entwurfsmodus. Wir müssen das Runbook veröffentlichen, um es in der Produktionsumgebung ausführen zu können. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In unserem Fall ist noch keine veröffentlichte Version vorhanden, da wir das Runbook gerade erst erstellt haben.

1.	Klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**. ![Schaltfläche "Veröffentlichen"](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2.	Wenn Sie jetzt einen Bildlauf nach links durchführen, um das Runbook im Bereich **Runbooks** anzuzeigen, weist das Runbook den Erstellungsstatus **Veröffentlicht** auf.
3.	Führen Sie wieder einen Bildlauf nach rechts durch, um den Bereich für **MyFirstRunbook-PowerShell** anzuzeigen. Mit den Optionen am oberen Rand können wir das Runbook starten, das Runbook anzeigen, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](automation-webhooks.md) erstellen, um den Start über einen HTTP-Aufruf zu ermöglichen.
4.	Wir möchten das Runbook hier nur starten. Klicken Sie darum auf **Start** und dann auf dem Blatt „Runbook starten“ auf **OK**. ![Schaltfläche „Start“](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5.	Ein Auftragsbereich für den soeben erstellten Runbookauftrag erscheint. Dieser Bereich kann zwar geschlossen werden, in diesem Fall lassen wir ihn jedoch geöffnet, um den Status des Auftrags verfolgen zu können.
6.	Der Auftragsstatus wird unter **Auftragszusammenfassung** angezeigt und entspricht den Statusoptionen, die wir bereits beim Testen des Runbooks gesehen haben. ![Auftragszusammenfassung](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7.	Wenn der Runbookstatus *Abgeschlossen* lautet, klicken Sie auf **Ausgabe**. Der Bereich „Ausgabe“ wird geöffnet, und der Text *Hello World* wird angezeigt. ![Auftragsausgabe](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.	Schließen Sie den Ausgabebereich.
9.	Klicken Sie auf **Alle Protokolle**, um den Bereich „Datenströme“ für den Runbookauftrag zu öffnen. Im Ausgabedatenstrom sollte nur *Hello World* angezeigt werden. Hier können aber auch andere Datenströme für einen Runbookauftrag (wie etwa „Ausführlich“ und „Fehler“) angezeigt werden, sofern das Runbook in diese schreibt. ![Alle Protokolle](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10.	Schließen Sie den Datenstrom- und den Auftragsbereich, um zum Bereich „MyFirstRunbook-PowerShell“ zurückzukehren.
11.	Klicken Sie auf **Aufträge**, um den Auftragsbereich für dieses Runbook zu öffnen. Dadurch werden alle von diesem Runbook erstellten Aufträge aufgeführt. Hier wird nur ein einzelner Auftrag aufgeführt, da wir den Auftrag bislang erst einmal ausgeführt haben. ![Auftragsliste](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12.	Wenn Sie auf diesen Auftrag klicken, wird wieder der Auftragsbereich geöffnet, den wir uns beim Starten des Runbooks angesehen haben. So können Sie bereits ausgeführte Aufträge öffnen und Details zu jedem Auftrag anzeigen, der für ein bestimmtes Runbook erstellt wurde.

## Schritt 5: Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen

Wir haben unser Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber noch nicht sonderlich hilfreich. Wir möchten damit Azure-Ressourcen verwalten. Dazu ist jedoch eine Authentifizierung mit den Anmeldeinformationen erforderlich, die in den [Voraussetzungen](#prerequisites) genannt sind. Wir verwenden zu diesem Zweck das Cmdlet **Add-AzureAccount**.

1.	Öffnen Sie den Text-Editor, indem Sie im Bereich „MyFirstRunbook-PowerShell“ auf **Bearbeiten** klicken. ![Runbook bearbeiten](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2.	Die Zeile **Write-Output** wird nicht mehr benötigt, also löschen Sie sie.
3.	Erweitern Sie im Bibliothekssteuerelement den Knoten **Cmdlets** und anschließend **Anmeldeinformationen**.
4.	Klicken Sie mit der rechten Maustaste auf das Anmeldeinformationsobjekt, und klicken Sie anschließend auf **Zum Zeichenbereich hinzufügen**. Auf diese Weise wird eine **Get-AutomationPSCredential**-Aktivität für das Anmeldeinformationsobjekt hinzugefügt.
5.	Geben Sie vor **Get-AutomationPSCredential** die Zeichenfolge *$Credential =* ein, um die Anmeldeinformationen einer Variablen zuzuweisen.
6.	Geben Sie in der nächsten Zeile *Add-AzureAccount -Credential $Credential* ein. ![Anmeldeinformationen](media/automation-first-runbook-textual-powershell/automation-get-credential.png)
7.	Klicken Sie auf den **Testbereich**, um das Runbook zu testen.
8.	Klicken Sie auf **Starten**, um den Test zu starten. Sobald der Vorgang abgeschlossen ist, sollten Sie eine Ausgabe mit der Abonnement-ID, dem Typ und den Mandanten Ihres Kontos erhalten. Auf diese Weise wird bestätigt, dass die Anmeldeinformationen gültig sind.

## Schritt 6: Hinzufügen von Code zum Starten eines virtuellen Computers

Nun, da das Runbook für das Azure-Abonnement authentifiziert ist, können wir Ressourcen verwalten. Wir fügen einen Befehl zum Starten eines virtuellen Computers hinzu. Sie können einen beliebigen virtuellen Computer in Ihrem Azure-Abonnement auswählen. Wir werden den Namen vorerst innerhalb des Cmdlets hartcodieren.

1.	Geben Sie nach *Add-AzureAccount* den Code *Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'* ein, um den Namen und Dienstnamen des virtuellen Computers anzugeben, der gestartet werden soll. ![StartVM](media/automation-first-runbook-textual-powershell/automation-startvm.png)  
2.	Speichern Sie das Runbook, und klicken Sie dann auf den **Testbereich**, um das Runbook zu testen.
3.	Klicken Sie auf **Starten**, um den Test zu starten. Vergewissern Sie sich nach Abschluss des Tests, dass der virtuelle Computer gestartet wurde.

## Schritt 7: Hinzufügen eines Eingabeparameters zum Runbook

Unser Runbook startet zwar nun den virtuellen Computer, den wir im Runbook hartcodiert haben, es wäre aber praktischer, wenn wir den virtuellen Computer beim Start des Runbooks angeben könnten. Zu diesem Zweck fügen wir dem Runbook nun Eingabeparameter hinzu.

1.	Fügen Sie dem Runbook Parameter für *VMName* und *VMServiceName* hinzu, und verwenden Sie diese Variablen mit dem Cmdlet **Start-AzureVM**, wie in der folgenden Abbildung gezeigt. ![Parameter hinzufügen](media/automation-first-runbook-textual-powershell/automation-add-parameter.png)  
2.	Speichern Sie das Runbook, und öffnen Sie den Testbereich. Beachten Sie, dass Sie nun Werte für die beiden Eingabevariablen angeben können, die im Test verwendet werden.
3.	Schließen Sie den Testbereich.
4.	Klicken Sie auf **Veröffentlichen**, um die neue Version des Runbooks zu veröffentlichen.
5.	Beenden Sie den virtuellen Computer, den Sie im vorherigen Schritt gestartet haben.
6.	Klicken Sie auf **Starten**, um das Runbook zu starten. Geben Sie **VMName** und **VMServiceName** für den virtuellen Computer ein, der gestartet wird. ![Parameter übergeben](media/automation-first-runbook-textual-powershell/automation-pass-parameter.png)  
7.	Vergewissern Sie sich nach Abschluss des Runbooks, dass der virtuelle Computer gestartet wurde.

## Unterschiede zu PowerShell-Workflow

PowerShell-Runbooks verfügen über den gleichen Lebenszyklus, die gleichen Funktionen und die gleiche Verwaltung wie PowerShell-Workflow-Runbooks, aber es gibt auch einige Unterschiede und Einschränkungen:

1.	PowerShell-Runbooks werden im Vergleich zu PowerShell-Workflow-Runbooks schneller ausgeführt, weil sie über keinen Kompilierungsschritt verfügen.
2.	PowerShell-Workflow-Runbooks unterstützen Prüfpunkte. Mithilfe von Prüfpunkten lassen sich PowerShell-Workflow-Runbooks von einem beliebigen Punkt aus fortsetzen. PowerShell-Runbooks können hingegen nur vom Anfang fortgesetzt werden.
3.	PowerShell-Workflow-Runbooks unterstützen eine parallele und serielle Ausführung, während PowerShell-Runbooks Befehle nur seriell ausführen können.
4.	In einem PowerShell-Workflow-Runbook kann eine Aktivität, ein Befehl oder ein Skriptblock über einen eigenen Runspace verfügen, während in einem PowerShell-Runbook der gesamte Inhalt eines Skripts in einem einzigen Runspace ausgeführt wird. Außerdem gibt es auch einige [syntaktische Unterschiede](https://technet.microsoft.com/magazine/dn151046.aspx) zwischen einem systemeigenen PowerShell-Runbook und einem PowerShell-Workflow-Runbook.

## Nächste Schritte

-	Informationen über die ersten Schritte mit grafischen Runbooks finden Sie unter [Mein erstes grafisches Runbook](automation-first-runbook-graphical.md).
-	Informationen über die ersten Schritte mit PowerShell-Workflow-Runbooks finden Sie unter [Mein erstes PowerShell-Workflow-Runbook](automation-first-runbook-textual.md)
-	Informationen über die verschiedenen Runbooktypen, ihre Vorteile und Einschränkungen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md).
-	Weitere Informationen zur PowerShell-Skriptunterstützungsfeature finden Sie unter [Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).

<!---HONumber=AcomDC_0302_2016-->