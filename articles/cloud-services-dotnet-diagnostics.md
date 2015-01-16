<properties urlDisplayName="Diagnostics" pageTitle="Verwenden von Diagnosen (.NET) - Azure-Featureleitfaden" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Hier erfahren Sie, wie Sie Diagnosedaten in Azure für Debugging, Leistungsmessungen, Überwachung, Datenverkehrsanalysen und mehr einsetzen." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="raynew" solutions="" manager="johndaw" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/23/2014" ms.author="raynew" />



# Aktivieren der Diagnose in Azure Cloud Services und Virtual Machines

Mit der Azure-Diagnose 1.2 können Sie Diagnosedaten von einer Worker- oder Webrolle oder einem virtuellen Computer erfassen, die bzw. der in Azure ausgeführt wird. In dieser Anleitung wird die Verwendung der Azure-Diagnose 1.2 beschrieben. Weitere ausführliche Anweisungen zum Erstellen einer Protokollierungs- und Nachverfolgungsstrategie und Verwenden von Diagnose und anderen Methoden zum Beheben von Problemen finden Sie unter [Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen][].

## Inhaltsverzeichnis

-   [Übersicht][]
-   [Aktivieren der Diagnose in einer Workerrolle][]
-   [Aktivieren der Diagnose auf einem virtuellen Computer][]
-   [Beispielkonfigurationsdatei und -schema][]
-   [Problembehandlung][]
-   [Häufig gestellte Fragen][]
-   [Vergleich zwischen Azure-Diagnose 1.0 und 1.2][]
-   [Zusätzliche Ressourcen][]

<h2><a name="overview"></a>Overview</h2>

Die Azure-Diagnose 1.2 ist eine Azure-Erweiterung, mit der Sie Telemetriedaten zur Diagnose von einer Worker- oder Webrolle oder einem virtuellen Computer erfassen können, die bzw. der in Azure ausgeführt wird. Die Telemetriedaten werden in einem Azure-Speicherkonto gespeichert und können zum Debuggen und Beheben von Fehlern, zum Messen der Leistung, zum Überwachen der Ressourcenauslastung, zum Analysieren des Datenverkehrs, zum Planen der Kapazität und zum Durchführen von Audits verwendet werden. 

Wenn Sie bisher mit Version 1.0 der Azure-Diagnose gearbeitet haben, werden Sie in Version 1.2 drei wichtige Unterschiede feststellen:

1.	Die Diagnose 1.2 kann nicht nur in Cloud-Diensten, sondern auch auf virtuellen Computern bereitgestellt werden.
2.	Die Azure-Diagnose 1.0 ist Teil des Azure-SDK und wird bei der Bereitstellung des Cloud-Diensts bereitgestellt. Die Diagnose 1.2 ist eine Erweiterung, die separat bereitgestellt wird.
3.	Mit der Diagnose 1.2 können ETW- und .NET-EventSource-Ereignisse erfasst werden.

Einen detaillierteren Vergleich finden Sie unter [Vergleich zwischen Azure-Diagnose 1.0 und 1.2][] am Ende dieses Artikels.

Mit der Azure-Diagnose können die folgenden Arten von Telemetriedaten erfasst werden:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Datenquelle</strong></td>
			<td><strong>Beschreibung</strong></td>
	</tr>
	<tr>
		<td>IIS-Protokolle</td>
		<td>Informationen zu IIS-Websites</td>            
	</tr>
	<tr>
		<td>Infrastrukturprotokolle der Azure-Diagnose</td>
		<td>Informationen zur Diagnose selbst</td>            
	</tr>
	<tr>
		<td>Protokolle zu IIS-Anforderungsfehlern	</td>
		<td>Informationen zu fehlgeschlagenen IIS-Website- oder IIS-Anwendungsanforderungen</td>            
	</tr>
	<tr>
		<td>Windows-Ereignisprotokolle</td>
		<td>An das Windows-System für die Ereignisprotokollierung gesendete Informationen</td>            
	</tr>
	<tr>
		<td>Leistungsindikatoren</td>
		<td>Leistungsindikatoren des Betriebssystems und benutzerdefinierte Leistungsindikatoren</td>            
	</tr>
	<tr>
		<td>Absturzabbilder</td>
		<td>Informationen zum Status des Prozesses im Fall eines Anwendungsabsturzes</td>            
	</tr>
	<tr>
		<td>Benutzerdefinierte Fehlerprotokolle</td>
		<td>Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle</td>            
	</tr>
	<tr>
		<td>.NET-EventSource</td>
		<td>Von Ihrem Code mit der .NET- <a href="http://msdn.microsoft.com/de-de/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource-Klasse generierte Ereignisse</a>.</td>            
	</tr>
	<tr>
		<td>Manifestbasiertes ETW</td>
		<td>Von einem beliebigen Prozess generierte ETW-Ereignisse</td>            
	</tr>
		
</tbody>
</table>

<h2><a name="worker-role"></a>Aktivieren der Diagnose in einer Workerrolle</h2>

In dieser Anleitung wird beschrieben, wie Sie eine Azure-Workerrolle implementieren, die mithilfe der .NET-EventSource-Klasse Telemetriedaten ausgibt. Die Telemetriedaten werden mit der Azure-Diagnose erfasst und in einem Azure-Speicherkonto gespeichert. Wenn Sie eine Workerrolle erstellen, aktiviert Visual Studio automatisch die Diagnose 1.0 als Teil der Projektmappe. In den folgenden Anweisungen wird beschrieben, wie Sie die Workerrolle erstellen, die Diagnose 1.0 in der Projektmappe deaktivieren und die Diagnose 1.2 in Ihrer Workerrolle bereitstellen.

<h3>Voraussetzungen</h3>
In diesem Artikel wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Visual Studio 2013 mit dem Azure-SDK verwenden. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion][] registrieren. Achten Sie darauf, dass Sie [Azure PowerShell Version 0.8.7 oder höher installieren und konfigurieren][].

<h3>Schritt 1: Erstellen einer Workerrolle</h3>
1.	Starten Sie **Visual Studio 2013**.
2.	Erstellen Sie ein neues **Windows Azure Cloud Service**-Projekt mithilfe der **Cloud**-Vorlage für .NET Framework 4.5.  Geben Sie dem Projekt den Namen "WadExample".
3.	Wählen Sie **Workerrolle** aus.
4.	Doppelklicken Sie im **Projektmappen-Explorer** auf die Eigenschaftendatei **WorkerRole1**.
5.	Deaktivieren Sie auf der Registerkarte **Konfiguration** die Option **Diagnose aktivieren**, um die Diagnose 1.0 zu deaktivieren.
6.	Erstellen Sie die Projektmappe, um zu überprüfen, ob Fehler vorliegen.

<h3>Schritt 2: Instrumentieren des Codes</h3>
Ersetzen Sie den Inhalt von "WorkerRole.cs" durch den folgenden Code. Die von der [EventSource-Klasse][] geerbte SampleEventSourceWriter-Klasse implementiert vier Protokollierungsmethoden: **SendEnums**, **MessageMethod**, **SetOther** und **HighFreq**. Der erste Parameter für die **WriteEvent**-Methode definiert die ID für das betreffende Ereignis. Die Run-Methode implementiert eine Endlosschleife, die jede der in der **SampleEventSourceWriter**-Klasse implementierten Protokollierungsmethoden alle 10 Sekunden aufruft.

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


<h3>Schritt 3: Bereitstellen der Workerrolle</h3>
1.	Stellen Sie Ihre Workerrolle von Visual Studio aus in Azure bereit. Wählen Sie dazu das Projekt **WadExample** und anschließend im Menü **Build** die Option **Veröffentlichen** aus.
2.	Wählen Sie Ihr Abonnement aus.
3.	Wählen Sie im Dialogfeld **Microsoft Azure-Veröffentlichungseinstellungen** die Option **<Neu erstellen...>** aus.
4.	Geben Sie im Dialogfeld **Cloud-Dienst und Speicherkonto erstellen** im Feld **Name** einen Namen ein (z. B. "WadExample"), und wählen Sie eine Region oder Affinitätsgruppe aus.
5.	Legen Sie **Umgebung** auf **Staging** fest.
6.	Ändern Sie unter **Einstellungen** weitere Einstellungen nach Bedarf, und klicken Sie dann auf **Veröffentlichen**.
7.	Überprüfen Sie nach Abschluss der Bereitstellung im Azure-Portal, ob sich Ihr Cloud-Dienst im Status **Wird ausgeführt** befindet.

<h3>Schritt 4: Erstellen der Diagnosekonfigurationsdatei und Installieren der Erweiterung</h3>
1.	Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl herunter:
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Fügen Sie Ihrem Projekt **WorkerRole1** eine XML-Datei hinzu. Klicken Sie dazu mit der rechten Maustaste auf das Projekt **WorkerRole1**, und wählen Sie **Hinzufügen** -> **Neues Element...** -> **Visual C#-Elemente** -> **Daten** -> **XML-Datei** aus. Nennen Sie die Datei "WadExample.xml".

	![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Ordnen Sie die Datei "WadConfig.xsd" der Konfigurationsdatei zu. Stellen Sie sicher, dass das Editorfenster mit "WadExample.xml" das aktive Fenster ist. Drücken Sie die Taste **F4**, um das Fenster **Eigenschaften** zu öffnen. Klicken Sie im Fenster **Eigenschaften** auf die Eigenschaft **Schemas**. Klicken Sie in der Eigenschaft **Schemas** auf **...**. Klicken Sie auf die Schaltfläche **Hinzufügen...**, navigieren Sie zum Speicherort der XSD-Datei, und wählen Sie die Datei "WadConfig.xsd" aus. Klicken Sie auf **OK**.
4.	Ersetzen Sie den Inhalt der Konfigurationsdatei "WadExample.xml" durch den folgenden XML-Code, und speichern Sie die Datei. Diese Konfigurationsdatei definiert ein Paar zu erfassender Leistungsindikatoren: einen für die CPU-Auslastung und einen für die Speicherauslastung. Außerdem werden in der Konfiguration vier Ereignisse definiert, die den Methoden in der SampleEventSourceWriter-Klasse entsprechen.

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

<h3>Schritt 5: Installieren der Diagnose in Ihrer Workerrolle</h3>
Für die Verwaltung der Diagnose in einer Web- oder Workerrolle werden folgende PowerShell-Cmdlets verwendet: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension und Remove-AzureServiceDiagnosticsExtension.

1.	Öffnen Sie Windows Azure PowerShell.
2.	Führen Sie das Skript zum Installieren der Diagnose in Ihrer Workerrolle aus (ersetzen Sie *StorageAccountKey* durch den Speicherkontoschlüssel für Ihr Speicherkonto "wadexample"):

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging


<h3>Schritt 6: Betrachten der Telemetriedaten</h3>
Navigieren Sie in Visual Studio im **Server-Explorer** zum Speicherkonto "wadexample". Wenn der Cloud-Dienst etwa fünf Minuten lang ausgeführt worden ist, sollten die Tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** und **WADSetOtherTable** angezeigt werden. Doppelklicken Sie auf eine der Tabellen, um die erfassten Telemetriedaten anzuzeigen.
	![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

<h2><a name="virtual-machine"></a>Aktivieren der Diagnose auf einem virtuellen Computer</h2>

In dieser Anleitung wird beschrieben, wie Sie von einem Entwicklungscomputer aus die Remoteinstallation der Diagnose auf einem virtuellen Azure-Computer durchführen. Außerdem erfahren Sie, wie Sie eine Anwendung implementieren, die auf diesem virtuellen Azure-Computer ausgeführt wird und mithilfe der .NET-[EventSource-Klasse][] Telemetriedaten ausgibt. Die Telemetriedaten werden mit der Azure-Diagnose erfasst und in einem Azure-Speicherkonto gespeichert.

<h3>Voraussetzungen</h3>
In dieser Anleitung wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Visual Studio 2013 mit dem Azure-SDK verwenden. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion][] registrieren. Achten Sie darauf, dass Sie [Azure PowerShell Version 0.8.7 oder höher installieren und konfigurieren][].

<h3>Schritt 1: Erstellen eines virtuellen Computers</h3>
1.	Starten Sie auf dem Entwicklungscomputer Visual Studio 2013.
2.	Klicken Sie im **Server-Explorer** von Visual Studio mit der rechten Maustaste auf **Windows Azure**, und wählen Sie dann **Virtueller Computer** -> **Virtuellen Computer erstellen** aus.
3.	Wählen Sie im Dialogfeld **Abonnement wählen** Ihr Azure-Abonnement aus, und klicken Sie auf **Weiter**.
4.	Wählen Sie im Dialogfeld **Image eines virtuellen Computers auswählen** die Option **Windows Server 2012 R2 Datacenter** aus, und klicken Sie auf **Weiter**.
5.	Legen Sie in **Grundlegende Einstellungen für virtuellen Computer** den Namen des virtuellen Computers auf "wadexample" fest. Legen Sie den Administratornamen und das zugehörige Kennwort fest, und klicken Sie auf **Weiter**.
6.	Erstellen Sie im Dialogfeld **Cloud-Dienst-Einstellungen** einen neuen Cloud-Dienst mit dem Namen "wadexampleVM". Erstellen Sie ein neues Speicherkonto mit dem Namen "wadexample", und klicken Sie auf **Weiter**.
7.	Klicken Sie auf **Erstellen**.

<h3>Schritt 2: Erstellen Ihrer Anwendung</h3>
1.	Starten Sie auf dem Entwicklungscomputer Visual Studio 2013.
2.	Erstellen Sie eine neue Visual C#-Konsolenanwendung für .NET Framework 4.5. Geben Sie dem Projekt den Namen "WadExampleVM".
	![CloudServices_diag_new_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	Ersetzen Sie den Inhalt von "Program.cs" durch den folgenden Code. Die **SampleEventSourceWriter**-Klasse implementiert vier Protokollierungsmethoden: **SendEnums**, **MessageMethod**, **SetOther** und **HighFreq**. Der erste Parameter für die WriteEvent-Methode definiert die ID für das betreffende Ereignis. Die Run-Methode implementiert eine Endlosschleife, die jede der in der **SampleEventSourceWriter**-Klasse implementierten Protokollierungsmethoden alle 10 Sekunden aufruft.

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


<h3>Schritt 3: Bereitstellen Ihrer Anwendung</h3>
1.	Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **WadExampleVM**, und wählen Sie **Ordner in Datei-Explorer öffnen** aus.
2.	Navigieren Sie zum Ordner *bin\Debug*, und kopieren Sie alle Dateien (WadExampleVM.*).
3.	Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Per Remotedesktop verbinden** aus.
4.	Sobald die Verbindung mit dem virtuellen Computer hergestellt ist, erstellen Sie einen Ordner mit dem Namen "WadExampleVM", und fügen Sie Ihre Anwendungsdateien in diesen Ordner ein.
5.	Starten Sie die Anwendung WadExampleVM.exe. Es sollte ein leeres Konsolenfenster angezeigt werden.

<h3>Schritt 4: Erstellen der Diagnosekonfiguration und Installieren der Erweiterung</h3>
1.	Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl auf den Entwicklungscomputer herunter:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Öffnen Sie eine neue XML-Datei in Visual Studio. Diese kann sich entweder in einem bereits geöffneten Projekt oder in einer Visual Studio-Instanz ohne geöffnete Projekte befinden. Wählen Sie in Visual Studio**Hinzufügen** -> **Neues Element...** -> **Visual C#-Elemente** -> **Daten** -> **XML-Datei** aus. Nennen Sie die Datei "WadExample.xml".
3.	Ordnen Sie die Datei "WadConfig.xsd" der Konfigurationsdatei zu. Stellen Sie sicher, dass das Editorfenster mit "WadExample.xml" das aktive Fenster ist. Drücken Sie die Taste **F4**, um das Fenster **Eigenschaften** zu öffnen. Klicken Sie im Fenster **Eigenschaften** auf die Eigenschaft **Schemas**. Klicken Sie in der Eigenschaft **Schemas** auf **...**. Klicken Sie auf die Schaltfläche **Hinzufügen...**, navigieren Sie zum Speicherort der XSD-Datei, und wählen Sie die Datei "WadConfig.xsd" aus. Klicken Sie auf **OK**.
4.	Ersetzen Sie den Inhalt der Konfigurationsdatei "WadExample.xml" durch den folgenden XML-Code, und speichern Sie die Datei. Diese Konfigurationsdatei definiert ein Paar zu erfassender Leistungsindikatoren: einen für die CPU-Auslastung und einen für die Speicherauslastung. Außerdem werden in der Konfiguration vier Ereignisse definiert, die den Methoden in der SampleEventSourceWriter-Klasse entsprechen.

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


<h3>Schritt 5: Durchführen einer Remoteinstallation der Diagnose auf dem virtuellen Azure-Computer </h3>
Für die Verwaltung der Diagnose auf einem virtuellen Computer werden folgende PowerShell-Cmdlets verwendet: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension und Remove-AzureVMDiagnosticsExtension.

1.	Öffnen Sie auf dem Entwicklungscomputer Windows Azure PowerShell.
2.	Führen Sie das Skript für die Remoteinstallation der Diagnose auf Ihrem virtuellen Computer aus (ersetzen Sie *StorageAccountKey* durch den Speicherkontoschlüssel für Ihr Speicherkonto "wadexamplevm"):

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


<h3>Schritt 6: Betrachten der Telemetriedaten</h3>
Navigieren Sie in Visual Studio im **Server-Explorer** zum Speicherkonto "wadexample". Wenn der virtuelle Computer etwa fünf Minuten lang ausgeführt worden ist, sollten die Tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** und **WADSetOtherTable** angezeigt werden. Doppelklicken Sie auf eine der Tabellen, um die erfassten Telemetriedaten anzuzeigen.
	![CloudServices_diag_wadexamplevm_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

<h2><a name="configuration-file-schema"></a>Schema der Konfigurationsdatei</h2>

Die Diagnosekonfigurationsdatei definiert Werte, die verwendet werden, um beim Start des Diagnosemonitors die Diagnosekonfigurationseinstellungen zu initialisieren. Eine Beispielkonfigurationsdatei und eine detaillierte Dokumentation zum Schema dieser Datei finden Sie hier: [Azure Diagnostics 1.2 Configuration Schema][] (Konfigurationsschema für die Azure-Diagnose 1.2, in englischer Sprache).

<h2><a name="troubleshooting"></a>Problembehandlung</h2>

<h3>Die Azure-Diagnose wird nicht gestartet.</h3>
Die Diagnose besteht aus zwei Komponenten: einem Gast-Agent-Plug-In und dem Überwachungs-Agent. Die Protokolldateien für das Gast-Agent-Plug-In befinden sich in folgender Datei: 

*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>*\CommandExecution.log

Die folgenden Fehlercodes werden von dem Plug-In zurückgegeben:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Exitcode</strong></td>
			<td><strong>Beschreibung</strong></td>
	</tr>
    <tr>
		<td>0</td>
		<td>Erfolgreich.</td>            
	</tr>
    <tr>
		<td>-1</td>
        <td>Allgemeiner Fehler.</td>		            
	</tr>
    <tr>
		<td>-2</td>
        <td><p>Die RCF-Datei konnte nicht geladen werden.</p>
<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-3</td>
        <td><p>Die Diagnosekonfigurationsdatei kann nicht geladen werden.</p>
<p>Lösung: Dier Grund hierfür ist, dass eine Konfigurationsdatei die Schemaüberprüfung nicht bestanden hat. Gelöst werden kann der Fehler durch Bereitstellung einer Konfigurationsdatei, die dem Schema entspricht.</p></td>		            
	</tr>
    <tr>
		<td>-4</td>
        <td><p>Es wird bereits eine andere Instanz des Überwachungs-Agents der Diagnose unter Verwendung des lokalen Ressourcenverzeichnisses ausgeführt.</p>
<p>Lösung: Geben Sie für <strong>LocalResourceDirectory einen anderen Wert an</strong>.</p></td>		            
	</tr>
    <tr>
		<td>-6</td>
        <td><p>Das Startprogramm für das Gast-Agent-Plug-In hat versucht, die Diagnose mit einer ungültigen Befehlszeile zu starten.</p>
<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-10</td>
        <td>Das Diagnose-Plug-In wurde mit einem Ausnahmefehler beendet.</td>		            
	</tr>
    <tr>
		<td>-11</td>
        <td><p>Der Gast-Agent konnte den für den Start und die Überwachung des Überwachungs-Agents zuständigen Prozess nicht erstellen.</p>

<p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.</p></td>		            
	</tr>
    <tr>
		<td>-101</td>
        <td><p>Ungültige Argumente beim Aufrufen des Diagnose-Plug-Ins.</p>

<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-102</td>
        <td><p>Der Plug-In-Prozess kann sich nicht selbst initialisieren.</p> 

<p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.</p></td>		            
	</tr>
    <tr>
		<td>-103</td>
        <td><p>Der Plug-In-Prozess kann sich nicht selbst initialisieren. Insbesondere kann das Protokollierungsobjekt nicht erstellt werden.</p>

<p>Lösung: Überprüfen Sie, ob genügend Systemressourcen zum Starten neuer Prozesse verfügbar sind.</p></td>		            
	</tr>
    <tr>
		<td>-104</td>
        <td><p>Die vom Gast-Agent bereitgestellte RCF-Datei konnte nicht geladen werden.</p>

<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Startprogramm für das Gast-Agent-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-105</td>
        <td><p>Das Diagnose-Plug-In kann die Diagnosekonfigurationsdatei nicht öffnen.</p>

<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-106</td>
        <td><p>Die Diagnosekonfigurationsdatei kann nicht gelesen werden.</p>

<p>Lösung: Dier Grund hierfür ist, dass eine Konfigurationsdatei die Schemaüberprüfung nicht bestanden hat. Gelöst werden kann der Fehler daher durch Bereitstellung einer Konfigurationsdatei, die dem Schema entspricht. Die XML-Datei, die an die Diagnose-Erweiterung übergeben wird, finden Sie im Ordner <i>%SystemDrive%\WindowsAzure\Config</i> auf dem virtuellen Computer. Öffnen Sie die entsprechende XML-Datei, und suchen Sie nach <strong>Microsoft.Azure.Diagnostics</strong>und dann nach dem Feld <strong>xmlCfg</strong> . Die Daten sind base64-codiert und müssen daher zunächst <a href="http://www.bing.com/search?q=base64+decoder">decodiert werden,</a> um die von der Diagnose geladenen XML-Daten anzuzeigen.</p></td>		            
	</tr>
    <tr>
		<td>-107</td>
        <td><p>Das an den Überwachungs-Agent übergebene Ressourcenverzeichnis ist ungültig.</p>

<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn der Überwachungs-Agent auf dem virtuellen Computer auf falsche Weise manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-108	</td>
        <td><p>Die Diagnosekonfigurationsdatei kann nicht in die Konfigurationsdatei des Überwachungs-Agents konvertiert werden.</p>

<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In mit einer ungültigen Konfigurationsdatei manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-110</td>
        <td><p>Allgemeiner Fehler in der Diagnosekonfiguration.</p>

<p>Dies ist ein interner Fehler, der nur auftreten sollte, wenn das Diagnose-Plug-In mit einer ungültigen Konfigurationsdatei manuell aufgerufen wird.</p></td>		            
	</tr>
    <tr>
		<td>-111</td>
        <td><p>Der Überwachungs-Agent kann nicht gestartet werden.</p>

<p>Lösung: Überprüfen Sie, ob genügend Systemressourcen verfügbar sind.</p></td>		            
	</tr>
    <tr>
		<td>-112</td>
        <td>Allgemeiner Fehler</td>		            
	</tr>    
</tbody>
</table>

<h3>Diagnosedaten werden nicht im Speicher protokolliert</h3>
Wenn Ereignisdaten fehlen, liegt das in den meisten Fällen an falsch definierten Speicherkontoinformationen. 

Lösung: Konfigurieren Sie die Diagnose-Konfigurationsdatei, und installieren Sie die Diagnose erneut.
Vor dem Hochladen von Ereignisdaten in Ihr Speicherkonto werden sie im Ordner gespeichert. Details zu **LocalResourceDirectory** finden Sie weiter oben.

Wenn dieser Ordner keine Dateien enthält, kann der Überwachungs-Agent nicht gestartet werden. Der Grund ist normalerweise eine ungültige Konfigurationsdatei, und in der Datei "CommandExecution.log" sollte es einen entsprechenden Eintrag geben. Wenn der Überwachungs-Agent erfolgreich Ereignisdaten erfasst, werden für jedes in der Konfigurationsdatei definierte Ereignis TSF-Dateien angezeigt.

Der Überwachungs-Agent protokolliert alle aufgetretenen Fehler in der Datei "MaEventTable.tsf". Um den Inhalt dieser Datei anzuzeigen, führen Sie den folgenden Befehl aus:

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.2.0.0\Monitor\x64\table2csv maeventtable.tsf

Das Tool generiert eine Datei mit dem Namen "maeventtable.csv", die Sie öffnen können, um die Protokolle auf Fehler zu prüfen.


<h2><a name="faq"></a>Häufig gestellte Fragen</h2>
Im Folgenden finden Sie einige häufig gestellte Fragen und die Antworten darauf:

**F:** Wie führe ich ein Upgrade meiner Visual Studio-Projektmappe von der Azure-Diagnose 1.0 auf die Azure-Diagnose 1.1 durch?

**A:** Das Upgrade der Visual Studio-Projektmappe von der Diagnose 1.0 auf die Diagnose 1.1 (oder höher) muss manuell durchgeführt werden:
- Deaktivieren Sie die Diagnose in Ihrer Visual Studio-Projektmappe, um zu verhindern, dass die Diagnose 1.0 mit Ihrer Rolle bereitgestellt wird.
- Wenn in Ihrem Code der Ablaufverfolgungslistener verwendet wird, müssen Sie ihn so ändern, dass .NET-EventSource verwendet wird. In der Diagnose 1.1 und höher wird der Ablaufverfolgungslistener nicht unterstützt.
- Ändern Sie den Bereitstellungsprozess, um die Erweiterung Diagnose 1.1 zu installieren.

**F:** Wenn ich die Erweiterung Diagnose 1.1 bereits in meiner Rolle oder auf meinem virtuellen Computer installiert habe, wie führe ich dann das Upgrade auf die Diagnose 1.2 durch?

**A:** Wenn Sie bei der Installation der Diagnose 1.1 "-Version "1.*"" angegeben haben, wird Ihre Rolle oder Ihr virtueller Computer beim nächsten Neustart automatisch auf die neueste Version aktualisiert, die dem regulären Ausdruck "1.*" entspricht. Wenn Sie "-Version "1.1"" angegeben haben, können Sie auf eine neue Version aktualisieren, indem Sie das Cmdlet Set- erneut ausführen und die Version angeben, die Sie installieren möchten.

**F:** Wie werden Tabellen benannt?

**A:** Tabellen werden entsprechend dem folgenden Code benannt:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Beispiel:

		<EtwEventSourceProviderConfiguration provider="prov1">
		  <Event id="1" />
		  <Event id="2" eventDestination="dest1" />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider="prov2">
		  <DefaultEvents eventDestination="dest2" />
		</EtwEventSourceProviderConfiguration>

Damit werden vier Tabellen generiert:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Ereignis</strong></td>
			<td><strong>Tabellenname</strong></td>			
	</tr>
	<tr>
			<td>provider="prov1" &lt;Event id="1" /&gt;</td>
			<td>WADEvent+MD5("prov1")+"1"</td>			
	</tr>
	<tr>
			<td>provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt;</td>
			<td>WADdest1</td>			
	</tr>
	<tr>
			<td>provider="prov1" &lt;DefaultEvents /&gt;</td>
			<td>WADDefault+MD5("prov1")</td>			
	</tr>
	<tr>
			<td>provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt;</td>
			<td>WADdest2</td>			
	</tr>
	

</table>
</tbody>

<h2><a name="comparing"></a>Vergleich zwischen Azure-Diagnose 1.0 und 1.2</h2>

In der folgenden Tabelle werden die Features gegenübergestellt, die von den Versionen 1.0 und 1.1/1.2 der Azure-Diagnose jeweils unterstützt werden:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Unterstützte Rollentypen</strong></td>
			<td><strong>Diagnose 1.0</strong></td>
			<td><strong>Diagnose 1.1/1.2</strong></td>
	</tr>

	<tr>
			<td>Webrolle</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Workerrolle</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>IaaS</td>
			<td>Nein</td>
			<td>Ja</td>
	</tr>
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Konfiguration und Bereitstellung</strong></td>
			<td><strong>Diagnose 1.0</strong></td>
			<td><strong>Diagnose 1.1/1.2</strong></td>
	</tr>

	<tr>
			<td>Integration mit Visual Studio - integriert in die Entwicklung von Azure-Web-/-Workerrolle.</td>
			<td>Ja</td>
			<td>Nein</td>
	</tr>
	<tr>
			<td>PowerShell-Skripts - Skripts zum Verwalten der Installation und Konfiguration der Diagnose in der Rolle.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Datenquelle</strong></td>
			<td><strong>Standardsammlung</strong></td>
			<td><strong>Format</strong></td>
			<td><strong>Beschreibung</strong></td>
			<td><strong>Diagnose 1.0</strong></td>
			<td><strong>Diagnose 1.1/1.2</strong></td>
	</tr>
	<tr>
			<td>Protokolle von System.Diagnostics.Trace</td>
			<td>Ja</td>
			<td>Tabelle</td>
			<td>Protokollieren Ablaufverfolgungsmeldungen, die vom Code an den Ablaufverfolgungslistener gesendet werden (ein Ablaufverfolgungslistener muss der Datei "web.config" oder "app.config" hinzugefügt werden). Protokolldaten werden in dem mit "scheduledTransferPeriod" angegebenen Übertragungsintervall in die WADLogsTable-Speichertabelle übertragen.</td>
			<td>Ja</td>
			<td>Nein (EventSource verwenden)</td>
	</tr>
	<tr>
			<td>IIS-Protokolle</td>
			<td>Ja</td>
			<td>Blob</td>
			<td>Protokollieren Informationen zu IIS-Websites. Protokolldaten werden in dem mit "scheduledTransferPeriod" angegebenen Übertragungsintervall in den von Ihnen angegebenen Container übertragen.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Infrastrukturprotokolle der Azure-Diagnose</td>
			<td>Ja</td>
			<td>Tabelle</td>
			<td>Protokolliert Informationen zur Diagnoseinfrastruktur und zu den Modulen RemoteAccess und RemoteForwarder. Protokolldaten werden in dem mit "scheduledTransferPeriodtransfer" angegebenen Intervall in die WADDiagnosticInfrastructureLogsTable-Speichertabelle übertragen.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Protokolle zu IIS-Anforderungsfehlern</td>
			<td>Nein</td>
			<td>Blob</td>
			<td>Protokollieren Informationen zu fehlgeschlagenen IIS-Website- oder IIS-Anwendungsanforderungen. Zur Aktivierung müssen Sie auch die Nachverfolgungsoptionen unter "system.WebServer" in "Web.config" festlegen. Protokolldaten werden in dem mit "scheduledTransferPeriod" angegebenen Übertragungsintervall in den von Ihnen angegebenen Container übertragen.</td>
			<td>Ja</td>
			<td>Yes</td>
	</tr>
	<tr>
			<td>Windows-Ereignisprotokolle</td>
			<td>Nein</td>
			<td>Tabelle</td>
			<td>Protokollieren Informationen, die Aufschluss über die Leistung von Betriebssystem, Anwendung oder Treiber geben. Leistungsindikatoren müssen explizit festgelegt werden. Falls sie hinzugefügt werden, werden Leistungsindikatordaten in dem mit "scheduledTransferPeriod" angegebenen Übertragungsintervall in die WADPerformanceCountersTable-Speichertabelle übertragen.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Leistungsindikatoren</td>
			<td>Nein</td>
			<td>Tabelle</td>
			<td>Protokollieren Informationen, die Aufschluss über die Leistung von Betriebssystem, Anwendung oder Treiber geben. Leistungsindikatoren müssen explizit festgelegt werden. Falls sie hinzugefügt werden, werden Leistungsindikatordaten in dem mit "scheduledTransferPeriod" angegebenen Übertragungsintervall in die WADPerformanceCountersTable-Speichertabelle übertragen.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Absturzabbilder</td>
			<td>Nein</td>
			<td>Blob</td>
			<td>Protokollieren Informationen zum Betriebssystemstatus im Fall eines Systemabsturzes. Miniabsturzabbilder werden lokal gesammelt. Vollständige Absturzabbilder können aktiviert werden. Protokolldaten werden in dem mit "scheduledTransferPeriod" angegebenen Übertragungsintervall in den von Ihnen angegebenen Container übertragen. Da die meisten Ausnahmen durch ASP.NET behandelt werden, sind diese Protokolle im Allgemeinen nur für eine Workerrolle oder einen virtuellen Computer von Nutzen.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Benutzerdefinierte Fehlerprotokolle</td>
			<td>Nein</td>
			<td>Blob</td>
			<td>Durch die Verwendung lokaler Speicherressourcen können benutzerdefinierte Daten protokolliert und sofort in den angegebenen Container übertragen werden.</td>
			<td>Ja</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>EventSource</td>
			<td>Nein</td>
			<td>Tabelle</td>
			<td>Protokolliert von Ihrem Code mit der .NET-EventSource-Klasse generierte Ereignisse.</td>
			<td>Nein</td>
			<td>Ja</td>
	</tr>
	<tr>
			<td>Manifestbasiertes ETW</td>
			<td>Nein</td>
			<td>Tabelle</td>
			<td>Von einem beliebigen Prozess generierte ETW-Ereignisse</td>
			<td>Nein</td>
			<td>Ja</td>
	</tr>
</tbody>
</table>

<h2><a name="additional"></a>Zusätzliche Ressourcen</h2>

- [Troubleshooting Best Practices for Developing Azure Applications][] (Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen, in englischer Sprache)
- [Sammeln von Protokollierungsdaten mit der Azure-Diagnose][]
- [Debuggen von Azure-Anwendungen][]
- [Konfigurieren der Azure-Diagnose][]

  

[Übersicht]: #overview
[Aktivieren der Diagnose in einer Workerrolle]: #worker-role
[Aktivieren der Diagnose auf einem virtuellen Computer]: #virtual-machine
[Beispielkonfigurationsdatei und -schema]: #configuration-file-schema
[Problembehandlung]: #troubleshooting
[Häufig gestellte Fragen]: #faq
[Vergleich zwischen Azure-Diagnose 1.0 und 1.2]: #comparing
[Zusätzliche Ressourcen]: #additional
[EventSource-Klasse]: http://msdn.microsoft.com/de-de/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Konfigurieren der Azure-Diagnose]: http://msdn.microsoft.com/de-de/library/windowsazure/dn186185.aspx
[Debuggen von Azure-Anwendungen]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405479.aspx   
[Sammeln von Protokollierungsdaten mit der Azure-Diagnose]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433048.aspx
[Troubleshooting Best Practices for Developing Azure Applications] (Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen, in englischer Sprache): http://msdn.microsoft.com/de-de/library/windowsazure/hh771389.aspx
[Kostenlose Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
[Installieren und Konfigurierenvon Azure PowerShell Version 0.8.7 oder höher]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/
[Azure Diagnostics 1.2 Configuration Schema] (Konfigurationsschema für die Azure-Diagnose 1.2, in englischer Sprache).: http://msdn.microsoft.com/de-de/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/de-de/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/de-de/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/de-de/library/dn495168.aspx

<!--HONumber=35.1-->
