<properties
	pageTitle="Übermitteln von Hive-Abfragen an Hadoop-Cluster im erweiterten Analyseprozess | Microsoft Azure"
	description="Verarbeiten von Daten aus Hive-Tabellen mithilfe von Hive-Abfragen"
	services="machine-learning"
	documentationCenter=""
	authors="hangzh-msft"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster im erweiterten Analyseprozess 

In diesem Dokument werden die verschiedenen Möglichkeiten zum Übermitteln von Hive-Abfragen an Hadoop-Cluster beschrieben, die durch einen HDInsight-Dienst in Azure verwaltet werden. Diese Aufgabe gehört zur Advanced Analytics Process and Technology (ADAPT), die von Azure Machine Learning bereitgestellt wird. Es werden mehrere Aufgaben zum Analysieren von Daten erläutert: das Durchsuchen von Daten und das Generieren von Funktionen. Generische Hive-Abfragen zeigen, wie Sie mit Hive in Azure HDInsight Hadoop-Clustern Daten durchsuchen oder Funktionen generieren. Diese Hive-Abfragen verwenden eingebettete Hive-UDFs (User Defined Function, benutzerdefinierte Funktion), die bereitgestellt werden.

Beispiele für Abfragen speziell für Szenarios mit den [NYC Taxi Trip-Daten](http://chriswhong.com/open-data/foil_nyc_taxi/) stehen auch im [GitHub-Repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) bereit. Für diese Abfragen ist bereits ein Datenschema angegeben, sodass sie bereit für die Übermittlung zur Ausführung sind.

Im letzten Abschnitt werden Parameter beschrieben, mit denen Sie die Leistung der Hive-Abfragen optimieren können.

## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage)
* Sie haben einen angepassten Hadoop-Cluster mit dem HDInsight-Dienst bereitgestellt. Anweisungen dazu finden Sie unter [Anpassen von Azure HDInsight Hadoop-Clustern für die erweiterte Analyse](machine-learning-data-science-customize-hadoop-cluster.md).
* Sie haben die Daten in Hive-Tabellen auf Azure HDInsight Hadoop-Clustern hochgeladen. Wenn dies nicht der Fall ist, führen Sie die unter [Erstellen und Laden von Daten in Hive-Tabellen](machine-learning-data-science-move-hive-tables.md) beschriebenen Schritte zum Hochladen von Daten in Hive-Tabellen aus.
* Sie haben den Remotezugriff auf den Cluster aktiviert. Anweisungen finden Sie unter [Zugreifen auf den Hauptknoten von Hadoop-Clustern](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


## <a name="submit"></a>Übermitteln von Hive-Abfragen
Hive-Abfragen können folgendermaßen übermittelt werden:

* an der Hadoop-Befehlszeile auf dem Hauptknoten des Clusters
* in einem IPython Notebook
* im Hive-Editor
* mit Azure PowerShell-Skripts

Hive-Abfragen sind ähnlich wie SQL-Abfragen. Mit SQL vertraute Benutzer finden möglicherweise das <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Cheat Sheet "Hive for SQL Users"</a> nützlich.

Beim Übermitteln von Hive-Abfragen können Sie auch das Ziel der Ausgabe der Hive-Abfragen steuern. Diese kann auf den Bildschirm, in eine lokale Datei auf dem Hauptknoten oder in ein Azure-Blob erfolgen.

### Über die Hadoop-Befehlszeile im Hauptknoten des Hadoop-Clusters

Wenn die Abfrage komplex ist, führen Hive-Abfragen direkt am Hauptknoten des Hadoop-Clusters i. d. R. schneller zu Ergebnissen als das Senden im Hive-Editor oder über Azure PowerShell-Skripts.

Melden Sie sich am Hauptknoten des Hadoop-Clusters an, öffnen Sie die Hadoop-Befehlszeile auf dem Desktop des Hauptknotens, und geben Sie den folgenden Befehl ein:

    cd %hive_home%\bin

Es gibt drei Möglichkeiten zum Übermitteln von Hive-Abfragen an der Hadoop-Befehlszeile.

* direkt über die Hadoop-Befehlszeile
* mithilfe von HQL-Dateien
* an der Hive-Befehlskonsole

#### Übermitteln der Hive-Abfragen direkt an der Hadoop-Befehlszeile

Sie können z. B. folgende Befehle ausführen:

	hive -e "<your hive query>;

Damit übermitteln sie die Hive-Abfragen direkt an der Hadoop-Befehlszeile. Hier ist ein Beispiel. Das rote Kästchen enthält den Befehl, der die Hive-Abfrage übermittelt, und das grüne Kästchen umschließt die Ausgabe der Hive-Abfrage.

![Arbeitsbereich erstellen][10]

#### Übermitteln der Hive-Abfragen in HQL-Dateien

Wenn die Hive-Abfrage sehr komplex ist und aus mehreren Zeilen besteht, ist das direkte Bearbeiten der Abfragen an der Hadoop-Befehlszeile oder an der Hive-Konsole nicht sehr praktikabel. Alternativ können die Hive-Abfragen mit einem Text-Editor auf dem Hauptknoten des Hadoop-Clusters in einer HQL-Datei in einem lokalen Verzeichnis auf dem Hauptknoten gespeichert werden. Anschließend kann die Hive-Abfrage in der HQL-Datei mithilfe des `-f`-Arguments im `hive`-Befehl übermittelt werden:

	`hive -f "<path to the .hql file>"`


#### Unterdrücken der Fortschrittsanzeige bei Hive-Abfragen

Standardmäßig wird bei Hive-Abfragen, die über die Hadoop-Befehlszeile übermittelt werden, der Fortschritt des Map/Reduce-Auftrags auf dem Bildschirm angezeigt. Um diese Fortschrittsanzeige zum Map/Reduce-Auftrag zu unterdrücken, können Sie das Argument `-S` (mit Unterscheidung von Groß-/Kleinschreibung) wie folgt an der Befehlszeile angeben:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Übermitteln von Hive-Abfragen an der Hive-Befehlskonsole

Sie können auch die Hive-Befehlskonsole starten, indem Sie den `hive`-Befehl an der Hadoop-Befehlszeile eingeben und dann Hive-Abfragen über die Hive-Befehlskonsole an der Eingabeaufforderung **hive>** übermitteln. Beispiel:

![Arbeitsbereich erstellen][11]

In diesem Beispiel kennzeichnen die beiden roten Kästchen die Befehle, die zum Starten der Hive-Befehlskonsole verwendet werden, und die Hive-Abfrage, die über die Hive-Befehlskonsole übermittelt wird. Das grüne Kästchen markiert die Ausgabe der Hive-Abfrage.

In den vorherigen Beispielen wurden die Ergebnisse der Hive-Abfrage direkt auf den Bildschirm ausgeben. Sie können die Ausgabe aber auch in eine lokale Datei auf dem Hauptknoten oder in ein Azure-Blob schreiben. Sie können dann andere Tools verwenden, um die Ausgabe der Hive-Abfragen weiter zu analysieren.

#### Ausgeben der Hive-Abfrageergebnisse in eine lokale Datei

Für die Ausgabe der Hive-Abfrageergebnisse in ein lokales Verzeichnis auf dem Hauptknoten müssen Sie die Hive-Abfrage an der Hadoop-Befehlszeile wie folgt übermitteln:

	`hive -e "<hive query>" > <local path in the head node>`


#### Ausgeben der Hive-Abfrageergebnisse in ein Azure-Blob

Sie können die Hive-Abfrageergebnisse auch in ein Azure-Blob im Standardcontainer des Hadoop-Clusters ausgeben. Die Hive-Abfrage hierfür sieht folgendermaßen aus:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Im folgenden Beispiel wird die Ausgabe der Hive-Abfrage in das Blob-Verzeichnis `queryoutputdir` innerhalb des Standardcontainer des Hadoop-Cluster geschrieben. Hier müssen Sie nur den Namen des Verzeichnisses ohne den Namen des Blobs angeben. Es wird ein Fehler ausgelöst, wenn Sie sowohl den Verzeichnis- als auch den Blobnamen angeben, z. B. **wasb:///queryoutputdir/queryoutput.txt*.

![Arbeitsbereich erstellen][13]

Sie können die Ausgabe der Hive-Abfrage im Blob-Speicher anzeigen, indem Sie den Standardcontainer des Hadoop-Clusters mithilfe des Azure-Speicher-Explorers (oder eines ähnlichen Tools) öffnen. Sie können Filter (markiert durch den roten Kasten) anwenden, um nur das Blob mit den angegebenen Buchstaben im Namen abzurufen.

![Arbeitsbereich erstellen][14]

### Über den Hive-Editor oder Azure PowerShell-Befehle

Sie können auch die Abfrage-Konsole (Hive-Editor) verwenden, indem Sie die URL

*https://&#60;Hadoop Clustername>.azurehdinsight.net/Home/HiveEditor*

in einem Webbrowser eingeben. Beachten Sie, dass Sie zur Eingabe der Anmeldeinformationen für den Hadoop-Cluster aufgefordert werden. Alternativ können Sie [Hive-Aufträge mit PowerShell übermitteln](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


##<a name="hive-dataexploration"></a>Durchsuchen von Daten
Hier finden Sie einige Hive-Beispielskripts, die zum Durchsuchen von Daten in den Hive-Tabellen verwendet werden können.

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

##<a name="hive-featureengineering"></a>Generieren von Funktionen

In diesem Abschnitt werden Methoden zum Generieren von Funktionen mithilfe von Hive-Abfragen beschrieben.

> [AZURE.NOTE]Bei den Beispiel-Hive-Abfragen in diesem Abschnitt wird davon ausgegangen, dass die Daten in Hive-Tabellen in Azure HDInsight Hadoop-Clustern hochgeladen wurden. Wenn dies nicht der Fall ist, führen Sie die unter [Erstellen und Laden von Daten in Hive-Tabellen](machine-learning-data-science-hive-tables.md) beschriebenen Schritte zum Hochladen von Daten in Hive-Tabellen aus.

Wenn Sie zusätzliche Funktionen generiert haben, können Sie diese als Spalten in der vorhandenen Tabelle hinzufügen oder eine neue Tabelle mit den zusätzlichen Funktionen und einem Primärschlüssel erstellen, die dann mit der ursprünglichen Tabelle zusammengeführt werden kann.

1. [Häufigkeitsbasierte Funktionsgenerierung](#hive-frequencyfeature)
2. [Risiken kategorischer Variablen in binären Klassifizierungen](#hive-riskfeature)
3. [Extrahieren von Funktionen aus "datetime"-Feldern](#hive-datefeatures)
4. [Extrahieren von Funktionen aus "Text"-Feldern](#hive-textfeatures)
5. [Berechnen der Entfernung zwischen GPS-Koordinaten](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Häufigkeitsbasierte Funktionsgenerierung

Oft ist es sinnvoll, die Häufigkeit der Ebenen einer kategorischen Variable oder die Häufigkeiten von bestimmten Ebenenkombinationen aus mehreren kategorischen Variablen zu berechnen. Mit dem folgenden Skript können Sie die Häufigkeiten berechnen:

		select
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select
				<column_name1>,<column_name2>, count(*) as sub_count
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;


###<a name="hive-riskfeature"></a>Risiken kategorischer Variablen in binären Klassifizierungen

Bei binären Klassifizierungen müssen manchmal nicht numerische kategorische Variablen in numerische Funktionen umgewandelt werden, wenn die Modelle nur numerische Funktionen akzeptieren. Dazu werden die nicht numerischen Ebenen durch numerische Risiken ersetzt. In diesem Abschnitt werden einige generische Hive-Abfragen zur Berechnung der Risikowerte (logarithmische Wahrscheinlichkeiten) einer kategorischen Variable gezeigt.


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select
	    	<column_name1>,<column_name2>,
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename>
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b

In diesem Beispiel werden die Variablen `smooth_param1` und `smooth_param2` zum Glätten der aus den Daten berechneten Risikowerte festgelegt. Die Risiken liegen zwischen "-Inf" und "Inf". Ein Risiko > 0 steht für die Wahrscheinlichkeit, dass das Ziel 1 größer als 0,5 ist.

Nach dem Berechnen der Risikotabelle können Sie einer Tabelle Risikowerte zuweisen, indem Sie sie mit der Risikotabelle zusammenführen. Die Hive-Abfrage für das Zusammenführen wurde im vorherigen Abschnitt beschrieben.

###<a name="hive-datefeatures"></a>Extrahieren von Funktionen aus "datetime"-Feldern

Hive bietet eine Reihe von UDFs für die Verarbeitung von "datetime"-Feldern. In Hive lautet das Standardformat für "datetime" "JJJJ-MM-TT 00:00:00" (wie z. B. in "1970-01-01 12:21:32"). In diesem Abschnitt werden Beispiele für das Extrahieren des Tags eines Monats und des Monats aus einem "datetime"-Feld gezeigt. Außerdem gibt es Beispiele zum Konvertieren einer "datetime"-Zeichenfolge in ein anderes Format als das Standardformat.

    	select day(<datetime field>), month(<datetime field>)
		from <databasename>.<tablename>;

Bei dieser Hive-Abfrage wird vorausgesetzt, dass das *&#60;Datum/Uhrzeit-Feld>* im Standardformat für "datetime" vorliegt.

Liegt ein "datetime"-Feld nicht im Standardformat vor, muss zunächst das "datetime"-Feld in einen Unix-Zeitstempel konvertierten werden, der dann in eine "datetime"-Zeichenfolge im Standardformat konvertiert wird. Wenn "datetime" das Standardformat aufweist, können Sie die eingebetteten "datetime"-UDFs anwenden, um Funktionen zu extrahieren.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

Wenn das *&#60;Datum/Uhrzeit-Feld>* bei dieser Abfrage das Muster *03/26/2015 12:04:39* hat, muss `'MM/dd/yyyy HH:mm:ss'` das *'&#60;Muster des Datum/Uhrzeit-Felds>'* sein. Zum Testen können Sie folgenden Code ausführen:

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

Die Tabelle *hivesampletable* in dieser Abfrage enthält in der Voreinstellung alle Azure HDInsight Hadoop-Cluster, wenn die Cluster bereitgestellt wurden.


###<a name="hive-textfeatures"></a>Extrahieren von Funktionen aus "Text"-Feldern

Wenn die Hive-Tabelle ein Textfeld enthält, das eine Zeichenfolge von durch Leerzeichen voneinander getrennten Wörtern ist, können Sie mit der folgenden Abfrage die Länge der Zeichenfolge und die Anzahl der Wörter in der Zeichenfolge extrahieren.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Berechnen der Entfernung zwischen GPS-Koordinaten

Die in diesem Abschnitt angegebene Abfrage kann direkt auf die "NYC Taxi Trip"-Daten angewendet werden. Diese Abfrage soll veranschaulichen, wie Sie die eingebetteten mathematischen Funktionen in Hive zum Generieren von Funktionen verwenden.

Die in dieser Abfrage verwendeten Felder sind GPS-Koordinaten von Start- und Zielorten mit den Bezeichnungen *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* und *dropoff\_latitude*. Die Abfragen zur Berechnung der direkten Entfernung zwischen den Start- und Zielkoordinaten sind:

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
		from nyctaxi.trip
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10;

Die mathematischen Gleichungen zur Berechnung der Entfernung zwischen zwei GPS-Koordinaten finden Sie auf der Website <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> von Peter Lapisu. In seinem JavaScript-Code ist die `toRad()`-Funktion nur *lat\_or\_lon*pi/180* und rechnet Grad in Radianten um. Hierbei ist *lat\_or\_lon* der Längen- oder Breitengrad. Da Hive keine `atan2`-Funktion bereitstellt, jedoch die `atan`-Funktion, wird die `atan2`-Funktion in der oben angegebenen Hive-Abfrage durch die `atan`-Funktion anhand der in <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a> angegebenen Definition implementiert.

![Arbeitsbereich erstellen][1]

Eine vollständige Liste der eingebettete Hive-UDFs finden Sie im Abschnitt **Built-in Functions** im <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive-Wiki</a>.

## <a name="tuning"></a>Weiterführende Themen: Optimieren von Hive-Parametern zur Verbesserung der Abfrage-Geschwindigkeit

Die Standardeinstellungen für die Parameter von Hive-Clustern eignen sich möglicherweise nicht für die Hive-Abfragen und die von den Abfragen verarbeiteten Daten. In diesem Abschnitt werden einige Parameter beschrieben, mit denen Sie die Leistung der Hive-Abfragen optimieren können. Sie müssen die Abfragen zur Parameteroptimierung vor den Abfragen der Verarbeitungsdaten einfügen.

1. **Java-Heapspeicher**: Für Abfragen, bei denen große Datasets zusammengeführt oder lange Datensätze verarbeitet werden, besteht ein typischer Fehler darin, **dass nicht über genügend Heapspeicher verfügt**. Dies kann durch Festlegen der Parameter *mapreduce.map.java.opts* und *mapreduce.task.io.sort.mb* auf die gewünschten Werte optimiert werden. Beispiel:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;


	Dieser Parameter ordnet dem Java-Heapspeicher 4 GB Arbeitsspeicher zu. Außerdem macht er die Sortierung effizienter, da er ihr mehr Arbeitsspeicher zuweist. Sie sollten etwas mit diesen Zuordnungen experimentieren, wenn bei einem Auftrag Fehler im Zusammenhang mit dem Heapspeicher auftreten.

2. **DFS-Blockgröße**: Mit diesem Parameter wird die kleinste Dateneinheit festgelegt, die das Dateisystem speichert. Wenn beispielsweise die DFS-Blockgröße 128 MB beträgt, werden alle Daten bis zu einer Größe von 128 MB in einem Block gespeichert. Daten, die größer als 128 MB sind, werden zusätzliche Blöcke zugewiesen. Die Auswahl einer sehr geringen Blockgröße führt zu starkem zusätzlichem Verbrauch in Hadoop, da der Namensknoten viel mehr Anforderungen verarbeiten muss, um den entsprechenden Block für die Datei zu finden. Eine empfohlene Einstellung bei der Arbeit mit Daten im Gigabytebereich (oder größer) ist:

		set dfs.block.size=128m;

3. **Optimieren von Join-Vorgängen in Hive**: Join-Vorgänge im Map/Reduce-Framework erfolgen meist in der Reduce-Phase. In einigen Fällen können jedoch enorme Vorteile erzielt werden, indem die Join-Vorgänge in die Map-Phase verlegt werden (so genannte "mapjoins"). Damit Hive dies möglichst häufig durchführt, legen Sie Folgendes fest:

		set hive.auto.convert.join=true;

4. **Angeben der Anzahl von Mappern für Hive**: Hadoop erlaubt das Festlegen der Anzahl von Reducern. Die Anzahl der Mapper kann aber in der Regel nicht vom Benutzer festgelegt werden. Mit einem Trick erhalten Sie ein gewisses Maß an Kontrolle über diese Anzahl: Wählen Sie die Hadoop-Variablen *mapred.min.split.size* und *mapred.max.split.size* aus, da die Größe der einzelnen Map-Aufgaben durch Folgendes bestimmt wird:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Der Standardwert von *mapred.min.split.size* ist 0, der von *mapred.max.split.size* ist **Long.MAX** und der von *dfs.block.size* ist 64 MB. Bei entsprechender Datengröße können Sie durch Festlegen dieser Parameter die Anzahl der verwendeten Mapper optimieren.

5. Einige andere **weiterführende Optionen** zur Optimierung der Leistung finden Sie unten. Diese ermöglichen das Festlegen des zugeordneten Speichers für "map"- und "reduce"-Aufgaben, die beim Optimieren der Leistung nützlich sein können. Beachten Sie aber, dass *mapreduce.reduce.memory.mb* nicht größer sein darf als die Größe des physischen Speichers der einzelnen Workerknoten im Hadoop-Cluster.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-process-hive-tables/atan2new.png
[10]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-3.png
 

<!---HONumber=Oct15_HO4-->