<properties 
	pageTitle="Codebeispiel: Exportieren aus Application Insights in SQL über eine Workerrolle" 
	description="Codieren Sie Ihre eigene Telemetrieanalyse in Application Insights mithilfe des Features für den fortlaufenden Export." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="klin"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="awills"/>
 
# Codebeispiel: Exportieren aus Application Insights in SQL über eine Workerrolle

Dieser Artikel zeigt, wie Sie Ihre Telemetriedaten mithilfe von [fortlaufendem Export][export] und etwas Code aus [Visual Studio Application Insights][start] in eine Azure SQL-Datenbank verschieben.

Beim fortlaufenden Export werden Ihre Telemetrie im JSON-Format in Azure Storage verschoben. Daher schreiben wir Code zum Analysieren der JSON-Objekte und zum Erstellen von Zeilen in einer Datenbanktabelle.

(Allgemeiner ausgedrückt, bietet der fortlaufende Export Ihnen die Möglichkeit, selbst eine Analyse der Telemetriedaten durchzuführen, die Ihre Apps an Application Insights senden. Sie können dieses Codebeispiel für andere Aufgaben mit den exportierten Telemetriedaten anpassen.)

Zu Beginn gehen wir davon aus, dass Sie bereits über die App verfügen, die Sie überwachen möchten.

## Hinzufügen des Application Insights-SDK

Zum Überwachen Ihrer Anwendung [fügen Sie Ihrer Anwendung ein Application Insights-SDK hinzu][start]. Es gibt verschiedene SDKs und Hilfstools für verschiedene Plattformen, IDEs und Sprachen. Sie können Webseiten, Java- oder ASP.NET-Webserver und verschiedene Arten von mobilen Geräten überwachen. Alle SDKs senden Telemetriedaten an das [Application Insights-Portal][portal], in dem Sie unsere leistungsstarken Analyse- und Diagnosetools verwenden und die Daten in den Speicher exportieren können.

Erste Schritte:

1. Fordern Sie ein [Konto in Microsoft Azure](http://azure.microsoft.com/pricing/) an.
2. Fügen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource für Ihre App hinzu:

    ![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights", und wählen Sie den Typ der Anwendung.](./media/app-insights-code-sample-export-telemetry-sql-database/010-new-asp.png)


    (Ihr App-Typ und Ihr Abonnement können abweichen.)
3. Informationen zum Einrichten des SDK für Ihren App-Typ finden Sie, indem Sie den Schnellstart öffnen.

    ![Wählen Sie "Schnellstart", und befolgen Sie die Anweisungen.](./media/app-insights-code-sample-export-telemetry-sql-database/020-quick.png)

    Wenn Ihr App-Typ nicht aufgeführt ist, sehen Sie sich die Seite [Erste Schritte][start] an.

4. In diesem Beispiel überwachen wir eine Web-App. Daher können wir die Azure-Tools in Visual Studio verwenden, um das SDK zu installieren. Geben Sie den Namen unserer Application Insights-Ressource an:

    ![Aktivieren Sie in Visual Studio im Dialogfeld "Neues Projekt" die Option "Application Insights hinzufügen", und wählen Sie unter "Telemetrie senden an", ob eine neue Anwendung erstellt oder eine vorhandene verwendet werden soll.](./media/app-insights-code-sample-export-telemetry-sql-database/030-new-project.png)


## Erstellen von Speicher in Azure

1. Erstellen Sie ein Speicherkonto in Ihrem Abonnement im [Azure-Portal][portal].

    ![Wählen Sie im Azure-Portal "Neu", "Daten" und "Speicher".](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)

2. Erstellen eines Containers

    ![Wählen Sie im neuen Speicher "Container" und anschließend "Hinzufügen".](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)


## Starten des fortlaufenden Exports in den Azure-Speicher

1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.

    ![Wählen Sie "Durchsuchen", "Application Insights" und Ihre Anwendung aus.](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)

2. Erstellen Sie einen fortlaufenden Export.

    ![Wählen Sie "Einstellungen", "Fortlaufender Export", "Hinzufügen".](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)


    Wählen Sie das zuvor erstellte Speicherkonto aus:

    ![Legen Sie das Exportziel fest.](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)
    
    Legen Sie die Ereignistypen fest, die Sie anzeigen möchten:

    ![Wählen Sie Ereignistypen aus.](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

Jetzt lehnen Sie sich zurück, und lassen Sie Ihre Benutzer die Anwendung eine Weile lang verwenden. Telemetriedaten gehen ein, und Sie sehen statistische Diagramme im [Metrik-Explorer][metrics] sowie einzelne Ereignisse in der [Diagnosesuche][diagnostic].

Außerdem werden die Daten in den Speicher exportiert, in dem Sie den Inhalt überprüfen können. Beispielsweise gibt es einen Speicher-Browser in Visual Studio:


![Öffnen Sie in Visual Studio den "Server-Browser", "Azure" und "Storage".](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)

Die Ereignisse werden in Blobdateien im JSON-Format geschrieben. Jede Datei kann ein oder mehrere Ereignisse enthalten. Daher schreiben wir Code zum Lesen der Ereignisdaten und zum Herausfiltern der gewünschten Felder. Es gibt viele verschiedene Möglichkeiten zur Nutzung der Daten, aber unser Plan besteht darin, Code zum Verschieben der Daten in eine SQL-Datenbank zu verfassen. Auf diese Weise können wir ganz einfach viele interessante Abfragen ausführen.

## Erstellen einer Azure-SQL-Datenbank

Beginnen Sie erneut bei Ihrem Abonnement im [Azure-Portal][portal], und erstellen Sie die Datenbank (und einen neuen Server, sofern noch keiner verfügbar ist), in die die Daten geschrieben werden sollen.

![Neu, Daten, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)


Stellen Sie sicher, dass der Datenbankserver den Zugriff auf Azure-Dienste ermöglicht:


![Durchsuchen, Server, Ihr Server, Einstellungen, Firewall, Zugriff auf Azure erlauben](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)


## Erstellen einer Workerrolle 

Jetzt können wir [Code](https://sesitai.codeplex.com/) zum Analysieren der JSON-Daten in den exportierten Blobs und zum Erstellen von Datensätzen in der Datenbank schreiben. Da sich der Exportspeicher und die Datenbank in Azure befinden, führen wir den Code in einer Azure-Workerrolle aus.


#### Erstellen eines Workerrollenprojekts

Erstellen Sie in Visual Studio ein neues Projekt für die Workerrolle.

!["Neues Projekt", "Visual C#", "Cloud", "Azure Cloud Service"](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![Wählen Sie im Dialogfeld für den neuen Clouddienst "Visual C#", "Workerrolle"](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)


#### Verbinden mit dem Speicherkonto

Rufen Sie in Azure die Verbindungszeichenfolge aus Ihrem Storage-Konto ab:

![Wählen Sie im Storage-Konto "Schlüssel" aus, und kopieren Sie die primäre Verbindungszeichenfolge](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

Konfigurieren Sie die Workerrolleneinstellungen in Visual Studio mit der Verbindungszeichenfolge für das Storage-Konto:


![Erweitern Sie im Projektmappen-Explorer unter dem Cloud Service-Projekt die Option "Rollen", und öffnen Sie die" Workerrolle". Öffnen Sie die Registerkarte "Einstellungen", wählen Sie "Einstellung hinzufügen", und legen Sie "name=StorageConnectionString" und "type=Verbindungszeichenfolge" fest. Klicken Sie, um den Wert festzulegen. Legen Sie ihn manuell fest, und fügen Sie die Verbindungszeichenfolge ein.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)


#### Pakete

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt "Workerrolle", und wählen Sie dann "NuGet-Pakete verwalten" aus. Suchen und installieren Sie diese Pakete:

 * EntityFramework 6.1.2 oder höher – Zum dynamischen Generieren des DB-Tabellenschemas basierend auf dem Inhalt der JSON-Daten im Blob.
 * JsonFx – Zum Reduzieren der JSON-Daten auf C#-Klasseneigenschaften

Verwenden Sie dieses Tool zum Generieren von C#-Klassendaten aus dem einzelnen JSON-Dokument. Dazu sind einige kleinere Änderungen erforderlich, wie das Reduzieren von JSON-Arrays in einzelne C#-Eigenschaften in einer einzigen Spalte in der Datenbanktabelle (z. B. urlData_port).

 * [C#-Klassen-Generator aus JSON](http://jsonclassgenerator.codeplex.com/)

## Code 

Sie können diesen Code in `WorkerRole.cs` ablegen.

#### Importe

    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### Abrufen der Speicherverbindungszeichenfolge

    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### Ausführen des Workers in regelmäßigen Abständen

Ersetzen Sie die vorhandene run-Methode, und wählen Sie das gewünschte Intervall. Es sollte mindestens eine Stunde umfassen, da das Exportfeature ein JSON-Objekt pro Stunde abschließt.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000
                
        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### Einfügen der einzelnen JSON-Objekte als Tabellenzeilen


    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
       	  {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
		//handle exception
      }
    }

#### Analysieren der einzelnen Blobs

    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());
    
        string json;
    
        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    
          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
    
          recCount = entities.Count();
          failureCount = 0; //resetting failure count
    
          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);
    
            Dictionary<string, object> dict = new Dictionary<string, object>();
    
            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");
    
            switch (FilterType)
            {
              case "PageViewPerformance":
    
              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
    	        }
    
              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }
    
            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);
    
            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;
    
            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### Vorbereiten eines Wörterbuchs für jedes JSON-Dokument


    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }
        }

#### Umwandeln des JSON-Dokuments in C#-Klassen-Telemetrieobjekteigenschaften

     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }

            return res;
        }

#### Aus JSON-Dokument generierte PageViewPerformance-Klassendatei

    public class PageViewPerformance
    {
    	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### DBcontext für SQL-Interaktion nach Entity Framework

	public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Fügen Sie die DB-Verbindungszeichenfolge des Namens `TelemetryContext` in `app.config` hinzu.

## Schema (nur zu Informationszwecken)

Dies ist das Schema für die Tabelle, die für PageView generiert wird.

> [AZURE.NOTE]Sie müssen dieses Skript nicht ausführen. Die Attribute in JSON bestimmen die Spalten in der Tabelle.

    CREATE TABLE [dbo].[PageViewPerformances](
	[Id] [uniqueidentifier] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlData_port] [int] NOT NULL,
	[urlData_protocol] [nvarchar](max) NULL,
	[urlData_host] [nvarchar](max) NULL,
	[urlData_base] [nvarchar](max) NULL,
	[urlData_hashTag] [nvarchar](max) NULL,
	[total_value] [float] NOT NULL,
	[networkConnection_value] [float] NOT NULL,
	[sendRequest_value] [float] NOT NULL,
	[receiveRequest_value] [float] NOT NULL,
	[clientProcess_value] [float] NOT NULL,
	[name] [nvarchar](max) NULL,
	[User] [nvarchar](max) NULL,
	[internal_data_id] [nvarchar](max) NULL,
	[internal_data_documentVersion] [nvarchar](max) NULL,
	[context_data_eventTime] [datetime] NULL,
	[context_device_id] [nvarchar](max) NULL,
	[context_device_type] [nvarchar](max) NULL,
	[context_device_os] [nvarchar](max) NULL,
	[context_device_osVersion] [nvarchar](max) NULL,
	[context_device_locale] [nvarchar](max) NULL,
	[context_device_userAgent] [nvarchar](max) NULL,
	[context_device_browser] [nvarchar](max) NULL,
	[context_device_browserVersion] [nvarchar](max) NULL,
	[context_device_screenResolution_value] [nvarchar](max) NULL,
	[context_user_anonId] [nvarchar](max) NULL,
	[context_user_anonAcquisitionDate] [nvarchar](max) NULL,
	[context_user_authAcquisitionDate] [nvarchar](max) NULL,
	[context_user_accountAcquisitionDate] [nvarchar](max) NULL,
	[context_session_id] [nvarchar](max) NULL,
	[context_session_isFirst] [bit] NOT NULL,
	[context_operation_id] [nvarchar](max) NULL,
	[context_location_point_lat] [float] NOT NULL,
	[context_location_point_lon] [float] NOT NULL,
	[context_location_clientip] [nvarchar](max) NULL,
	[context_location_continent] [nvarchar](max) NULL,
	[context_location_country] [nvarchar](max) NULL,
	[context_location_province] [nvarchar](max) NULL,
	[context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


Um dieses Beispiel in Aktion zu sehen, [laden Sie den vollständigen Arbeitscode herunter](https://sesitai.codeplex.com/), ändern Sie die `app.config`-Einstellungen, und veröffentlichen Sie die Workerrolle in Azure.


## Verwandte Artikel

* [Exportieren in SQL über eine Workerrolle](app-insights-code-sample-export-telemetry-sql-database.md)
* [Fortlaufender Export in Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO3-->