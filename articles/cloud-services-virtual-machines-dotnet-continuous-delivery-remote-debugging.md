<properties urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Aktivieren von Remotedebugging mit kontinuierlicher Zustellung" metaKeywords="" description="Hier erfahren Sie, wie Sie Remotedebugging aktivieren, wenn kontinuierliche Zustellung f&uuml;r die Bereitstellung in Azure verwendet wird." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Aktivieren von Remotedebuggen, wenn kontinuierliche Zustellung f&uuml;r die Ver&ouml;ffentlichung in Azure verwendet wird" authors="kempb" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="kempb" />

# Aktivieren von Remotedebuggen, wenn kontinuierliche Zustellung für die Veröffentlichung in Azure verwendet wird

Sie können mithilfe folgender Schritte Remotedebuggen in Azure aktivieren, wenn Sie [kontinuierliche Zustellung][kontinuierliche Zustellung] für die Veröffentlichung in Azure verwenden.

In diesem Thema:

[Aktivieren von Remotedebuggen für Cloud-Dienste][Aktivieren von Remotedebuggen für Cloud-Dienste]

[Aktivieren von Remotedebuggen für virtuelle Computer][Aktivieren von Remotedebuggen für virtuelle Computer]

## <a name="cloudservice"></a>Aktivieren von Remotedebuggen für Cloud-Dienste

1.  Richten Sie im Build-Agent die initiale Umgebung für Azure ein, wie in [Befehlszeilentool für Azure][Befehlszeilentool für Azure] dargestellt.
2.  Da die Remotedebugging-Laufzeit (msvsmon.exe) für das Paket erforderlich ist, müssen Sie [Remote Tools für Visual Studio 2013][Remote Tools für Visual Studio 2013] (oder [Remote Tools für Visual Studio 2012 Update 4][Remote Tools für Visual Studio 2012 Update 4] bei Verwendung von Visual Studio 2012) installieren. Alternativ können Sie auch die Remotedebugging-Binärdateien aus einem System kopieren, auf dem Visual Studio installiert ist.
3.  Erstellen Sie ein Zertifikat, wie in [Erstellen eines Dienstzertifikats für Azure][Erstellen eines Dienstzertifikats für Azure] gezeigt. Bewahren Sie die .pfx-Datei und den RDP-Zertifikatfingerabdruck auf, und laden Sie das Zertifikat zum Zielcloud-Dienst hoch.
4.  Verwenden Sie folgende Optionen in der MSBuild-Befehlszeile, um ein Build und ein Paket mit aktiviertem Remotedebugging zu erstellen. (Aktualisieren Sie die Pfade zu Ihren System- und Projektdateien.)

    /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Veröffentlichen Sie zum Zielcloud-Dienst, indem Sie das Paket und die .cscfg-Datei verwenden, die im vorherigen Schritt generiert wurden.
6.  Importieren Sie das Zertifikat (.pfx-Datei) zum Computer, auf dem Visual Studio mit Azure SDK 2.4 installiert ist.

## <a name="virtualmachine"></a>Aktivieren von Remotedebuggen für virtuelle Computer

1.  Erstellen Sie einen virtuellen Azure-Computer. Informationen finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers] oder [Erstellen virtueller Azure-Computer in Visual Studio][Erstellen virtueller Azure-Computer in Visual Studio].
2.  Sehen Sie sich auf der [Azure-Portalseite][Azure-Portalseite] das Dashboard des virtuellen Computers an, um den RDP-Zertifikatfingerabdruck des virtuellen Computers anzuzeigen. Dies wird für den ServerThumbprint-Wert in der Erweiterungskonfiguration verwendet.
3.  Erstellen Sie ein Clientzertifikat, wie in [Erstellen eines Dienstzertifikats für Azure][Erstellen eines Dienstzertifikats für Azure] gezeigt (behalten Sie die .pfx-Datei und den RDP-Zertifikatfingerabdruck bei).
4.  Installieren Sie [Azure Powershell][Azure Powershell] (Version 0.7.4 oder höher) aus dem Microsoft Download Center.
5.  Führen Sie folgendes Skript aus, um die RemoteDebug-Erweiterung zu aktivieren. Ersetzen Sie die persönlichen Daten mit Ihren eigenen Daten, zum Beispiel Abonnementname, Dienstname und Fingerabdruck. (HINWEIS: Dieses Skript ist für Visual Studio 2013 konfiguriert. Wenn Sie Visual Studio 2012 nutzen, verwenden Sie „RemoteDebugVS2013“ für ReferenceName und ExtensionName.)

        Add-AzureAccount

        Select-AzureSubscription "My Microsoft Subscription"

        $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

        $endpoints = @(
        ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
        ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
        )

        foreach($endpoint in $endpoints)
        {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
        }

        $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
        $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
        $extensionName = "RemoteDebugVS2013"
        $version = "1.*"
        $publicConfiguration = "true56D7D1B25B472268E332F7FC0C87286458BFB6B2E7DCB00CB916C468CC3228261D6E4EE45C8ED3C63039831398"

        $vm | Set-AzureVMExtension `
        -ReferenceName $referenceName `
        -Publisher $publisher `
        -ExtensionName $extensionName `
        -Version $version `
        -PublicConfiguration $publicConfiguration

        foreach($extension in $vm.VM.ResourceExtensionReferences)
        {   
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
        $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
        break
        }
        }

        $vm | Update-AzureVM 

6.  Importieren Sie das Zertifikat (.pfx-Datei) zum Computer, auf dem Visual Studio mit Azure-SDK für .NET 2.4 installiert ist.

  [kontinuierliche Zustellung]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Aktivieren von Remotedebuggen für Cloud-Dienste]: #cloudservice
  [Aktivieren von Remotedebuggen für virtuelle Computer]: #virtualmachine
  [Befehlszeilentool für Azure]: http://msdn.microsoft.com/de-de/library/hh535755.aspx
  [Remote Tools für Visual Studio 2013]: http://www.microsoft.com/de-de/download/details.aspx?id=40781
  [Remote Tools für Visual Studio 2012 Update 4]: http://www.microsoft.com/de-de/download/details.aspx?id=38184
  [Erstellen eines Dienstzertifikats für Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Erstellen eines virtuellen Windows Server-Computers]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-windows-tutorial/
  [Erstellen virtueller Azure-Computer in Visual Studio]: http://msdn.microsoft.com/de-de/library/azure/dn569263.aspx
  [Azure-Portalseite]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [Azure Powershell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
