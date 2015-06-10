<properties 
   pageTitle="Azure Automation DSC – Übersicht" 
   description="Eine Übersicht über Azure Automation Desired State Configuration (DSC), die verwendeten Begriffe und bekannte Fehler." 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="05/04/2015"
   ms.author="coreyp"/>

# Azure Automation DSC – Übersicht #

>[AZURE.IMPORTANT]**Azure Automation DSC befindet sich derzeit in der eingeschränkten Vorschau** und wird für Produktionsworkloads nicht unterstützt. Es ist vorläufig ausschließlich Cmdlet-basiert und weist keine grafische Benutzeroberfläche auf. Durch Ihre Registrierung für die Azure Automation DSC-Vorschau erkennen Sie an, dass dieses Feature sich in der Vorschau befindet und eingeschränkten oder anderen Dienstbedingungen unterliegt als denjenigen in der [Servicevereinbarung](https://go.microsoft.com/fwLink/p/?LinkID=389530&clcid=0x409). Gleichzeitig stimmen Sie den [Zusatzbedingungen für die Vorschau](https://go.microsoft.com/fwLink/p/?LinkID=247638&clcid=0x409) zu. Der Dienst wird zurzeit kostenlos, in Zukunft jedoch als kostenpflichtiger Dienst angeboten werden.

## Was ist PowerShell DSC? ##
Desired State Configuration (DSC) ist eine neue Verwaltungsplattform in Windows PowerShell, die eine Konfigurationsverwaltung für physische Hosts und virtuelle Computer über eine deklarative PowerShell-Syntax ermöglicht.

DSC stellt einen Satz an Windows PowerShell-Spracherweiterungen, neue Windows PowerShell-Cmdlets und Ressourcen bereit, mit deren Hilfe Sie Ihre Softwareumgebung deklarativ konfigurieren können. Darüber hinaus ist es möglich, vorhandene Konfigurationen zu warten und zu verwalten.

### Praktische Anwendung ###
Nachfolgend werden einige Beispielszenarien gezeigt, in denen Sie integrierte DSC-Ressourcen zum automatisierten Konfigurieren und Verwalten einer Gruppe von Computern (auch Zielknoten genannt) verwenden können:

- Aktivieren oder Deaktivieren von Serverrollen und -features
- Verwalten von Registrierungseinstellungen
- Verwalten von Dateien und Verzeichnissen
- Starten, Beenden und Verwalten von Prozessen und Diensten
- Verwalten von Gruppen und Benutzerkonten
- Bereitstellen neuer Software
- Verwalten von Umgebungsvariablen
- Ausführen von Windows PowerShell-Skripts
- Korrigieren einer Konfiguration, die vom gewünschten Zustand abweicht
- Ermitteln des aktuellen Konfigurationsstatus auf einem vorgegebenen Knoten 

Zusätzlich können Sie benutzerdefinierte Ressourcen erstellen, um den Zustand einer beliebigen Anwendungs- oder Systemeinstellung zu konfigurieren.


Weitere Informationen zu PowerShell DSC finden Sie unter [Configuration in a DevOps World – Windows PowerShell Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx) (in englischer Sprache).

##Was ist Azure Automation DSC?##
Azure Automation DSC setzt auf den mit PowerShell DSC eingeführten Grundlagen auf, um eine einfachere Konfigurationsverwaltung zu ermöglichen. Azure Automation DSC führt dieselbe Verwaltungsschicht in [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx) <link> ein, wie Azure Automation sie bereits für die PowerShell-Skripterstellung bietet.

Mithilfe von Azure Automation DSC können Sie [PowerShell DSC-Konfigurationen erstellen und verwalten](https://technet.microsoft.com/library/dn249918.aspx), [DSC-Ressourcen](https://technet.microsoft.com/library/dn282125.aspx) importieren und DSC-Knotenkonfigurationen (MOF-Dokumente) generieren – und das alles in der Cloud. Diese DSC-Elemente werden auf dem [DSC-Pullserver](https://technet.microsoft.com/library/dn249913.aspx) für Azure Automation platziert, sodass Zielknoten (beispielsweise physische und virtuelle Computer) diese in der Cloud oder lokal abrufen können, sich automatisch an den festgelegten Zustand anpassen und ihre Kompatibilität mit dem gewünschten Zustand an Azure Automation zurückmelden.

## Azure Automation DSC – Begriffe ##
### Konfiguration ###
PowerShell DSC führt ein neues Konzept der sogenannten Konfigurationen ein. Mithilfe von Konfigurationen können Sie über die PowerShell-Syntax den gewünschten Zustand Ihrer Umgebung definieren. Um DSC zum Konfigurieren Ihrer Umgebung zu verwenden, definieren Sie zunächst einen Windows PowerShell-Skriptblock mit dem Schlüsselwort "Configuration". Auf den Block muss ein Bezeichner folgen, anschließend wird der Block mit Klammern ({}) abgetrennt.

![alt text](./media/automation-dsc-overview/AADSC_1.png)

Innerhalb des Konfigurationsblocks können Sie Knotenkonfigurationsblöcke definieren, mit denen die gewünschte Konfiguration für einen Satz an Knoten (Computern) in Ihrer Umgebung festgelegt wird, die exakt gleich konfiguriert werden sollen. Auf diese Weise stellt eine Knotenkonfiguration eine "Rolle" für einen oder mehrere Knoten dar. Ein Knotenkonfigurationsblock beginnt mit dem Schlüsselwort "Node". Nach diesem Schlüsselwort folgt der Name der Rolle, hierbei kann es sich um eine Variable handeln. Verwenden Sie nach dem Computernamen Klammern {}, um den Knotenkonfigurationsblock abzutrennen.

![alt text](./media/automation-dsc-overview/AADSC_2.png)
 
Im Knotenkonfigurationsblock können Sie Ressourcenblöcke definieren, um spezifische DSC-Ressourcen zu konfigurieren. Ein Ressourcenblock beginnt mit dem Namen der Ressource, gefolgt vom gewünschten Bezeichner für diesen Block und Klammern {} zum Abtrennen des Blocks.

![alt text](./media/automation-dsc-overview/AADSC_3.png)

Ausführlichere Informationen zum Schlüsselwort "Configuration" finden Sie unter [Grundlegendes zum Schlüsselwort "Configuration" in DSC](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Grundlegendes zum Schlüsselwort "Configuration" in DSC").

Das Ausführen (Kompilieren) einer DSC-Konfiguration führt zur Erstellung von einer oder mehreren DSC-Knotenkonfigurationen (MOF-Dokumenten), die die Einstellungen vorgeben, die von den DSC-Knoten zum Erreichen des gewünschten Zustands angewendet werden müssen.

Azure Automation DSC ermöglicht Ihnen das Importieren, Erstellen und Kompilieren von DSC-Konfigurationen in Azure Automation – ähnlich wie Runbooks in Azure Automation importiert, erstellt und gestartet werden können.

Azure Automation DSC stellt für die Verwaltung von DSC-Konfigurationen zurzeit die folgenden Cmdlets im **PowerShell-Modul des Azure-Ressourcen-Managers** bereit:

- `Get-AzureAutomationDscConfiguration`
- `Import-AzureAutomationDscConfiguration`

>[AZURE.IMPORTANT]Eine Konfiguration sollte nur einen Konfigurationsblock mit demselben Namen wie die Konfiguration in Azure Automation DSC enthalten.

###Knotenkonfiguration###

Wenn eine DSC-Konfiguration kompiliert wird, werden in Abhängigkeit von den Node-Blöcken in der Konfiguration eine oder mehrere Knotenkonfigurationen erstellt. Eine Knotenkonfiguration ist dasselbe wie ein MOF- oder Konfigurationsdokument (wenn Sie mit diesen PS DSC-Begriffen vertraut sind) und repräsentiert eine "Rolle", wie z. B. einen Webserver oder Worker, deren gewünschten Zustand einer oder mehrere Knoten annehmen sollen.

PS DSC-Knoten erkennen Knotenkonfigurationen, die entweder per DSC-Push oder mithilfe von Pullmethoden angewendet werden sollen. Azure Automation DSC basiert auf der DSC-Pullmethode, bei der Knoten Knotenkonfigurationen anfordern, die Sie von Azure Automation DSC-Pullservern anwenden sollen. Da die Knoten die Anforderung an Azure Automation DSC senden, können sich die Knoten hinter Firewalls befinden, keine eingehenden Ports geöffnet haben usw. Die Knoten benötigen lediglich ausgehenden Zugriff auf das Internet.

Azure Automation DSC stellt für die Verwaltung von DSC-Knotenkonfigurationen zurzeit die folgenden Cmdlets im **PowerShell-Modul des Azure-Ressourcen-Managers** bereit: `Get-AzureAutomationDscNodeConfiguration`


###Knoten###

Ein DSC-Knoten ist ein beliebiger Computer, dessen Konfiguration von DSC verwaltet wird. Es kann sich hierbei um einen virtuellen Azure-Computer oder um einen lokalen virtuellen Computer / physischen Host handeln. Knoten wenden Knotenkonfigurationen an, um Kompatibilität mit dem gewünschten definierten Zustand zu erzielen und zu erhalten, und sie können ihren Konfigurationsstatus und ihre Kompatibilität an einen Berichtserver melden.

Azure Automation DSC vereinfacht die Integration von Knoten zur Verwaltung durch Azure Automation DSC und erlaubt das Ändern der Knotenkonfiguration, die jedem Knoten serverseitig zugewiesen ist. Wenn also ein Knoten den Server das nächste Mal auf Anweisungen überprüft, wird eine andere Rolle angenommen und die Konfiguration entsprechend abgeändert. Knoten melden außerdem ihren Zustand und ihre Konfigurationskompatibilität an Azure Automation DSC.

Azure Automation DSC stellt für die Verwaltung von DSC-Knoten zurzeit die folgenden Cmdlets im [PowerShell-Modul des Azure-Ressourcen-Managers](powershell-azure-resource-manager.md) bereit:

-	`Get-AzureAutomationDscNode`  
-	`Register-AzureAutomationDscNode` (zur Integration von Azure v2 VMs als Knoten)
-	`Get-AzureAutomationDscOnboardingMetaconfig` (zur Integration von Knoten)
-	`Set-AzureAutomationDscNode` (zum Einrichten/Aktualisieren von Zuordnungen zwischen Knoten)
-	`Unregister-AzureAutomationDscNode`
-	`Get-AzureAutomationDscNodeReport`
-	`Export-AzureAutomationDscNodeReportContent`

Das Cmdlet `Get-AzureAutomationRegistrationInfo` kann verwendet werden, um die Registrierungs-URL und den Schlüssel abzurufen, den Sie zur Integration von klassischen Azure-VMs in ein Azure Automation-Konto benötigen – entweder über die VM-Erweiterung von Azure Automation DSC im Azure-Portal, oder über PowerShell.


VM-Erweiterung von Azure Automation DSC:

![alt text](./media/automation-dsc-overview/AADSC_4.png)


PowerShell:

    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""


    # get Azure Automation DSC registration info
    Switch-AzureMode AzureResourceManager

    $Account = Get-AzureAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName

    $RegistrationInfo = $Account | Get-AzureAutomationRegistrationInfo


    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    Switch-AzureMode AzureServiceManagement

    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
        SasToken = ""
        ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfig.zip"
        ConfigurationFunction = "RegistrationMetaConfig.ps1\RegistrationMetaConfig"

        # update these DSC agent configurations if these defaults are not what you want. 
        # See https://technet.microsoft.com/de-de/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
        Properties = @{
            RegistrationKey = $RegistrationInfo.PrimaryKey
            RegistrationUrl = $RegistrationInfo.Endpoint
            NodeConfigurationName = "configname.webserver"
            ConfigurationMode = "ApplyAndMonitor"
            ConfigurationModeFrequencyMins = 15
            RefreshFrequencyMins = 30
           RebootNodeIfNeeded = $False
           ActionAfterReboot = "ContinueConfiguration"
           AllowModuleOverwrite = $False
        }
    } 

    $VM = Set-AzureVMExtension `
        -VM $vm `
        -Publisher Microsoft.Powershell `
        -ExtensionName DSC `
        -Version 1.9 `
        -PublicConfiguration $PublicConfiguration

    $VM | Update-AzureVM
 

###Ressource###
DSC-Ressourcen sind Bausteine, die Sie zum Definieren einer Windows PowerShell DSC-Konfiguration (Desired State Configuration) verwenden können. DSC verfügt über einen Satz integrierter Funktionen zum Konfigurieren von Ressourcen, darunter beispielsweise Dateien und Ordner, Serverfunktionen und -rollen, Registrierungseinstellungen, Umgebungsvariablen sowie Dienste und Prozesse. Eine vollständige Liste der integrierten DSC-Ressourcen und Informationen zu deren Verwendung finden Sie unter [Integrierte Windows PowerShell DSC-Ressourcen](https://technet.microsoft.com/library/dn249921.aspx).

DSC-Ressourcen können auch als Bestandteil der PowerShell-Module importiert werden, um den Satz der integrierten DSC-Ressourcen zu erweitern. Nicht standardmäßige Ressourcen werden von den DSC-Knoten vom DSC-Pullserver abgerufen, wenn eine durch den Knoten anzuwendende Knotenkonfiguration Verweise auf diese Ressourcen enthält. Informationen zum Erstellen von benutzerdefinierten Ressourcen finden Sie unter [Erstellen von benutzerdefinierten Windows PowerShell DSC-Ressourcen](https://technet.microsoft.com/library/dn249927.aspx).

Zum Lieferumfang von Azure Automation DSC gehören dieselben integrierten DSC-Ressourcen wie bei PS DSC. Sie können Azure Automation DSC zusätzliche Ressourcen hinzufügen, indem Sie PowerShell-Module mit den gewünschten Ressourcen in Azure Automation importieren.

Azure Automation DSC stellt für die Verwaltung von DSC-Knoten zurzeit die folgenden Cmdlets im [PowerShell-Modul des Azure-Ressourcen-Managers](powershell-azure-resource-manager.md) bereit:

- `New-AzureAutomationModule`
- `Remove-AzureAutomationModule`
- `Set-AzureAutomationModule`
- `Get-AzureAutomationModule`

###Kompilierungsauftrag###
Ein Kompilierungsauftrag in Azure Automation DSC ist eine Instanz einer Konfigurationskompilierung, um eine oder mehrere Knotenkonfigurationen zu erstellen. Sie ähneln Runbookaufträgen in Azure Automation, führen jedoch abgesehen von der Erstellung von Knotenkonfigurationen keine wirklichen Aufgaben aus. Alle durch einen Kompilierungsauftrag erstellten Knotenkonfigurationen werden automatisch auf dem Azure Automation DSC-Pullserver platziert und überschreiben vorherige Versionen der Knotenkonfigurationen, sofern vorhanden. Der Name einer über einen Kompilierungsauftrag erstellten Knotenkonfiguration weist das Format "<Configuration-name>.<Node configuration-block-name>" auf. Beispielsweise würde durch das Kompilieren der nachstehenden Konfiguration eine einzelne Knotenkonfiguration mit dem Namen "MyConfiguration.webserver" erstellt werden.


![alt text](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Ebenso wie Runbooks können Konfigurationen veröffentlicht werden. Dies hat nichts mit dem Platzieren von DSC-Elementen auf dem Azure Automation DSC-Pullserver zu tun. Kompilierungsaufträge sorgen dafür, dass DSC-Elemente auf dem Azure Automation DSC-Pullserver platziert werden. Weitere Informationen zum "Veröffentlichen" in Azure Automation finden Sie unter [Veröffentlichen eines Runbooks](https://msdn.microsoft.com/library/dn903765.aspx).


Azure Automation DSC stellt für die Verwaltung von DSC-Kompilierungsaufträgen zurzeit die folgenden Cmdlets im [PowerShell-Modul des Azure-Ressourcen-Managers](powershell-azure-resource-manager.md) bereit:

-	`Get-AzureAutomationDscCompilationJob`
-	`Get-AzureAutomationDscCompilationJobOutput`
-	`Start-AzureAutomationDscCompilationJob`

##Häufige / bekannte Probleme:##

- Da sich Azure Automation DSC in der Vorschau befindet, müssen Sie sich bei der ersten Verwendung dieses Features mithilfe von Azure PowerShell-Cmdlets registrieren. Sie können die Registrierung durch Aufruf der folgenden zwei Cmdlets durchführen:

 - `Register-AzureProvider –ProviderNamespace Microsoft.Automation`
 - `Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation` 
 
>[AZURE.NOTE]Die Azure Automation DSC-Registrierung kann bis zu einer Stunde in Anspruch nehmen, und die Funktion kann erst nach Abschluss der Registrierung verwendet werden.

- Azure Automation DSC bietet zurzeit keine Unterstützung für teilweise oder zusammengesetzte DSC-Konfigurationen.

- Für den PowerShell DSC-Agent für Windows muss die neueste Version von WMF 5 installiert werden, um eine Kommunikation mit Azure Automation zu ermöglichen. Der PowerShell DSC-Agent für Linux bietet zurzeit keine Unterstützung für die Kommunikation mit Azure Automation. Dies soll in Kürze geändert werden.

- Azure Automation bietet keine Unterstützung für die parallele Verwendung von PowerShell-Modulen. Dies bedeutet, dass alle Konfigurationen innerhalb eines Automation-Kontos mit der neuesten Version eines in dieses Automation-Konto importierten PowerShell-Moduls sowie mit der letzten Version aller PowerShell DSC-Ressourcen arbeiten müssen, die im Modul enthalten sind und von der Konfiguration verwendet werden.

- Der traditionelle PowerShell DSC-Pullserver erwartet, dass Modul-ZIP-Dateien im Format **Modulname_Version.zip** auf dem Pullserver platziert werden.  Azure Automation erwartet, dass PowerShell-Module mit Namen im Format **Modulname.zip** importiert werden. In [diesem Blogbeitrag](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) finden Sie weitere Informationen zum erforderlichen Format von Integrationsmodulen, um diese Module in Azure Automation zu importieren.

- PowerShell-Module, die über das Format "Version pro Ordner" parallele DSC-Ressourcen im Modul angeben, können in Azure Automation zurzeit nicht verwendet werden.

- Der Import eines PowerShell-Moduls in Azure Automation über die Cmdlets `New-AzureAutomationModule` oder `Set-AzureAutomationModule` erfolgt asynchron.  Selbst wenn das Cmdlet erfolgreich zurückgegeben wird, bedeutet dies nicht, dass der Modulimport erfolgreich war. Prüfen Sie mit `Get-AzureAutomationModule –Name <ModuleName>` (rufen Sie nur dieses Modul ab, nicht alle Module), ob der Import erfolgreich war, und überprüfen Sie, ob das Feld **ProvisioningState** einen erfolgreichen Status anzeigt.

- In Azure Automation importierte PowerShell-Module dürfen keine DOC- oder DOCX-Dateien enthalten. Einige PowerShell-Module mit DSC-Ressourcen enthalten diese Dateien zur Bereitstellung von Hilfeinformationen. Diese Dateien müssen vor dem Import in Azure Automation aus den Modulen entfernt werden.

- Konfigurationen in Azure Automation unterstützten zurzeit nur Zeichenfolgen für Parameterwerte. Wenn Sie als Wert eines Parameters etwas anderes übergeben möchten als eine Zeichenfolge, übergeben Sie den Parameterwert als JSON-Zeichenfolge, und verwenden Sie innerhalb der Konfiguration "ConvertFrom-Json", um die JSON-Zeichenfolge in einen Nicht-Zeichenfolgenwert zurückzukonvertieren.

- Bei der ersten Registrierung eines Knotens für ein Azure Automation-Konto, oder wenn der Knoten serverseitig einer anderen Knotenkonfiguration zugewiesen wird, ist sein Zustand kompatibel – selbst, wenn der Knotenzustand tatsächlich nicht mit der Knotenkonfiguration kompatibel ist, der er jetzt zugeordnet ist. Sobald der Knoten nach der Registrierung oder einer Änderungen der Knotenkonfigurationszuweisung seinen ersten Bericht sendet, kann der Knotenzustand als vertrauenswürdig eingestuft werden.

- Wenn beim Integrieren eines virtuellen Azure-Computers zur Verwaltung mit Azure Automation DSC über `Register-AzureAutomationDscNode`, `Set-AzureAutomationDscExtension` oder die VM-Erweiterung von Azure Automation DSC im Azure-Vorschauportal die Registrierung mit dem Fehler **Der Computername wurde nicht angegeben, und das Konfigurationsverzeichnis enthält keine Konfigurationsdateien** abgebrochen wird, ist dies ein Fehlalarm. Die VM-Registrierung war tatsächlich erfolgreich. Die erfolgreiche Registrierung kann mit dem Cmdlet `Get-AzureAutomationDscNode` überprüft werden.

<!---HONumber=58-->