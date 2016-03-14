<properties
	pageTitle="Verwenden der Azure-Diagnose (.NET) mit Cloud Services | Microsoft Azure"
	description="Verwenden von Azure-Diagnose zum Sammeln von Daten von Azure Cloud Services für Fehlerbehebung, Leistungsmessung, Überwachung, Datenverkehrsanalysen und mehr."
	services="cloud-services"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="robb"/>



# Aktivieren der Azure-Diagnose in Azure Cloud Services

Hintergrundinformationen über Azure-Diagnose finden Sie unter [Übersicht über Azure-Diagnose](../azure-diagnostics.md).


## Aktivieren der Diagnose in einer Workerrolle

In dieser Anleitung wird beschrieben, wie Sie eine Azure-Workerrolle implementieren, die mithilfe der .NET-EventSource-Klasse Telemetriedaten ausgibt. Die Telemetriedaten werden mit der Azure-Diagnose erfasst und in einem Azure-Speicherkonto gespeichert. Wenn Sie eine Workerrolle erstellen, aktiviert Visual Studio automatisch die Diagnose 1.0 als Teil der Projektmappe in Azure SDKs für .NET 2.4 und niedriger. In den folgenden Anweisungen wird beschrieben, wie Sie die Workerrolle erstellen, die Diagnose 1.0 in der Projektmappe deaktivieren und die Diagnose 1.2 oder 1.3 in Ihrer Workerrolle bereitstellen.

### Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Visual Studio 2013 mit dem Azure-SDK verwenden. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion][] registrieren. [Installieren und konfigurieren Sie Azure PowerShell Version 0.8.7 oder höher][].

### Schritt 1: Erstellen einer Workerrolle
1.	Starten Sie **Visual Studio 2013**.
2.	Erstellen Sie ein neues **Azure Cloud Service**-Projekt mithilfe der **Cloudvorlage** für .NET Framework 4.5. Geben Sie dem Projekt den Namen "WadExample", und klicken Sie auf "OK".
3.	Wählen Sie **Workerrolle** aus, und klicken Sie auf "OK". Das Projekt wird erstellt.
4.	Doppelklicken Sie im Projektmappen-Explorer auf die Eigenschaftendatei **WorkerRole1**.
5.	Deaktivieren Sie auf der Registerkarte **Konfiguration** die Option **Diagnose aktivieren**, um Diagnose 1.0 (Azure SDK 2.4 und niedriger) zu deaktivieren.
6.	Erstellen Sie die Projektmappe, um zu überprüfen, ob Fehler vorliegen.

### Schritt 2: Instrumentieren des Codes
Ersetzen Sie den Inhalt von "WorkerRole.cs" durch den folgenden Code. Die von der [EventSource-Klasse][] geerbte SampleEventSourceWriter-Klasse implementiert vier Protokollierungsmethoden: **SendEnums**, **MessageMethod**, **SetOther** und **HighFreq**. Der erste Parameter für die **WriteEvent**-Methode definiert die ID für das betreffende Ereignis. Die Run-Methode implementiert eine Endlosschleife, die jede der in der **SampleEventSourceWriter**-Klasse implementierten Protokollierungsmethoden alle 10 Sekunden aufruft.

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
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

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

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

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


### Schritt 3: Bereitstellen der Workerrolle
1.	Stellen Sie Ihre Workerrolle von Visual Studio aus in Azure bereit. Wählen Sie dazu das Projekt **WadExample** aus, und klicken Sie anschließend im Menü **Build** auf **Veröffentlichen**.
2.	Wählen Sie Ihr Abonnement aus.
3.	Wählen Sie im Dialogfeld **Microsoft Azure-Veröffentlichungseinstellungen** die Option **Neu erstellen...** aus.
4.	Geben Sie im Dialogfeld **Clouddienst und Speicherkonto erstellen** im Feld **Name** einen Namen ein (z. B. "WadExample"), und wählen Sie eine Region oder Affinitätsgruppe aus.
5.	Legen Sie **Umgebung** auf **Staging** fest.
6.	Ändern Sie unter **Einstellungen** weitere Einstellungen nach Bedarf, und klicken Sie dann auf **Veröffentlichen**.
7.	Überprüfen Sie nach Abschluss der Bereitstellung im klassischen Azure-Portal, ob Ihr Clouddienst den Status **Wird ausgeführt** hat.

### Schritt 4: Erstellen der Diagnosekonfigurationsdatei und Installieren der Erweiterung
1.	Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl herunter: 2. (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.	Fügen Sie Ihrem Projekt **WorkerRole1** eine XML-Datei hinzu. Klicken Sie dazu mit der rechten Maustaste auf das Projekt **WorkerRole1**, und wählen Sie **Hinzufügen** -> **Neues Element…** -> **Visual C#-Elemente** -> **Daten** -> **XML-Datei** aus. Nennen Sie die Datei "WadExample.xml".

	![CloudServices\_diag\_add\_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

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

### Schritt 5: Installieren von Diagnose für die Workerrolle
Für die Verwaltung von Diagnose in einer Web- oder Workerrolle werden folgende PowerShell-Cmdlets verwendet: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension und Remove-AzureServiceDiagnosticsExtension.

1.	Öffnen Sie Azure PowerShell.
2.	Führen Sie das Skript zum Installieren der Diagnose in Ihrer Workerrolle aus (ersetzen Sie *StorageAccountKey* durch den Speicherkontoschlüssel für Ihr Speicherkonto "wadexample"):

```
	$storage_name = "wadexample"
	$key = "<StorageAccountKey>"
	$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
	$service_name="wadexample"
	$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
	Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### Schritt 6: Betrachten der Telemetriedaten
Navigieren Sie in Visual Studio im Server-Explorer zum Speicherkonto "wadexample". Wenn der Cloud-Dienst etwa fünf Minuten lang ausgeführt worden ist, sollten die Tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** und **WADSetOtherTable** angezeigt werden. Doppelklicken Sie auf eine der Tabellen, um die erfassten Telemetriedaten anzuzeigen. ![CloudServices\_diag\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)


## Schema der Konfigurationsdatei

Die Diagnosekonfigurationsdatei definiert Werte, die verwendet werden, um beim Start des Diagnoseagenten die Diagnosekonfigurationseinstellungen zu initialisieren. Gültige Werte und Beispiele finden Sie unter [Azure-Diagnose-Konfigurationsschemas](https://msdn.microsoft.com/library/azure/mt634524.aspx).

## Problembehandlung

Unter [Problembehandlung bei Azure-Diagnose](../azure-diagnostics-troubleshooting.md) finden Sie Informationen zur Behebung von häufig auftretenden Problemen.

## Nächste Schritte
In der [Liste von Artikeln zu Azure-Diagnose im Zusammenhang mit virtuellen Computern](azure-diagnostics.md#cloud-services) finden Sie eine Anleitung zum Ändern erfasster Daten und zur Fehlerbehebung und erfahren mehr über Diagnose im Allgemeinen.


[EventSource-Klasse]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[kostenlose Testversion]: http://azure.microsoft.com/pricing/free-trial/
[Installieren und konfigurieren Sie Azure PowerShell Version 0.8.7 oder höher]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->