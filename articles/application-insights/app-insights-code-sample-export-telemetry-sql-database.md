<properties 
	pageTitle="Beispiel: Exportieren aus Application Insights über eine Worker-Rolle in SQL" 
	description="Die Codeanalyse eigene der Telemetrie in Application Insights mithilfe des Features für die kontinuierliche exportieren." 
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
 
# Beispiel: Exportieren aus Application Insights über eine Worker-Rolle in SQL

Dieser Artikel zeigt, wie verschieben Sie Ihre Daten aus [Visual Studio Application Insights][start] in einer Azure SQL-Datenbank mithilfe von [fortlaufende exportieren][export] und eine geringe Menge an Code.

Fortlaufende Export verschoben so, Ihrer Telemetrie in den Azure-Speicher im JSON-Format schreiben Code zum Analysieren von JSON-Objekten und Zeilen in einer Datenbanktabelle erstellen.

(Im Allgemeinen ist kontinuierlichen exportieren so eigene Analyse der Telemetrie Anwendungen an Application Insights gesendet. Sie können dieses Codebeispiel für andere Aufgaben mit den exportierten Telemetrie anpassen.)

Wir beginnen mit der Annahme, dass Sie bereits die Anwendung, die Sie überwachen möchten.

## Hinzufügen von Application Insights SDK

Überwachen Ihrer Anwendung Sie [Hinzufügen von Application Insights-SDK][start] zu Ihrer Anwendung. Es gibt verschiedene SDKs und Hilfstools für verschiedene Plattformen, Sprachen und IDEs. Sie können Webseiten, Java oder ASP.NET Web-Server und verschiedene Arten von mobilen Geräten überwachen. Alle SDKs senden Telemetrie auf die [Application Insights-Portal][portal], wobei Sie können unsere leistungsstarken Analyse und Diagnose-Tools verwenden, und exportieren die Daten in den Speicher.

Erste Schritte:

1. Abrufen einer [Konto in Microsoft Azure](http://azure.microsoft.com/pricing/).
2. In der [Azure-Portal][portal], fügen Sie eine neue Application Insights-Ressource für Ihre app:

    ![Wählen Sie neu, Entwicklerdienste, Application Insights, und wählen Sie den Typ der Anwendung](./media/app-insights-code-sample-export-telemetry-sql-database/010-new-asp.png)


    (Ihr app-Typ und Ihr Abonnement können abweichen.)
3. Öffnen Sie die Schnellstart zum Einrichten des SDK für app-Typ gefunden.

    ![Wählen Sie im Schnellstart und Anleitung](./media/app-insights-code-sample-export-telemetry-sql-database/020-quick.png)

    Wenn Ihre app-Typ aufgeführt ist, sehen Sie sich die [Erste Schritte][start] Seite.

4. In diesem Beispiel überwachen wir eine Web-app, damit wir Azure Tools in Visual Studio verwenden können, um das SDK zu installieren. Geben sie den Namen der unsere Application Insights-Ressource:

    ![In Visual Studio, klicken Sie im Dialogfeld "Neues Projekt" Überprüfen von Application Insights hinzufügen und unter Senden Telemetrie an, ob eine neue Anwendung erstellen oder eine bestehende verwenden.](./media/app-insights-code-sample-export-telemetry-sql-database/030-new-project.png)


## Erstellen Sie in Azure storage

1. Erstellen Sie ein Speicherkonto in Ihrem Abonnement in der [Azure-Portal][portal].

    ![Wählen Sie in Azure-Portal neu, Daten und Speicher](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)

2. Erstellen eines Containers

    ![Wählen Sie in der neuen Speicher Container hinzufügen und dann auf](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)


## Starten Sie fortlaufende Export den Azure-Speicher

1. Navigieren Sie in Azure-Portal zu Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.

    ![Wählen Sie durchsuchen, Application Insights Ihrer Anwendung](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)

2. Erstellen Sie eine fortlaufende exportieren.

    ![Wählen Sie Einstellungen, kontinuierliche Export hinzufügen](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)


    Wählen Sie das Speicherkonto, das Sie zuvor erstellt haben:

    ![Legen Sie das Exportziel](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)
    
    Legen Sie die Ereignistypen, die Sie anzeigen möchten:

    ![Wählen Sie die Ereignistypen](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

Jetzt lehnen Sie sich zurück und Personen, die die Anwendung für eine Weile verwenden können. Telemetrie ergeben, und sehen Sie statistische Diagramme in [metrische Explorer][metrics] und einzelne Ereignisse in [diagnostische Suche][diagnostic].

Und außerdem exportiert die Daten auf Ihrem Speicher, in dem Sie den Inhalt überprüfen können. Es ist z. B. ein Speicher-Browser in Visual Studio:


![Öffnen Sie in Visual Studio Server-Browser, Azure, Speicher](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)

Die Ereignisse werden in blob-Dateien im JSON-Format geschrieben. Jede Datei kann ein oder mehrere Ereignisse enthalten. Daher möchten wir schreiben Code zum Lesen von Daten für das Ereignis und herausfiltern, die wir die gewünschten Felder. Es gibt verschiedene Möglichkeiten, die wir mit den Daten tun kann, aber wir planen heute ist das Schreiben von Code zum Verschieben von Daten in einer SQL-Datenbank. Mit können auf einfache viele interessante Abfragen ausgeführt werden.

## Erstellen einer Azure-SQL-Datenbank

Erneut starten aus dem Abonnement in [Azure-Portal][portal], erstellen Sie die Datenbank (und einem neuen Server, wenn Sie bereits eine geeignete Problemstellung haben), die die Daten geschrieben werden.

![Neue, Daten, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)


Stellen Sie sicher, dass der Server Zugriff auf Azure-Diensten ermöglicht:


![Durchsuchen, Server, dem Server-Einstellungen, Firewall, ermöglichen den Zugriff auf Azure](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)


## Erstellen Sie eine Worker-Rolle 

Nachdem wir schreiben können [Code](https://sesitai.codeplex.com/) zum Analysieren von JSON in der exportierten Blobs und Datensätze in der Datenbank erstellen. Da der Speicher exportieren und die Datenbank in Azure sind, führen wir den Code in einer Azure workerrolle.


#### Erstellen von workerrollenprojekt

Erstellen Sie in Visual Studio ein neues Projekt für die Worker-Rolle:

![Neues Projekt, Visual c\#, Cloud, Azure-Cloud-Dienst](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![Neue Cloud Service-Dialog Wählen Sie in Visual c\#-Worker-Rolle](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)


#### Herstellen einer Verbindung mit dem Speicherkonto

Erhalten Sie die Verbindungszeichenfolge in Azure aus Ihrem Speicherkonto:

![Wählen Sie im Speicherkonto die Schlüssel, und kopieren Sie die primäre Verbindungszeichenfolge](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

Konfigurieren Sie die Worker-Rolle-Einstellungen in Visual Studio mit der Verbindungszeichenfolge für Speicherkonto:


![Klicken Sie im Projektmappen-Explorer unter dem Cloud-Dienst-Projekt erweitern Sie Rollen, und öffnen Sie die Worker-Rolle. Öffnen Sie die Registerkarte "Einstellungen", wählen Sie die Einstellung hinzufügen und Festlegen von Name = StorageConnectionString, Typ = Verbindungszeichenfolge, klicken Sie auf, um den Wert festzulegen. Legen Sie ihn manuell, und fügen Sie der Verbindungszeichenfolge.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)


#### Pakete

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts Worker-Rolle, und wählen Sie "NuGet-Pakete verwalten". Suchen und diese Pakete installieren:

 * EntityFramework 6.1.2 oder später - verwenden wir dies zum Generieren des Schemas des DB-Tabelle dynamisch basierend auf dem Inhalt von JSON in das Blob.
 * JsonFx - dies verwenden wir für das Reduzieren der zu C#-Klasseneigenschaften JSON.

Verwenden Sie dieses Tool zum Generieren von C#-Klasse aus unserer einzelnes JSON-Dokument. Es erfordert einige kleinere Änderungen vor, wie die Reduzierung von JSON-Arrays in einzelne C#-Eigenschaft aktivieren Sie die einzelnen Spalte in der Datenbanktabelle (z. B. UrlData_port)

 * [Generator für JSON c\#-Klasse](http://jsonclassgenerator.codeplex.com/)

## Code 

Legen Sie diesen Code in `WorkerRole.cs`.

#### Importe

    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### Die Speicher-Verbindungszeichenfolge abrufen

    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### Den Arbeitsthread in regelmäßigen Abständen ausführen

Ersetzen Sie die vorhandene run-Methode, und wählen Sie das gewünschte Intervall. Es sind mindestens eine Stunde, da das Exportfeature ein JSON-Objekt in einer Stunde abgeschlossen ist.

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

#### Jede JSON-Objekt als Zeile in einer Tabelle einfügen


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

#### Jedes Blob zu analysieren.

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
                {GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
    	        }
    
              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
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

#### Ein Wörterbuch für jedes Dokument JSON vorbereiten


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

#### Umwandeln von JSON-Dokument in C#-Klasse Telemetrie Objekteigenschaften

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

#### PageViewPerformance-Klassendatei aus JSON-Dokument generiert

    public class PageViewPerformance
    {
    	public int Id { get; set; }

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


#### Für die SQL-Interaktion von Entity Framework DBcontext

	public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Hinzufügen die DB-Verbindungszeichenfolge mit dem Namen `TelemetryContext` in `app.config`.

## Schema (nur zu Informationszwecken)

Dies ist das Schema für die Tabelle, die für die Seite generiert wird.

> [AZURE.NOTE]Sie müssen dieses Skript ausführen. Die Attribute in JSON bestimmen die Spalten in der Tabelle.

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


Dieses Beispiel in Aktion sehen [herunterladen](https://sesitai.codeplex.com/) den vollständigen Arbeitscode, ändern die `app.config` Einstellungen & Worker-Rolle in Azure veröffentlichen.


## Verwandte Artikel

* [Exportieren Sie in eine Worker-Rolle mit SQL](app-insights-code-sample-export-telemetry-sql-database.md)
* [Fortlaufende Export in Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->