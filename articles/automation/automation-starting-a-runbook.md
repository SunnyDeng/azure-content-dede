<properties 
   pageTitle="Starten eines Runbooks in Azure Automation"
   description="Bietet eine Übersicht über die verschiedenen Methoden, die zum Starten eines Runbooks in Azure Automation verwendet werden können, und stellt Informationen zur Verwendung des Azure-Portals sowie von Windows PowerShell bereit."
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
   ms.date="09/28/2015"
   ms.author="bwren;sngun"/>

# Starten eines Runbooks in Azure Automation

Die folgende Tabelle hilft Ihnen dabei herauszufinden, welche Methode zum Starten eines Runbooks in Azure Automation sich am besten für Ihr jeweiliges Szenario eignet. Dieser Artikel enthält Details zum Starten eines Runbooks über das Azure-Portal oder mit Windows PowerShell. Details zu den anderen Methoden werden in anderen Artikeln bereitgestellt, auf die Sie über unten stehende Links zugreifen können.

<table>
 <tr>
  <td>METHODE</td>
  <td>MERKMALE</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Azure-Portal</a></td>
  <td>
   <ul>
    <li>Einfachste Methode mit interaktiver Benutzeroberfläche.</li>
    <li>Formular zum Bereitstellen von einfachen Parameterwerten.</li>
    <li>Einfaches Nachverfolgen des Auftragsstatus.</li>
    <li>Authentifizierter Zugriff über Azure-Anmeldung.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>Aufruf über Befehlszeile mit Windows PowerShell-Cmdlets.</li>
     <li>Kann in eine automatisierte Lösung mit mehreren Schritten eingebunden werden.</li>
     <li>Anforderung wird über Zertifikat oder OAuth-Benutzerprinzipal/-Dienstprinzipal authentifiziert.</li>
     <li>Bereitstellen von einfachen und komplexen Parameterwerten.</li>
     <li>Nachverfolgen des Auftragsstatus.</li>
     <li>Client erforderlich zur Unterstützung der PowerShell-Befehle.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">Azure Automation-API</a></td>
  <td>
   <ul>
    <li>Flexibelste Methode, jedoch auch sehr komplex.</li>
    <li>Aufruf aus jedem benutzerdefinierten Code, der HTTP-Anforderungen ausführen kann.</li>
    <li>Anforderung wird über Zertifikat oder OAuth-Benutzerprinzipal/-Dienstprinzipal authentifiziert.</li>
    <li>Bereitstellen von einfachen und komplexen Parameterwerten.</li>
    <li>Nachverfolgen des Auftragsstatus.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>Starten eines Runbooks über eine einzelne HTTP-Anforderung.</li>
    <li>Authentifizierung über Sicherheitstoken in der URL.</li>
    <li>Client kann keine Parameterwerte überschreiben, die beim Erstellen des Webhooks festgelegt wurden. Ein Runbook kann einen einzelnen Parameter definieren, der mit den Details zur HTTP-Anforderung aufgefüllt wird.</li>
    <li>Keine Möglichkeit, den Auftragsstatus über die Webhook-URL nachzuverfolgen.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Reagieren auf eine Azure-Warnung</a></td>
  <td>
   <ul>
    <li>Starten Sie ein Runbook als Reaktion auf eine Azure-Warnung.</li>
    <li>Konfigurieren eines Webhooks für Runbook und Link zum Warnen.</li>
    <li>Authentifizierung über Sicherheitstoken in der URL.</li>
    <li>Unterstützt derzeit nur Warnungen für Metriken.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">Zeitplan</a></td>
  <td>
   <ul>
    <li>Automatisches Starten eines Runbooks: stündlich, täglich oder wöchentlich.</li>
    <li>Bearbeiten des Zeitplans über das Azure-Portal, PowerShell-Cmdlets oder die Azure-API.</li>
    <li>Bereitstellen von Parameterwerten, die mit dem Zeitplan verwendet werden sollen.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/dn857355.aspx">Über ein anderes Runbook</a></td>
  <td>
   <ul>
    <li>Verwenden eines Runbooks als Aktivität in einem anderen Runbook.</li>
    <li>Nützlich bei Funktionen, die von mehreren Runbooks verwendet werden.</li>
    <li>Bereitstellen von Parameterwerten für untergeordnete Runbooks und Verwenden der Ausgabe im übergeordneten Runbook.</li>
   </ul>
  </td>
 </tr>
</table>
<br>



## Starten eines Runbooks über das Azure-Portal

1. Wählen Sie im Azure-Portal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
1. Wählen Sie die Registerkarte **Runbooks**.
1. Wählen Sie ein Runbook aus, und klicken Sie auf **Starten**.
1. Wenn das Runbook Parameter enthält, werden Sie aufgefordert, für jeden Parameter einen Wert in ein Textfeld einzugeben. Weitere Informationen zu Parametern finden Sie im Abschnitt [Runbookparameter](#Runbook-parameters) weiter unten.
1. Wählen Sie **Auftrag anzeigen** neben der Runbookmeldung **Wird gestartet** oder wählen Sie die Registerkarte **Aufträge** für das Runbook, für das Sie den Auftragsstatus anzeigen möchten.

## Starten eines Runbooks über das Azure-Vorschauportal

1. Klicken Sie in Ihrem Automation-Konto auf **Runbooks**, um das Blatt **Runbooks** zu öffnen.
1. Klicken Sie auf ein Runbook, um das zugehörige **Runbook**-Blatt zu öffnen.
2. Klicken Sie auf **Start**.
1. Wenn das Runbook keine Parameter enthält, werden Sie dazu aufgefordert, zu bestätigen, dass Sie es starten möchten. Wenn das Runbook Parameter enthält, wird das Blatt **Runbook starten** geöffnet, auf dem Sie Parameterwerte bereitstellen können. Weitere Informationen zu Parametern finden Sie im Abschnitt [Runbookparameter](#Runbook-parameters) weiter unten.
3. Das Blatt **Auftrag** wird geöffnet, auf dem Sie den Auftragsstatus nachverfolgen können.


## Starten eines Runbooks mit Windows PowerShell

Sie können das Cmdlet [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) verwenden, um ein Runbook mit Windows PowerShell zu starten. Der folgende Beispielcode startet ein Runbook namens "Test-Runbook".

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook gibt ein Auftragsobjekt zurück, mit dem Sie den Status nachverfolgen können, sobald das Runbook gestartet wurde. Anschließend können Sie dieses Auftragsobjekt zum Ermitteln des Auftragsstatus mit [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) und zum Abrufen der Ausgabe mit [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) verwenden. Der folgende Beispielcode startet ein Runbook namens "Test-Runbook", wartet, bis die Ausführung beendet ist, und zeigt anschließend die Runbookausgabe an.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Wenn das Runbook Parameter erfordert, müssen Sie diese als [Hashtabelle](http://technet.microsoft.com/library/hh847780.aspx) bereitstellen, wobei der Schlüssel der Hashtabelle dem Parameternamen und der Wert dem Parameterwert entspricht. Das folgende Beispiel zeigt das Starten eines Runbooks mit zwei Zeichenfolgenparametern "FirstName" und "LastName", einem ganzzahligen Wert namens "RepeatCount" und einem booleschen Parameter namens "Show". Weitere Informationen zu Parametern finden Sie im Abschnitt [Runbookparameter](#Runbook-parameters) weiter unten.

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Runbookparameter

Wenn Sie ein Runbook über das Azure-Verwaltungsportal oder mit Windows PowerShell starten, wird die Anweisung über den Azure Automation-Webdienst gesendet. Dieser Dienst unterstützt keine Parameter mit komplexen Datentypen. Wenn Sie einen Wert für einen komplexen Parameter bereitstellen müssen, müssen Sie diesen inline aus einem anderen Runbook aufrufen, wie im Artikel [Starten eines Runbooks aus einem anderen Runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx)beschrieben.

Der Azure Automation-Webdienst bietet spezielle Funktionen für Parameter, die bestimmte Datentypen verwenden, wie in den folgenden Abschnitten beschrieben.

### Benannte Werte

Wenn der Parameter den Datentyp [object] enthält, können Sie das folgende JSON-Format verwenden, um eine Liste benannter Werte an den Parameter zu senden: *{"Name1":Wert1, "Name2":Wert2, "Name3":Wert3}*. Bei den Werten muss es sich um einfache Typen handeln. Das Runbook empfängt den Parameter als [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) mit Eigenschaften, die den einzelnen benannten Werten entsprechen.

Betrachten Sie das folgende Testrunbook, das einen Parameter namens "user" akzeptiert.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][object]$user
	   )
	    if ($user.Show) {
	        foreach ($i in 1..$user.RepeatCount) {
	            $user.FirstName
	            $user.LastName
	        }
	    } 
	}

Für den Parameter "user" könnte folgender Text verwendet werden.

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

Dadurch wird die folgende Ausgabe zurückgegeben.

	Joe
	Smith
	Joe
	Smith

### Arrays

Wenn es sich bei dem Parameter um ein Array wie beispielsweise [array] oder [string] handelt, können Sie das folgende JSON-Format verwenden, um eine Liste von Werten an den Parameter zu senden: *[Wert1,Wert2,Wert3]*. Bei den Werten muss es sich um einfache Typen handeln.

Betrachten Sie das folgende Testrunbook, das einen Parameter namens *user* akzeptiert.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][array]$user
	   )
	    if ($user[3]) {
	        foreach ($i in 1..$user[2]) {
	            $ user[0]
	            $ user[1]
	        }
	    } 
	}

Für den Parameter "user" könnte folgender Text verwendet werden.

	["Joe","Smith",2,true]

Dadurch wird die folgende Ausgabe zurückgegeben.

	Joe
	Smith
	Joe
	Smith

### Anmeldeinformationen

Wenn der Parameter den Datentyp **PSCredential** aufweist, können Sie den Namen eines Azure Automation-[Anmeldeinformationsobjekts](http://msdn.microsoft.com/library/azure/dn940015.aspx) bereitstellen. Das Runbook ruft die Anmeldeinformationen mit dem von Ihnen angegebenen Namen ab.

Betrachten Sie das folgende Testrunbook, das einen Parameter namens "credential" akzeptiert.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

Für den Parameter "user" kann der folgende Text verwendet werden, vorausgesetzt, es ist ein Anmeldeinformationsobjekt namens *My Credential* vorhanden.

	My Credential

Wenn der Benutzernamen in den Anmeldeinformationen *jsmith* lautet, erhalten Sie folgende Ausgabe.

	jsmith

## Verwandte Artikel

- [Starten eines Runbooks aus einem anderen Runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx) 

<!---HONumber=Oct15_HO1-->