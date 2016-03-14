<properties
	pageTitle="Gewusst wie: Verwenden von Azure-Diagnose in Virtual Machines | Microsoft Azure"
	description="Verwenden von Azure-Diagnose zum Sammeln von Daten aus Azure Virtual Machines für Fehlerbehebung, Leistungsmessung, Überwachung, Datenverkehrsanalysen und mehr."
	services="virtual-machines"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>



# Aktivieren der Diagnose in Azure Virtual Machines

Hintergrundinformationen über Azure-Diagnose finden Sie unter [Übersicht über Azure-Diagnose](azure-diagnostics.md).

## Aktivieren der Diagnose auf einem virtuellen Computer

In dieser Anleitung wird beschrieben, wie Sie von einem Entwicklungscomputer aus die Remoteinstallation der Diagnose auf einem virtuellen Azure-Computer durchführen. Außerdem erfahren Sie, wie Sie eine Anwendung implementieren, die auf diesem virtuellen Azure-Computer ausgeführt wird und mithilfe der .NET-[EventSource-Klasse][] Telemetriedaten ausgibt. Die Telemetriedaten werden mit der Azure-Diagnose erfasst und in einem Azure-Speicherkonto gespeichert.

### Voraussetzungen
In dieser Anleitung wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Visual Studio 2013 mit dem Azure-SDK verwenden. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion][] registrieren. [Installieren und konfigurieren Sie Azure PowerShell Version 0.8.7 oder höher][].

### Schritt 1: Erstellen eines virtuellen Computers
1.	Starten Sie auf dem Entwicklungscomputer Visual Studio 2013.
2.	Erweitern Sie in Visual Studio im **Server-Explorer** **Azure**, klicken Sie mit der rechten Maustaste auf **Virtuelle Computer**, und wählen Sie dann **Virtuellen Computer erstellen** aus.
3.	Wählen Sie im Dialogfeld **Abonnement wählen** Ihr Azure-Abonnement aus, und klicken Sie auf **Weiter**.
4.	Wählen Sie im Dialogfeld **Image eines virtuellen Computers auswählen** die Option **Windows Server 2012 R2 Datacenter, November 2014** aus, und klicken Sie auf **Weiter**.
5.	Legen Sie unter **Grundlegende Einstellungen für virtuellen Computer** den Namen des virtuellen Computers auf "wadexample" fest. Legen Sie den Administratornamen und das zugehörige Kennwort fest, und klicken Sie auf **Weiter**.
6.	Erstellen Sie im Dialogfeld **Clouddienst-Einstellungen** einen neuen Clouddienst mit dem Namen "wadexampleVM". Erstellen Sie ein neues Speicherkonto mit dem Namen "wadexample", und klicken Sie auf **Weiter**.
7.	Klicken Sie auf **Erstellen**.

### Schritt 2: Erstellen der Anwendung
1.	Starten Sie auf dem Entwicklungscomputer Visual Studio 2013.
2.	Erstellen Sie eine neue Visual C#-Konsolenanwendung für .NET Framework 4.5. Geben Sie dem Projekt den Namen "WadExampleVM". ![CloudServices\_diag\_new\_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.	Ersetzen Sie den Inhalt von "Program.cs" durch den folgenden Code. Die **SampleEventSourceWriter**-Klasse implementiert vier Protokollierungsmethoden: **SendEnums**, **MessageMethod**, **SetOther** und **HighFreq**. Der erste Parameter für die WriteEvent-Methode definiert die ID für das betreffende Ereignis. Die Run-Methode implementiert eine Endlosschleife, die jede der in der **SampleEventSourceWriter**-Klasse implementierten Protokollierungsmethoden alle 10 Sekunden aufruft.

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	Speichern Sie die Datei, und wählen Sie im Menü **Build** die Option **Projektmappe erstellen** aus, um den Code zu erstellen.


### Schritt 3. Bereitstellen der Anwendung
1.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **WadExampleVM**, und wählen Sie **Ordner in Datei-Explorer öffnen** aus.
2.	Navigieren Sie zum Ordner *bin\\Debug*, und kopieren Sie alle Dateien (WadExampleVM.*).
3.	Klicken Sie im Server-Explorer mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Per Remotedesktop verbinden** aus.
4.	Sobald die Verbindung mit dem virtuellen Computer hergestellt ist, erstellen Sie einen Ordner mit dem Namen "WadExampleVM", und fügen Sie Ihre Anwendungsdateien in diesen Ordner ein.
5.	Starten Sie die Anwendung WadExampleVM.exe. Es sollte ein leeres Konsolenfenster angezeigt werden.

### Schritt 4: Erstellen der Diagnosekonfiguration und Installieren der Erweiterung
1.	Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl auf den Entwicklungscomputer herunter:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.	Öffnen Sie eine neue XML-Datei in Visual Studio. Diese kann sich entweder in einem bereits geöffneten Projekt oder in einer Visual Studio-Instanz ohne geöffnete Projekte befinden. Wählen Sie in Visual Studio **Hinzufügen** -> **Neues Element…** -> **Visual C#-Elemente** -> **Daten** -> **XML-Datei** aus. Nennen Sie die Datei "WadExample.xml".
3.	Ordnen Sie die Datei "WadConfig.xsd" der Konfigurationsdatei zu. Stellen Sie sicher, dass das Editorfenster mit "WadExample.xml" das aktive Fenster ist. Drücken Sie die Taste **F4**, um das Fenster **Eigenschaften** zu öffnen. Klicken Sie im Fenster **Eigenschaften** auf die Eigenschaft **Schemas**. Klicken Sie in der Eigenschaft **Schemas** auf **…**. Klicken Sie auf die Schaltfläche **Hinzufügen…**, navigieren Sie zum Speicherort der XSD-Datei, und wählen Sie die Datei "WadConfig.xsd" aus. Klicken Sie auf **OK**.
4.	Ersetzen Sie den Inhalt der Konfigurationsdatei "WadExample.xml" durch den folgenden XML-Code, und speichern Sie die Datei. Diese Konfigurationsdatei definiert ein Paar zu erfassender Leistungsindikatoren: einen für die CPU-Auslastung und einen für die Speicherauslastung. Außerdem werden in der Konfiguration vier Ereignisse definiert, die den Methoden in der SampleEventSourceWriter-Klasse entsprechen.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### Schritt 5: Durchführen einer Remoteinstallation von Diagnose auf dem virtuellen Azure-Computer
Für die Verwaltung von Diagnose auf einem virtuellen Computer werden folgende PowerShell-Cmdlets verwendet: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension und Remove-AzureVMDiagnosticsExtension.

1.	Öffnen Sie auf dem Entwicklungscomputer Azure PowerShell.
2.	Führen Sie das Skript für die Remoteinstallation der Diagnose auf Ihrem virtuellen Computer aus (ersetzen Sie *StorageAccountKey* durch den Speicherkontoschlüssel für Ihr Speicherkonto "wadexamplevm"):

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### Schritt 6: Betrachten der Telemetriedaten
Navigieren Sie in Visual Studio im Server-Explorer zum Speicherkonto "wadexample". Wenn der virtuelle Computer etwa fünf Minuten lang ausgeführt worden ist, sollten die Tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** und **WADSetOtherTable** angezeigt werden. Doppelklicken Sie auf eine der Tabellen, um die erfassten Telemetriedaten anzuzeigen.

![CloudServices\_diag\_wadexamplevm\_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## Schema der Konfigurationsdatei

Die Diagnosekonfigurationsdatei definiert Werte, die verwendet werden, um beim Start des Diagnoseagenten die Diagnosekonfigurationseinstellungen zu initialisieren. Gültige Werte und Beispiele finden Sie unter [Azure-Diagnose-Konfigurationsschemas](https://msdn.microsoft.com/library/azure/mt634524.aspx).

## Problembehandlung

Weitere Informationen finden Sie unter [Problembehandlung von Azure-Diagnose](azure-diagnostics-troubleshooting.md).


## Nächste Schritte
In der [Liste von Artikeln zu Azure-Diagnose im Zusammenhang mit virtuellen Computern](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) finden Sie eine Anleitung zum Ändern erfasster Daten und zur Fehlerbehebung und erfahren mehr über Diagnose im Allgemeinen.


[EventSource-Klasse]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[kostenlose Testversion]: http://azure.microsoft.com/pricing/free-trial/
[Installieren und konfigurieren Sie Azure PowerShell Version 0.8.7 oder höher]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->