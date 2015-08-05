<properties 
	pageTitle="Exemplarische Vorgehensweise: Exportieren von Telemetriedaten in SQL-Datenbank mit Application Insights" 
	description="Codieren Sie Ihre eigene Telemetrieanalyse in Application Insights mithilfe des Features für den fortlaufenden Export." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/13/2015" 
	ms.author="awills"/>
 
# Exemplarische Vorgehensweise: Exportieren aus Application Insights in SQL mit Stream Analytics

Dieser Artikel zeigt, wie Sie Ihre Telemetriedaten aus [Visual Studio Application Insights][start] mithilfe von [fortlaufendem Export][export] und [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytics/) in eine Azure SQL-Datenbank verschieben.

Beim fortlaufenden Export werden die Telemetriedaten im JSON-Format in Azure Storage verschoben. Wir analysieren die JSON-Objekte mithilfe von Azure Stream Analytics und erstellen Zeilen in einer Datenbanktabelle.

(Allgemeiner ausgedrückt, bietet der fortlaufende Export Ihnen die Möglichkeit, selbst eine Analyse der Telemetriedaten durchzuführen, die Ihre Apps an Application Insights senden. Sie können dieses Codebeispiel für andere Aufgaben mit der exportierten Telemetrie anpassen, z. B. die Aggregation der Daten und die Veröffentlichung der Daten in der PowerBI-Visualisierungssuite.)

Zu Beginn gehen wir davon aus, dass Sie bereits über die App verfügen, die Sie überwachen möchten.


In diesem Beispiel verwenden wir die Seitenzugriffsdaten. Dasselbe Muster kann jedoch problemlos auf andere Datentypen, wie z. B. benutzerdefinierte Ereignisse und Ausnahmen, übertragen werden.


## Hinzufügen des Application Insights-SDK

Zum Überwachen Ihrer Anwendung [fügen Sie Ihrer Anwendung ein Application Insights-SDK hinzu][start]. Es gibt verschiedene SDKs und Hilfstools für verschiedene Plattformen, IDEs und Sprachen. Sie können Webseiten, Java- oder ASP.NET-Webserver und verschiedene Arten von mobilen Geräten überwachen. Alle SDKs senden Telemetriedaten an das [Application Insights-Portal][portal], in dem Sie unsere leistungsstarken Analyse- und Diagnosetools verwenden und die Daten in den Speicher exportieren können.

Erste Schritte:

1. Fordern Sie ein [Konto in Microsoft Azure](http://azure.microsoft.com/pricing/) an.
2. Fügen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource für Ihre App hinzu:

    ![Wählen Sie "Neu", "Entwicklerdienste", "Application Insights", und wählen Sie den Typ der Anwendung.](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (Ihr App-Typ und Ihr Abonnement können abweichen.)
3. Informationen zum Einrichten des SDK für Ihren App-Typ finden Sie, indem Sie den Schnellstart öffnen.

    ![Wählen Sie "Schnellstart", und befolgen Sie die Anweisungen.](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    Wenn Ihr App-Typ nicht aufgeführt ist, sehen Sie sich die Seite [Erste Schritte][start] an.

4. In diesem Beispiel überwachen wir eine Web-App und können daher die Azure-Tools in Visual Studio verwenden, um das SDK zu installieren. Geben Sie den Namen Ihrer Application Insights-Ressource an:

    ![Klicken Sie im Visual Studio Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen" aus. Erstellen Sie bei "Telemetriedaten senden an" eine neue Ressource, oder verwenden Sie eine vorhandene Ressource.](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. Veröffentlichen Sie Ihre App, und beobachten Sie die Telemetriedaten, die in Ihrer Application Insights-Ressource angezeigt werden.


## Erstellen von Speicher in Azure

Durch fortlaufende Exportaktivitäten werden Daten an ein Azure-Speicherkonto übertragen, daher müssen Sie zuerst Speicher erstellen.

1. Erstellen Sie ein Speicherkonto in Ihrem Abonnement im [Azure-Portal][portal].

    ![Wählen Sie im Azure-Portal "Neu", "Daten" und "Speicher".](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Erstellen eines Containers

    ![Wählen Sie im neuen Speicher "Container" und anschließend "Hinzufügen".](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Kopieren Sie den Speicherzugriffsschlüssel.

    Sie benötigen ihn bald, um die Eingabe für den Stream Analytics-Dienst einzurichten.

    ![Öffnen Sie im Speicher die Optionen "Einstellungen", "Schlüssel", und kopieren Sie den primären Zugriffsschlüssel.](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Starten des fortlaufenden Exports in den Azure-Speicher

1. Navigieren Sie im Azure-Portal zu der Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.

    ![Wählen Sie "Durchsuchen", "Application Insights" und Ihre Anwendung aus.](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Erstellen Sie einen fortlaufenden Export.

    ![Wählen Sie "Einstellungen", "Fortlaufender Export", "Hinzufügen".](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Wählen Sie das zuvor erstellte Speicherkonto aus:

    ![Legen Sie das Exportziel fest.](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Legen Sie die Ereignistypen fest, die Sie anzeigen möchten:

    ![Wählen Sie Ereignistypen aus.](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

Jetzt lehnen Sie sich zurück, und lassen Sie Ihre Benutzer die Anwendung eine Weile lang verwenden. Telemetriedaten gehen ein, und Sie sehen statistische Diagramme im [Metrik-Explorer][metrics] sowie einzelne Ereignisse in der [Diagnosesuche][diagnostic].

Außerdem werden die Daten in den Speicher exportiert, in dem Sie den Inhalt überprüfen können. Beispielsweise gibt es einen Speicher-Browser in Visual Studio:


![Öffnen Sie in Visual Studio den "Server-Browser", "Azure" und "Storage".](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

Die Ereignisse werden in Blobdateien im JSON-Format geschrieben. Jede Datei kann ein oder mehrere Ereignisse enthalten. Daher möchten wir die Ereignisdaten lesen und die gewünschten Felder herausfiltern. Es gibt viele verschiedene Möglichkeiten zur Nutzung der Daten, aber unser Plan besteht darin, Stream Analytics zu verwenden, um die Daten in eine SQL-Datenbank zu verschieben. Auf diese Weise können wir ganz einfach viele interessante Abfragen ausführen.

## Erstellen einer Azure-SQL-Datenbank

Beginnen Sie erneut bei Ihrem Abonnement im [Azure-Portal][portal], und erstellen Sie die Datenbank (und einen neuen Server, sofern noch keiner verfügbar ist), in die die Daten geschrieben werden sollen.

![Neu, Daten, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Stellen Sie sicher, dass der Datenbankserver den Zugriff auf Azure-Dienste ermöglicht:


![Durchsuchen, Server, Ihr Server, Einstellungen, Firewall, Zugriff auf Azure erlauben](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Erstellen einer Tabelle in Azure SQL-Datenbank

Stellen Sie über Ihr bevorzugtes Verwaltungstool eine Verbindung mit der Datenbank her, die Sie im vorherigen Abschnitt erstellt haben. In dieser exemplarischen Vorgehensweise verwenden wir die [SQL Server-Verwaltungstools](https://msdn.microsoft.com/ms174173.aspx).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Erstellen Sie eine neue Abfrage, und führen Sie folgende T-SQL-Anweisung aus:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

## Erstellen einer Azure Stream Analytics-Instanz

Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) den Azure Stream Analytics-Dienst aus, und erstellen Sie einen neuen Stream Analytics-Auftrags:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Wenn der neue Auftrag erstellt ist, erweitern Sie die Details:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Festlegen des Blobspeicherorts

Legen Sie den Auftrag so fest, dass er Eingaben vom Blob für den fortlaufenden Export erhält:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Jetzt benötigen Sie den primären Zugriffsschlüssel aus Ihrem Speicherkonto, den Sie zuvor notiert haben. Legen Sie diesen als Speicherkontoschlüssel fest.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Festlegen des Präfixmusters des Pfads 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Achten Sie darauf, dass das Datum das Format "JJJJ-MM-TT" (mit Bindestrichen) aufweist.

Das Präfixmuster des Pfads gibt an, wie Stream Analytics die Eingabedateien im Speicher ermittelt. Sie müssen es so einrichten, dass es der Speicherung der Daten durch den fortlaufenden Export entspricht. Legen Sie ihn wie folgt fest:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

In diesem Beispiel:

* `webapplication27` ist der Name der Application Insights-Ressource. 
* `1000...` Ist der Instrumentationsschlüssel der Application Insights-Ressource. 
* `PageViews` ist die Art der zu analysierenden Daten. Die verfügbaren Typen sind abhängig von dem Filter, den Sie im fortlaufenden Export festlegen. Untersuchen Sie die exportierten Daten, um die anderen verfügbaren Typen anzuzeigen.
* `/{date}/{time}` ist ein als Literal geschriebenes Muster.

Um den Namen und iKey Ihrer Application Insights-Ressource zu erhalten, öffnen Sie auf der Seite "Übersicht" den Eintrag "Essentials", oder öffnen Sie "Einstellungen".

#### Fertig stellen des ersten Setups

Bestätigen Sie das Serialisierungsformat:

![Bestätigen Sie den Vorgang, und schließen Sie den Assistenten.](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Schließen Sie den Assistenten, und warten Sie, bis das Setup abgeschlossen ist.

## Festlegen der Abfrage

Öffnen Sie den Abfrageabschnitt:

![Wählen Sie in Stream Analytics die Option "Abfrage".](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Ersetzen Sie die Standardabfrage durch folgende:

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOuput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Beachten Sie, dass die ersten Eigenschaften sich speziell auf die Seitenzugriffsdaten beziehen. Exporte anderer Telemetrietypen verfügen über andere Eigenschaften.

## Einrichten der Ausgabe an die Datenbank

Wählen Sie SQL als Ausgabe.

![Wählen Sie in Stream Analytics die Option "Ausgaben".](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Geben Sie die SQL-Datenbank an.


![Geben Sie Details zu Ihrer Datenbank ein.](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Schließen Sie den Assistenten, und warten Sie auf eine Benachrichtigung, dass die Ausgabe eingerichtet wurde.

## Starten der Verarbeitung

Starten Sie den Auftrag über die Aktionsleiste:

![Klicken Sie in Stream Analytics auf "Start".](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Sie können auswählen, ob die Daten ab sofort verarbeitet werden oder ob Sie mit früheren Daten beginnen möchten. Letzteres ist nützlich, wenn der fortlaufende Export bereits seit einer Weile ausgeführt wurde.


![Klicken Sie in Stream Analytics auf "Start".](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Nach einigen Minuten wechseln Sie zurück zu den SQL Server-Verwaltungstools, und beobachten Sie den Eingang der Daten. Verwenden Sie beispielsweise eine Abfrage ähnlich der folgenden:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


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

 

<!---HONumber=July15_HO4-->