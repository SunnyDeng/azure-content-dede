<properties
	pageTitle="Durchsuchen von Daten in Hive-Tabellen mithilfe von Hive-Abfragen | Microsoft Azure"
	description="Durchsuchen Sie Daten in Hive-Tabellen mithilfe von Hive-Abfragen."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="hangzh;bradsev" />

# Durchsuchen von Daten in Hive-Tabellen mithilfe von Hive-Abfragen 

Dieses **Menü** enthält Links zu Themen, die die Verwendung von Tools zum Untersuchen von Daten aus verschiedenen Speicherumgebungen beschreiben. Diese Aufgabe ist ein Teil des Cortana-Analyseprozesses (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## Einführung

Dieses Dokument enthält Hive-Beispielskripts, die zum Durchsuchen von Daten in den Hive-Tabellen verwendet werden.

## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage)
* Sie haben einen angepassten Hadoop-Cluster mit dem HDInsight-Dienst bereitgestellt. Anweisungen dazu finden Sie unter [Anpassen von Azure HDInsight Hadoop-Clustern für die erweiterte Analyse](machine-learning-data-science-customize-hadoop-cluster.md).
* Sie haben die Daten in Hive-Tabellen auf Azure HDInsight Hadoop-Clustern hochgeladen. Wenn dies nicht der Fall ist, führen Sie die unter [Erstellen und Laden von Daten in Hive-Tabellen](machine-learning-data-science-move-hive-tables.md) beschriebenen Schritte zum Hochladen von Daten in Hive-Tabellen aus.
* Sie haben den Remotezugriff auf den Cluster aktiviert. Anweisungen finden Sie unter [Zugreifen auf den Hauptknoten von Hadoop-Clustern](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Informationen zum Übermitteln von Hive-Abfragen finden Sie unter [Übermitteln von Hive-Abfragen](machine-learning-data-science-move-hive-tables.md#submit)

## Hive-Beispielabfrageskripts für das Durchsuchen von Daten

1. Abrufen der Anzahl der Vorkommen pro Partition `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Abrufen der Anzahl der Vorkommen pro Tag `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Abrufen der Ebenen in einer Kategoriespalte `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Abrufen der Anzahl von Ebenen in Kombination zweier kategorischer Spalten `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Abrufen der Verteilung der numerischen Spalten `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extrahieren von Datensätzen durch Zusammenführen von zwei Tabellen:

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## Zusätzliche Abfrageskripts für Szenarios mit Taxi-Verkehrsdaten

Beispiele für Abfragen speziell für Szenarios mit den [NYC Taxi Trip-Daten](http://chriswhong.com/open-data/foil_nyc_taxi/) stehen auch im [GitHub-Repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) bereit. Für diese Abfragen ist bereits ein Datenschema angegeben, sodass sie bereit für die Übermittlung zur Ausführung sind.

 

<!---HONumber=Oct15_HO4-->