<properties 
   pageTitle="Grundlagen des PowerShell-Workflows"
   description="Runbooks in Azure Automation basieren auf dem Windows PowerShell-Workflow. Dieser Artikel ist als kurze Lektion für Autoren gedacht, die mit PowerShell vertraut sind, um die Grundlagen der speziellen Unterschiede zwischen PowerShell und dem PowerShell-Workflow zu verdeutlichen."
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
   ms.date="07/06/2015"
   ms.author="bwren" />

# Grundlagen des Windows PowerShell-Workflows

Runbooks in Azure Automation sind als Windows PowerShell-Workflows implementiert. Ein Windows PowerShell-Workflow ähnelt einem Windows PowerShell-Skript, weist aber einige wesentliche Unterschiede auf, die für einen neuen Benutzer verwirrend sein können. Dieser Artikel richtet sich an Benutzer, die bereits mit PowerShell vertraut sind. Er enthält kurze Erklärungen von Konzepten, mit denen Sie vertraut sein müssen, wenn Sie ein PowerShell-Skript zur Verwendung in einem Runbook in einen PowerShell-Workflow konvertieren.

Bei einem Workflow handelt es sich um eine Sequenz von programmierten, zusammenhängenden Schritten, mit denen zeitaufwändige Aufgaben ausgeführt werden oder für die mehrere Schritte auf verschiedenen Geräten oder verwalteten Knoten koordiniert werden müssen. Die Vorteile eines Workflows gegenüber einem normalen Skript liegen darin, dass eine Aktion gleichzeitig für mehrere Geräte ausgeführt und bei Auftreten von Fehlern eine automatische Wiederherstellung durchgeführt werden kann. Ein Windows PowerShell-Workflow ist ein Windows PowerShell-Skript, das Windows Workflow Foundation nutzt. Wenngleich der Workflow mit Windows PowerShell-Syntax geschrieben und über Windows PowerShell gestartet wird, erfolgt die Verarbeitung durch Windows Workflow Foundation.

Ausführliche Informationen zu den Themen in diesem Artikel finden Sie unter [Erste Schritte mit dem Windows PowerShell-Workflow](http://technet.microsoft.com/library/jj134242.aspx).

## Arten von Runbooks

In Azure Automation gibt es zwei Arten von Runbooks: *Text* und *Grafik*. Sie können den Runbooktyp beim Erstellen des Runbooks definieren, und es ist nicht möglich, ein Runbook nach der Erstellung in den anderen Typ zu konvertieren.

Runbooks vom Typ „Text“ sind für Benutzer bestimmt, die lieber direkt mit dem PowerShell-Workflowcode arbeiten – entweder mit dem Text-Editor in Azure Automation oder mit einem Offline-Editor wie PowerShell ISE. Sie sollten die Informationen in diesem Artikel verstanden haben, wenn Sie ein Runbook vom Typ „Text“ erstellen.

Grafische Runbooks ermöglichen Ihnen die Erstellung von Runbooks mit den gleichen Aktivitäten und Cmdlets, aber Sie nutzen eine grafische Oberfläche, bei der die komplexen Zusammenhänge des zugrunde liegenden PowerShell-Workflows verborgen sind. Die Konzepte in diesem Artikel, z. B. Prüfpunkte und parallele Ausführung, gelten auch für grafische Runbooks, aber Sie müssen sich dabei keine Gedanken über die ausführliche Syntax machen.

## Grundlegende Struktur eines Workflows

Der erste Schritt beim Konvertieren eines PowerShell-Skripts in einen PowerShell-Workflow ist das Umschließen mit einem **Workflow**-Schlüsselwort. Ein Workflow beginnt mit dem Schlüsselwort **Workflow**, gefolgt vom Hauptteil des Skripts, der in Klammern gesetzt ist. Auf das Schlüsselwort **Workflow** folgt der Name des Workflows, wie in der folgenden Syntax gezeigt.

    Workflow Test-Workflow
    {
       <Commands>
    }

Der Name des Workflows muss mit dem Namen des Automation-Runbooks übereinstimmen. Wenn das Runbook importiert wird, muss der Dateiname mit dem Workflownamen übereinstimmen und auf .ps1 enden.

Zum Hinzufügen von Parametern für den Workflow verwenden Sie das Schlüsselwort **Param** genauso wie bei einem Skript.

## Änderungen am Code

Der PowerShell-Workflowcode sieht bis auf einige signifikante Änderungen fast genauso wie PowerShell-Skriptcode aus. In den folgenden Abschnitten werden Änderungen beschrieben, die Sie an einem PowerShell-Skript vornehmen müssen, damit es in einem Workflow ausgeführt werden kann.

### Aktivitäten

Eine Aktivität ist eine bestimmte Aufgabe in einem Workflow. Ebenso wie ein Skript aus einem oder mehreren Befehlen zusammengesetzt ist, setzt sich ein Workflow aus einer oder mehreren Aktivitäten zusammen, die in einer bestimmten Reihenfolge ausgeführt werden. Windows PowerShell Workflow konvertiert viele der Windows PowerShell-Cmdlets automatisch in Aktivitäten, wenn ein Workflow ausgeführt wird. Wenn Sie eines dieser Cmdlets in Ihrem Runbook angeben, wird von Windows Workflow Foundation tatsächlich die entsprechende Aktivität ausgeführt. Für Cmdlets ohne entsprechende Aktivität führt Windows PowerShell Workflow das Cmdlet automatisch in einer [InlineScript](#inlinescript)-Aktivität aus. Es gibt einen Satz Cmdlets, der hiervon ausgeschlossen ist und nicht in einem Workflow verwendet werden kann – es sei denn, Sie schließen diese Cmdlets explizit in einen InlineScript-Block ein. Weitere Informationen zu diesen Konzepten finden Sie unter [Verwenden von Aktivitäten in Skriptworkflows](http://technet.microsoft.com/library/jj574194.aspx).

Workflowaktivitäten teilen sich einen Satz allgemeiner Parameter, um ihren Betrieb zu konfigurieren. Ausführliche Informationen zu den allgemeinen Workflowparametern finden Sie unter [about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### Positionsparameter

Sie können Positionsparameter nicht mit Aktivitäten und Cmdlets in einem Workflow verwenden. Dies bedeutet lediglich, dass Sie Parameternamen verwenden müssen.

Betrachten Sie beispielsweise den folgenden Code, mit dem alle ausgeführten Dienste abgerufen werden:

	 Get-Service | Where-Object {$_.Status -eq "Running"}

Wenn Sie versuchen, den gleichen Code in einem Workflow auszuführen, erhalten Sie eine Meldung wie „Der Parametersatz kann mit den angegebenen benannten Parametern nicht aufgelöst werden“. Um dies zu korrigieren, geben Sie einfach den Parameternamen wie folgt an:

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### Deserialisierte Objekte

Objekte in Workflows werden deserialisiert. Dies bedeutet, dass ihre Eigenschaften weiterhin verfügbar sind, aber nicht ihre Methoden. Sehen Sie sich beispielsweise den folgenden PowerShell-Code an, mit dem ein Dienst mit der Stop-Methode des Service-Objekts beendet wird.

	$Service = Get-Service -Name MyService
	$Service.Stop()

Wenn Sie versuchen, dies in einem Workflow auszuführen, erhalten Sie den Fehler „Das Aufrufen von Methoden wird in einem Windows PowerShell-Workflow nicht unterstützt“.

Eine Möglichkeit besteht darin, diese beiden Codezeilen in einen [InlineScript](#InlineScript)-Block einzufügen. In diesem Fall ist $Service ein Dienstobjekt innerhalb des Blocks.

	Workflow Stop-Service
	{
		InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
		}
	} 

Eine weitere Möglichkeit ist die Verwendung eines anderen Cmdlets, mit dem die gleichen Funktionen wie mit der Methode durchgeführt werden (sofern verfügbar). In unserem Beispiel werden mit dem Stop-Service-Cmdlet die gleichen Funktionen wie mit der Stop-Methode bereitgestellt, und Sie können Folgendes für einen Workflow verwenden:

	Workflow Stop-MyService
	{
		$Service = Get-Service -Name MyService
		Stop-Service -Name $Service.Name
	}


## InlineScript

Die**InlineScript**-Aktivität ist nützlich, wenn Sie einen oder mehrere Befehle als herkömmliches PowerShell-Skript ausführen müssen, anstatt als PowerShell-Workflow. Wenngleich die Befehle in einem Workflow zur Verarbeitung an Windows Workflow Foundation gesendet werden, werden die Befehle in einem InlineScript-Block durch Windows PowerShell verarbeitet.

Der InlineScript-Block verwendet die nachstehende Syntax.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Sie können die Ausgabe eines InlineScript-Blocks zurückgeben, indem Sie die Ausgabe einer Variablen zuweisen. Im unten angegebenen Beispiel wird ein Dienst beendet und anschließend der Dienstname ausgegeben.

	Workflow Stop-MyService
	{
		$Output = InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


Sie können Werte in einen InlineScript-Block übergeben, aber Sie müssen den **$Using**-Bereichsmodifizierer verwenden. Das folgende Beispiel ist mit dem vorherigen Beispiel identisch, mit der Ausnahme, dass der Dienstname über eine Variable bereitgestellt wird.

	Workflow Stop-MyService
	{
		$ServiceName = "MyService"
	
		$Output = InlineScript {
			$Service = Get-Service -Name $Using:MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


InlineScript-Aktivitäten können in bestimmten Workflows wichtig sein, aber sie bieten keine Unterstützung für Workflowkonstrukte und sollten nur genutzt werden, wenn dies aus den folgenden Gründen erforderlich ist:

- Sie können keine [Prüfpunkte](#Checkpoints) in einem InlineScript-Block verwenden. Wenn innerhalb des Blocks ein Fehler auftritt, muss der Vorgang am Anfang des Blocks neu gestartet werden.
- Sie können in einem InlineScript-Block keine [parallele Ausführung](#parallel-execution) nutzen.
- InlineScript-Blöcke beeinträchtigen die Skalierbarkeit des Workflows, da die Windows PowerShell-Sitzung für die gesamte Dauer des InlineScript-Blocks übernommen wird.

Ausführliche Informationen zur Verwendung von InlineScript finden Sie unter [Ausführen von Windows PowerShell-Befehlen in einem Workflow](http://technet.microsoft.com/library/jj574197.aspx) und unter [about\_InlinScript](http://technet.microsoft.com/library/jj649082.aspx).


## Parallele Verarbeitung

Ein Vorteil von Windows PowerShell-Workflows besteht darin, dass sie einen Satz an Befehlen parallel – und nicht wie in einem typischen Skript sequenziell – ausführen können.

Sie können mit dem Schlüsselwort **Parallel** einen Skriptblock mit mehreren Befehlen erstellen, die gleichzeitig ausgeführt werden. Diese Methode wird in der nachstehend gezeigten Syntax verwendet. In diesem Fall starten "Activity1" und "Activity2" gleichzeitig. "Activity3" startet erst, wenn sowohl "Activity1" als auch "Activity2" abgeschlossen wurden.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Sehen Sie sich beispielsweise die folgenden PowerShell-Befehle an, mit denen mehrere Dateien an ein Netzwerkziel kopiert werden. Diese Befehle werden nacheinander ausgeführt, sodass der Kopiervorgang einer Datei abgeschlossen sein muss, bevor der nächste Vorgang gestartet wird.

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Im folgenden Workflow werden die gleichen Befehle parallel ausgeführt, sodass die Kopiervorgänge alle gleichzeitig beginnen. Die Abschlussmeldung wird erst angezeigt, nachdem alle Kopiervorgänge abgeschlossen sind.

	Workflow Copy-Files
	{
		Parallel 
		{
			$Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
		}

		Write-Output "Files copied."
	}


Sie können das Konstrukt **ForEach -Parallel** verwenden, um Befehle für jedes Element in einer Auflistung gleichzeitig zu verarbeiten. Die Elemente in der Auflistung werden parallel ausgeführt, während die Befehle im Skriptblock sequenziell ausgeführt werden. Diese Methode wird in der nachstehend gezeigten Syntax verwendet. In diesem Fall startet "Activity1" für alle Elemente in der Auflistung gleichzeitig. "Activity2" wird für alle Elemente gestartet, nachdem "Activity1" abgeschlossen wurde. "Activity3" startet erst, wenn sowohl "Activity1" als auch "Activity2" für alle Elemente abgeschlossen wurden.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Das folgende Beispiel ähnelt dem vorherigen Beispiel mit dem parallelen Kopieren der Dateien. In diesem Fall wird für jede Datei nach Abschluss des Kopiervorgangs eine Meldung angezeigt. Die endgültige Abschlussmeldung wird aber erst angezeigt, nachdem alle Dateien vollständig kopiert wurden.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach -Parallel ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
		}
		
		Write-Output "All files copied."
	}

> [AZURE.NOTE]Wir raten davon ab, untergeordnete Runbooks parallel auszuführen, da dies häufig zu unzuverlässigen Ergebnissen führt. Die Ausgabe des untergeordneten Runbooks wird in einigen Fällen nicht angezeigt, und die Einstellungen in einem untergeordneten Runbook können sich auf andere untergeordnete Runbooks auswirken.


## Prüfpunkte

Ein *Prüfpunkt* ist eine Momentaufnahme des aktuellen Zustands des Workflows, der den aktuellen Wert für Variablen und sämtliche Ausgaben einschließt, die bis zu diesem Punkt generiert wurden. Wenn ein Workflow mit einem Fehler endet oder [angehalten](suspending-a-workflow) wird, wird er bei der nächsten Ausführung ab dem letzten Prüfpunkt gestartet, und nicht ab dem Anfang des Workflows. Sie können mithilfe der Aktivität **Checkpoint-Workflow** einen Prüfpunkt in einem Workflow setzen.

Im folgenden Beispielcode führt eine Ausnahme nach „Activity2“ dazu, dass der Workflow beendet wird. Bei der Fortsetzung der Ausführung wird zunächst „Activity2“ ausgeführt, da diese Aktivität unmittelbar auf den zuletzt gesetzten Prüfpunkt folgt.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Sie können Prüfpunkte in einem Workflow nach Aktivitäten setzen, die möglicherweise anfällig für das Auftreten von Ausnahmen sind und die nach dem Fortsetzen eines Workflows nicht wiederholt werden sollen. Angenommen, Ihr Workflow erstellt einen virtuellen Computer. Sie können sowohl vor als auch nach den Befehlen zum Erstellen des virtuellen Computers einen Prüfpunkt setzen. Wenn bei der Erstellung ein Fehler auftritt, werden die Befehle wiederholt, wenn der Workflow erneut gestartet wird. Falls für den Workflow nach der erfolgreichen Erstellung ein Fehler auftritt, wird der virtuelle Computer nicht erneut erstellt, wenn der Workflow fortgesetzt wird.

Im folgenden Beispiel werden mehrere Dateien an einen Netzwerkspeicherort kopiert, und nach jeder Datei wird ein Prüfpunkt gesetzt. Wenn die Verbindung mit dem Netzwerkspeicherort verloren geht, wird der Workflow mit einem Fehler beendet. Beim erneuten Starten wird der Vorgang beim letzten Prüfpunkt fortgesetzt. Dies bedeutet, dass nur die Dateien übersprungen werden, die bereits kopiert wurden.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
			Checkpoint-Workflow
		}
		
		Write-Output "All files copied."
	}



Weitere Informationen zu Prüfpunkten finden Sie unter [Hinzufügen von Prüfpunkten zu einem Skriptworkflow](http://technet.microsoft.com/library/jj574114.aspx).



## Verwandte Artikel

- [Erste Schritte mit dem Windows PowerShell-Workflow](http://technet.microsoft.com/library/jj134242.aspx) 

<!---HONumber=August15_HO6-->