<properties
   pageTitle="Azure Automation DSC – Fortlaufende Bereitstellung mit Chocolatey | Microsoft Azure"
   description="Fortlaufende DevOps-Bereitstellung per Azure Automation DSC und Chocolatey-Paket-Manager. Beispiel mit vollständiger JSON-ARM-Vorlage und PowerShell-Quelle."
   services="automation"
   documentationCenter=""
   authors="sebastus"
   manager="stevenka"
   editor=""/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="golive"/>

# Kontinuierliche Bereitstellung für IaaS-VMs mit Azure Automation DSC und Chocolatey

In einer DevOps-Welt gibt es viele Tools, die an den verschiedenen Punkten der Continuous Integration-Pipeline als Hilfe dienen. Azure Automation Desired State Configuration (DSC), also die Konfiguration des gewünschten Zustands, ist eine willkommene neue Option für DevOps-Teams. In diesem Artikel wird das Einrichten der fortlaufenden Bereitstellung (Continuous Deployment, CD) für einen Windows-Computer veranschaulicht. Sie können das Verfahren leicht so erweitern, dass in der Rolle (z. B. einer Website) so viele Windows-Computer wie nötig enthalten sind, und von diesem Punkt aus noch eine Erweiterung auf zusätzliche Rollen durchführen.

Meta: Dieser Artikel wurde im September 2015 verfasst. Die Version von Visual Studio lautet 2015. [PowerShell-Tools für Visual Studio](http://adamdriscoll.github.io/poshtools/) ist installiert und vereinfacht das Arbeiten mit PowerShell, indem die Syntaxhervorhebung und Intellisense hinzugefügt werden. Derzeit befindet sich Azure Automation DSC in der öffentlichen Vorschauphase, sodass auf dem Weg zur allgemeinen Verfügbarkeit ggf. noch Änderungen vorgenommen werden. Den [vollständigen Quellcode](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) für dieses Beispiel finden Sie bei GitHub.

![Kontinuierliche Bereitstellung für virtuelle IaaS-Computer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## Allgemeines

Dieser Bereich ist recht komplex, aber glücklicherweise lässt er sich in zwei Hauptprozesse unterteilen:

  - Das Schreiben und Testen von Code und das anschließende Erstellen und Veröffentlichen von Installationspaketen für größere und kleinere Versionen des Systems. 
  - Das Erstellen und Verwalten von virtuellen Computern, mit denen der Code in den Paketen installiert und ausgeführt wird.  

Nachdem diese beiden Kernprozesse eingerichtet wurden, ist es nur noch ein kleiner Schritt zum automatischen Aktualisieren des Pakets, das auf einem bestimmten virtuellen Computer ausgeführt wird, wenn neue Versionen erstellt und bereitgestellt werden.

## Übersicht über die Komponenten

Paket-Manager, z. B. [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool), sind in der Linux-Welt recht bekannt, aber dies gilt nicht so sehr für die Windows-Welt. [Chocolatey](https://chocolatey.org/) ist hierfür ein Beispiel, und der [Blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) von Scott Hanselman zu diesem Thema stellt eine gute Einführung dar. Zusammenfassend lässt sich sagen, dass Sie mit Chocolatey Pakete über die Befehlszeile aus einem zentralen Paketrepository auf einem Windows-System installieren können. Sie können ein eigenes Repository erstellen und verwalten, und Chocolatey kann Pakete aus allen Repositorys installieren, die Sie angeben.

Desired State Configuration (DSC) ([Übersicht](https://technet.microsoft.com/de-DE/library/dn249912.aspx)) ist ein PowerShell-Tool, mit dem Sie die gewünschte Konfiguration für einen Computer deklarieren können. Beispielsweise können Sie angeben, dass Sie „Chocolatey installieren, IIS installieren, Port 80 öffnen und Version 1.0.0 Ihrer Website installieren“ möchten. Diese Konfiguration wird dann mit dem lokalen Konfigurations-Manager (LCM) für DSC implementiert. Ein DSC-Pullserver enthält ein Repository mit Konfigurationen für Ihre Computer. Der LCM jedes Computers checkt sich regelmäßig ein, um zu überprüfen, ob die Konfiguration mit der gespeicherten Konfiguration übereinstimmt. Es kann entweder der Status gemeldet werden, oder es kann versucht werden, den Computer wieder auf den Stand der gespeicherten Konfiguration zu bringen. Sie können die gespeicherte Konfiguration auf dem Pullserver bearbeiten, um einen Computer oder eine Gruppe von Computern auf den Stand der geänderten Konfiguration zu bringen.

Azure Automation ist ein verwalteter Dienst in Microsoft Azure, der Ihnen die Automatisierung verschiedener Aufgaben ermöglicht, indem Sie Runbooks und andere Assets wie Knoten, Anmeldeinformationen und Ressourcen verwenden. Azure Automation DSC erweitert diese Automatisierungsfunktion auf PowerShell DSC-Tools. Dies ist eine gute [Übersicht](automation-dsc-overview.md).

Eine DSC-Ressource ist ein Codemodul mit speziellen Funktionen, z. B. zur Verwaltung von Netzwerken, Active Directory oder SQL Server. Die Chocolatey DSC-Ressource kann zum Zugreifen auf einen NuGet-Server (unter anderem), Herunterladen von Paketen, Installieren von Paketen usw. verwendet werden. Der [PowerShell-Katalog](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) enthält noch viele weitere DSC-Ressourcen. Diese Module wurden (von Ihnen) auf Ihrem Azure Automation DSC-Pullserver installiert, damit sie für die Konfigurationen verwendet werden können.

ARM-Vorlagen stellen einen deklarativen Weg zum Generieren Ihrer Infrastruktur dar: Netzwerke, Subnetze, Netzwerksicherheit und -routing, Load Balancer, Netzwerkschnittstellenkarten, virtuelle Computer usw. In [diesem Artikel](../resource-manager-deployment-model.md) wird das ARM-Bereitstellungsmodell (deklarativ) mit dem (imperativen) Azure Service Management-Bereitstellungsmodell (ASM oder klassisch) verglichen. Und in [diesem Artikel](../virtual-machines\virtual-machines-azurerm-versus-azuresm.md) geht es um die Bereiche Coreressourcenanbieter, Compute, Speicher und Netzwerk.

Ein wichtiges Feature einer ARM-Vorlage ist die Möglichkeit, bei der Bereitstellung eine VM-Erweiterung auf einem virtuellen Computer zu installieren. Eine VM-Erweiterung verfügt über bestimmte Funktionen, z. B. das Ausführen eines benutzerdefinierten Skripts, Installieren einer Antivirensoftware oder Ausführen eines DSC-Konfigurationsskripts. Es gibt noch viele andere Arten von VM-Erweiterungen.

## Kurzvorstellung des Diagramms

Sie beginnen oben und schreiben Ihren Code, führen die Erstellung und das Testen durch und erstellen dann ein Installationspaket. Chocolatey kann verschiedene Arten von Installationspaketen verarbeiten, z. B. MSI, MSU, ZIP. Und Sie verfügen über die volle Leistungsfähigkeit von PowerShell zum Durchführen der eigentlichen Installation, falls die systemeigenen Funktionen von Chocolatey nicht ausreichen sollten. Legen Sie das Paket an einem erreichbaren Ort ab, z. B. in einem Paketrepository. Ich selbst verwende einen öffentlichen Ordner in meinem Azure-Blob-Speicherkonto, aber er kann sich an jedem beliebigen Ort befinden. Chocolatey arbeitet standardmäßig mit NuGet-Servern und einigen anderen zusammen, was die Verwaltung der Paketmetadaten betrifft. Die Optionen werden in [diesem Artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) beschrieben. In diesem Beispiel wird NuGet verwendet. Bei „Nuspec“ handelt es sich um Metadaten zu Ihren Paketen. Nuspec-Elemente werden in NuPkg-Element „kompiliert“ und auf einem NuGet-Server gespeichert. Wenn Ihre Konfiguration ein Paket dem Namen nach anfordert und auf einen NuGet-Server verweist, verwendet die Chocolatey DSC-Ressource (die sich jetzt auf dem virtuellen Computer befindet) das Paket und installiert es für Sie. Sie können auch eine bestimmte Version eines Pakets anfordern.

Im unteren linken Bereich des Bilds befindet sich eine Azure-Ressourcen-Manager-Vorlage. In diesem Beispiel wird der virtuelle Computer von der VM-Erweiterung für den Azure Automation DSC-Pullserver (also von einem Pullserver) als Knoten registriert. Die Konfiguration wird auf dem Pullserver gespeichert. Eigentlich wird sie zweimal gespeichert: einmal in reiner Textform und einmal kompiliert als MOF-Datei (als Hinweis für Benutzer, die sich hiermit auskennen). Im Portal ist die MOF-Datei eine „Knotenkonfiguration“ (im Gegensatz zu einer normalen „Konfiguration“). Es ist das Artefakt, das einem Knoten zugeordnet ist, damit dem Knoten die Konfiguration bekannt ist. Die Details unten verdeutlichen, wie Sie die Knotenkonfiguration dem Knoten zuweisen.

Wahrscheinlich erledigen Sie dies, bzw. den größten Teil davon, bereits im oberen Bereich. Das Erstellen von Nuspec und das Kompilieren und Speichern auf einem NuGet-Server bedeutet keinen großen Aufwand. Und Sie verwalten bereits virtuelle Computer. Der nächste Schritte der fortlaufenden Bereitstellung erfordert das Einrichten des Pullservers (einmalig), das Registrieren Ihrer Knoten dafür (einmalig) und das Erstellen und Speichern der Konfiguration darunter (am Anfang). Wenn die Pakete dann aktualisiert und im Repository bereitgestellt werden, aktualisieren Sie die Konfiguration auf dem Pullserver (nach Bedarf wiederholen).

Falls Sie nicht mit einer ARM-Vorlage beginnen, ist dies auch in Ordnung. Es sind PowerShell-Cmdlets vorhanden, mit denen Sie Ihre virtuellen Computer für den Pullserver und den anderen Komponenten registrieren können.


## Schritt 1: Einrichten des Pullservers und des Automatisierungskontos

An einer authentifizierten (Add-AzureAccount) PowerShell-Befehlszeile: (es kann einige Minuten dauern, bis der Pullserver eingerichtet ist)

    Switch-AzureMode -Name AzureResourceManager 
    Register-AzureProvider –ProviderNamespace Microsoft.Automation 
    Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation 
    New-AzureResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Sie können Ihr Automatisierungskonto in einer der folgenden Regionen anordnen: Japan, Osten, USA (Ost) 2, Westeuropa, Südostasien, USA (Mitte/Süden). Sie sollten natürlich darauf achten, dass sich Ihre Automatisierungsressourcengruppe in derselben Region wie Ihr Automatisierungskonto befindet.

## Schritt 2: Optimierungen der VM-Erweiterung für die ARM-Vorlage

Details zur VM-Registrierung (mit der PowerShell DSC-VM-Erweiterung) finden Sie in dieser [Azure-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver). Bei diesem Schritt wird Ihr neuer virtueller Computer beim Pullserver in der Liste der DSC-Knoten registriert.

## Schritt 3: Hinzufügen von erforderlichen DSC-Ressourcen zum Pullserver

Der PowerShell-Katalog wird genutzt, um DSC-Ressourcen in Ihrem Azure Automation-Konto zu installieren. Navigieren Sie zur gewünschten Ressource, und klicken Sie auf die Schaltfläche „In Azure Automation bereitstellen“.

![PowerShell-Katalog-Beispiel](./media/automation-dsc-cd-chocolatey/cchoco.png)

Es gibt auch eine manuelle Vorgehensweise. Die Ordnerstruktur eines PowerShell-Integrationsmoduls für einen Windows-Computer unterscheidet sich etwas von der Ordnerstruktur, die vom Azure Automation DSC-Pullserver erwartet wird. Hierfür ist es erforderlich, dass Sie eine kleine Optimierung vornehmen. Dies ist aber nicht schwierig und muss nur einmal pro Ressource durchgeführt werden (es sei denn, Sie möchten später ein Upgrade durchführen).

-   Installieren Sie das Modul, das Sie auf Ihrer Arbeitsstation benötigen, wie folgt:
    -   Installieren Sie [Windows Management Framework, v5](http://aka.ms/wmf5latest). 
    -   `Install-Module  –ModuleName MODULENAME` <—Holt sich das Modul aus dem PowerShell-Katalog. 
-   Kopieren Sie den Modulordner unter `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` in einen temporären Ordner. 
-   Löschen Sie Beispiele und Dokumentation aus dem Hauptordner. 
-   Zippen Sie den Hauptordner, und geben Sie der ZIP-Datei genau den gleichen Namen des Ordners. 
-   Legen Sie die ZIP-Datei an einem erreichbaren HTTP-Speicherort ab. 
-   Führen Sie Folgendes in PowerShell aus:

        New-AzureAutomationModule ``
            -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ``
            -Name MODULE-NAME –ContentLink "https://STORAGE-URI/public/MODULE-NAME.zip"
        

Im beigefügten Beispiel werden diese Schritte für cChoco und xNetworking ausgeführt.

## Schritt 4: Hinzufügen der Knotenkonfiguration zum Pullserver

Es ist nichts Besonderes zu beachten, wenn Sie Ihre Konfiguration zum ersten Mal in den Pullserver importieren und die Kompilierung durchführen. Alle nachfolgenden Import- und Kompiliervorgänge einer Konfiguration sehen dann genau gleich aus. Bei jeder Aktualisierung des Pakets und der Umstellung auf die Produktion führen Sie diesen Schritt aus, nachdem Sie sichergestellt haben, dass die Konfigurationsdatei korrekt ist – einschließlich der neuen Version Ihres Pakets. Hier sind die Konfigurationsdatei und die PowerShell-Daten angegeben:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking
    
        Node "isvbox" {   <— node must be named, $AllNodes.NodeName won’t work.
    
            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }
    
            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }
    
            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }
    
            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

    Import-AzureAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force
    
    $jobData = Start-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 
    
    $compilationJobId = $jobData.Id
    
    Get-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Diese Schritte führen zu einer neuen Knotenkonfiguration mit dem Namen „ISVBoxConfig.isvbox“. Der Name wird in der Form „configurationName.nodeName“ erstellt.

## Schritt 5: Zuweisen des Knotenkonfiguration zum Knoten

Für diesen Schritt müssen Sie den Pullserver abfragen, um die ID des neuen virtuellen Computers abzurufen, nachdem er registriert wurde. Das Cmdlet hierfür ist Get-AzureAutomationDscNode. Es verfügt über einige Filter, die ggf. funktionieren, aber normalerweise lassen Sie die ausgegebenen Daten hierbei durch einen Filter laufen, bei dem virtuelle Computer mit einem Namensmuster erfasst werden. Wenn Sie nur virtuelle Computer einer einzelnen Rolle erstellen, können Sie alle Elemente erfassen, für die NodeConfigurationName „ConfigureLCMforAAPull.isvbox“ lautet. Dies ist der Konfigurationsname jedes neu registrierten virtuellen Computers, wenn Sie dieses Beispiel zugrunde legen. Wenn Sie über die Liste mit den IDs verfügen, ist dies das Cmdlet dazu:

    Set-AzureAutomationDscNode ` 
        -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -NodeConfigurationName ISVBoxConfig.isvbox -Id $theId

## Schritt 6: Wohin mit all den PowerShell-Daten?

Wenn Sie benutzerdefinierte ARM-Vorlagen schreiben, können Sie ein von Visual Studio bereitgestelltes PowerShell-Skript nutzen, mit dem die Vorlage aufgerufen wird. Die Cmdlets, mit denen Ihre Konfiguration importiert wird, die Kompilierung durchgeführt wird, die IDs Ihrer virtuellen Computer abgerufen werden usw., können an dieses Skript angefügt werden. Bei GitHub finden Sie die Quelldateien zu den Beispielen. Integrieren Sie für die Teile, die bei jeder Aktualisierung Ihres Pakets ausgeführt werden, die erforderlichen Befehle in die Continuous Integration-Pipeline.

## Schritt 7: Erstellen und Verwalten von Paketmetadaten

Für jedes Paket, das Sie im Paketrepository ablegen, benötigen Sie ein Nuspec-Element, mit dem das Paket beschrieben wird. Dieses Nuspec-Element muss auf Ihrem NuGet-Server kompiliert und gespeichert werden. Dieser Prozess ist [hier](http://docs.nuget.org/create/creating-and-publishing-a-package) beschrieben. Sie können „MyGet.org“ als NuGet-Server verwenden. Dieser Dienst wird von dem Unternehmen zum Kauf angeboten, aber es ist auch eine kostenlose Start-SKU vorhanden. Unter „NuGet.org“ finden Sie eine Anleitung zum Installieren Ihres eigenen NuGet-Servers für Ihre privaten Pakete.

## Hinweise

Dieses Beispiel beginnt mit einem virtuellen Computer aus einem generischen Windows 2012 R2-Image aus dem Azure-Katalog. Sie können es über ein beliebiges gespeichertes Image starten und dann mit der Optimierung mithilfe der DSC-Konfiguration beginnen.

Sie müssen keine ARM-Vorlage und dann die VM-Erweiterung verwenden, um dieses Verfahren mit Ihren virtuellen Computern zu nutzen. Und Ihre virtuellen Computer müssen nicht unter Azure vorhanden sein, um in den Genuss der CD-Verwaltung zu kommen. Lediglich Chocolatey muss installiert sein, und der lokale Konfigurations-Manager (LCM) muss für den virtuellen Computer konfiguriert werden, damit er informiert ist, wo sich der Pullserver befindet.

Wenn Sie ein Paket auf einem virtuellen Computer aktualisieren, der sich in der Produktion befindet, müssen Sie diesen virtuellen Computer natürlich aus der Rotation nehmen, während das Update installiert wird. Die Vorgehensweise kann dabei stark variieren. Beispielsweise können Sie bei einem virtuellen Computer hinter einem Azure Load Balancer einen benutzerdefinierten Test hinzufügen. Richten Sie dies so ein, dass der Testendpunkt während der Aktualisierung des virtuellen Computers eine „400“ zurückgibt. Die erforderliche Optimierung für diese Änderung kann sich innerhalb Ihrer Konfiguration befinden. Dies gilt auch für den Wechsel zurück zur Rückgabe einer „200“, nachdem das Update abgeschlossen ist.

Den vollständigen Quellcode für dieses Beispiel finden Sie in [diesem Visual Studio-Projekt](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) bei GitHub.

<!---HONumber=Oct15_HO3-->