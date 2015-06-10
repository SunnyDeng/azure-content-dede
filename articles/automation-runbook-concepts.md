<properties 
   pageTitle="Azure Automation-Runbooks – Konzepte"
   description="Beschreibt die grundlegenden Konzepte, die Sie kennen sollten, um Runbooks in Azure Automation zu erstellen."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure Automation-Runbooks – Konzepte

Runbooks in Azure Automation sind als Windows PowerShell-Workflows implementiert. Dieser Abschnitt enthält eine kurze Übersicht über die wichtigsten Funktionen von Workflows, die in Automation-Runbooks verwendet werden. Ausführliche Informationen zu Workflows finden Sie in [Erste Schritte mit Windows PowerShell Workflow](http://technet.microsoft.com/library/jj134242.aspx).


## Windows PowerShell-Workflows

Bei einem Workflow handelt es sich um eine Sequenz von programmierten, zusammenhängenden Schritten, mit denen zeitaufwändige Aufgaben ausgeführt werden oder für die mehrere Schritte auf verschiedenen Geräten oder verwalteten Knoten koordiniert werden müssen. Die Vorteile eines Workflows gegenüber einem normalen Skript liegen darin, dass eine Aktion gleichzeitig für mehrere Geräte ausgeführt und bei Auftreten von Fehlern eine automatische Wiederherstellung durchgeführt werden kann. Ein Windows PowerShell-Workflow ist ein Windows PowerShell-Skript, das Windows Workflow Foundation nutzt. Wenngleich der Workflow mit Windows PowerShell-Syntax geschrieben und über Windows PowerShell gestartet wird, erfolgt die Verarbeitung durch Windows Workflow Foundation.

### Grundlegende Struktur

Ein Windows PowerShell-Workflow beginnt mit dem Schlüsselwort **Workflow**, gefolgt vom Hauptteil des Skripts, der von Klammern umschlossen wird. Auf das Schlüsselwort **Workflow** folgt der Name des Workflows, wie in der folgenden Syntax gezeigt. Der Name des Workflows stimmt mit dem Namen des Automation-Runbooks überein.

    Workflow Test-Runbook
    {
       <Commands>
    }

Um dem Workflow Parameter hinzuzufügen, verwenden Sie das Schlüsselwort **Param**, wie in der folgenden Syntax gezeigt. Das Verwaltungsportal fordert den Benutzer auf, Werte für diese Parameter bereitzustellen, wenn das Runbook gestartet wird.  Das vorliegende Beispiel verwendet das optionale Attribut "Parameter", mit dem angegeben wird, ob der Parameter erforderlich ist oder nicht.

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### Benennung

Der Name des Workflows sollte dem Verb-Substantiv-Format entsprechen, dem Standard in Windows PowerShell. Eine Liste der genehmigten Verben finden Sie unter [Genehmigte Verben für Windows PowerShell-Befehle] (http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx). Der Name des Workflows muss mit dem Namen des Automation-Runbooks übereinstimmen. Wenn das Runbook importiert wird, muss der Dateiname mit dem Workflownamen übereinstimmen und auf .ps1 enden.

### Einschränkungen

Eine vollständige Liste der Einschränkungen und der Syntaxunterschiede zwischen Windows PowerShell-Workflows und Windows PowerShell finden Sie unter [Syntaktische Unterschiede zwischen Skriptworkflows und Skripts](http://technet.microsoft.com/library/jj574140.aspx).

## Aktivitäten

Eine Aktivität ist eine bestimmte Aufgabe in einem Workflow. Ebenso wie ein Skript aus einem oder mehreren Befehlen zusammengesetzt ist, setzt sich ein Workflow aus einer oder mehreren Aktivitäten zusammen, die in einer bestimmten Reihenfolge ausgeführt werden. Windows PowerShell Workflow konvertiert viele der Windows PowerShell-Cmdlets automatisch in Aktivitäten, wenn ein Workflow ausgeführt wird. Wenn Sie eines dieser Cmdlets in Ihrem Runbook angeben, wird von Windows Workflow Foundation tatsächlich die entsprechende Aktivität ausgeführt. Für Cmdlets ohne entsprechende Aktivität führt Windows PowerShell Workflow das Cmdlet automatisch in einer [InlineScript](#inlinescript)-Aktivität aus. Es gibt einen Satz Cmdlets, der hiervon ausgeschlossen ist und nicht in einem Workflow verwendet werden kann – es sei denn, Sie schließen diese Cmdlets explizit in einen InlineScript-Block ein. Weitere Informationen zu diesen Konzepten finden Sie unter [Verwenden von Aktivitäten in Skriptworkflows](http://technet.microsoft.com/library/jj574194.aspx).

Workflowaktivitäten teilen sich einen Satz allgemeiner Parameter, um ihren Betrieb zu konfigurieren. Ausführliche Informationen zu den allgemeinen Workflowparametern finden Sie unter [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

## Integrationsmodule

Ein *Integrationsmodul* ist ein Paket, das ein Windows PowerShell-Modul enthält und in Azure Automation importiert werden kann. Cmdlets in Integrationsmodulen, die in Azure Automation importiert werden, stehen automatisch für alle Runbooks im selben Automation-Konto zur Verfügung. Da Azure Automation auf Windows PowerShell 4.0 basiert, bietet es Unterstützung für das automatische Laden von Modulen, d. h. Cmdlets installierter Module können verwendet werden, ohne diese mit [Import-Module](http://technet.microsoft.com/library/hh849725.aspx) in das Skript zu importieren.

## Parallele Ausführung

Ein Vorteil von Windows PowerShell-Workflows besteht darin, dass sie einen Satz an Befehlen parallel – und nicht wie in einem typischen Skript sequenziell – ausführen können. Dies ist in Runbooks besonders nützlich, da möglicherweise mehrere Aktionen ausgeführt werden, die sehr viel Zeit in Anspruch nehmen. Ein Runbook kann beispielsweise einen Satz an virtuellen Computern bereitstellen. Statt jeden Bereitstellungsvorgang nacheinander auszuführen, können die Aktionen simultan durchgeführt werden und verbessern so die Effizienz insgesamt. Erst wenn alle Bereitstellungsvorgänge abgeschlossen sind, wird das Runbook fortgesetzt.

Sie können mit dem Schlüsselwort **Parallel** einen Skriptblock mit mehreren Befehlen erstellen, die gleichzeitig ausgeführt werden. Diese Methode wird in der nachstehend gezeigten Syntax verwendet. In diesem Fall starten "Activity1" und "Activity2" gleichzeitig. "Activity3" startet erst, wenn sowohl "Activity1" als auch "Activity2" abgeschlossen wurden.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Sie können das Konstrukt **ForEach -Parallel** verwenden, um Befehle für jedes Element in einer Auflistung gleichzeitig zu verarbeiten. Die Elemente in der Auflistung werden parallel ausgeführt, während die Befehle im Skriptblock sequenziell ausgeführt werden. Diese Methode wird in der nachstehend gezeigten Syntax verwendet. In diesem Fall startet "Activity1" für alle Elemente in der Auflistung gleichzeitig. "Activity2" wird für alle Elemente gestartet, nachdem "Activity1" abgeschlossen wurde. "Activity3" startet erst, wenn sowohl "Activity1" als auch "Activity2" für alle Elemente abgeschlossen wurden.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Das Schlüsselwort **Sequence** wird zur sequenziellen Ausführung von Befehlen innerhalb eines **Parallel**-Skriptblocks verwendet. Der **Sequence**-Skriptblock wird parallel mit anderen Befehlen ausgeführt, aber die Befehle innerhalb des Blocks werden sequenziell ausgeführt. Diese Methode wird in der nachstehend gezeigten Syntax verwendet. In diesem Fall starten "Activity1", "Activity2" und "Activity3" gleichzeitig. "Activity4" startet erst, wenn "Activity3" abgeschlossen wurde. "Activity5" startet, nachdem alle weiteren Aktivitäten abgeschlossen wurden.

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## Prüfpunkte

Ein *Prüfpunkt* ist eine Momentaufnahme des aktuellen Zustands des Workflows, der den aktuellen Wert für Variablen und sämtliche Ausgaben einschließt, die bis zu diesem Punkt generiert wurden. Der zuletzt angewendete Prüfpunkt in einem Runbook wird in der Automation-Datenbank gespeichert, damit der Workflow fortgesetzt werden kann, wenn zur Laufzeit ein Problem – wie z. B. ein Computerausfall – auftritt. Ohne Prüfpunkt würde der Workflow von vorn starten. Die Prüfpunktdaten werden entfernt, sobald der Runbookauftrag abgeschlossen wurde.

Sie können mithilfe der Aktivität **Checkpoint-Workflow** einen Prüfpunkt in einem Workflow setzen. Wenn Sie diese Aktivität in einem Runbook einschließen, wird sofort ein Prüfpunkt gesetzt. Wenn das Runbook durch eine Ausnahme angehalten wird, erfolgt die Fortsetzung bei Wiederaufnahme des Auftrags vom zuletzt gesetzten Prüfpunkt.

Im folgenden Beispielcode führt eine Ausnahme nach "Activity2" dazu, dass das Runbook angehalten wird. Bei Wiederaufnahme des Auftrags wird zunächst "Activity2" ausgeführt, da diese Aktivität unmittelbar auf den zuletzt gesetzten Prüfpunkt folgt.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Sie können Prüfpunkte nach Aktivitäten setzen, die möglicherweise anfällig für das Auftreten von Ausnahmen sind und die nach dem Fortsetzen eines Runbooks nicht wiederholt werden sollen. Angenommen, Ihr Runbook erstellt einen virtuellen Computer. Sie können sowohl vor als auch nach den Befehlen zum Erstellen des virtuellen Computers einen Prüfpunkt setzen. Wenn bei der Erstellung ein Fehler auftritt, werden die Befehle wiederholt, wenn das Runbook fortgesetzt wird. Wenn die Erstellung erfolgreich war, aber später ein Fehler im Runbook auftritt, wird der virtuelle Computer beim Fortsetzen des Runbooks nicht erneut erstellt.

Weitere Informationen zu Prüfpunkten finden Sie unter [Hinzufügen von Prüfpunkten zu einem Skriptworkflow](http://technet.microsoft.com/library/jj574114.aspx).

## Anhalten eines Workflows

Sie können mit der Aktivität **Suspend-Workflow** das Anhalten eines Runbooks erzwingen. Diese Aktivität setzt einen Prüfpunkt und veranlasst, dass der Workflow sofort angehalten wird. Das Anhalten eines Workflows ist sinnvoll, wenn ein Runbook einen manuellen Schritt erfordert, bevor ein weitere Satz an Aktivitäten ausgeführt wird.

Weitere Informationen zum Anhalten eines Workflows finden Sie unter [Anhalten eines Workflows durch sich selbst](http://technet.microsoft.com/library/jj574175.aspx).

## InlineScript

Die Aktivität **InlineScript** führt einen Block mit Befehlen nicht im PowerShell-Workflow, sondern in einem traditionellen PowerShell-Skript aus und gibt die Ausgabe an den Workflow zurück.  Wenngleich die Befehle in einem Workflow zur Verarbeitung an Windows Workflow Foundation gesendet werden, werden die Befehle in einem InlineScript-Block durch Windows PowerShell verarbeitet. Die Aktivität verwendet die standardmäßigen allgemeinen Parameter, darunter **PSCredential**, mit denen Sie angeben können, dass der Codeblock mit alternativen Anmeldeinformationen ausgeführt werden soll.

Der InlineScript-Block verwendet die nachstehende Syntax.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Wenngleich InlineScript-Aktivitäten in bestimmten Runbooks wichtig sein können, bieten sie keine Unterstützung für Workflowkonstrukte und sollten aus den folgenden Gründen nur eingesetzt werden, wenn dies erforderlich ist:

- Sie können keine Prüfpunkte aus einem InlineScript-Block verwenden. Wenn innerhalb des Blocks ein Fehler auftritt, muss der Vorgang am Anfang des Blocks neu gestartet werden.
- InlineScript-Blöcke beeinträchtigen die Skalierbarkeit des Runbooks, da die Windows PowerShell-Sitzung für die gesamte Dauer des InlineScript-Blocks übernommen wird.
- Aktivitäten wie "Get-AutomationVariable" und "Get-AutomationPSCredential" stehen in einem InlineScript-Block nicht zur Verfügung.  

Wenn Sie einen InlineScript-Block verwenden, sollten Sie dessen Umfang einschränken. Wenn Ihr Runbook beispielsweise eine Auflistung durchläuft und dabei denselben Vorgang für jedes Element ausführt, sollte die Schleife außerhalb des InlineScript-Blocks erfolgen. Dies bietet folgende Vorteile:

- Sie können nach jeder Iteration einen [Prüfpunkt](#checkpoints) anwenden. Wenn der Auftrag angehalten oder unterbrochen und dann fortgesetzt wird, kann die Schleife wiederaufgenommen werden.
- Sie können mithilfe von **ForEach -Parallel** Auflistungselemente gleichzeitig verarbeiten.

Berücksichtigen Sie bei Verwendung eines InlineScript-Blocks in Ihrem Runbook die folgenden Empfehlungen:

- Sie können mit dem Bereichsmodifikator **$Using** Werte an das Skript übergeben. Beispiel: Eine Variable namens "$abc", die außerhalb des InlineScript-Blocks festgelegt wurde, wird innerhalb des InlineScript-Blocks zu "$using:abc".

- Um eine Ausgabe aus einem InlineScript-Block zurückzugeben, weisen Sie die Ausgabe einer Variablen zu und geben alle zurückzugebenden Daten an den Ausgabestream aus. Das folgende Beispiel weist die Zeichenfolge "hi" einer Variablen mit dem Namen "$output" zu.

	<pre><code>$output = InlineScript { Write-Output "hi" }</code></pre>

- Vermeiden Sie das Definieren von Workflows im InlineScript-Bereich. Selbst wenn einige Workflows ordnungsgemäß funktionieren können, handelt es sich nicht um ein getestetes Szenario. Es kann daher zu verwirrenden Fehlermeldungen oder einem unerwarteten Verhalten kommen.

Ausführliche Informationen zur Verwendung von InlineScript finden Sie unter [Ausführen von Windows PowerShell-Befehlen in einem Workflow](http://technet.microsoft.com/library/jj574197.aspx) und unter [about_InlinScript](http://technet.microsoft.com/library/jj649082.aspx).


## Verwandte Artikel

- [Erstellen oder Importieren eines Runbooks](http://technet.microsoft.com/library/dn919921.aspx)

<!---HONumber=58-->