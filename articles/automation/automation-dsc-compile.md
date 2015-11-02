<properties 
   pageTitle="Kompilieren von Konfigurationen in Azure Automation DSC | Microsoft Azure" 
   description="Übersicht über die beiden Arten der Kompilierung von DSC-Konfigurationen (Desired State Configuration, Konfiguration des gewünschten Zustands): im Azure-Portal und mit Windows PowerShell." 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="10/15/2015"
   ms.author="coreyp"/>
   
#Kompilieren von Konfigurationen in Azure Automation DSC#

Sie können DSC-Konfigurationen (Desired State Configuration, Konfiguration des gewünschten Zustands) in Azure Automation auf zwei Arten kompilieren: im Azure-Portal und mit Windows PowerShell. Die folgenden Informationen helfen bei der Entscheidung, welche Methode sich in welcher Situation am besten eignet:

###Azure-Vorschauportal###
- Einfachste Methode mit interaktiver Benutzeroberfläche
- Formular zum Bereitstellen von einfachen Parameterwerten
- Einfaches Nachverfolgen des Auftragsstatus
- Authentifizierter Zugriff über Azure-Anmeldung

###Windows PowerShell###
- Aufruf über Befehlszeile mit Windows PowerShell-Cmdlets
- Kann in eine automatisierte Lösung mit mehreren Schritten eingebunden werden
- Bereitstellen von einfachen und komplexen Parameterwerten
- Nachverfolgen des Auftragsstatus
- Client erforderlich zur Unterstützung der PowerShell-Befehle
- Übergeben von ConfigurationData-Werten
- Kompilieren von Konfigurationen mit Anmeldeinformationen

Wenn Sie sich für eine Kompilierungsmethode entschieden haben, folgen Sie dem jeweiligen unten beschriebenen Verfahren, um mit der Kompilierung zu beginnen.

##Kompilieren einer DSC-Konfiguration mit dem Azure-Vorschauportal##

1.  Klicken Sie in Ihrem Automation-Konto auf **Konfigurationen**.
2.  Klicken Sie auf eine Konfiguration, um das zugehörige Blatt zu öffnen.
3.  Klicken Sie auf **Kompilieren**.
4.  Wenn die Konfiguration keine Parameter enthält, werden Sie dazu aufgefordert, zu bestätigen, dass Sie die Konfiguration kompilieren möchten. Wenn die Konfiguration Parameter enthält, wird das Blatt **Konfiguration kompilieren** geöffnet, auf dem Sie Parameterwerte angeben können. Weitere Informationen zu Parametern finden Sie im Abschnitt <a href="#basic-parameters">**Grundlegende Parameter**</a> weiter unten.
5.  Das Blatt **Kompilierungsauftrag** wird geöffnet, auf dem Sie den Status des Kompilierungsauftrags sowie die Knotenkonfigurationen (MOF-Konfigurationsdokumente) nachverfolgen können, die durch den Auftrag auf dem Azure Automation DSC-Pullserver platziert werden.

##Kompilieren einer DSC-Konfiguration mit Windows PowerShell##

Sie können [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) verwenden, um mit der Kompilierung mit Windows PowerShell zu beginnen. Der folgende Beispielcode startet die Kompilierung einer DSC-Konfiguration namens **SampleConfig**.

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` gibt ein Kompilierungsauftragsobjekt zurück, das zum Nachverfolgen des Auftragsstatus verwendet werden kann. Sie können dieses Kompilierungsauftragsobjekt mit [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) verwenden, um den Status des Kompilierungsauftrags zu ermitteln. Wenn Sie das Objekt mit [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) verwenden, können Sie die Datenströme (Ausgabe) anzeigen. Der folgende Beispielcode startet die Kompilierung der **SampleConfig**-Konfiguration, wartet, bis die Kompilierung abgeschlossen ist, und zeigt dann die Datenströme an.
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##Grundlegende Parameter##

Die Parameterdeklaration in DSC-Konfigurationen, einschließlich der Parametertypen und -eigenschaften, funktioniert genauso wie in Azure Automation-Runbooks. Informationen zu Runbookparametern finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).

Das folgende Beispiel verwendet zwei Parameter namens **FeatureName** und **IsPresent**, um die Werte der Eigenschaften in der während der Kompilierung generierten **ParametersExample.sample**-Knotenkonfiguration zu ermitteln.

    Configuration ParametersExample {
    	param(
    	[Parameter(Mandatory=$true)]
    
    	[string] $FeatureName,
    
    	[Parameter(Mandatory=$true)]
    	[boolean] $IsPresent
    
    )
    
    $EnsureString = "Present"
    if($IsPresent -eq $false) {
    	$EnsureString = "Absent"
    
    }
    
    Node "sample" {
    
    	WindowsFeature ($FeatureName + "Feature") {
    		Ensure = $EnsureString
    		Name = $FeatureName
    		}
    	}
    }

Sie können DSC-Konfigurationen kompilieren, die grundlegende Parameter im Azure Automation DSC-Portal oder in Azure PowerShell verwenden:

###Portal###

Im Portal können Sie Parameterwerte eingeben, nachdem Sie auf **Kompilieren** geklickt haben.

![alt text](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell erfordert Parameter in einer [Hashtabelle](http://technet.microsoft.com/library/hh847780.aspx), in der der Schlüssel dem Parameternamen entspricht und der Wert gleich dem Parameterwert ist.

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRMAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

Informationen zum Übergeben von PSCredentials als Parameter finden Sie im Abschnitt <a href="#credential-assets">**Anmeldeinformationen**</a> weiter unten.

##ConfigurationData##

Mit **ConfigurationData** können Sie bei Verwendung von PowerShell DSC die Konfiguration der Struktur von jeglicher umgebungsspezifischer Konfiguration trennen. Informationen zu **ConfigurationData** finden Sie unter [Separating "What" from "Where" in PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx).

>[AZURE.NOTE]Sie können **ConfigurationData** während der Kompilierung in Azure Automation DSC mithilfe von Azure PowerShell verwenden, nicht jedoch im Azure-Portal.

Die folgende DSC-Beispielkonfiguration verwendet **ConfigurationData** über die Schlüsselwörter **$ConfigurationData** und **$AllNodes**. Für dieses Beispiel benötigen Sie außerdem das [Modul **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

     Configuration ConfigurationDataSample {
    	Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    
    	Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 
    
    	Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    	{
    
    		xWebsite Site
    		{
    
    			Name = $Node.SiteName
    			PhysicalPath = $Node.SiteContents
    			Ensure   = "Present"
    		}
    
    	}
 
    }

Sie können die oben stehende DSC-Konfiguration mit PowerShell kompilieren. Hierbei werden dem Azure Automation DSC-Pullserver die beiden Knotenkonfigurationen **ConfigurationDataSample.MyVM1** und **ConfigurationDataSample.MyVM3** hinzugefügt.

    $ConfigData = @{
    	AllNodes = @(
			@{
    			NodeName = "MyVM1"
    			Role = "WebServer"
    		},
    		@{
    			NodeName = "MyVM2"
    			Role = "SQLServer"
    		},
    		@{
    			NodeName = "MyVM3"
    			Role = "WebServer"
    
    		}
    
    	)
    
    	NonNodeData = @{
    		SomeMessage = "I love Azure Automation DSC!"
    
    	}
    
    } 
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


##Objekte##

Objektverweise in Azure Automation DSC-Konfigurationen und -Runbooks sind gleich. Weitere Informationen finden Sie unter den folgenden Links:

- [Zertifikate](automation-certificates.md)
- [Verbindungen](automation-connections.md)
- [Anmeldeinformationen](automation-credentials.md)
- [Variablen](automation-variables.md)

###Anmeldeinformationen###
DSC-Konfigurationen in Azure Automation können mithilfe von **Get-AutomationPSCredential** auf Anmeldeinformationen verweisen. Diese Informationen können aber bei Bedarf auch über Parameter übergeben werden. Wenn in einer Konfiguration ein Parameter des Typs **PSCredential** vorhanden ist, müssen Sie den Zeichenfolgennamen eines Anmeldeinformationsobjekts von Azure Automation als Wert dieses Parameters übergeben, keine PSCredential-Objekt. Im Hintergrund wird das Azure Automation-Anmeldeinformationsobjekt mit diesem Namen abgerufen und an die Konfiguration übergeben.

Um die Sicherheit von Anmeldeinformationen in Knotenkonfigurationen (MOF-Konfigurationsdokumente) zu gewährleisten, müssen diese Informationen in der MOF-Datei der Knotenkonfiguration verschlüsselt werden. Azure Automation geht sogar noch einen Schritt weiter und verschlüsselt die gesamte MOF-Datei. Zurzeit müssen Sie PowerShell DSC jedoch noch darüber informieren, dass die Anmeldeinformationen während der Generierung der Knotenkonfiguration-MOF-Datei im Klartext übergeben werden. PowerShell DSC hat keinerlei Informationen darüber, dass Azure Automation die gesamte MOF-Datei nach dem Generieren über einen Kompilierungsauftrag verschlüsselt.

Verwenden Sie <a href="#configurationdata">**ConfigurationData**</a>, um PowerShell DSC mitzuteilen, dass die Anmeldeinformationen als Klartext übergeben werden dürfen. Übergeben Sie `PSDscAllowPlainTextPassword = $true` über **ConfigurationData** für den Namen jedes Knotenblocks, der in der DSC-Konfiguration angezeigt wird und Anmeldeinformationen verwendet.

Das folgende Beispiel zeigt eine DSC-Konfiguration, die ein Automation-Anmeldeinformationsobjekt verwendet.

    Configuration CredentialSample {
    
       $Cred = Get-AutomationPSCredential -Name "SomeCredentialAsset"
    
    	Node $AllNodes.NodeName { 
    
    		File ExampleFile { 
    			SourcePath = "\\Server\share\path\file.ext" 
    			DestinationPath = "C:\destinationPath" 
    			Credential = $Cred 
    
       		}
    
    	}
    
    }

Sie können die oben stehende DSC-Konfiguration mit PowerShell kompilieren. Hierbei werden dem Azure Automation DSC-Pullserver die beiden Knotenkonfigurationen **CredentialSample.MyVM1** und **CredentialSample.MyVM2** hinzugefügt.


    $ConfigData = @{
    	AllNodes = @(
    		 @{
    			NodeName = "*"
    			PSDscAllowPlainTextPassword = $True
    		},
    
    		@{
    			NodeName = "MyVM1"
    		},
    
    		@{
    			NodeName = "MyVM2"
    		}
    	)
    } 
    
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData

<!---HONumber=Oct15_HO4-->