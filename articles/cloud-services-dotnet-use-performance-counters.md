<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Verwenden von Leistungsindikatoren in Azure
===========================================

Sie können in einer Azure-Anwendung Leistungsindikatoren verwenden, um Daten zu sammeln, mit deren Hilfe Systemengpässe ermittelt und die System- sowie Anwendungsleistung optimiert werden können. In Windows Server 2008, Windows Server 2012, IIS und ASP.NET verfügbare Leistungsindikatoren können erfasst und verwendet werden, um die Intaktheit der Azure-Anwendung zu ermitteln.

In diesem Thema wird erläutert, wie Leistungsindikatoren in Ihrer Anwendung mithilfe der Konfigurationsdatei diagnostics.wadcfg aktiviert werden. Informationen zum Überwachen der Leistung Ihrer Anwendung im [Azure-Verwaltungsportal](http://manage.windowsazure.com) finden Sie unter [Überwachen von Cloud-Diensten](https://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-monitor-a-cloud-service/). Weitere ausführliche Anweisungen zum Erstellen einer Protokollierungs- und Nachverfolgungsstrategie und zum Verwenden von Diagnose- und anderen Methoden zum Beheben von Problemen und Optimieren von Azure-Anwendungen finden Sie unter [Troubleshooting Best Practices for Developing Azure Applications](http://msdn.microsoft.com/de-de/library/windowsazure/hh771389.aspx) (Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen, in englischer Sprache).

Die vorliegende Aufgabe umfasst die folgenden Schritte:

-   [Voraussetzungen](#prereqs)
-   [Schritt 1: Sammeln und Speichern von Daten der Leistungsindikatoren](#step1)
-   [Schritt 2: (Optional) Erstellen benutzerdefinierter Leistungsindikatoren](#step2)
-   [Schritt 3: Abfragen von Leistungsindikatordaten](#step3)
-   [Nächste Schritte](#nextsteps)
-   [Zusätzliche Ressourcen](#additional)

Voraussetzungen
---------------

In diesem Artikel wird davon ausgegangen, dass Sie den Diagnosemonitor in die Anwendung importiert und die Konfigurationsdatei diagnostics.wadcfg der Visual Studio-Projektmappe hinzugefügt haben. Weitere Informationen finden Sie unter den Schritten 1 und 2 in [Aktivieren der Diagnose in Azure](https://www.windowsazure.com/de-de/develop/net/common-tasks/diagnostics/).

Schritt 1: Sammeln und Speichern von Daten der Leistungsindikatoren
-------------------------------------------------------------------

Nachdem Sie der Visual Studio-Projektmappe die Datei diagnostics.wadcfg hinzugefügt haben, können Sie die Sammlung und Speicherung von Leistungsindikatordaten in einer Azure-Anwendung konfigurieren. Dazu werden der Datei diagnostics.wadcfg Leistungsindikatoren hinzugefügt. Die Diagnosedaten einschließlich der Leistungsindikatoren werden zuerst in der Instanz gesammelt. Die Daten werden dann in der Tabelle **WADPerformanceCountersTable** im Azure-Tabellendienst gespeichert, sodass Sie in Ihrer Anwendung auch das Speicherkonto angeben müssen. Wenn Sie Ihre Anwendung im Serveremulator lokal testen, können Sie die Diagnosedaten auch lokal im Speicheremulator speichern. Bevor Sie die Diagnosedaten speichern, müssen Sie das [Azure-Verwaltungsportal](http://manage.windowsazure.com) aufrufen und ein Speicherkonto erstellen. Nach Möglichkeit sollten Sie Ihr Speicherkonto am selben geografischen Standort wie Ihre Azure-Anwendung platzieren, um Kosten für externe Bandbreite zu vermeiden und die Latenz zu verringern.

### Hinzufügen von Leistungsindikatoren zur Datei diagnostics.wadcfg

Es gibt zahlreiche Leistungsindikatoren, die Sie erfassen können. Das folgende Beispiel zeigt verschiedene Leistungsindikatoren, die für die Überwachung von Web- und Workerrollen empfohlen werden.

Öffnen Sie die Datei diagnostics.wadcfg, und fügen Sie dem Element **DiagnosticMonitorConfiguration** Folgendes hinzu:

		<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />
        <!-- Verwenden der Indikatoren der Kategorie Process(w3wp) in einer Webrolle -->
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />
        <!-- Verwenden der Indikatoren der Kategorie Process(WaWorkerHost) in einer Workerrolle.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" /> 
		--\>
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
        </PerformanceCounters>    

Das Attribut **bufferQuotaInMB** gibt die maximale Größe des Dateisystemspeichers an, der für den Datensammlungstyp (Azure-Protokolle, IIS-Protokolle usw.) zur Verfügung steht. Der Standardwert ist 0. Wenn das Kontingent erreicht ist, werden die ältesten Daten gelöscht, sobald neue Daten hinzugefügt werden. Die Summe aller **bufferQuotaInMB**-Eigenschaften müssen größer sein als der Wert des Attributs **OverallQuotaInMB**. Ausführlichere Informationen dazu, wie Sie feststellen, wie viel Speicher für die Sammlung von Diagnosedaten erforderlich ist, finden Sie im Abschnitt "Setup WAD" im Artikel [Troubleshooting Best Practices for Developing Azure Applications](http://msdn.microsoft.com/de-de/library/windowsazure/hh771389.aspx) (Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen, in englischer Sprache).

Das Attribut **scheduledTransferPeriod** gibt das auf die nächste Minute gerundete Intervall zwischen geplanten Datenübertragungen an. In den folgenden Beispielen ist es auf PT30M (30 Minuten) festgelegt. Wenn das Übertragungsintervall auf einen kleinen Wert wie z. B. 1 Minute festgelegt wird, hat dies in der Produktion negative Auswirkungen auf die Anwendungsleistung, kann jedoch beim Testen nützlich sein, um festzustellen, ob die Diagnose schnell arbeitet. Das geplante Übertragungsintervall muss so klein sein, dass Diagnosedaten in der Instanz nicht überschrieben werden, andererseits aber auch so groß, dass die Leistung der Anwendung nicht beeinträchtigt wird.

Das Attribut **counterSpecifier** gibt den zu erfassenden Leistungsindikator an.

Das Attribut **sampleRate** gibt die Rate an, mit der der Leistungsindikator gemessen werden soll, in diesem Fall 30 Sekunden.

Nachdem Sie die zu erfassenden Leistungsindikatoren hinzugefügt haben, speichern Sie die Änderungen in der Datei diagnostics.wadcfg. Anschließend müssen Sie das Speicherkonto angeben, in dem die Diagnosedaten gespeichert werden.

### Angeben des Speicherkontos

Um die Diagnoseinformationen in Ihrem Azure-Speicherkonto abzulegen, müssen Sie in der Dienstkonfigurationsdatei (ServiceConfiguration.cscfg) eine Verbindungszeichenfolge angeben. Azure-Tools für Visual Studio Version 1.4 (August 2011) und höher lassen unterschiedliche Konfigurationsdateien (ServiceConfiguration.cscfg) für lokale und Cloud-Ausführung zu. Mehrere Dienstkonfigurationen sind hilfreich für die Diagnose, da Sie den Speicheremulator für die kostenlose lokale Prüfung verwenden und zugleich eine separate Konfigurationsdatei für die Produktion vorhalten können.

So legen Sie die Verbindungszeichenfolgen fest:

1.  Öffnen Sie die Datei ServiceConfiguration.Cloud.cscfg mit Ihrem bevorzugten Texteditor, und legen Sie die Verbindungszeichenfolge für Ihr Speicherkonto fest:

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    Die Werte für **AccountName** und **AccountKey** finden Sie im Verwaltungsportal im Speicherkonto-Dashboard unter **Manage Keys**.

2.  Speichern Sie die Datei ServiceConfiguration.Cloud.cscfg.
3.  Öffnen Sie die Datei ServiceConfiguration.Local.cscfg, und prüfen Sie, ob **UseDevelopmentStorage** auf "true" (Standardeinstellung) eingestellt ist.

        <ConfigurationSettings>
			<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>

    Nachdem die Verbindungszeichenfolgen festgelegt sind, speichert Ihre Anwendung Diagnosedaten in Ihrem Speicherkonto, wenn die Anwendung bereitgestellt wird.

4.  Speichern und Erstellen Sie das Projekt, und stellen Sie danach Ihre Anwendung bereit.

Schritt 2: (Optional) Erstellen benutzerdefinierter Leistungsindikatoren
------------------------------------------------------------------------

Neben den vordefinierten Leistungsindikatoren können Sie Ihre eigenen benutzerdefinierten Leistungsindikator hinzufügen, um Web- oder Workerrollen zu überwachen. Mit benutzerdefinierten Leistungsindikatoren können Sie anwendungsspezifisches Verhalten verfolgen und überwachen. Diese Indikatoren können in einer Startaufgabe, einer Webrolle oder einer Workerrolle mit erhöhten Rechten erstellt oder gelöscht werden.

Führen Sie die folgenden Schritte aus, um einen einfachen benutzerdefinierten Leistungsindikator mit dem Namen "\\MyCustomCounterCategory\\MyButton1Counter" zu erstellen:

1.  Öffnen Sie die Dienstdefinitionsdatei (CSDEF) für Ihre Anwendung.
2.  Fügen Sie das Element **Runtime** dem Element **WebRole** oder **WorkerRole** hinzu, um eine Ausführung mit erhöhten Rechten zuzulassen:

        <Runtime executionContext="elevated" />

3.  Speichern Sie die Datei.
4.  Öffnen Sie die Datei diagnostics.wadcfg, und fügen Sie dem Element **DiagnosticMonitorConfiguration** Folgendes hinzu:

        <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
        </PerformanceCounters>        

5.  Speichern Sie die Datei.
6.  Erstellen Sie die benutzerdefinierte Leistungsindikatorkategorie in der **OnStart**-Methode Ihrer Rolle, bevor **base.OnStart** aufgerufen wird. Mit dem folgenden C\#-Beispiel wird eine benutzerdefinierte Kategorie erstellt, falls noch keine vorhanden ist:

        public override bool OnStart()
        {
        if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
        {
           CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

           // Indikator hinzufügen, der Benutzerklicks auf button1 erfasst
           CounterCreationData operationTotal1 = new CounterCreationData();
           operationTotal1.CounterName = "MyButton1Counter";
           operationTotal1.CounterHelp = "Mein benutzerdefinierter Indikator für Button1";
           operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
           counterCollection.Add(operationTotal1);

           PerformanceCounterCategory.Create(
             "MyCustomCounterCategory",
             "Meine benutzerdefinierte Indikatorkategorie",
             PerformanceCounterCategoryType.SingleInstance, counterCollection);

           Trace.WriteLine("Benutzerdefinierte Indikatorkategorie erstellt.");
        }
        else{
           Trace.WriteLine("Benutzerdefinierte Indikatorkategorie existiert bereits.");
        }

        return base.OnStart();
        }

7.  Aktualisieren Sie die Indikatoren in Ihrer Anwendung. Mit dem folgenden Beispiel wird ein benutzerdefinierter Leistungsindikator bei **Button1\_Click**-Ereignissen aktualisiert:

        protected void Button1_Click(object sender, EventArgs e)
        {
           PerformanceCounter button1Counter = new PerformanceCounter(
              "MyCustomCounterCategory",
              "MyButton1Counter",
              string.Empty,
              false);

           button1Counter.Increment();
           this.Button1.Text = "Anzahl Button 1: " +
              button1Counter.RawValue.ToString();
        }

8.  Speichern Sie die Datei.

Wenn diese Schritte abgeschlossen sind, werden Daten des benutzerdefinierten Leistungsindikators vom Azure-Diagnosemonitor erfasst.

Schritt 3: Abfragen von Leistungsindikatordaten
-----------------------------------------------

Wenn Ihre Anwendung bereitgestellt ist und ausgeführt wird, beginnt der Diagnosemonitor mit dem Erfassen von Leistungsindikatoren und dem Speichern dieser Daten im Azure-Speicher. Mit Tools wie **Server-Explorer in Visual Studio**, [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/) oder [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) von Cerebrata zeigen Sie die Daten der Leistungsindikatoren in der Tabelle **WADPerformanceCountersTable** an. Sie können den Tabellendienst mit [C\#][], [Java](http://www.windowsazure.com/de-de/develop/java/how-to-guides/table-service/), [Node.js][], [Python](http://www.windowsazure.com/de-de/develop/python/how-to-guides/table-service/) oder [PHP](http://www.windowsazure.com/de-de/develop/php/how-to-guides/table-service/) auch programmgesteuert abfragen.

Das folgende C\#-Beispiel zeigt eine einfache Abfrage der Tabelle **WADPerformanceCountersTable** und speichert die Diagnosedaten in einer CSV-Datei. Wenn Sie die Leistungsindikatoren in einer CSV-Datei speichern, können Sie die Daten mit den grafischen Darstellungsfunktionen von Microsoft Excel oder eines anderen Tools darstellen. Fügen Sie einen Verweis auf die Datei Microsoft.WindowsAzure.Storage.dll hinzu, die im Azure SDK für .NET Oktober 2012 und höher enthalten ist. Die Assembly wird im Verzeichnis %Program Files%\\Microsoft SDKs\\Windows Azure.NET SDK\\version-num\\ref\\ installiert.

		using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Table;

        ...

        // Verbindungszeichenfolge abrufen. Bei Verwendung von Azure-Cloud-Diensten wird empfohlen, 
        // die Verbindungszeichenfolge mit dem Azure-Dienstkonfigurationssystem
        // zu speichern (*.csdef and *.cscfg files). Mit dem Typ CloudConfigurationManager kann die 
        // Speicherverbindungszeichenfolge abgerufen werden.  Werden die Cloud-Dienste nicht verwendet, wird
        // empfohlen, die Verbindungszeichenfolge in der Datei web.config oder app.config zu speichern.
        // Die Speicherverbindungszeichenfolge mit dem Typ ConfigurationManager abrufen.
        
        string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
        //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
        // Mit der Verbindungszeichenfolge einen Verweis auf das Speicherkonto abrufen.  Auch das Entwicklungsspeicherkonto (Speicheremulator) kann für das
        // lokale Debugging verwendet werden.              
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

        // Tabellenclient erstellen.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

        // CloudTable-Objekt erstellen, das die Tabelle "WADPerformanceCountersTable" darstellt.
        CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

        // Die Tabellenabfrage erstellen, nach CounterName, DeploymentId und RoleInstance filtern.
        TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
            .Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
                    TableOperators.And,
                    TableQuery.CombineFilters(                        
                        TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")                        
                    )
                )
            );

        // Tabellenabfrage ausführen.
        IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

        // Abfrageergebnisse verarbeiten und eine CSV-Datei erstellen.
        StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");
            
        foreach (PerformanceCountersEntity entity in result)
        {
            sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + "," 
            	+ entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
        }

        StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
        sw.Write(sb.ToString());
        sw.Close();

Entitäten werden mithilfe einer aus **TableEntity** abgeleiteten benutzerdefinierten Klasse C\#-Objekten zugeordnet. Der folgende Code definiert in der Tabelle **WADPerformanceCountersTable** eine Entitätsklasse, die einen Leistungsindikator darstellt.

     public class PerformanceCountersEntity : TableEntity
        {
            public long EventTickCount { get; set; }
            public string DeploymentId { get; set; }
            public string Role { get; set; }
            public string RoleInstance { get; set; }
            public string CounterName { get; set; }
            public double CounterValue { get; set; }                
        }

Nächste Schritte
----------------

Nachdem Sie sich mit den Grundlagen der Erfassung von Leistungsindikatoren vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Problembehandlungsszenarien implementiert werden.

-   [Troubleshooting Best Practices for Developing Azure Applications (Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen, in englischer Sprache)](http://msdn.microsoft.com/de-de/library/windowsazure/hh771389.aspx)
-   [Überwachen von Cloud-Diensten](https://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-monitor-a-cloud-service/)
-   [Verwenden des Anwendungsblocks für die automatische Skalierung](http://www.windowsazure.com/de-de/develop/net/how-to-guides/autoscaling/)
-   [Building Elastic and Resilient Cloud Apps (Erstellen elastischer und robuster Cloud-Apps)](http://msdn.microsoft.com/de-de/library/hh680949(PandP.50).aspx)

Zusätzliche Ressourcen
----------------------

-   [Aktivieren der Diagnose in Azure](https://www.windowsazure.com/de-de/develop/net/common-tasks/diagnostics/)
-   [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](http://msdn.microsoft.com/de-de/library/windowsazure/gg433048.aspx)
-   [Debuggen von Cloud-Diensten](http://msdn.microsoft.com/de-de/library/windowsazure/ee405479.aspx)


  [Overview of Creating and Using Performance Counters in an Azure Application]: http://msdn.microsoft.com/de-de/library/windowsazure/hh411520.aspx
  [Prerequisites]: #prereqs
  [Step 1: Collect and store data from performance counters]: #step1
  [Step 2: (Optional) Create custom performance counters]: #step2
  [Step 3: Query performance counter data]: #step3
  [Next Steps]: #nextsteps
  [Additional Resources]: #additional
  
  [Collecting Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433048.aspx
  [Debugging an Azure Application]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405479.aspx
  [How to Use the Autoscaling Application Block]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/autoscaling/
  [Troubleshooting Best Practices for Developing Azure Applications]: http://msdn.microsoft.com/de-de/library/windowsazure/hh771389.aspx
  [Enabling Diagnostics in Azure]: https://www.windowsazure.com/de-de/develop/net/common-tasks/diagnostics/
  [How to use the Table Storage Service]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/table-services/
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  
  [Java]: http://www.windowsazure.com/de-de/develop/java/how-to-guides/table-service/
  [Python]: http://www.windowsazure.com/de-de/develop/python/how-to-guides/table-service/
  [PHP]: http://www.windowsazure.com/de-de/develop/php/how-to-guides/table-service/
  
  [Building Elastic and Resilient Cloud Apps]: http://msdn.microsoft.com/de-de/library/hh680949(PandP.50).aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [Azure Diagnostics Manager]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [How to Monitor Cloud Services]: https://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-monitor-a-cloud-service/
