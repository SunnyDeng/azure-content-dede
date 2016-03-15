
<properties
   pageTitle="Ausführen der automatisierten Elasticsearch-Leistungstests | Microsoft Azure"
   description="Beschreibung der Art und Weise, in der Sie die Leistungstests in Ihrer eigenen Umgebung ausführen können."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Ausführen der automatisierten Elasticsearch-Leistungstests

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

Die Dokumente [Optimierung der Datenerfassungsleistung für Elasticsearch in Azure] und [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure] (Optimierung der Datenaggregations- und Abfrageleistung für Elasticsearch in Azure) beschreiben eine Reihe von Leistungstests, die für einen Elasticsearch-Beispielcluster ausgeführt wurden.

Diese Tests wurden in Skripts niedergeschrieben, damit sie automatisiert ausgeführt werden können. Dieses Dokument beschreibt, wie Sie die Tests in Ihrer eigenen Umgebung wiederholen können.

## Voraussetzungen

Die automatisierten Tests erfordern die folgenden Elemente:

-  Einen Elasticsearch-Cluster.

- Ein JMeter-Umgebungssetup, das der Beschreibung im Dokument [Creating a Performance Testing Environment for Elasticsearch on Azure] (Erstellen einer Leistungstestumgebung für Elasticsearch in Azure) entspricht.

- Die folgende Software wird nur auf der JMeter-Master-VM installiert.

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## Funktionsweise des Tests
Die Tests werden mit JMeter ausgeführt. Ein JMeter-Masterserver lädt einen Testplan und übergibt ihn einer Gruppe von untergeordneten JMeter-Servern, die die Tests ausführen. Der JMeter-Masterserver koordiniert die untergeordneten JMeter-Server und sammelt die Ergebnisse.

Die folgenden Testpläne werden bereitgestellt:

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Dieser Testplan führt den Erfassungstest über einen Cluster mit 3 Knoten aus.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Dieser Testplan führt den Erfassungstest über einen Cluster mit 6 Knoten aus.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Dieser Testplan führt den Erfassungs- und Abfragetest über einen Cluster mit 6 Knoten aus.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Dieser Testplan führt den ausschließlichen Abfragetest über einen Cluster mit 6 Knoten aus.


Sie können diese Testpläne als Grundlage für Ihre eigenen Szenarien verwenden, wenn Sie weniger oder mehr Knoten benötigen.

Die Testpläne verwenden einen JUnit-Request-Sampler zum Generieren und Hochladen der Testdaten. Der JMeter-Testplan erstellt diesen Sampler, führt ihn aus und überwacht alle Elasticsearch-Knoten auf Leistungsdaten. Weitere Informationen finden Sie in den Anhängen der Dokumente [Optimierung der Datenerfassungsleistung für Elasticsearch in Azure] und [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure] (Optimierung der Datenaggregations- und Abfrageleistung für Elasticsearch in Azure).

## Erstellen und Bereitstellen von JUnit JAR und Abhängigkeiten
Vor dem Ausführen der Resilienztests sollten Sie die JUnit-Tests herunterladen, kompilieren und bereitstellen, die sich im Ordner „performance/junitcode“ befinden. Auf dieses Tests verweist der JMeter-Testplan. Weitere Informationen finden Sie im Verfahren „Importing an Existing JUnit Test Project into Eclipse“ (Importieren eines vorhandenen JUnit-Testprojekts in Eclipse) im Dokument [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance] (Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Elasticsearch-Leistung).

Es gibt zwei Versionen der JUnit-Tests: – [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Verwenden Sie diesen Code für die Erfassungstests. Diese Tests verwenden Elasticsearch 1.73 – [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Verwenden Sie diesen Code für die Abfragetests. Diese Tests verwenden Elasticsearch 2.1 und höher.

Kopieren Sie die entsprechende JAR-Datei zusammen mit dem Rest der Abhängigkeiten auf Ihre JMeter-Computer. Der Vorgang wird beschrieben in [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance][] (Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Elasticsearch-Leistung).

> **Wichtig** Verwenden Sie nach der Bereitstellung eines JUnit-Tests JMeter zum Laden und Konfigurieren der Testpläne, die auf diesen JUnit-Test verweisen, und stellen Sie sicher, dass die Threadgruppe *BulkInsertLarge* auf die richtige JAR-Datei, den richtigen JUnit-Klassennamen und die richtige Testmethode verweist:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Speichern Sie die aktualisierten Testpläne vor dem Ausführen der Tests.

## Erstellen der Testindizes
Jeder Test führt Erfassung und/oder Abfragen für einen einzelnen Index aus, der bei Ausführung des Tests angegeben wird. Erstellen Sie den Index unter Verwendung der in den Anhängen zu den Dokumenten [Optimierung der Datenerfassungsleistung für Elasticsearch in Azure] und [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure] (Optimierung der Datenaggregations- und Abfrageleistung für Elasticsearch in Azure) beschriebenen Schemas, und konfigurieren Sie sie gemäß Ihrer Testszenarien (Doc-Werte aktiviert oder deaktiviert, mehrere Replikate usw.). Beachten Sie, dass die Testpläne voraussetzen, dass der Index einen einzelnen Typ namens *ctip* umfasst.

## Konfigurieren der Testskriptparameter
Kopieren Sie die folgenden Testskriptparameterdateien auf den JMeter-Servercomputer:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Diese Datei gibt die Anzahl der zu verwendenden JMeter-Testthreads, die Dauer des Tests (in Sekunden), die IP-Adresse eines Knotens (oder eines Lastenausgleich) im Elasticsearch-Cluster und den Namen des Clusters an:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Bearbeiten Sie diese Datei, und geben Sie die entsprechenden Werte für Ihren Test und Cluster an.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) und [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Diese beiden Dateien enthalten die gleichen Informationen. Die *win*-Datei ist für Windows-Dateinamen und -Pfade formatiert, und die *nix*-Datei ist für Linux-Dateinamen und Pfade formatiert:

  ```ini
  [DEFAULT]
  debug=true #Falls „true“, werden Konsolenprotokolle angezeigt.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #Pfad, in dem Testergebnisse gespeichert werden.
  jmx=C:\\Users\\administrator1\\testplan.jmx #Pfad zum JMeter-Testplan.
  machines=10.0.0.1,10.0.02,10.0.0.3 #IPs der Elasticsearch-Datenknoten, getrennt durch Kommas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name der Berichte, getrennt durch Kommas. tests=idx1 #Name des zu testenden Elasticsearch-Indexes. properties=run.properties #Name der Eigenschaftendatei.
  ```

  Bearbeiten Sie diese Datei, um die Speicherorte der Testergebnisse, den Namen des JMeter-Testplans, die IP-Adressen der Elasticsearch-Datenknoten, die unformatierte Leistungsdaten enthaltenden Berichte, die generiert werden, und den Namen (bzw. die Namen) des getesteten Indexes anzugeben. Wenn die Datei *run.properties* sich in einem anderen Ordner oder Verzeichnis befindet, geben Sie den vollständigen Pfad zu dieser Datei an.

## Ausführen der Tests

* Kopieren Sie die Datei [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) auf den JMeter-Servercomputer, im gleichen Ordner wie die Dateien *run.properties* und *query-config-win.ini* (*query-config-nix.ini*).

* Stellen Sie sicher, dass *jmeter.bat* (Windows) oder *jmeter.sh* (Linux) sich im ausführbaren Pfad für Ihre Umgebung befinden.

* Starten Sie das Skript *query-test.py* zur Ausführung der Tests über die Befehlszeile:

  ```cmd
  py query-test.py
  ```

* Wenn der Test abgeschlossen ist, werden die Ergebnisse, wie in der Datei *query-config-win.ini* (*query-config-nix.ini*) angegeben, als Satz von CSV-Dateien gespeichert. Sie können diese Daten in Excel analysieren und in Diagrammen darstellen.


[Optimierung der Datenerfassungsleistung für Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
