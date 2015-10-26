<properties 
	pageTitle="Überwachen von Abhängigkeiten, Ausnahmen und Ausführungszeiten in Java-Web-Apps" 
	description="Erweiterte Überwachung Ihrer Java-Website mit Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2015" 
	ms.author="awills"/>
 
# Überwachen von Abhängigkeiten, Ausnahmen und Ausführungszeiten in Java-Web-Apps

*Application Insights befindet sich in der Vorschau.*

Wenn Sie [Ihre Java-Web-App mit Application Insights instrumentiert haben][java], können Sie den Java-Agent ohne Codeänderungen verwenden, um detailliertere Informationen zu erhalten:


* **Abhängigkeiten**: Daten über Aufrufe der Anwendung an andere Komponenten, einschließlich:
 * **REST-Aufrufe** über "HttpClient", "OkHttp" und "RestTemplate" (Spring).
 * **Redis**-Aufrufe über den Jedis-Client. Wenn der Aufruf länger als 10 s dauert, wird der Agent auch die Argumente des Aufrufs abrufen.
 * **[JDBC-Aufrufe](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**: MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB oder Apache Derby DB. Aufrufe von "executeBatch" werden unterstützt. Wenn bei MySQL und PostgreSQL der Aufruf länger als 10 s dauert, meldet der Agent dem Abfrageplan. 
* **Abgefangene Ausnahmen**: Daten zu Ausnahmen, die vom Code verarbeitet werden.
* **Methodenausführungszeit**: Daten über die Zeit, die zum Ausführen bestimmter Methoden benötigt wird.

Um den Java-Agent zu verwenden, installieren Sie ihn auf Ihrem Server. Ihre Web-Apps müssen mit dem [Application Insights-Java-SDK][java] instrumentiert werden.

## Installieren des Application Insights-Agents für Java

1. Laden Sie auf dem Computer, auf dem Ihr Java-Server ausgeführt wird, [den Agent herunter](http://go.microsoft.com/fwlink/?LinkId=618633).
2. Bearbeiten Sie das Startskript des Anwendungsservers, und fügen Sie die folgende JVM hinzu:

    `javaagent:`*Vollständiger Pfad der JAR-Datei des Agents*

    Beispielsweise in Tomcat auf einem Linux-Computer:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Starten Sie den Anwendungsserver neu.

## Konfigurieren des Agents

Erstellen Sie eine Datei mit dem Namen `AI-Agent.xml`, und speichern Sie sie im selben Ordner wie die JAR-Datei des Agents.

Legen Sie den Inhalt der XML-Datei fest. Bearbeiten Sie das folgende Beispiel, um Features Ihrer Wahl aufzunehmen oder nicht.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments will be sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan will be reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Sie müssen für einzelne Methoden die Berichtausnahmen und Methodenzeiten aktivieren.

Standardmäßig ist `reportExecutionTime` "true" und `reportCaughtExceptions` "false".

## Anzeigen der Daten

In der Application Insights-Ressource werden aggregierte Remoteabhängigkeiten und Methodenausführungszeiten [auf der Kachel "Leistung"][metrics] angezeigt.

Um nach den einzelnen Instanzen der Abhängigkeits-, Ausnahmen- und Methodenberichte zu suchen, öffnen Sie die [Suche][diagnostic].

[Diagnostizieren von Problemen mit Abhängigkeiten – weitere Informationen](app-insights-dependencies.md#diagnosis).



## Fragen? Probleme?

[Problembehandlung für Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=Oct15_HO3-->