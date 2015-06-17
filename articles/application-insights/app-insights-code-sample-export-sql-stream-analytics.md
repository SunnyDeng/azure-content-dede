<properties 
	pageTitle="Exemplarische Vorgehensweise: Exportieren von Telemetriedaten zur SQL-Datenbank von Application Insights" 
	description="Die Codeanalyse eigene der Telemetrie in Application Insights mithilfe des Features für die kontinuierliche exportieren." 
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
 
# Exemplarische Vorgehensweise: Exportieren Sie nach SQL Application Insights mit Streamanalyse

Dieser Artikel zeigt, wie verschieben Sie Ihre Daten aus [Visual Studio Application Insights][start] in einer Azure SQL-Datenbank mithilfe von [fortlaufende exportieren][export] und [Azure Streamanalyse](http://azure.microsoft.com/services/stream-analytics/).

Fortlaufende Export verschiebt die Telemetriedaten in Azure-Speicher im JSON-Format. Wir analysieren von JSON-Objekten, die mithilfe von Azure-Stream-Analysen und Zeilen in einer Datenbanktabelle erstellen.

(Im Allgemeinen ist kontinuierlichen exportieren so eigene Analyse der Telemetrie Anwendungen an Application Insights gesendet. Sie können dieses Codebeispiel für andere Aufgaben mit den exportierten Telemetrie, z. B. Aggregation der Daten und Veröffentlichung von Daten zur Visualisierung PowerBI Suite anpassen.)

Wir beginnen mit der Annahme, dass Sie bereits die Anwendung, die Sie überwachen möchten.


In diesem Beispiel verwenden wir die Seitendaten, aber das gleiche Muster kann problemlos in andere Datentypen, wie z. B. benutzerdefinierte Ereignisse und Ausnahmen erweitert werden.


## Hinzufügen von Application Insights SDK

Überwachen Ihrer Anwendung Sie [Hinzufügen von Application Insights-SDK][start] zu Ihrer Anwendung. Es gibt verschiedene SDKs und Hilfstools für verschiedene Plattformen, Sprachen und IDEs. Sie können Webseiten, Java oder ASP.NET Web-Server und verschiedene Arten von mobilen Geräten überwachen. Alle SDKs senden Telemetrie auf die [Application Insights-Portal][portal], wobei Sie können unsere leistungsstarken Analyse und Diagnose-Tools verwenden, und exportieren die Daten in den Speicher.

Erste Schritte:

1. Abrufen einer [Konto in Microsoft Azure](http://azure.microsoft.com/pricing/).
2. In der [Azure-Portal][portal], fügen Sie eine neue Application Insights-Ressource für Ihre app:

    ![Wählen Sie neu, Entwicklerdienste, Application Insights, und wählen Sie den Typ der Anwendung](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (Ihr app-Typ und Ihr Abonnement können abweichen.)
3. Öffnen Sie die Schnellstart zum Einrichten des SDK für app-Typ gefunden.

    ![Wählen Sie im Schnellstart und Anleitung](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    Wenn Ihre app-Typ aufgeführt ist, sehen Sie sich die [Erste Schritte][start] Seite.

4. In diesem Beispiel überwachen wir eine Web-app, damit wir Azure Tools in Visual Studio verwenden können, um das SDK zu installieren. Geben sie den Namen der unsere Application Insights-Ressource:

    ![In Visual Studio, klicken Sie im Dialogfeld "Neues Projekt" Überprüfen von Application Insights hinzufügen und unter Senden Telemetrie an, ob eine neue Anwendung erstellen oder eine bestehende verwenden.](./media/app-insights-code-sample-export-sql-stream-analytics/030-new-project.png)


## Erstellen Sie in Azure storage

1. Erstellen Sie ein Speicherkonto in Ihrem Abonnement in der [Azure-Portal][portal].

    ![Wählen Sie in Azure-Portal neu, Daten und Speicher](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Erstellen eines Containers

    ![Wählen Sie in der neuen Speicher Container hinzufügen und dann auf](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Kopieren Sie den speicherzugriffsschlüssel

    Sie benötigen sie schnell auf die Eingabe für den Datenstrom Analytics-Dienst einzurichten.

    ![Öffnen Sie im Speicher die Einstellungen, Schlüssel, und eine Kopie der Access-Primärschlüssel](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Starten Sie fortlaufende Export den Azure-Speicher

1. Navigieren Sie in Azure-Portal zu Application Insights-Ressource, die Sie für Ihre Anwendung erstellt haben.

    ![Wählen Sie durchsuchen, Application Insights Ihrer Anwendung](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Erstellen Sie eine fortlaufende exportieren.

    ![Wählen Sie Einstellungen, kontinuierliche Export hinzufügen](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Wählen Sie das Speicherkonto, das Sie zuvor erstellt haben:

    ![Legen Sie das Exportziel](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Legen Sie die Ereignistypen, die Sie anzeigen möchten:

    ![Wählen Sie die Ereignistypen](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

Jetzt lehnen Sie sich zurück und Personen, die die Anwendung für eine Weile verwenden können. Telemetrie ergeben, und sehen Sie statistische Diagramme in [metrische Explorer][metrics] und einzelne Ereignisse in [diagnostische Suche][diagnostic].

Und außerdem exportiert die Daten auf Ihrem Speicher, in dem Sie den Inhalt überprüfen können. Es ist z. B. ein Speicher-Browser in Visual Studio:


![Öffnen Sie in Visual Studio Server-Browser, Azure, Speicher](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

Die Ereignisse werden in blob-Dateien im JSON-Format geschrieben. Jede Datei kann ein oder mehrere Ereignisse enthalten. Daher möchten wir schreiben Code zum Lesen von Daten für das Ereignis und herausfiltern, die wir die gewünschten Felder. Es gibt verschiedene Möglichkeiten, die wir mit den Daten tun kann, aber wir planen heute ist das Schreiben von Code zum Verschieben von Daten in einer SQL-Datenbank. Mit können auf einfache viele interessante Abfragen ausgeführt werden.

## Erstellen einer Azure-SQL-Datenbank

Erneut starten aus dem Abonnement in [Azure-Portal][portal], erstellen Sie die Datenbank (und einem neuen Server, wenn Sie bereits eine geeignete Problemstellung haben), die die Daten geschrieben werden.

![Neue, Daten, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Stellen Sie sicher, dass der Server Zugriff auf Azure-Diensten ermöglicht:


![Durchsuchen, Server, dem Server-Einstellungen, Firewall, ermöglichen den Zugriff auf Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Erstellen einer Tabelle in Azure SQL-Datenbank

Verbinden Sie mit der Datenbank, die im vorherigen Abschnitt mit Ihrem bevorzugten Tool erstellt. In dieser exemplarischen Vorgehensweise wir verwenden [SQL Server-Verwaltungstools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Erstellen Sie eine neue Abfrage, und führen Sie folgende T-SQL-Anweisung:

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

## Erstellen Sie eine Instanz der Azure-Stream-Analysen

Aus der [klassischen Azure-Verwaltungsportal](https://manage.windowsazure.com/), wählen Sie den Dienst Azure-Stream-Analysen und Erstellen eines neuen Auftrags für den Datenstrom Analytics:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Wenn das neue Projekt erstellt wird, erweitern Sie ihre Details:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Festlegen von Blob-Speicherort

Legen Sie Eingaben über die fortlaufende exportieren Blob auszuführen:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Jetzt benötigen den primären Zugriffsschlüssel aus Ihrem Speicherkonto, Sie, den Sie zuvor notiert haben. Legen Sie diese als Speicherkontoschlüssel.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Set Pfad Präfix-Muster 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Achten Sie darauf, dass das Datum das Format JJJJ-MM-TT (mit Bindestrichen) fest.

Der Pfad das Präfix Muster gibt an, wie Streamanalyse die Eingabedateien in den Speicher ermittelt. In diesem Fall müssen Sie eine einrichten, wie das kontinuierliche Exportieren der Daten speichert entsprechend. Legen Sie es wie folgt:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

In diesem Beispiel:

* `webapplication27` ist der Name der Ressource Application Insights. 
* `1000...` ist der instrumentationsschlüssel der Application Insights-Ressource. 
* `PageViews` ist die Art der zu analysierenden Daten. Die verfügbaren Typen abhängig von der festgelegten Filter, in der kontinuierlichen exportieren. Untersuchen Sie die exportierten Daten, um die anderen verfügbaren Typen anzuzeigen.
* `/{date}/{time}` ein Muster wird als Literal geschrieben werden.

Um den Namen und die iKey Ihrer Application Insights-Ressource zu erhalten, öffnen Sie auf der Seite "Übersicht" Essentials oder öffnen Sie Einstellungen.

#### Anfängliche Setup Fertig stellen

Bestätigen Sie das Serialisierungsformat an:

![Bestätigen und den Assistenten zu schließen](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Schließen Sie den Assistenten, und warten Sie, bis die Installation abgeschlossen.

## Set-Abfrage

Öffnen Sie den Abfrage-Abschnitt:

![Wählen Sie im Stream-Analysen Abfrage](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Ersetzen Sie die Standardabfrage mit:

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

Beachten Sie, dass die ersten paar Eigenschaften auf Daten der Seite Ansicht spezifisch sind. Exporte anderer Typen Telemetrie werden über verschiedene Eigenschaften verfügen.

## Richten Sie die Ausgabe in der Datenbank

Wählen Sie SQL als Ausgabe.

![Wählen Sie in der Analyse der Datenstrom Ausgaben](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Geben Sie die SQL-Datenbank.


![Füllen Sie die Details Ihrer Datenbank](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Schließen Sie den Assistenten, und Warten auf eine Benachrichtigung, die die Ausgabe eingerichtet wurde.

## Verarbeitung starten

Starten Sie den Auftrag aus der Aktion:

![Klicken Sie im Startmenü in der streamanalyse](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Sie können auswählen, ob verarbeitet die Daten sofort oder zu früheren Daten ab. Letzteres ist nützlich, wenn Sie fortlaufende zu exportieren, die bereits eine Weile ausgeführt wurde.


![Klicken Sie im Startmenü in der streamanalyse](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Nach einigen Minuten wechseln Sie zurück zur SQL Server-Verwaltungstools, und sehen Sie sich die Daten in. Verwenden Sie beispielsweise eine Abfrage wie folgt:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


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