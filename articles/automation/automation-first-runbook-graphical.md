<properties
	pageTitle="Mein erstes grafisches Runbook in Azure Automation"
	description="Tutorial, in dem Sie sich mit dem Erstellen, Testen und Veröffentlichen eines einfachen grafischen Runbooks vertraut machen können. Dabei werden verschiedene Konzepte behandelt – beispielsweise die Authentifizierung gegenüber Azure-Ressourcen sowie Eingabeparameter und bedingte Verknüpfungen."
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
	ms.date="09/17/2015"
	ms.author="bwren"/>


# Mein erstes grafisches Runbook

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [PowerShell Workflow](automation-first-runbook-textual.md)

Dieses Tutorial führt Sie durch die Erstellung eines [grafischen Runbooks](automation-runbook-types.md#graphical-runbooks) in Azure Automation. Wir beginnen mit einem einfachen Runbook, das wir testen und veröffentlichen. Dabei erläutern wir, wie Sie den Status des Runbookauftrags nachverfolgen. Anschließend ändern wir das Runbook, um damit tatsächlich Azure-Ressourcen zu verwalten. Im vorliegenden Fall soll ein virtueller Azure-Computer gestartet werden. Danach fügen wir Runbookparameter und eine bedingte Verknüpfung hinzu, um das Runbook hilfreicher zu machen.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich <a href="/pricing/free-trial/" target="_blank">[für eine kostenlose Testversion registrieren](http://azure.microsoft.com/pricing/free-trial/).
- [Automation-Konto](automation-configuring.md) zum Speichern des Runbooks.
- Einen virtuellen Azure-Computer. Da dieser Computer gestartet und beendet wird, darf er sich nicht in der Produktionsumgebung befinden.
- [Azure Active Directory-Benutzer und Automation-Anmeldeinformationsobjekt](automation-configuring.md) für die Authentifizierung gegenüber Azure-Ressourcen. Dieser Benutzer muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.

## Schritt 1: Erstellen eines neuen Runbooks

Wir erstellen zunächst ein einfaches Runbook, das den Text *Hello World* ausgibt.

1. Öffnen Sie im Azure-Vorschauportal Ihr Automation-Konto. Die Seite des Automation-Kontos bietet einen kurzen Überblick über die Ressourcen des Kontos. Sie sollten bereits über einige Objekte verfügen. Bei den meisten handelt es sich um die Module, die automatisch in einem neuen Automation-Konto enthalten sind. Darunter müsste sich auch das in den [Voraussetzungen](#prerequisites) erwähnte Anmeldeinformationsobjekt befinden.
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.<br> ![Steuerelement für Runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
2. Erstellen Sie ein neues Runbook, indem Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen** klicken.
3. Nennen Sie das Runbook *MyFirstRunbook-Graphical*.
4. Da wir in diesem Fall ein [grafisches Runbook](automation-graphical-authoring-intro.md) erstellen, legen Sie den Runbooktyp auf **Grafisch** fest.<br> ![Neues Runbook](media/automation-first-runbook-graphical/new-runbook.png)
5. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen und den grafischen Editor zu öffnen.

## Schritt 2: Hinzufügen von Aktivitäten zum Runbook

Mithilfe des Bibliotheksteuerelements auf der linken Seite des Editors können Sie Aktivitäten auswählen und Ihrem Runbook hinzufügen. Wir fügen ein **Write-Output**-Cmdlet hinzu, um unseren Text aus dem Runbook auszugeben.

1.   Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Microsoft.PowerShell.Utility**.<br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.   Führen Sie einen Bildlauf zum Ende der Liste durch. Klicken Sie mit der rechten Maustaste auf **Write-Output**, und klicken Sie anschließend auf **Zum Zeichenbereich hinzufügen**.
4.   Klicken Sie im Zeichenbereich auf die Aktivität **Write-Output**. Daraufhin öffnet sich das Konfigurationssteuerelement, mit dem Sie die Aktivität konfigurieren können.
5.   Die Bezeichnung ist standardmäßig auf den Namen des Cmdlets festgelegt, kann aber in einen aussagekräftigeren Wert geändert werden. Legen Sie ihn auf *Write Hello World to output* fest.
6.   Klicken Sie auf **Parameter**, um Parameterwerte für das Cmdlet anzugeben. Einige Cmdlets verfügen über mehrere Parametersätze, und Sie müssen auswählen, welchen Sie verwenden möchten. **Write-Output** besitzt in diesem Fall lediglich einen einzelnen Parametersatz, sodass Sie keinen auswählen müssen. <br> ![Write-Output-Eigenschaften](media/automation-first-runbook-graphical/write-output-properties.png)
7.   Wählen Sie den **InputObject**-Parameter aus. Dies ist der Parameter, in dem wir den Text für den Ausgabestream angeben.
9.   Wählen Sie im Dropdownfeld **Datenquelle** die Option **PowerShell-Ausdruck** aus. Das Dropdownfeld **Datenquelle** enthält verschiedene Quellen, die Sie zum Auffüllen eines Parameterwerts verwenden. Sie können Ausgaben von solchen Quellen (etwa eine andere Aktivität, ein Automation-Objekt oder ein PowerShell-Ausdruck) verwenden. In diesem Fall soll lediglich der Text *Hello World* ausgeben werden. Hierzu können wir einen PowerShell-Ausdruck verwenden und eine Zeichenfolge angeben.
10.   Geben Sie im Feld **Ausdruck** die Zeichenfolge *"Hello World"* ein, und klicken Sie dann zweimal auf **OK**, um zum Zeichenbereich zurückzukehren.<br> ![PowerShell-Ausdruck](media/automation-first-runbook-graphical/expression-hello-world.png)
11.   Klicken Sie auf **Speichern**, um das Runbook zu speichern.<br> ![Runbook speichern](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Schritt 3: Testen des Runbooks

Bevor wir das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, möchten wir uns vergewissern, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die **Entwurfsversion** des Runbooks aus und sehen sich interaktiv die Ausgabe an.
 
2. Klicken Sie auf **Testbereich**, um den Testbereich zu öffnen.<br> ![Testbereich](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2. Klicken Sie auf **Starten**, um den Test zu starten. Andere Optionen dürften nicht zur Verfügung stehen.
3. Ein [Runbookauftrag](automation-runbook-execution) wird erstellt, und der dazugehörige Status wird angezeigt. Der Auftrag besitzt zunächst den Status *In der Warteschlange*, um anzugeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet...* und anschließend zu *Wird ausgeführt...*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  
4. Nach Abschluss des Runbookauftrags wird die Ausgabe angezeigt. In unserem Fall: *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5. Schließen Sie den Testbereich, um zum Zeichenbereich zurückzukehren.


## Schritt 4: Veröffentlichen und Starten des Runbooks

Das soeben erstellte Runbook befindet sich immer noch im Entwurfsmodus. Wir müssen das Runbook veröffentlichen, um es in der Produktionsumgebung ausführen zu können. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In unserem Fall ist noch keine veröffentlichte Version vorhanden, da wir das Runbook gerade erst erstellt haben.

1. Klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**.<br> ![Veröffentlichen](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2. Wenn Sie jetzt einen Bildlauf nach links durchführen, um das Runbook im Bereich **Runbooks** anzuzeigen, weist das Runbook den Erstellungsstatus **Veröffentlicht** auf.
3. Führen Sie wieder einen Bildlauf nach rechts durch, um den Bereich für **MyFirstRunbook** anzuzeigen. Mit den Optionen am oberen Rand können wir das Runbook starten, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](automation-webhooks.md) erstellen, um den Start über einen HTTP-Aufruf zu ermöglichen. 
4. Wir möchten das Runbook einfach nur starten. Klicken Sie daher auf **Starten** und anschließend auf **Ja**.<br> ![Runbook starten](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5. Ein Auftragsbereich für den soeben erstellten Runbookauftrag erscheint. Dieser Bereich kann zwar geschlossen werden, in diesem Fall lassen wir ihn jedoch geöffnet, um den Status des Auftrags verfolgen zu können.
6.  Der Auftragsstatus wird unter **Auftragszusammenfassung** angezeigt und entspricht den Statusoptionen, die wir bereits beim Testen des Runbooks gesehen haben.<br> ![API-Zusammenfassung](media/automation-first-runbook-graphical/job-pane-summary.png)
7.  Wenn der Runbookstatus *Abgeschlossen* lautet, klicken Sie auf **Ausgabe**. Der Bereich **Ausgabe** wird geöffnet, und der Text *Hello World* wird angezeigt.<br> ![API-Zusammenfassung](media/automation-first-runbook-graphical/job-pane-output.png)  
8.  Schließen Sie den Ausgabebereich.
9.  Klicken Sie auf **Datenströme**, um den gleichnamigen Bereich für den Runbookauftrag zu öffnen. Im Ausgabestream sollte nur *Hello World* angezeigt werden. Hier können aber auch andere Datenströme für einen Runbookauftrag (wie etwa "Ausführlich" und "Fehler") angezeigt werden, sofern das Runbook in diese schreibt.<br> ![API-Zusammenfassung](media/automation-first-runbook-graphical/job-pane-streams.png) 
9. Schließen Sie den Datenstrom- und den Auftragsbereich, um zum Bereich „MyFirstRunbook“ zurückzukehren.
9.  Klicken Sie auf **Aufträge**, um den Auftragsbereich für dieses Runbook zu öffnen. Dadurch werden alle von diesem Runbook erstellten Aufträge aufgeführt. Hier wird nur ein einzelner Auftrag aufgeführt, da wir den Auftrag bislang erst einmal ausgeführt haben.<br> ![Aufträge](media/automation-first-runbook-graphical/runbook-control-jobs.png) 
9. Wenn Sie auf diesen Auftrag klicken, wird wieder der Auftragsbereich geöffnet, den wir uns beim Starten des Runbooks angesehen haben. So können Sie bereits ausgeführte Aufträge öffnen und Details zu jedem Auftrag anzeigen, der für ein bestimmtes Runbook erstellt wurde.

## Schritt 5: Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen

Wir haben unser Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber noch nicht sonderlich hilfreich. Wir möchten damit Azure-Ressourcen verwalten. Dazu ist jedoch eine Authentifizierung mit den Anmeldeinformationen erforderlich, die in den [Voraussetzungen](#prerequisites) genannt sind. Wir verwenden zu diesem Zweck das Cmdlet **Set-AzureAccount**.

1.  Öffnen Sie den grafischen Editor, indem Sie im Bereich "MyFirstRunbook" auf **Bearbeiten** klicken.<br> ![Runbook bearbeiten](media/automation-first-runbook-graphical/runbook-toolbar-edit.png) 
2.  **Write Hello World to output** wird nicht mehr benötigt. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Löschen**.
8.  Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Azure**. 
9.  Fügen Sie **Add-AzureAccount** dem Zeichenbereich hinzu.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png) 
12.  Wählen Sie **Add-AzureAccount** aus, und klicken Sie anschließend im Konfigurationsbereich auf **Parameter**.
13.  Da **Add-AzureAccount** über mehrere Parametersätze verfügt, müssen wir einen davon auswählen, um Parameterwerte angeben zu können. Klicken Sie auf **Parametersatz**, und wählen Sie anschließend den Parametersatz **User** aus.
14.  Wenn Sie den Parametersatz auswählen, werden die Parameter im Konfigurationsbereich für die Aktivitätsparameter angezeigt. Klicken Sie auf **Credential**.<br> ![Azure-Kontoparameter hinzufügen](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
15.  Da dieses Cmdlet ein Anmeldeinformationsobjekt aus unserem Automation-Konto verwenden soll, wählen Sie als Datenquelle die entsprechende Option aus.
16.  Wählen Sie das Anmeldeinformationsobjekt aus, das zum Starten und Beenden eines virtuellen Computers in Ihrer Azure-Umgebung berechtigt ist.<br> ![Datenquelle für Azure-Konto hinzufügen](media/automation-first-runbook-graphical/credential-data-source.png)
17.  Klicken Sie zweimal auf **OK**, um zum Zeichenbereich zurückzukehren.


## Schritt 6: Hinzufügen einer Aktivität zum Starten eines virtuellen Computers

In diesem Schritt fügen wir eine Aktivität vom Typ **Start-AzureVM** hinzu, um einen virtuellen Computer zu starten. Sie können einen beliebigen virtuellen Computer in Ihrem Azure-Abonnement auswählen. Wir werden den Namen vorerst innerhalb des Cmdlets hartcodieren.

1. Erweitern Sie **Cmdlets** und anschließend **Azure**.
2. Fügen Sie **Start-AzureVM** dem Zeichenbereich hinzu, und ziehen Sie das Element anschließend unter **Add-AzureAccount**.
11.  Zeigen Sie auf **Add-AzureAccount**, bis am unteren Rand der Form ein Kreis erscheint. Klicken Sie auf den Kreis, und ziehen Sie den Pfeil zu **Start-AzureVM**. Bei dem soeben erstellten Pfeil handelt es sich um eine *Verknüpfung*. Das Runbook wird mit **Add-AzureAccount** gestartet und führt dann **Start-AzureVM** aus.<br> ![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
5. Wählen Sie **Start-AzureVM**. Klicken Sie auf **Parameter** und anschließend auf **Parametersatz**, um die Sätze für **Start-AzureVM** anzuzeigen. Wählen Sie den Parametersatz **ByName** aus. Beachten Sie, dass neben **Name** und **ServiceName** jeweils ein Ausrufezeichen angezeigt wird. Das bedeutet, dass es sich dabei um erforderliche Parameter handelt.
7. Wählen Sie **Name**. Verwenden Sie für die Datenquelle die Option **Konstanter Wert**, und geben Sie den Namen des virtuellen Computers ein, der gestartet werden soll. Klicken Sie auf **OK**.
8. Wählen Sie **ServiceName**. Verwenden Sie für die Datenquelle die Option **Konstanter Wert**, und geben Sie den Namen des virtuellen Computers ein, der gestartet werden soll. Klicken Sie auf **OK**.<br> ![Start-AzureVM – Parameter](media/automation-first-runbook-graphical/start-azurevm-params.png)
9. Klicken Sie auf den Testbereich, um das Runbook zu testen.
10. Klicken Sie auf **Starten**, um den Test zu starten. Vergewissern Sie sich nach Abschluss des Tests, dass der virtuelle Computer gestartet wurde.


## Schritt 7: Hinzufügen eines Eingabeparameters zum Runbook

Unser Runbook startet zwar nun den virtuellen Computer, den wir im Cmdlet **Start-AzureVM** angegeben haben, es wäre aber praktischer, wenn wir den virtuellen Computer beim Start des Runbooks angeben könnten. Zu diesem Zweck fügen wir dem Runbook nun einen Eingabeparameter hinzu.

1. Öffnen Sie den grafischen Editor, indem Sie im Bereich **MyFirstRunbook** auf **Bearbeiten** klicken.
2. Klicken Sie auf **Eingabe und Ausgabe** und anschließend auf **Eingabe hinzufügen**, um den Eingabeparameterbereich für das Runbook zu öffnen.<br> ![Runbookeingabe und -ausgabe](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
4. Geben Sie als **Name** die Zeichenfolge *VMName* an. Behalten Sie für **Typ** die Einstellung *string* bei, aber ändern Sie **Obligatorisch** in *Ja*. Klicken Sie auf **OK**.
5. Erstellen Sie einen zweiten obligatorischen Eingabeparameter namens *VMServiceName*, und klicken Sie anschließend auf **OK**, um den Bereich **Eingabe und Ausgabe** zu schließen.<br> ![Runbook-Eingabeparameter](media/automation-first-runbook-graphical/input-parameters.png) 
6. Wählen Sie die Aktivität **Start-AzureVM** aus, und klicken Sie anschließend auf **Parameter**.
7. Ändern Sie die **Datenquelle** für **Name** in **Runbookeingabe**, und wählen Sie anschließend **VMName** aus.<br> ![Start-AzureVM – Namensparameter](media/automation-first-runbook-graphical/vmname-property.png) 
8. Ändern Sie die **Datenquelle** für **ServiceName** in **Runbookeingabe**, und wählen Sie anschließend **VMServiceName** aus.<br> ![Start-AzureVM – Parameter](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png) 
9. Speichern Sie das Runbook, und öffnen Sie den Testbereich. Beachten Sie, dass Sie nun Werte für die beiden Eingabevariablen angeben können, die im Test verwendet werden. 
11.  Schließen Sie den Testbereich.
12.  Klicken Sie auf **Veröffentlichen**, um die neue Version des Runbooks zu veröffentlichen.
13.  Beenden Sie den virtuellen Computer, den Sie im vorherigen Schritt gestartet haben.
13.  Klicken Sie auf **Starten**, um das Runbook zu starten. Geben Sie **VMName** und **VMServiceName** für den virtuellen Computer ein, der gestartet wird.<br> ![Starten des Runbooks](media/automation-first-runbook-graphical/start-runbook-input-params.png) 
14.  Vergewissern Sie sich nach Abschluss des Runbooks, dass der virtuelle Computer gestartet wurde.

## Schritt 8: Erstellen einer bedingten Verknüpfung

In diesem Schritt ändern wir das Runbook, sodass es nur gestartet wird, wenn es noch nicht gestartet wurde. Zu diesem Zweck fügen wir dem Runbook ein **Get-AzureVM**-Cmdlet hinzu, das den aktuellen Zustand des virtuellen Computers enthält. Anschließend fügen eine bedingte Verknüpfung hinzu, der **Start-AzureVM** nur ausführt, wenn der aktuelle Ausführungszustand angibt, dass das Runbook beendet ist. Ist das Runbook nicht beendet, wird eine Meldung ausgegeben.

1. Öffnen Sie **MyFirstRunbook** im grafischen Editor.
2. Entfernen Sie die Verknüpfung zwischen **Add-AzureAccount** und **Start-AzureVM**, indem Sie auf die Verknüpfung klicken und die ENTF-TASTE drücken.
3. Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Azure**.
4. Fügen Sie dem Zeichenbereich **Get-AzureVM** hinzu.
5. Erstellen Sie eine Verknüpfung zwischen **Add-AzureAccount** und **Get-AzureVM**. Erstellen Sie eine weitere Verknüpfung zwischen **Get-AzureVM** und **Start-AzureVM**.<br> ![Runbook mit Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)   
6.  Wählen Sie **Get-AzureVM** aus, und klicken Sie auf **Parameter**. Wählen Sie den Parametersatz *GetVMByServiceAndVMName* aus.
7.  Legen Sie die **Datenquelle** für **Name** auf **Runbookeingabe** fest, und wählen Sie anschließend **VMName** aus.   
7.  Legen Sie die **Datenquelle** für **ServiceName** auf **Runbookeingabe** fest, und wählen Sie anschließend **VMServiceName** aus.  
8.  Wählen Sie die Verknüpfung zwischen **Get-AzureVM** und **Start-AzureVM** aus.
9.  Legen Sie die Option zur Anwendung der Bedingung im Konfigurationsbereich auf **True** fest. Beachten Sie, dass die Verknüpfung nun als gestrichelte Linie dargestellt wird. Das bedeutet, dass die Aktivität nur ausgeführt wird, wenn die Bedingung zu "true" aufgelöst wird.
10.  Geben Sie für den Bedingungsausdruck Folgendes ein: *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*. Nun wird **Start-AzureVM** nur noch ausgeführt, wenn der virtuelle Computer beendet ist.<br> ![Verknüpfungsbedingung](media/automation-first-runbook-graphical/link-condition.png) 
11.  Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** und anschließend **Microsoft.PowerShell.Utility**.
12.  Fügen Sie dem Zeichenbereich **Write-Output** hinzu.
13.  Erstellen Sie eine Verknüpfung zwischen **Get-AzureVM** und **Write-Output**.
14.  Wählen Sie die Verknüpfung aus, und legen Sie die Option zur Anwendung der Bedingung auf **True** fest.
14.  Geben Sie für den Bedingungsausdruck Folgendes ein: *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*. Dadurch wird **Write-Output** nur ausgeführt, wenn der virtuelle Computer nicht beendet ist.<br> ![Runbook mit Write-Output](media/automation-first-runbook-graphical/write-output-link.png) 
15.  Wählen Sie **Write-Output** aus, und klicken Sie auf **Parameter**.
16.  Legen Sie die **Datenquelle** für **InputObject** auf **PowerShell-Ausdruck** fest, und geben Sie den Ausdruck *"$VMName.Name already started."* ein.
17.  Speichern Sie das Runbook, und öffnen Sie den Testbereich.
18.  Wenn Sie nun das Runbook starten, während der virtuellen Computer beendet ist, wird der virtuelle Computer gestartet.
19.  Wenn Sie das Runbook starten und der virtuelle Computer bereits gestartet ist, wird ein entsprechender Hinweis ausgegeben.
 

## Verwandte Artikel

- [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
- [Mein erstes PowerShell-Workflow-Runbook](automation-first-runbook-textual.md)

 

<!---HONumber=Sept15_HO4-->