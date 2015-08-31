<properties
	pageTitle="Mein erstes Textrunbook in Azure Automation | Microsoft Azure"
	description="Tutorial, in dem Sie sich mit dem Erstellen, Testen und Veröffentlichen eines einfachen Textrunbooks unter Verwendung eines PowerShell-Workflows vertraut machen können. Dabei werden verschiedene Konzepte behandelt – beispielsweise die Authentifizierung gegenüber Azure-Ressourcen und Eingabeparameter."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="08/18/2015"
	ms.author="bwren"/>


# Mein erstes Textrunbook

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [Textual](automation-first-runbook-textual.md)

Dieses Tutorial führt Sie durch die Erstellung eines [Textrunbooks](automation-powershell-workflow.md) in Azure Automation. Wir beginnen mit einem einfachen Runbook, das wir testen und veröffentlichen. Dabei erläutern wir, wie Sie den Status des Runbookauftrags nachverfolgen. Anschließend ändern wir das Runbook, um damit tatsächlich Azure-Ressourcen zu verwalten. Im vorliegenden Fall soll ein virtueller Azure-Computer gestartet werden. Danach fügen wir Runbookparameter hinzu, um die Stabilität des Runbooks zu erhöhen.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich <a href="/pricing/free-trial/" target="_blank">[für eine kostenlose Testversion registrieren](http://azure.microsoft.com/pricing/free-trial/).
- [Automation-Konto](automation-configuring.md) zum Speichern des Runbooks.
- Einen virtuellen Azure-Computer. Da dieser Computer gestartet und beendet wird, darf er sich nicht in der Produktionsumgebung befinden.
- [Azure Active Directory-Benutzer und Automation-Anmeldeinformationsobjekt](automation-configuring.md) für die Authentifizierung gegenüber Azure-Ressourcen. Dieser Benutzer muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.

## Schritt 1: Erstellen eines neuen Runbooks

Wir erstellen zunächst ein einfaches Runbook, das den Text *Hello World* ausgibt.

1. Öffnen Sie im Azure-Vorschauportal Ihr Automation-Konto. Die Seite des Automation-Kontos bietet einen kurzen Überblick über die Ressourcen des Kontos. Sie sollten bereits über einige Objekte verfügen. Bei den meisten handelt es sich um die Module, die automatisch in einem neuen Automation-Konto enthalten sind. Darunter müsste sich auch das in den [Voraussetzungen](#prerequisites) erwähnte Anmeldeinformationsobjekt befinden.
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.<br> ![Steuerelement für Runbooks](media/automation-first-runbook-textual/runbooks-control.png)
2. Erstellen Sie ein neues Runbook, indem Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen** klicken.
3. Nennen Sie das Runbook *MyFirstRunbook-Textual*.
4. In diesem Fall erstellen wir ein [Textrunbook](automation-powershell-workflow.md) basierend auf einem PowerShell-Workflow, deshalb wählen wir als **Runbooktyp** die Option **PowerShell-Workflow** aus.<br> ![Neues Runbook](media/automation-first-runbook-textual/new-runbook.png)
5. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen und den Text-Editor zu öffnen.

## Schritt 2 – Hinzufügen von Code zum Runbook

Sie können entweder direkt Code in das Runbook eingeben, oder Sie wählen Cmdlets, Runbooks und Objekte aus dem Bibliotheksteuerelement aus und fügen diese mit entsprechenden Parametern zum Runbook hinzu. In dieser exemplarischen Vorgehensweise erfolgt eine direkte Eingabe in das Runbook.

1. Unser Runbook ist zurzeit leer. Es enthält lediglich das erforderliche Schlüsselwort *workflow*, den Namen des Runbooks und die geschweiften Klammern, die den gesamten Workflow umschließen. <br> ![Steuerelement für Runbooks](media/automation-first-runbook-textual/empty-runbook.png)
2. Geben Sie zwischen den geschweiften Klammern *Write-Output "Hello World."* ein. <br> ![Hello World](media/automation-first-runbook-textual/hello-world.png)
3.   Klicken Sie auf **Speichern**, um das Runbook zu speichern.<br> ![Runbook speichern](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## Schritt 3: Testen des Runbooks

Bevor wir das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, möchten wir uns vergewissern, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die **Entwurfsversion** des Runbooks aus und sehen sich interaktiv die Ausgabe an.
 
2. Klicken Sie auf **Testbereich**, um den Testbereich zu öffnen.<br> ![Testbereich](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2. Klicken Sie auf **Starten**, um den Test zu starten. Andere Optionen dürften nicht zur Verfügung stehen.
3. Ein [Runbookauftrag](automation-runbook-execution) wird erstellt, und der dazugehörige Status wird angezeigt. Der Auftrag besitzt zunächst den Status *In der Warteschlange*, um anzugeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet...* und anschließend zu *Wird ausgeführt...*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  
4. Nach Abschluss des Runbookauftrags wird die Ausgabe angezeigt. In unserem Fall: *Hello World*.<br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. Schließen Sie den Testbereich, um zum Zeichenbereich zurückzukehren.

## Schritt 4: Veröffentlichen und Starten des Runbooks

Das soeben erstellte Runbook befindet sich immer noch im Entwurfsmodus. Wir müssen das Runbook veröffentlichen, um es in der Produktionsumgebung ausführen zu können. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In unserem Fall ist noch keine veröffentlichte Version vorhanden, da wir das Runbook gerade erst erstellt haben.

1. Klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**.<br> ![Veröffentlichen](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2. Wenn Sie jetzt einen Bildlauf nach links durchführen, um das Runbook im Bereich **Runbooks** anzuzeigen, weist das Runbook den Erstellungsstatus **Veröffentlicht** auf.
3. Führen Sie wieder einen Bildlauf nach rechts durch, um den Bereich für **MyFirstRunbook-Textual** anzuzeigen. Mit den Optionen am oberen Rand können wir das Runbook starten, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](automation-webhooks.md) erstellen, um den Start über einen HTTP-Aufruf zu ermöglichen. 
4. Wir möchten das Runbook einfach nur starten. Klicken Sie daher auf **Starten** und anschließend auf **Ja**.<br> ![Runbook starten](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5. Ein Auftragsbereich für den soeben erstellten Runbookauftrag erscheint. Dieser Bereich kann zwar geschlossen werden, in diesem Fall lassen wir ihn jedoch geöffnet, um den Status des Auftrags verfolgen zu können.
6.  Der Auftragsstatus wird unter **Auftragszusammenfassung** angezeigt und entspricht den Statusoptionen, die wir bereits beim Testen des Runbooks gesehen haben.<br> ![API-Zusammenfassung](media/automation-first-runbook-textual/job-pane-summary.png)
7.  Wenn der Runbookstatus *Abgeschlossen* lautet, klicken Sie auf **Ausgabe**. Der Bereich **Ausgabe** wird geöffnet, und der Text *Hello World* wird angezeigt.<br> ![API-Zusammenfassung](media/automation-first-runbook-textual/job-pane-output.png)  
8.  Schließen Sie den Ausgabebereich.
9.  Klicken Sie auf **Datenströme**, um den gleichnamigen Bereich für den Runbookauftrag zu öffnen. Im Ausgabestream sollte nur *Hello World* angezeigt werden. Hier können aber auch andere Datenströme für einen Runbookauftrag (wie etwa "Ausführlich" und "Fehler") angezeigt werden, sofern das Runbook in diese schreibt.<br> ![API-Zusammenfassung](media/automation-first-runbook-textual/job-pane-streams.png) 
9. Schließen Sie den Datenstrom- und den Auftragsbereich, um zum Bereich „MyFirstRunbook“ zurückzukehren.
9.  Klicken Sie auf **Aufträge**, um den Auftragsbereich für dieses Runbook zu öffnen. Dadurch werden alle von diesem Runbook erstellten Aufträge aufgeführt. Hier wird nur ein einzelner Auftrag aufgeführt, da wir den Auftrag bislang erst einmal ausgeführt haben.<br> ![Aufträge](media/automation-first-runbook-textual/runbook-control-jobs.png) 
9. Wenn Sie auf diesen Auftrag klicken, wird wieder der Auftragsbereich geöffnet, den wir uns beim Starten des Runbooks angesehen haben. So können Sie bereits ausgeführte Aufträge öffnen und Details zu jedem Auftrag anzeigen, der für ein bestimmtes Runbook erstellt wurde.

## Schritt 5: Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen

Wir haben unser Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber noch nicht sonderlich hilfreich. Wir möchten damit Azure-Ressourcen verwalten. Dazu ist jedoch eine Authentifizierung mit den Anmeldeinformationen erforderlich, die in den [Voraussetzungen](#prerequisites) genannt sind. Wir verwenden zu diesem Zweck das Cmdlet **Add-AzureAccount**.

1.  Öffnen Sie den Text-Editor, indem Sie im Bereich "MyFirstRunbook-Textual" auf **Bearbeiten** klicken.<br> ![Runbook bearbeiten](media/automation-first-runbook-textual/runbook-toolbar-edit.png) 
2.  Die Zeile **Write-Output** wird nicht mehr benötigt, also löschen Sie sie.
3.  Positionieren Sie den Cursor in einer leeren Zeile zwischen den geschweiften Klammern.
3.  Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Anmeldeinformationen**.
4.  Klicken Sie mit der rechten Maustaste auf Ihr Anmeldeinformationsobjekt, und klicken Sie anschließend auf **Zum Zeichenbereich hinzufügen**. Auf diese Weise wird eine **Get-AutomationPSCredential**-Aktivität für Ihr Anmeldeinformationsobjekt hinzugefügt.
5.  Geben Sie vor **Get-AutomationPSCredential** die Zeichenfolge *$Credential =* ein, um die Anmeldeinformationen einer Variablen zuzuweisen. 
3.  Geben Sie auf der nächsten Zeile *Add-AzureAccount -Credential $Credential* ein. <br> ![Authentifizieren](media/automation-first-runbook-textual/authentication.png) 
3. Klicken Sie auf den **Testbereich**, um das Runbook zu testen.
10. Klicken Sie auf **Starten**, um den Test zu starten. Nach Abschluss des Tests sollten Sie eine ähnliche Ausgabe wie die folgende erhalten, mit der Informationen für den Benutzer im Anmeldeinformationsobjekt zurückgegeben werden. Auf diese Weise wird bestätigt, dass die Anmeldeinformationen gültig sind.<br> ![Authentifizieren](media/automation-first-runbook-textual/authentication-test.png) 

## Schritt 6: Hinzufügen von Code zum Starten eines virtuellen Computers

Nun, da das Runbook für das Azure-Abonnement authentifiziert ist, können wir Ressourcen verwalten. Wir fügen einen Befehl zum Starten eines virtuellen Computers hinzu. Sie können einen beliebigen virtuellen Computer in Ihrem Azure-Abonnement auswählen. Wir werden den Namen vorerst innerhalb des Cmdlets hartcodieren.


1. Geben Sie nach *Add-AzureAccount* den Code *Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'* ein, um den Namen und Dienstnamen des virtuellen Computers anzugeben, der gestartet werden soll. <br> ![Authentifizieren](media/automation-first-runbook-textual/start-azurevm.png) 
9. Speichern Sie das Runbook, und klicken Sie dann auf den **Testbereich**, um das Runbook zu testen.
10. Klicken Sie auf **Starten**, um den Test zu starten. Vergewissern Sie sich nach Abschluss des Tests, dass der virtuelle Computer gestartet wurde.


## Schritt 7: Hinzufügen eines Eingabeparameters zum Runbook

Unser Runbook startet zwar nun den virtuellen Computer, den wir im Runbook hartcodiert haben, es wäre aber praktischer, wenn wir den virtuellen Computer beim Start des Runbooks angeben könnten. Zu diesem Zweck fügen wir dem Runbook nun Eingabeparameter hinzu.

1. Fügen Sie Parameter für *VMName* und *VMServiceName* zum Runbook hinzu, und verwenden Sie diese Variablen mit dem**Start-AzureVM**-Cmdlet, wie in der folgenden Abbildung gezeigt. <br> ![Authentifizieren](media/automation-first-runbook-textual/params.png) 
9. Speichern Sie das Runbook, und öffnen Sie den Testbereich. Beachten Sie, dass Sie nun Werte für die beiden Eingabevariablen angeben können, die im Test verwendet werden. 
11.  Schließen Sie den Testbereich.
12.  Klicken Sie auf **Veröffentlichen**, um die neue Version des Runbooks zu veröffentlichen.
13.  Beenden Sie den virtuellen Computer, den Sie im vorherigen Schritt gestartet haben.
13.  Klicken Sie auf **Starten**, um das Runbook zu starten. Geben Sie **VMName** und **VMServiceName** für den virtuellen Computer ein, der gestartet wird.<br> ![Starten des Runbooks](media/automation-first-runbook-textual/start-runbook-input-params.png) 

14.  Vergewissern Sie sich nach Abschluss des Runbooks, dass der virtuelle Computer gestartet wurde.


## Verwandte Artikel

- [Mein erstes grafisches Runbook](automation-first-runbook-graphical.md)

<!---HONumber=August15_HO8-->