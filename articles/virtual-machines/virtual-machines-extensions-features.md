<properties 
 pageTitle="Informationen zu Erweiterungen und Features für virtuelle Computer | Microsoft Azure"
	description="Beschreibt viele Erweiterungen für virtuelle Computer, gruppiert nach der bereitgestellten oder verbesserten Funktion, wie z. B. Konnektivität und grundlegende Verwaltung."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""/>
<tags 
 ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="infrastructure-services"
	ms.date="08/25/2015"
	ms.author="rasquill"/>
#Informationen zu Erweiterungen und Features für virtuelle Computer
Microsoft Azure bietet VM-Erweiterungen, die von Microsoft und von vertrauenswürdigen Drittanbieter erstellt werden, um Sicherheit, Laufzeit, Debuggen, Verwaltung und andere Features zu ermöglichen, die Sie nutzen können, um Ihre Produktivität mit virtuellen Azure-Computern zu erhöhen. In diesem Thema werden die verschiedenen Features beschrieben, die Azure-VM-Erweiterungen für virtuelle Windows- und Linux-Computer bieten, und es wird auf die jeweilige Dokumentation verwiesen.

Weitere Informationen zu den VM-Agents und deren Unterstützung von VM-Erweiterungen finden Sie unter [Übersicht über VM-Agents und VM-Erweiterungen](https://msdn.microsoft.com/library/dn832621.aspx).

##Azure-VM-Erweiterungen

VM-Erweiterungen implementieren die meisten der wichtigsten Funktionen, die Sie für Ihre virtuellen Computer verwenden möchten, darunter Grundfunktionen wie das Zurücksetzen von Kennwörtern, das Konfigurieren von RDP und viele andere. Da ständig neue Erweiterungen hinzugefügt werden, nimmt die Anzahl der möglichen Features, die Ihre virtuellen Computer in Azure unterstützen, weiter zu. Standardmäßig werden mehrere grundlegende VM-Erweiterungen installiert, wenn Sie Ihren virtuellen Computer über den Imagekatalog erstellen, einschließlich **IaaSDiagnostics** (derzeit nur virtuelle Windows-Computer), **VMAccess** und **BGInfo** (derzeit nur für Windows). Jedoch werden nicht alle Erweiterungen zu einem bestimmten Zeitpunkt unter Windows und Linux implementiert, da es ständig Featureupdates und neue Erweiterungen gibt.

##Konnektivität und grundlegende Verwaltung

Die folgenden Erweiterungen sind entscheidend zum Aktivieren, Reaktivieren oder Deaktivieren der Konnektivität Ihrer virtuellen Computer, nachdem sie erstellt wurden und ausgeführt werden.

|Name der VM-Erweiterung|Featurebeschreibung|Weitere Informationen
|---|---|---|
|VMAccessAgent (Windows)|Benutzerinformationen und Konfigurationen von RDP- und SSH-Verbindungen erstellen, aktualisieren und zurücksetzen.|[Windows](https://msdn.microsoft.com/library/dn606308.aspx)
|VMAccessForLinux (Linux)|Benutzerinformationen und Konfigurationen von RDP- und SSH-Verbindungen erstellen, aktualisieren und zurücksetzen.|[Linux](http://azure.microsoft.com/blog/2014/08/25/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)

##Bereitstellungs- und Konfigurationsverwaltung

Die folgenden Erweiterungen unterstützen verschiedene Arten von Szenarien und Features für die Bereitstellungs- und Konfigurationsverwaltung. Weitere Informationen finden Sie auch weiter unten im Abschnitt über Betrieb und Verwaltung virtueller Computer.

|Name der VM-Erweiterung|Featurebeschreibung|Weitere Informationen|
|---|---|---|
|**MSEnterpriseApplication**|Implementiert Features für die Unterstützung durch Windows System Center.|[System Center 2012 R2 – Rollen virtueller Computer](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus Deploy** (basiert auf DSC-Erweiterung)|Unterstützt die automatisierte Bereitstellung von ASP.NET-Webanwendungen und Windows-Diensten in Entwicklungs-, Test- und Produktionsumgebungen.|[Getting Started with Octopus Deploy](http://docs.octopusdeploy.com/display/OD/Getting%20started) (in englischer Sprache)|
|**Visual Studio Release Manager** (basiert auf DSC-Erweiterung)|Unterstützt die kontinuierliche Bereitstellung mit Visual Studio.|[Automatisieren von Bereitstellungen mit Release Management](https://msdn.microsoft.com/library/dn217874.aspx)|
|**CentosChefClient**|||
|**ChefClient**|Erstellt einen Chef-Client unter Windows. (Kann auch die weiter unter aufgeführte DSC-Erweiterung verwenden.)|[Chef und Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|Installiert den Docker-Daemon zur Unterstützung von Docker-Remotebefehlen.|[Verwenden der Docker-Erweiterung für virtuelle Computer](virtual-machines-docker-vm-extension.md) Ausführlichere Informationen finden Sie im [Benutzerhandbuch für die Docker-VM-Erweiterung](https://github.com/Azure/azure-docker-extension/blob/master/README.md)|
|**DSC**|PowerShell-DSC-Erweiterung (Desired State Configuration, Konfigurieren des gewünschten Zustands).|[Azure PowerShell DSC (Desired State Configuration) extension](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx) (in englischer Sprache)|
|**PuppetEnterpriseAgent**|Implementiert die Features von Puppet Enterprise. |[Puppet on Azure](http://puppetlabs.com/solutions/microsoft) (in englischer Sprache)|
|**CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux)|Ruft benutzerdefinierte Skripts auf dem virtuellen Computer zu einem beliebigen Zeitpunkt auf: beim Start oder während der Lebensdauer.|[CustomScript-Erweiterung](https://msdn.microsoft.com/library/dn781373.aspx)[Linux](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)|
|**AzureCATExtensionHandler**|Nutzt die von **IaaSDiagnostics** und einigen anderen Datenquellen wie z. B. [Metriken der Azure-Speicheranalyse](https://msdn.microsoft.com/library/azure/hh343270.aspx) erfassten Diagnosedaten und wandelt diese in einen aggregierten Datensatz um, der vom SAP-Hoststeuerungprozess verwendet werden kann.|[Azure Enhanced Monitoring for SAP](http://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/) (in englischer Sprache)|

##Sicherheit und Schutz

Die Erweiterungen in diesem Abschnitt bieten wichtige Sicherheitsfeatures für Ihre Azure-VMs.

|Name der VM-Erweiterung|Featurebeschreibung|Weitere Informationen|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Bietet Microsoft Azure-Kunden die Möglichkeit, die Daten der virtuellen Computer in einer freigegebenen Infrastruktur mit mehreren Mandanten zu verschlüsseln und die Verschlüsselungsschlüssel für die verschlüsselten Daten in der Azure-Speicherinfrastruktur vollständig zu kontrollieren.|[Securing Microsoft Azure Virtual Machines leveraging BitLocker and Native OS encryption](http://www.cloudlinktech.com/azure) (in englischer Sprache)|
|**McAfeeEndpointSecurity**|Schützt Ihren virtueller Computer vor Schadsoftware.|[McAfee](http://www.mcafeeasap.com/)|
|**TrendMicroDSA**|Ermöglicht die Unterstützung der Deep Security-Plattform von TrendMicro, um Angriffserkennung und -verhinderung, Firewall, Antischadsoftware, Web-Reputation, Protokollprüfug und Überwachung der Integrität zu bieten.|[Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Azure-Computer](http://virtual-machines-install-trend.md)|
|**PortalProtectExtension**|Schützt vor Bedrohungen der Microsoft SharePoint-Umgebung.|[Securing Your SharePoint Deployment on Azure](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/) (in englischer Sprache)|
|**IaaSAntimalware**|Antischadsoftware für Azure-Clouddienste und virtuelle Computer von Microsoft ist eine Funktion für Echtzeitschutz, die beim Erkennen und Entfernen von Viren, Spyware und anderer Schadsoftware hilft. Sie bietet konfigurierbare Warnungen, wenn bekannte Schadsoftware oder unerwünschte Software versucht, sich selbst zu installieren oder auf Ihrem System zu starten.|[Laden Sie die Dokumentation der Antischadsoftware herunter](http://go.microsoft.com/fwlink/?linkid=398023&clcid=0x409)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 ermöglicht Sicherheit und Leistung für physische und virtuelle Systeme.|[Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer](http://virtual-machines-install-symantec.md)

##Betrieb und Verwaltung virtueller Computer

Unterstützt allgemeine Features und Verhaltensweisen bei der Verwaltung des Betriebs. Weitere Informationen finden Sie weiter oben im Abschnitt zur Bereitstellungs- und Konfigurationsverwaltung.

|**Name der VM-Erweiterung**|Featurebeschreibung|Weitere Informationen|
|---|---|---|
|**AzureVmLogCollector**|Sie können die Erweiterung **AzureVMLogCollector** bedarfsgesteuert verwenden, um eine einmalige Sammlung von Protokollen von einer oder mehreren Clouddienst-VMs (von Web- und Workerrollen) durchzuführen und die gesammelten Dateien an ein Azure-Speicherkonto zu übertragen – alles ohne Remoteanmeldung auf den virtuellen Computern. |[AzureLogCollector-Erweiterung](https://msdn.microsoft.com/library/dn927183.aspx)|
|**IaaSDiagnostics**|Aktiviert, deaktiviert und konfiguriert die Azure-Diagnose und wird auch von **AzureCATExtensionHandler** zur Unterstützung der SAP-Überwachung verwendet.|[Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) (in englischer Sprache)|
|**OSPatchingForLinux**|Ermöglicht Administratoren von Azure-VMs das Automatisieren von Betriebssystemupdates für virtuelle Computer mit benutzerdefinierten Konfigurationen. Sie können die Erweiterung OSPatching verwenden, um Betriebssystemupdates für Ihre virtuellen Computer zu konfigurieren, einschließlich der Angabe, wie oft und wann Betriebssystempatches installiert werden sollen, welche Patches installiert werden sollen und der Konfiguration des Verhaltens beim Neustart nach Updates.|[Blogbeitrag zur OSPatching-Erweiterung](http://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). Weitere Informationen finden Sie in der Infodatei und der Quelle auf Github unter [OS Patching Extension](https://github.com/Azure/azure-linux-extensions) (in englischer Sprache).|

##Entwickeln und Debuggen

Diese VM-Erweiterungen werden der Vollständigkeit halber hier aufgeführt, da sie Unterstützung für Visual Studio-bezogene Features bieten und nicht direkt verwendet werden sollen.

|Name der VM-Erweiterung|Featurebeschreibung|Weitere Informationen|
|---|---|---|
|**VS14CTPDebugger**|Unterstützt das Remotedebuggen von Visual Studio mit dem Azure SDK 2.4|[Remotedebuggen in Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|Unterstützt das Remotedebuggen von Visual Studio mit dem Azure SDK 2.4||
|**VS2012Debugger**|Unterstützt das Remotedebuggen von Visual Studio mit dem Azure SDK 2.4||
|**RemoteDebugVS14CTP**|Unterstützt das Remotedebuggen von Visual Studio mit dem Azure SDK 2.3||
|**RemoteDebugVS2013**|Unterstützt das Remotedebuggen von Visual Studio mit dem Azure SDK 2.3||
|**RemoteDebugVS2012**|Unterstützt das Remotedebuggen von Visual Studio mit dem Azure SDK 2.3||
|**WebDeployForVSDevTest**|Installiert und konfiguriert IIS und Web Deploy unter Windows Server. Ein Entfernen oder Deaktivieren wird nicht unterstützt.|

##Verschiedene Features

Diese Erweiterungen bieten Unterstützung für andere Features von virtuellen Computern, die hilfreich sein können.

|Name der VM-Erweiterung|Featurebeschreibung|Weitere Informationen|
|---|---|---|
|**BGInfo**|Stellt eine konsolidierte Ansicht nützlicher Serverinformationen auf dem Desktop dar, wenn RDP verwendet wird.|[Erweiterung "BGInfo"](https://msdn.microsoft.com/library/dn606289.aspx)|
|**HpcVmDrivers**|Installiert, konfiguriert und verwaltet die folgenden Netzwerk-Gerätetreiber auf virtuellen Computern der Größe A8 oder A9, damit der virtuelle Computer direkt auf das RDMA-Netzwerk (Remote Direct Memory Access) von Azure zugreifen kann.|[HpcVmDrivers-Erweiterung](https://msdn.microsoft.com/library/dn690126.aspx)

<!---HONumber=September15_HO1-->