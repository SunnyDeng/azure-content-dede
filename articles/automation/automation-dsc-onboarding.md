<properties 
   pageTitle="Integrieren physischer und virtueller Computer für die Verwaltung durch Azure Automation DSC | Microsoft Azure" 
   description="Einrichten von Computern für die Verwaltung mit Azure Automation DSC" 
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
   ms.date="03/15/2016"
   ms.author="coreyp"/>

# Integrieren von Computern für die Verwaltung durch Azure Automation DSC

## Gründe für das Verwalten von Computern mit Azure Automation DSC

Wie [PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) (Desired State Configuration, Konfiguration des gewünschten Zustands) ist Azure Automation DSC ein einfacher, aber leistungsfähiger Konfigurationsverwaltungsdienst für DSC-Knoten (physische und virtuelle Computer) in jedem beliebigen Cloud- oder lokalen Datencenter. Sie können damit Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer integrieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist. Die Azure Automation DSC-Verwaltungsebene ist für DSC, was die Azure Automation-Verwaltungsebene für PowerShell-Skripts ist. So hilft Azure Automation auf die gleiche Weise wie beim Verwalten von PowerShell-Skripts bei der Verwaltung von DSC-Konfigurationen. Weitere Informationen zu den Vorteilen von Azure Automation DSC finden Sie unter [Azure Automation DSC – Übersicht](automation-dsc-overview/).

Azure Automation DSC kann verwendet werden, um eine Vielzahl von Computern zu verwalten:

*    Virtuelle Azure-Computer (klassisch)
*    Azure Virtual Machines
*    Physische/virtuelle Windows-Computer, lokal oder in einer anderen Cloud als Azure
*    Physische/virtuelle Linux-Computer, lokal, in Azure oder in einer anderen Cloud als Azure

Wenn Sie noch nicht zum Verwalten von Computerkonfigurationen in der Cloud bereit sind, kann Azure Automation DSC auch ausschließlich als Endpunkt für Berichte verwendet werden. Dadurch können Sie die gewünschte Konfiguration über DSC lokal festlegen und umfassende Berichtsdetails zur Kompatibilität von Knoten mit dem gewünschten Zustand in Azure Automation anzeigen.

In den folgenden Abschnitten wird beschrieben, wie Sie jeden Computertyp in Azure Automation DSC integrieren können.

## Virtuelle Azure-Computer (klassisch)

Mit Azure Automation DSC können Sie problemlos virtuelle Azure-Computer (klassisch) für die Konfigurationsverwaltung integrieren, indem Sie das Azure-Portal oder PowerShell verwenden. Ohne Remotezugriff eines Administrators auf den virtuellen Computer registriert die Azure-VM-Erweiterung für DSC den virtuellen Computer bei Azure Automation DSC. Da die Azure-VM-Erweiterung für DSC asynchron ausgeführt wird, finden Sie die Schritte zum Verfolgen des Fortschritts oder zur Problembehandlung im Abschnitt [**Problembehandlung bei der Integration virtueller Azure-Computer**](#troubleshooting-azure-virtual-machine-onboarding) weiter unten.


### Azure-Portal

Klicken Sie im [Azure-Portal](http://portal.azure.com/) auf **Durchsuchen** und dann auf **Virtuelle Computer (klassisch)**. Wählen Sie die Windows-VM aus, die Sie integrieren möchten. Klicken Sie auf dem Dashboardblatt des virtuellen Computers auf **Alle Einstellungen** -> **Erweiterungen** -> **Hinzufügen** -> **Azure Automation DSC** -> **Erstellen**. Geben Sie die [Werte des lokalen Konfigurations-Managers von PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4), die für Ihren Anwendungsfall erforderlich sind, den Registrierungsschlüssel und die Registrierungs-URL Ihres Automation-Kontos und optional eine Knotenkonfiguration, die dem virtuellen Computer zugewiesen werden soll, ein.


![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


Informationen zum Suchen von Registrierungs-URL und Registrierungsschlüssel für das Automation-Konto zum Integrieren des Computers finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Add-AzureRmAccount
    
    # fill in correct values for your VM/Automation account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
     Properties = @{
        RegistrationKey = @{
          UserName = 'notused'
          Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = "ApplyAndMonitor"
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = "ContinueConfiguration"
        AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension `
     -VM $vm `
     -Publisher Microsoft.Powershell `
     -ExtensionName DSC `
     -Version 2.15 `
     -PublicConfiguration $PublicConfiguration `
     -PrivateConfiguration $PrivateConfiguration `
     -ForceUpdate

    $VM | Update-AzureVM

## Azure Virtual Machines

Mit Azure Automation DSC können Sie problemlos virtuelle Azure-Computer für die Konfigurationsverwaltung integrieren, indem Sie das Azure-Portal, Azure-Ressourcen-Manager-Vorlagen oder PowerShell verwenden. Ohne Remotezugriff eines Administrators auf den virtuellen Computer registriert die Azure-VM-Erweiterung für DSC den virtuellen Computer bei Azure Automation DSC. Da die Azure-VM-Erweiterung für DSC asynchron ausgeführt wird, finden Sie die Schritte zum Verfolgen des Fortschritts oder zur Problembehandlung im Abschnitt [**Problembehandlung bei der Integration virtueller Azure-Computer**](#troubleshooting-azure-virtual-machine-onboarding) weiter unten.


### Azure-Portal

Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu dem Azure Automation-Konto, in das Sie virtuelle Computer integrieren möchten. Klicken Sie im Dashboard des Automation-Kontos auf **DSC-Knoten** -> **Azure-VM hinzufügen**.

Wählen Sie unter **Virtuelle Computer für die Integration auswählen** die virtuellen Azure-Computer aus, die Sie integrieren möchten.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


Geben Sie unter **Registrierungsdaten konfigurieren** die Werte des [lokalen Konfigurations-Managers von PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4), die für Ihren Anwendungsfall erforderlich sind, und optional eine Knotenkonfiguration, die dem virtuellen Computer zugewiesen werden soll, ein.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Azure-Ressourcen-Manager-Vorlagen

Virtuelle Azure-Computer können über Azure-Ressourcen-Manager-Vorlagen in Azure Automation DSC bereitgestellt und integriert werden. Unter [Konfigurieren eines virtuellen Computers über die DSC-Erweiterung und Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) finden Sie eine Beispielvorlage, mit der ein vorhandener virtueller Computer in Azure Automation DSC integriert wird. Informationen zum Suchen des Registrierungsschlüssels und der Registrierungs-URL, die als Eingaben in dieser Vorlage verwendet werden, finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.

### PowerShell

Mit dem Cmdlet [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) können virtuelle Computer im Azure-Portal über PowerShell integriert werden.

## Physische/virtuelle Windows-Computer, lokal oder in einer anderen Cloud als Azure

Lokale Windows-Computer und Windows-Computer in anderen Clouds als Azure (z. B. Amazon Web Services) können auch in Azure Automation DSC integriert werden, sofern sie über ausgehenden Zugriff auf das Internet verfügen. Es sind nur wenige einfache Schritte erforderlich:

1. Stellen Sie sicher, dass die neueste Version von [WMF 5](http://aka.ms/wmf5latest) auf den Computern installiert ist, die Sie in Azure Automation DSC integrieren möchten.
2. Führen Sie die Schritte im nachstehenden Abschnitt [**Generieren von DSC-Metakonfigurationen**](#generating-dsc-metaconfigurations) aus, um einen Ordner mit den erforderlichen DSC-Metakonfigurationen zu generieren.
3. Wenden Sie die PowerShell DSC-Metakonfiguration remote auf die Computer an, die Sie integrieren möchten. **Auf dem Computer, auf dem dieser Befehl ausgeführt wird, muss die neueste Version von [WMF 5](http://aka.ms/wmf5latest) installiert sein**:

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

4. Wenn Sie die PowerShell DSC-Metakonfigurationen nicht remote anwenden können, kopieren Sie den Metakonfigurationsordner aus Schritt 2 auf jeden Computer, den Sie integrieren möchten. Rufen Sie dann **Set-DscLocalConfigurationManager** lokal auf jedem Computer auf, den Sie integrieren möchten.
5. Überprüfen Sie mit dem Azure-Portal oder den Cmdlets, ob die Computer, die Sie integrieren möchten, jetzt als DSC-Knoten angezeigt werden, die in Ihrem Azure Automation-Konto registriert sind.

## Physische/virtuelle Linux-Computer, lokal, in Azure oder in einer anderen Cloud als Azure

Lokale Linux-Computer und Linux-Computer in Azure sowie in anderen Clouds können auch in Azure Automation DSC integriert werden, sofern sie über ausgehenden Zugriff auf das Internet verfügen. Es sind nur wenige einfache Schritte erforderlich:

1. Stellen Sie sicher, dass die neueste Version des [DSC-Linux-Agents](http://www.microsoft.com/download/details.aspx?id=49150) auf den Computern installiert ist, die Sie in Azure Automation DSC integrieren möchten.

2. Gehen Sie folgendermaßen vor, [wenn die Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) zu Ihrem Anwendungsfall passen und Sie Computer so integrieren möchten, dass diese **sowohl** Informationen von Azure Automation DSC abrufen als auch Informationen an Azure Automation DSC senden:

	*    Verwenden Sie auf jedem Linux-Computer, den Sie in Azure Automation DSC integrieren möchten, "Register.py" für eine Integration mit den Standardwerten des lokalen Konfigurations-Managers von PowerShell DSC:

		`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

	*    Informationen zum Suchen des Registrierungsschlüssels und der Registrierungs-URL für Ihr Automation-Konto finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.

	Wenn die Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC **nicht** zu Ihrem Anwendungsszenario passen, oder Sie Computer so integrieren möchten, dass Sie nur an Azure Automation DSC berichten und keine Konfigurationen oder PowerShell-Module davon abrufen, führen Sie die Schritte 3 - 6 aus. Fahren Sie andernfalls direkt mit Schritt 6 fort.

3.	Führen Sie die Schritte im Abschnitt [**Generieren von DSC-Metakonfigurationen**](#generating-dsc-metaconfigurations) aus, um einen Ordner mit den erforderlichen DSC-Metakonfigurationen zu generieren.
4.  Wenden Sie die PowerShell DSC-Metakonfiguration remote auf die Computer an, die Sie integrieren möchten:
    	
    	$SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
        $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs
	
Auf dem Computer, auf dem dieser Befehl ausgeführt wird, muss die neueste Version von [WMF 5](http://aka.ms/wmf5latest) installiert sein.

5.  Wenn Sie die PowerShell DSC-Metakonfigurationen nicht remote anwenden können, kopieren Sie für jeden Linux-Computer, den Sie integrieren möchten, die Metakonfiguration für diesen Computer aus dem Ordner in Schritt 5 auf den Linux-Computer. Rufen Sie anschließend `SetDscLocalConfigurationManager.py` lokal auf jedem Linux-Computer auf, den Sie in Azure Automation DSC integrieren möchten:

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

6.  Überprüfen Sie mit dem Azure-Portal oder den Cmdlets, ob die Computer, die Sie integrieren möchten, jetzt als DSC-Knoten angezeigt werden, die in Ihrem Azure Automation-Konto registriert sind.

##Generieren von DSC-Metakonfigurationen
Sie können eine DSC-Metakonfiguration generieren, um einen beliebigen Computer generisch in Azure Automation DSC zu integrieren. Wird diese angewendet, weist sie den DSC-Agent an, sowohl Informationen von Azure Automation DSC abzurufen als auch Informationen an Azure Automation DSC zu senden. Die DSC-Metakonfigurationen für Azure Automation DSC können sowohl über eine PowerShell DSC-Konfiguration als auch über die Azure Automation PowerShell-Cmdlets generiert werden.

**Hinweis:** DSC-Metakonfigurationen enthalten die notwendigen Geheimschlüssel, um einen Computer in ein Automation-Konto für die Verwaltung zu integrieren. Stellen Sie den ordnungsgemäßen Schutz aller von Ihnen generierten DSC-Metakonfigurationen sicher oder löschen Sie diese nach der Verwendung.

###Verwenden einer DSC-Konfiguration
1.	Öffnen Sie PowerShell ISE als Administrator auf einem Computer in Ihrer lokalen Umgebung. Auf dem Computer muss die neueste Version von [WMF 5](http://aka.ms/wmf5latest) installiert sein.

2.	Kopieren Sie das folgende Skript in Ihre lokale Umgebung. Dieses Skript enthält eine PowerShell DSC-Konfiguration zum Erstellen von Metakonfigurationen und einen Befehl zum Starten der Metakonfigurationserstellung.
    
        # The DSC configuration that will generate metaconfigurations
        [DscLocalConfigurationManager()]
        Configuration DscMetaConfigs 
        { 
            param 
            ( 
                [Parameter(Mandatory=$True)] 
                [String]$RegistrationUrl,
         
                [Parameter(Mandatory=$True)] 
                [String]$RegistrationKey,

                [Parameter(Mandatory=$True)] 
                [String[]]$ComputerName,

                [Int]$RefreshFrequencyMins = 30, 
            
                [Int]$ConfigurationModeFrequencyMins = 15, 
            
                [String]$ConfigurationMode = "ApplyAndMonitor", 
            
                [String]$NodeConfigurationName,

                [Boolean]$RebootNodeIfNeeded= $False,

                [String]$ActionAfterReboot = "ContinueConfiguration",

                [Boolean]$AllowModuleOverwrite = $False,

                [Boolean]$ReportOnly
            )

    
            if(!$NodeConfigurationName -or $NodeConfigurationName -eq "") 
            { 
                $ConfigurationNames = $null 
            } 
            else 
            { 
                $ConfigurationNames = @($NodeConfigurationName) 
            }

            if($ReportOnly)
            {
               $RefreshMode = "PUSH"
            }
            else
            {
               $RefreshMode = "PULL"
            }

            Node $ComputerName
            {

                Settings 
                { 
                    RefreshFrequencyMins = $RefreshFrequencyMins 
                    RefreshMode = $RefreshMode 
                    ConfigurationMode = $ConfigurationMode 
                    AllowModuleOverwrite  = $AllowModuleOverwrite 
                    RebootNodeIfNeeded = $RebootNodeIfNeeded 
                    ActionAfterReboot = $ActionAfterReboot 
                    ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins 
                }

                if(!$ReportOnly)
                {
                   ConfigurationRepositoryWeb AzureAutomationDSC 
                    { 
                        ServerUrl = $RegistrationUrl 
                        RegistrationKey = $RegistrationKey 
                        ConfigurationNames = $ConfigurationNames 
                    }

                    ResourceRepositoryWeb AzureAutomationDSC 
                    { 
                       ServerUrl = $RegistrationUrl 
                       RegistrationKey = $RegistrationKey 
                    }
                }

                ReportServerWeb AzureAutomationDSC 
                { 
                    ServerUrl = $RegistrationUrl 
                    RegistrationKey = $RegistrationKey 
                }
            } 
        }
        
        # Create the metaconfigurations
        # TODO: edit the below as needed for your use case
        $Params = @{
             RegistrationUrl = '<fill me in>';
             RegistrationKey = '<fill me in>';
             ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
             NodeConfigurationName = 'SimpleConfig.webserver';
             RefreshFrequencyMins = 30;
             ConfigurationModeFrequencyMins = 15;
             RebootNodeIfNeeded = $False;
             AllowModuleOverwrite = $False;
             ConfigurationMode = 'ApplyAndMonitor';
             ActionAfterReboot = 'ContinueConfiguration';
             ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
        }
        
        # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
        # For more info about splatting, run: Get-Help -Name about_Splatting
        DscMetaConfigs @Params

3.	Geben Sie den Registrierungsschlüssel und die URL für Ihr Automation-Konto sowie die Namen der Computer ein, die integriert werden sollen. Alle anderen Parameter sind optional. Informationen zum Suchen des Registrierungsschlüssels und der Registrierungs-URL für Ihr Automation-Konto finden Sie im Abschnitt [**Sichere Registrierung**](#secure-registration) weiter unten.

4.	Wenn die Computer zwar DSC-Statusinformationen an Azure Automation DSC senden, jedoch keine Konfiguration oder PowerShell-Module abrufen sollen, müssen Sie für den **ReportOnly**-Parameter die Einstellung „true“ wählen.

5.	Führen Sie das Skript aus. Ihr Arbeitsverzeichnis sollte nun einen Ordner namens **DscMetaConfigs** mit den PowerShell DSC-Metakonfigurationen der zu integrierenden VMs enthalten.

###Verwenden der Azure Automation-Cmdlets
Wenn die Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC zu Ihrem Anwendungsfall passen und Sie Computer so integrieren möchten, dass diese sowohl Informationen von Azure Automation DSC abrufen als auch Informationen an Azure Automation DSC senden, bieten Ihnen die Azure Automation-Cmdlets eine vereinfachte Möglichkeit zur Generierung der benötigten DSC-Metakonfigurationen an:

1.	Öffnen Sie die PowerShell-Konsole oder PowerShell ISE als Administrator auf einem Computer in Ihrer lokalen Umgebung.

2.	Verwenden Sie **Add-AzureRmAccount**, um die Verbindung mit dem Azure-Ressourcen-Manager herzustellen.

3.	Laden Sie von dem Automation-Konto, in das Sie Knoten integrieren möchten, die PowerShell DSC-Metakonfigurationen für die Computer herunter, die Sie integrieren möchten:

        # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
        $Params = @{
            ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
            AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
            ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
            OutputFolder = "$env:UserProfile\Desktop";
        }
        
        # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
        # For more info about splatting, run: Get-Help -Name about_Splatting
        Get-AzureRmAutomationDscOnboardingMetaconfig @Params

Nun sollte ein Ordner namens ***DscMetaConfigs*** mit den PowerShell DSC-Metakonfigurationen der zu integrierenden VMs bestehen.

##Sichere Registrierung

Computer können über das WMF 5-DSC-Registrierungsprotokoll sicher in ein Azure Automation-Konto integriert werden. Mit dem Protokoll kann ein DSC-Knoten bei einem PowerShell DSC V2-Pull- oder -Berichtsserver authentifiziert werden (einschließlich Azure Automation DSC). Der Knoten wird beim Server an einer **Registrierungs-URL** registriert und mit einem **Registrierungsschlüssel** authentifiziert. Während der Registrierung handeln der DSC-Knoten und der DSC-Pull-/-Berichtsserver ein eindeutiges Zertifikat für diesen Knoten aus, das zur Authentifizierung beim Server nach der Registrierung verwendet wird. Durch diesen Prozess wird verhindert, dass integrierte Knoten die Identität eines anderen Knotens annehmen, z. B. wenn ein Knoten kompromittiert wurde und böswillige Absichten verfolgt. Nach der Registrierung wird der Registrierungsschlüssel nicht wieder für die Authentifizierung verwendet und vom Knoten gelöscht.

Sie finden die für das DSC-Registrierungsprotokoll erforderlichen Informationen auf dem Blatt **Schlüssel verwalten** im Azure-Vorschauportal. Öffnen Sie dieses Blatt, indem Sie im Bereich **Essentials** für das Automation-Konto auf das Schlüsselsymbol klicken.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    Die Registrierungs-URL ist das Feld "URL" auf dem Blatt "Schlüssel verwalten".
*    Der Registrierungsschlüssel ist der primäre Zugriffsschlüssel oder der sekundäre Zugriffsschlüssel auf dem Blatt "Schlüssel verwalten". Beide Schlüssel können verwendet werden.

Zur Erhöhung der Sicherheit können die primären und sekundären Zugriffsschlüssel eines Automation-Kontos jederzeit generiert werden (auf dem Blatt **Schlüssel verwalten**), um künftige Knotenregistrierungen mit alten Schlüsseln zu verhindern.

##Problembehandlung bei der Integration virtueller Azure-Computer

Mit Azure Automation DSC können Sie problemlos Azure Windows-VMs für die Konfigurationsverwaltung integrieren. Die Azure-VM-Erweiterung für DSC wird verwendet, um den virtuellen Computer bei Azure Automation DSC zu registrieren. Da die Azure-VM-Erweiterung für DSC asynchron ausgeführt wird, können die Überwachung des Fortschritts und die Problembehandlung bei der Ausführung wichtig sein.

>[AZURE.NOTE] Bei Methoden zum Integrieren einer Azure Windows-VM in Azure Automation DSC, in denen die Azure-VM-Erweiterung für DSC verwendet wird, kann es bis zu einer Stunde dauern, bis der Knoten als in Azure Automation registriert angezeigt wird. Dies liegt an der Installation von Windows Management Framework 5.0 auf dem virtuellen Computer durch die Azure VM-Erweiterung für DSC, mit der der virtuelle Computer in Azure Automation DSC integriert werden muss.

Zur Problembehandlung oder Anzeige des Status der Azure-VM-Erweiterung für DSC wechseln Sie im Azure-Portal zu dem virtuellen Computer, der integriert wird, und klicken Sie dann auf -> **Alle Einstellungen** -> **Erweiterungen** -> **DSC**. Weitere Details erhalten Sie, indem Sie auf **Detaillierten Status anzeigen** klicken.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## Ablauf und erneute Registrierung eines Zertifikats

Nach der Registrierung eines Computers als DSC-Knoten in Azure Automation DSC gibt es eine Reihe von Gründen, warum Sie diesen Knoten in Zukunft erneut registrieren müssen:

* Nach der Registrierung handelt jeder Knoten automatisch ein eindeutiges Zertifikat für die Authentifizierung aus, die nach einem Jahr abläuft. Gegenwärtig kann das PowerShell DSC-Registrierungsprotokoll Zertifikate nicht automatisch erneuern, wenn sie sich dem Ablaufdatum nähern, weshalb Sie die Knoten nach einem Jahr erneut registrieren müssen. Stellen Sie vor einer erneuten Registrierung sicher, dass jeder Knoten Windows Management Framework 5.0 RTM ausführt. Wenn das Authentifizierungszertifikat eines Knotens abläuft und er nicht erneut registriert wird, schlägt die Kommunikation mit Azure Automation fehl und der Knoten wird als „nicht reagierend“ markiert. Eine erneute Registrierung, die 90 Tage oder weniger vor Ablaufzeitpunkt des Zertifikats oder zu einem beliebigen Zeitpunkt nach dessen Ablaufzeitpunkt durchgeführt wird, hat zur Folge, dass ein neues Zertifikat generiert und verwendet wird.

* Zum Ändern von [Werten des lokalen Konfigurations-Managers von PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4), die während der anfänglichen Registrierung des Knotens an, z. B. „ConfigurationMode“, festgelegt wurden. Diese DSC-Agent-Werte können derzeit nur über eine erneute Registrierung geändert werden. Die einzige Ausnahme ist die Knotenkonfiguration, die dem Knoten zugewiesen ist. Diese kann in Azure Automation DSC direkt geändert werden.

Eine erneute Registrierung kann auf die gleiche Weise wie beim ersten Registrieren des Knotens mithilfe einer der in diesem Dokument beschriebenen Methoden für die Integration erfolgen. Sie müssen die Registrierung eines Knotens in Azure Automation DSC nicht aufheben, bevor Sie ihn neu registrieren.


## Verwandte Artikel
* [Azure Automation DSC – Übersicht](automation-dsc-overview.md)
* [Azure Automation DSC-Cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure Automation DSC – Preise](https://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=AcomDC_0316_2016-->
