<properties 
   pageTitle="Grafische Erstellung in Azure Automation"
   description="Die grafische Erstellung ermöglicht Ihnen das Erstellen von Runbooks für Azure Automation, ohne mit Code zu arbeiten. Dieser Artikel bietet eine Einführung in die grafische Erstellung und alle Informationen, die Sie für die Erstellung eines grafischen Runbooks benötigen."
   services="automation"   
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/04/2015"
   ms.author="bwren" />

# Grafische Erstellung in Azure Automation

## Einführung

Mithilfe der grafischen Erstellung können Sie ohne die Komplexität des zugrunde liegenden Windows PowerShell-Workflowcodes Runbooks für Azure Automation erstellen. Sie können Aktivitäten aus einer Cmdlet-Bibliothek und andere Aktivitäten zu einem Zeichenbereich hinzufügen und diese zu einem Workflow verknüpfen.

Dieser Artikel bietet eine Einführung in die grafische Erstellung und die Konzepte, die Sie für den Einstieg in die Erstellung eines grafischen Runbooks benötigen.

## Grafische Runbooks

Alle Runbooks in Azure Automation sind Windows PowerShell-Workflows. Grafische Runbooks generieren PowerShell-Code, der von den Automation-Workern ausgeführt wird. Sie können diesen Code jedoch weder anzeigen noch direkt ändern. Grafische Runbooks können weder in Textrunbooks konvertiert werden, noch können vorhandene Textrunbooks in den grafischen Editor importiert werden.


## Übersicht über den grafischen Editor

Sie können den grafischen Editor im Azure-Vorschauportal öffnen, indem Sie ein grafisches Runbook erstellen oder bearbeiten.

![Grafischer Arbeitsbereich](media/automation-graphical-authoring-intro/graphical-editor.png)


Die folgenden Abschnitte beschreiben die Steuerelemente im grafischen Editor.


### Zeichenbereich
Der Zeichenbereich ist der Bereich, in dem Sie Ihr Runbook entwerfen. Sie fügen über die Knoten im Steuerelement "Bibliothek" Aktivitäten zum Runbook hinzu und verbinden diese mit Verknüpfungen zur Definition der Logik des Runbooks.

### Steuerelement "Bibliothek"

Über das Steuerelement "Bibliothek" wählen Sie die [Aktivitäten](#activities) aus, die Sie Ihrem Runbook hinzufügen möchten. Sie fügen diese zum Zeichenbereich hinzu, wenn Sie sie mit anderen Aktivitäten verbinden. Das Steuerelement enthält vier Abschnitte, die in der folgenden Tabelle beschrieben werden.

| Abschnitt | Beschreibung |
|:---|:---|
| Cmdlets | Enthält alle Cmdlets, die in Ihrem Runbook verwendet werden können. Die Cmdlets sind nach Modul angeordnet. Es sind alle Module verfügbar, die Sie in Ihrem Automation-Konto installiert haben. |
| Runbooks | Umfasst die Runbooks in Ihrem Automation-Konto, angeordnet nach Tag. Da ein Runbook mehrere Tags aufweisen kann, wird es möglicherweise unterhalb von mehreren Tags aufgeführt. Diese Runbooks können dem Zeichenbereich hinzugefügt werden, um als untergeordnete Runbooks verwendet zu werden. Das aktuell bearbeitete Runbook wird angezeigt, kann jedoch nicht dem Zeichenbereich hinzugefügt werden, da ein Runbook sich nicht selbst aufrufen kann.
| Objekte | Enthalten die [Automation-Objekte](http://msdn.microsoft.com/library/dn939988.aspx) in Ihrem Automation-Konto, die im Runbook verwendet werden können. Wenn Sie ein Objekt in ein Runbook einfügen, wird eine Workflowaktivität zum Abrufen des ausgewählten Objekts hinzugefügt. Im Fall von Variablenobjekten können Sie auswählen, ob Sie eine Aktivität zum Abrufen der Variable oder eine Aktivität zum Festlegen der Variable hinzufügen möchten.
| Steuerelement "Runbook" | Enthält die Runbookaktivitäten, die im aktuellen Runbook verwendet werden können. Eine *Verbindung* akzeptiert mehrere Eingaben und wartet, bis sämtliche dieser Eingaben abgeschlossen wurden, bevor der Workflow fortgesetzt wird. Ein *Workflowskript* führt eine oder mehrere Zeilen PowerShell-Worflowcode aus. Sie können diese Aktivität für benutzerdefinierten Code oder für Funktionalitäten nutzen, die mit anderen Aktivitäten nicht erzielt werden können.|

### Steuerelement "Konfiguration"

Über das Steuerelement "Konfiguration" stellen Sie Details zu einem im Zeichenbereich ausgewählten Objekt bereit. Die in diesem Steuerelement verfügbaren Eigenschaften richten sich nach dem Typ des ausgewählten Objekts. Wenn Sie eine Option im Steuerelement "Konfiguration" auswählen, werden zusätzliche Blätter zum Bereitstellen zusätzlicher Informationen geöffnet.

### Steuerelement "Test"

Das Steuerelement "Test" wird beim ersten Start des grafischen Editors nicht angezeigt. Es wird geöffnet, wenn Sie ein [grafisches Runbook interaktiv testen](#graphical-runbook-procedures).

## Grafische Runbooks – Verfahren 

### Exportieren und Importieren eines grafischen Runbooks

Sie können nur die veröffentlichte Version eines grafisch Runbooks exportieren. Wenn das Runbook noch nicht veröffentlicht wurde, ist die Schaltfläche **Veröffentlichtes exportieren** deaktiviert. Beim Klicken auf die Schaltfläche **Veröffentlichtes exportieren** wird das Runbook auf Ihren lokalen Computer heruntergeladen. Der Name der Datei entspricht dem Namen des Runbooks mit der Erweiterung *graphrunbook*.

![Veröffentlichte exportieren](media/automation-graphical-authoring-intro/runbook-export.png)

Sie können eine grafische Runbookdatei importieren, indem Sie die Option **Importieren** auswählen, wenn Sie ein Runbook hinzufügen. Wenn Sie die zu importierende Datei auswählen, können Sie denselben **Name**n beibehalten oder einen neuen vergeben.

![Runbook importieren](media/automation-graphical-authoring-intro/runbook-import.png)


### Testen eines grafischen Runbooks

Sie können die Entwurfsversion eines Runbooks im Azure-Vorschauportal testen und dabei die veröffentliche Version des Runbooks unverändert lassen, oder Sie testen ein neues Runbook, bevor Sie dieses veröffentlichen. Auf diese Weise stellen Sie sicher, dass das Runbook ordnungsgemäß arbeitet, bevor Sie die veröffentlichte Version ersetzen. Wenn Sie ein Runbook testen, wird die Entwurfsversion des Runbooks ausgeführt, und alle darin ausgeführten Aktionen werden abgeschlossen. Es wird kein Auftragsverlauf erstellt, aber im Fensterbereich "Testausgabe" wird die Ausgabe angezeigt.

Sie öffnen das Steuerelement "Test" für ein Runbook, indem Sie das Runbook zur Bearbeitung öffnen und dann auf die Schaltfläche **Testbereich** klicken.

![Schaltfläche "Testbereich"](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

Das Steuerelement "Test" fordert alle benötigten Eingabeparameter an, und Sie können das Runbook starten, indem Sie auf die Schaltfläche **Start** klicken.

![Schaltflächen im Steuerelement "Test"](media/automation-graphical-authoring-intro/runbook-test-start.png)

### Veröffentlichen eines grafischen Runbooks

Jedes Runbook in Azure Automation umfasst eine Entwurfsversion und eine veröffentlichte Version. Nur die veröffentlichte Version kann ausgeführt werden, und nur die Entwurfsversion kann bearbeitet werden. Die veröffentlichte Version bleibt von Änderungen an der Entwurfsversion unberührt. Wenn die Entwurfsversion zur Verfügung gestellt werden kann, können Sie sie veröffentlichen und auf diese Weise die veröffentlichte Version überschreiben.

Sie können ein grafisches Runbook veröffentlichen, indem Sie das Runbook zur Bearbeitung öffnen und dann auf die Schaltfläche **Veröffentlichen** klicken.

![Schaltfläche "Veröffentlichen"](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Wenn ein Runbook noch nicht veröffentlicht wurde, weist es den Status **Neu** auf. Nach dem Veröffentlichen erhält es den Status **Veröffentlicht**. Wenn Sie das Runbook nach der Veröffentlichung bearbeiten und sich die Entwurfsversion und die veröffentlichte Version unterscheiden, weist das Runbook den Status **In Bearbeitung** auf.

![Statuswerte für ein Runbook](media/automation-graphical-authoring-intro/runbook-statuses.png)

Sie haben außerdem die Möglichkeit, ein Runbook auf die veröffentlichte Version zurückzusetzen. Auf diese Weise werden alle Änderungen seit der letzten Veröffentlichung verworfen, und die Entwurfsversion des Runbooks wird durch die veröffentlichte Version ersetzt.

![Schaltfläche "Auf veröffentlichte Version zurücksetzen"](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)


## Aktivitäten

Aktivitäten sind die Bausteine eines Runbooks. Bei einer Aktivität kann es sich um ein PowerShell-Cmdlet, um ein untergeordnetes Runbook oder um eine Workflowaktivität handeln. Sie fügen dem Runbook eine Aktivität hinzu, indem Sie mit der rechten Maustaste im Steuerelement "Bibliothek" auf das Runbook klicken und die Option **Zu Zeichenbereich hinzufügen** auswählen. Anschließend können Sie die Aktivität per Drag & Drop an einer beliebigen Stelle im Zeichenbereich platzieren. Die Position der Aktivität im Zeichenbereich hat keinerlei Einfluss auf den Vorgang im Runbook. Sie können das Layout Ihres Runbooks so wählen, wie es Ihnen zur visuellen Darstellung der Vorgänge am geeignetsten erscheint.

![Zum Zeichenbereich hinzufügen](media/automation-graphical-authoring-intro/add-to-canvas.png)

Wählen Sie die Aktivität im Zeichenbereich, um ihre Eigenschaften und Parameter im Blatt "Konfigurieren" zu konfigurieren. Sie können die **Bezeichnung** der Aktivität in eine für Sie aussagekräftige Beschreibung ändern. Das ursprüngliche Cmdlet wird weiterhin ausgeführt, Sie ändern lediglich den Anzeigenamen, der im grafischen Editor verwendet wird. Die Bezeichnung muss innerhalb des Runbooks eindeutig sein.

### Parametersätze

Ein Parametersatz definiert die erforderlichen und optionalen Parameter, die Werte für ein bestimmtes Cmdlet akzeptieren. Alle Cmdlets müssen über mindestens einen Parametersatz verfügen, einige enthalten mehrere. Wenn ein Cmdlet mehrere Parametersätze aufweist, können Sie die Parameter erst konfigurieren, wenn Sie den zu verwendenden Parametersatz ausgewählt haben. Die Parameter, die Sie konfigurieren können, richten sich nach dem ausgewählten Parametersatz. Sie ändern den durch eine Aktivität verwendeten Parametersatz, indem Sie **Parametersatz** auswählen und einen anderen Satz angeben. In diesem Fall gehen alle bereits konfigurierten Parameterwerte verloren.

Im folgenden Beispiel verfügt das Cmdlet "Get-AzureVM" über zwei Parametersätze. Sie können erst dann Parameterwerte konfigurieren, wenn Sie einen der Parametersätze ausgewählt haben. Der Parametersatz "ListAllVMs" dient der Rückgabe aller virtuellen Computer und umfasst einen einzelnen optionalen Parameter. Der Parametersatz "GetVMByServiceand VMName" dient zur Festlegung des virtuellen Computers, den Sie zurückgeben möchten, und umfasst einen erforderlichen und zwei optionale Parameter.

![Parametersatz](media/automation-graphical-authoring-intro/parameter-set.png)

#### Parameterwerte

Wenn Sie einen Wert für einen Parameter angeben, wählen Sie eine Datenquelle aus, um festzulegen, wie der Wert angegeben wird. Die für einen bestimmten Parameter verfügbaren Datenquellen richten sich nach den gültigen Werten für diesen Parameter. Beispielsweise ist "Null" keine verfügbare Option für einen Parameter, der Null-Werte nicht zulässt.

| Datenquelle | Beschreibung |
|:---|:---|
|Konstanter Wert|Geben Sie einen Wert für den Parameter ein. Dies ist nur für die folgenden Datentypen möglich: Int32, Int64, String, Boolean, DateTime, Switch. |
|Aktivitätsausgabe|Ausgabe einer Aktivität, die der aktuellen Aktivität im Workflow vorausgeht. Es werden alle Aktivitäten aufgelistet. Wählen Sie einfach die Aktivität, um ihre Ausgabe für den Parameterwert zu verwenden. Wenn es sich bei der Aktivitätsausgabe um ein Objekt mit mehreren Eigenschaften handelt, können Sie nach Auswahl der Aktivität den Namen der Eigenschaft eingeben.|
|Runbook-Eingabeparameter|Wählen Sie einen Runbook-Eingabeparameter als Eingabe für den Aktivitätsparameter aus.|  
|Automation-Variablenressource|Wählen Sie eine Automation-Variable als Eingabe aus.|  
|Automation-Anmeldeinformationsobjekt|Wählen Sie ein Automation-Anmeldeinformationsobjekt als Eingabe aus.|  
|Automation-Zertifikatobjekt|Wählen Sie ein Automation-Zertifikatobjekt als Eingabe aus.|  
|Automation-Verbindungsobjekt|Wählen Sie ein Automation-Verbindungsobjekt als Eingabe aus.| 
|PowerShell-Ausdruck|Geben Sie einen einfachen PowerShell-Ausdruck an. Der Ausdruck wird vor der Aktivität ausgewertet, und das Ergebnis wird für den Parameterwert verwendet. Sie können Variablen verwenden, um auf die Ausgabe einer Aktivität oder einen Eingabeparameter für ein Runbook zu verweisen.|
|Leere Zeichenfolge|Ein leerer Zeichenfolgenwert.|
|Null|Ein Null-Wert.|
|Auswahl aufheben|Löscht einen Wert, der zuvor konfiguriert wurde.|


#### Optionale zusätzliche Parameter

Alle Cmdlets bieten die Möglichkeit, zusätzliche Parameter festzulegen. Hierbei handelt es sich um allgemeine PowerShell-Parameter oder andere benutzerdefinierte Parameter. Es wird Ihnen ein Textfeld angezeigt, in dem Sie unter Verwendung der PowerShell-Syntax Parameter bereitstellen können. Um beispielsweise den allgemeinen Parameter **Verbose** zu verwenden, geben Sie **"-Verbose:$True"** an.

### Steuerelement "Workflowskript"

Das Steuerelement "Workflowskript" ist eine besondere Aktivität, die PowerShell-Workflowcode akzeptiert, um Funktionalität bereitzustellen, die andernfalls nicht zur Verfügung steht. Es handelt sich nicht um einen vollständigen Workflow, das Skript muss jedoch gültige PowerShell-Codezeilen enthalten. Es kann keine Parameter akzeptieren, kann jedoch Variablen für die Aktivitätsausgabe und Runbookeingabeparameter verwenden. Die Ausgabe der Aktivität wird dem Datenbus hinzugefügt, sofern eine ausgehende Verknüpfung vorhanden ist. Andernfalls wird die Ausgabe zur Runbookausgabe hinzugefügt.

Der folgende Code beispielsweise führt Datumsberechnungen unter Verwendung einer Runbookeingabevariable namens "$NumberOfDays" durch. Anschließend wird ein berechneter DateTime-Wert als Ausgabe an nachfolgende Aktivitäten im Runbook gesendet.

    $DateTimeNow = InlineScript{(Get-Date).ToUniversalTime()}
    $DateTimeStart = InlineScript{($using:DateTimeNow).AddDays(-$using:NumberOfDays)}
	$DateTimeStart


## Verknüpfungen und Workflow

Eine **Verknüpfung** in einem grafischen Runbook verbindet zwei Aktivitäten miteinander. Sie wird im Zeichenbereich als Pfeil dargestellt, der von der Quellaktivität zur Zielaktivität zeigt. Die Aktivitäten werden in Richtung des Pfeils ausgeführt, wobei die Zielaktivität gestartet wird, sobald die Quellaktivität abgeschlossen wurde.

### Erstellen einer Verknüpfung

Sie erstellen eine Verknüpfung zwischen zwei Aktivitäten, indem Sie die Quellaktivität auswählen und auf den Kreis am unteren Ende der Form klicken. Ziehen Sie den Pfeil bis zur Zielaktivität, und lassen Sie die Maustaste los.

![Erstellen einer Verknüpfung](media/automation-graphical-authoring-intro/create-link.png)

Wählen Sie die Verknüpfung aus, um ihre Eigenschaften im Blatt "Konfiguration" zu konfigurieren. Hierzu gehört beispielsweise der Verknüpfungstyp, der in der folgenden Tabelle beschrieben wird.

| Verknüpfungstyp | Beschreibung |
|:---|:---|
| Pipeline | Die Zielaktivität wird einmal für jede Objektausgabe der Quellaktivität ausgeführt. Die Zielaktivität wird nicht ausgeführt, wenn die Quellaktivität zu keiner Ausgabe führt. Die Ausgabe der Quellaktivität steht als Objekt zur Verfügung. |
| Sequenz | Die Zielaktivität wird nur einmal ausgeführt. Sie empfängt ein Array aus Objekten von der Quellaktivität. Die Ausgabe der Quellaktivität steht als Array aus Objekten zur Verfügung. |

### Startaktivität

Ein grafisches Runbook wird mit allen Aktivitäten gestartet, die keine eingehende Verknüpfung besitzen. Dies ist oft nur eine Aktivität, die als Startaktivität für das Runbook dient. Wenn mehrere Aktivitäten keine eingehende Verknüpfung aufweisen, wird das Runbook durch eine parallele Ausführung dieser Aktivitäten gestartet. Anschließend werden die Verknüpfungen durchlaufen, um nach jeder Beendigung einer Aktivität weitere Aktivitäten auszuführen.

### Bedingungen

Wenn Sie für eine Verknüpfung eine Bedingung angeben, wird die Zielaktivität nur ausgeführt, wenn die Bedingung als wahr ausgewertet wird. Sie verwenden typischerweise eine $ActivityOutput-Variable in einer Bedingung, um die Ausgabe der Quellaktivität abzurufen.

Bei einer Pipelineverknüpfung geben Sie eine Bedingung für ein einzelnes Objekt an, und die Bedingung wird für jedes ausgegebene Objekt der Quellaktivität ausgewertet. Die Zielaktivität wird anschließend für jedes Objekt ausgeführt, das die Bedingung erfüllt. Wenn Sie beispielsweise "Get-AzureVM" als Quellaktivität verwenden, könnte die folgende Syntax für eine Pipelineverknüpfung mit Bedingung verwendet werden, um nur virtuelle Computer abzurufen, die zurzeit ausgeführt werden.

	$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'

Bei einer Sequenzverknüpfung wird die Bedingung nur einmal ausgewertet, da ein einzelnes Array mit sämtlichen Objekten zurückgegeben wird, die von der Quellaktivität ausgegeben werden. Aus diesem Grund kann eine Sequenzverknüpfung nicht wie eine Pipelineverknüpfung zur Filterung verwendet werden, sondern ermittelt einfach nur, ob die nächste Aktivität ausgeführt wird oder nicht. Der folgende Code zeigt dasselbe Beispiel zur Auswertung der Ausgabe von "Get-AzureVM" zum Ermitteln der virtuellen Computer, die zurzeit ausgeführt werden. In diesem Fall prüft der Code jedes Objekt im Array und wird als "true" ausgewertet, wenn mindestens ein virtueller Computer ausgeführt wird. Die Zielaktivität ist für das Analysieren dieser Daten zuständig.

	$test = $false
	$VMs = $ActivityOutput['Get-AzureVm']
	Foreach ($VM in VMs)
	{
		If ($VM.PowerState –eq 'Started')
			{
				$test = $true
			}
	}
	$test

Wenn Sie eine bedingte Verknüpfung verwenden, werden die verfügbaren Daten aus der Quellaktivität für andere Aktivitäten in dieser Verzweigung durch die Bedingung gefiltert. Wenn eine Aktivität die Quelle für mehrere Verknüpfungen ist, richten sich die verfügbaren Daten für Aktivitäten in jeder Verzweigung nach der Bedingung in der Verknüpfung, die mit dieser Verzweigung verbunden ist.

Beispielsweise ruft die Quellaktivität im nachstehenden Runbook alle virtuellen Computer ab. Die Aktivität weist zwei bedingte Verknüpfungen und eine Verknüpfung ohne Bedingung auf. Die erste bedingte Verknüpfung verwendet den Ausdruck *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'*, um nur die virtuellen Computer herauszufiltern, die zurzeit ausgeführt werden. Die zweite Verknüpfung verwendet den Ausdruck *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Stopped'*, um nur die virtuellen Computer herauszufiltern, die zurzeit beendet sind.

![Beispiel einer bedingten Verknüpfung](media/automation-graphical-authoring-intro/conditional-links.png)

Alle Aktivitäten, die auf die erste Verknüpfung folgen und die Aktivitätsausgabe von "Get-AzureVM" verwenden, empfangen nur die virtuellen Computer, die zum Zeitpunkt der Ausführung von "Get-AzureVM" gestartet waren. Alle Aktivitäten, die auf die zweite Verknüpfung folgen, empfangen die virtuellen Computer, die zum Zeitpunkt der Ausführung von "Get-AzureVM" beendet waren. Jede Aktivität, die auf die dritte Verknüpfung folgt, empfängt alle virtuellen Computer – unabhängig von deren Ausführungsstatus.

### Verbindungen

Eine Verbindung ist eine spezielle Aktivität, die wartet, bis alle eingehenden Verzweigungen abgeschlossen wurden. Auf diese Weise können mehrere Aktivitäten parallel ausgeführt werden, und vor dem Fortfahren sichergestellt, dass alle Aktivitäten abgeschlossen wurden.

Wenngleich eine Verbindung eine unbegrenzte Anzahl von eingehenden Verknüpfungen aufweisen kann, darf nur eine dieser Verknüpfungen eine Pipeline sein. Für die Anzahl der eingehenden Sequenzverknüpfungen gilt keine Beschränkung. Sie können die Verbindung mit mehreren eingehenden Pipelineverknüpfungen erstellen und das Runbook speichern, bei der Ausführung tritt jedoch ein Fehler auf.

Das nachstehende Beispiel ist ein Teil eines Runbooks, das einen Satz virtueller Computer startet, während gleichzeitig Patches heruntergeladen werden, die auf diese Computer angewendet werden sollen. Eine Verbindung stellt sicher, dass beide Prozesse abgeschlossen sind, bevor das Runbook fortgesetzt wird.

![Verbindung](media/automation-graphical-authoring-intro/junction.png)

### Zyklen

Wenn eine Zielaktivität eine Verknüpfung zurück auf die Quellaktivität oder eine andere Aktivität umfasst, die letztlich eine Verknüpfung mit der zugehörigen Quellaktivität aufweist, wird dies als Zyklus bezeichnet. Zyklen werden in der grafischen Erstellung zurzeit nicht unterstützt. Wenn Ihr Runbook einen Zyklus aufweist, wird dieser ordnungsgemäß gespeichert, verursacht bei der Ausführung jedoch einen Fehler.

![Zyklus](media/automation-graphical-authoring-intro/cycle.png)

### Schleifen

Eine Schleife liegt vor, wenn Sie eine Aktivität für eine bestimmte Anzahl von Durchläufen oder so lange wiederholen, bis eine bestimmte Bedingung erfüllt ist. Schleifen werden in grafischen Runbooks zurzeit nicht unterstützt.

### Freigeben von Daten zwischen Aktivitäten

Sämtliche Daten, die von einer Aktivität mit ausgehender Verknüpfung ausgegeben werden, werden in den *Datenbus* für das Runbook geschrieben. Alle Aktivitäten im Runbook können die Daten im Datenbus zum Auffüllen von Parameterwerten oder zur Nutzung in Skriptcode verwenden. Eine Aktivität kann auf die Ausgabe einer beliebigen vorherigen Aktivität im Workflow zugreifen.

Wie die Daten in den Datenbus geschrieben werden, richtet sich nach dem Typ der Verknüpfung der Aktivität. Bei einer **Pipeline** werden die Daten als mehrere Objekte ausgegeben. Bei einer **Sequenz** werden die Daten als Array ausgegeben. Wenn nur ein Wert vorliegt, wird dieser als ein einzelnes Elementarray ausgegeben.

Sie können über eine von zwei Methoden auf die Daten im Datenbus zugreifen. Die erste Methode besteht darin, über eine Datenquelle vom Typ **Aktivitätsausgabe** einen Parameter einer anderen Aktivität aufzufüllen. Wenn es sich bei der Ausgabe um ein Objekt handelt, können Sie eine einzelne Eigenschaft festlegen.

![Aktivitätsausgabe](media/automation-graphical-authoring-intro/activity-output-datasource.png)

Sie können außerdem die Ausgabe einer Aktivität in einer Datenquelle vom Typ **PowerShell-Ausdruck** oder mit einer ActivityOutput-Variable aus einer Aktivität vom Typ **Workflowskript** abrufen. Wenn es sich bei der Ausgabe um ein Objekt handelt, können Sie eine einzelne Eigenschaft festlegen. ActivityOutput-Variablen verwenden die folgende Syntax.

	$ActivityOutput['Activity Label']
	$ActivityOutput['Activity Label'].PropertyName 

### Prüfpunkte

Die Anleitungen zum Festlegen von [Prüfpunkten](automation-powershell-workflow/#checkpoints) in Ihrem Runbook gelten auch für grafische Runbooks. Sie können eine Aktivität für das Cmdlet "Checkpoint-Workflow" hinzufügen, wenn Sie einen Prüfpunkt setzen möchten. Sie sollten nach dieser Aktivität "Add-AzureAccount" einfügen, falls das Runbook von diesem Prüfpunkt aus auf einem anderen Worker gestartet wird.

## Authentifizierung bei Azure-Ressourcen

Die meisten Runbooks in Azure Automation erfordern eine Authentifizierung bei den Azure-Ressourcen. Die typische Methode für die Authentifizierung ist das Cmdlet "Add-AzureAccount" mit einem [Anmeldeinformationsobjekt](http://msdn.microsoft.com/library/dn940015.aspx), das einen Active Directory-Benutzer mit Zugriff auf das Azure-Konto repräsentiert. Dieser Vorgang wird in [Konfigurieren von Azure Automation](automation-configuring.md) erläutert.

Sie können diese Funktionalität zu einem grafischen Runbook hinzufügen, indem Sie dem Zeichenbereich ein Anmeldeinformationsobjekt gefolgt von einer Add-AzureAccount-Aktivität hinzufügen. "Add-AzureAccount" verwendet die Anmeldeinformationen aus der Aktivität als Eingabe. Dies wird im folgenden Beispiel veranschaulicht.

![Authentifizierungsaktivitäten](media/automation-graphical-authoring-intro/authentication-activities.png)

Sie müssen beim Start des Runbooks und nach jedem Prüfpunkt eine Authentifizierung durchführen. Dies bedeutet, dass Sie nach jeder Checkpoint-Workflow-Aktivität eine Add-AzureAccount-Aktivität einfügen. Sie müssen keine zusätzliche Aktivität für die Anmeldung hinzufügen, da dieselbe Aktivität verwendet werden kann.

![Aktivitätsausgabe](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Eingabe und Ausgabe in Runbooks

### Runbookeingabe

Ein Runbook erfordert möglicherweise Eingaben durch den Benutzer, wenn es über das Azure-Vorschauportal oder (wenn das aktuelle Runbook als untergeordnetes Runbook verwendet wird) aus einem anderen Runbook gestartet wird. Wenn Sie beispielsweise über ein Runbook verfügen, das einen virtuellen Computer erstellt, müssen Sie bei jedem Start des Runbooks möglicherweise Informationen wie den Namen des virtuellen Computers und andere Eigenschaften bereitstellen.

Sie akzeptieren eine Eingabe für ein Runbook, indem Sie einen oder mehrere Eingabeparameter definieren. Die Werte für diese Parameter werden bei jedem Start des Runbooks bereitgestellt. Wenn Sie ein Runbook mit dem Azure-Vorschauportal starten, werden Sie aufgefordert, Werte für jeden Eingabeparameter des Runbooks einzugeben.

Sie können auf die Eingabeparameter für ein Runbook zugreifen, indem Sie auf die Schaltfläche **Eingabe und Ausgabe** auf der Symbolleiste des Runbooks klicken.

![Eingabe und Ausgabe in Runbooks](media/automation-graphical-authoring-intro/runbook-edit-input-output.png)

Daraufhin wird das Steuerelement **Eingabe und Ausgabe** geöffnet, in dem Sie einen vorhandenen Eingabeparameter bearbeiten oder durch Klicken auf **Eingabe hinzufügen** einen neuen Eingabeparameter erstellen können.

![Eingabe hinzufügen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Jeder Eingabeparameter wird durch die Eigenschaften in der folgenden Tabelle definiert.

|Eigenschaft|Beschreibung|
|:---|:---|
| Name | Der eindeutige Name des Parameters. Dieser darf nur aus alphanumerischen Zeichen bestehen und darf keine Leerzeichen enthalten. |
| Beschreibung | Eine optionale Beschreibung für den Eingabeparameter. |
| Typ | Der für den Parameterwert erwartete Datentyp. Im Azure-Vorschauportal wird bei Eingabeanforderung ein geeignetes Steuerelement für den jeweiligen Datentyp des Parameters angezeigt. |
| Erforderlich | Gibt an, ob ein Wert für den Parameter bereitgestellt werden muss. Das Runbook kann nicht gestartet werden, wenn der Wert für einen erforderlichen Parameter fehlt, für den kein Standardwert definiert wurde. |
| Standardwert | Gibt an, welcher Wert für den Parameter verwendet wird, wenn kein Wert bereitgestellt wird. Dieser Wert kann entweder "Null" lauten oder einen spezifischen Wert angeben. |


### Runbookausgabe

Daten, die von einer Aktivität ohne ausgehende Verknüpfung erstellt werden, werden der [Ausgabe des Runbooks](http://msdn.microsoft.com/library/azure/dn879148.aspx) hinzugefügt. Die Ausgabe wird mit dem Runbookauftrag gespeichert und steht einem übergeordneten Runbook zur Verfügung, wenn das Runbook als untergeordnetes Runbook verwendet wird.


## Verwandte Artikel

- [Grundlagen des Windows PowerShell-Workflows](automation-powershell-workflow.md)
- [Automation-Objekte](http://msdn.microsoft.com/library/azure/dn939988.aspx)
 

<!---HONumber=Sept15_HO4-->