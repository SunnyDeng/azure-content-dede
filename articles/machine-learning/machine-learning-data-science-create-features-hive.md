<properties
	pageTitle="Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen | Microsoft Azure"
	description="Beispiele für Hive-Abfragen, mit denen Funktionen in den Daten erstellt werden, die in einem Azure HDInsight Hadoop-Cluster gespeichert sind."
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
	ms.date="02/05/2016"
	ms.author="hangzh;bradsev" />

#Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen

## Einführung
Es werden Beispiele für Hive-Abfragen vorgestellt, mit denen Funktionen in den Daten erstellt werden, die in einem Azure HDInsight Hadoop-Cluster gespeichert sind. Diese Hive-Abfragen verwenden eingebettete Hive-UDFs (User Defined Function, benutzerdefinierte Funktion), für die die Skripts bereitgestellt werden.

Beispiele für Abfragen speziell für Szenarios mit den [NYC Taxi Trip-Daten](http://chriswhong.com/open-data/foil_nyc_taxi/) stehen auch im [GitHub-Repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) bereit. Für diese Abfragen ist bereits ein Datenschema angegeben, sodass sie bereit für die Übermittlung zur Ausführung sind.

Im letzten Abschnitt werden Parameter beschrieben, mit denen Sie die Leistung der Hive-Abfragen optimieren können.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]
Dieses **Menü** stellt eine Verknüpfung mit Themen dar, in denen beschrieben wird, wie Features für Daten in verschiedenen Umgebungen erstellt werden. Diese Aufgabe ist ein Teil des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../hdinsight-get-started.md#storage)
* Sie haben einen angepassten Hadoop-Cluster mit dem HDInsight-Dienst bereitgestellt. Anweisungen hierzu finden Sie unter [Anpassen von Azure HDInsight Hadoop-Clustern für die erweiterte Analyse](machine-learning-data-science-customize-hadoop-cluster.md).
* Sie haben die Daten in Hive-Tabellen auf Azure HDInsight Hadoop-Clustern hochgeladen. Wenn dies nicht der Fall ist, führen Sie die unter [Erstellen und Laden von Daten in Hive-Tabellen](machine-learning-data-science-move-hive-tables.md) beschriebenen Schritte zum Hochladen von Daten in Hive-Tabellen aus.
* Sie haben den Remotezugriff auf den Cluster aktiviert. Anweisungen finden Sie unter [Zugreifen auf den Hauptknoten von Hadoop-Clustern](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Generieren von Funktionen

In diesem Abschnitt sind verschiedene Beispiele dafür enthalten, wie Funktionen mithilfe von Hive-Abfragen generiert werden können. Wenn Sie zusätzliche Funktionen generiert haben, können Sie diese als Spalten in der vorhandenen Tabelle hinzufügen oder eine neue Tabelle mit den zusätzlichen Funktionen und einem Primärschlüssel erstellen, die dann mit der ursprünglichen Tabelle zusammengeführt werden kann. Beispiele:

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

Bei dieser Hive-Abfrage wird vorausgesetzt, dass das *&#60;Datum/Uhrzeit-Feld>* im Standardformat für „datetime“ vorliegt.

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

Die mathematischen Gleichungen zur Berechnung der Entfernung zwischen zwei GPS-Koordinaten finden Sie auf der Website <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> von Peter Lapisu. In seinem JavaScript-Code ist die `toRad()`-Funktion nur *lat\_or\_lon*pi/180* und rechnet Grad in Radianten um. Hierbei ist *lat\_or\_lon* der Längen- oder Breitengrad. Da Hive keine `atan2`-Funktion bereitstellt, jedoch die `atan`-Funktion, wird die `atan2`-Funktion in der oben angegebenen Hive-Abfrage anhand der in <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a> angegebenen Definition durch die `atan`-Funktion implementiert.

![Arbeitsbereich erstellen][1]

Eine vollständige Liste der eingebetteten Hive-UDFs finden Sie im Abschnitt **Built-in Functions** im <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive-Wiki</a>.

## <a name="tuning"></a>Weiterführende Themen: Optimieren von Hive-Parametern zur Verbesserung der Abfrage-Geschwindigkeit

Die Standardeinstellungen für die Parameter von Hive-Clustern eignen sich möglicherweise nicht für die Hive-Abfragen und die von den Abfragen verarbeiteten Daten. In diesem Abschnitt werden einige Parameter beschrieben, mit denen Sie die Leistung der Hive-Abfragen optimieren können. Sie müssen die Abfragen zur Parameteroptimierung vor den Abfragen der Verarbeitungsdaten einfügen.

1. **Java-Heapspeicher**: Für Abfragen, bei denen große Datasets zusammengeführt oder lange Datensätze verarbeitet werden, besteht ein typischer Fehler darin, **dass nicht genügend Heapspeicher verfügbar ist**. Dies kann durch Festlegen der Parameter *mapreduce.map.java.opts* und *mapreduce.task.io.sort.mb* auf die gewünschten Werte optimiert werden. Beispiel:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;


	Dieser Parameter ordnet dem Java-Heapspeicher 4 GB Arbeitsspeicher zu. Außerdem macht er die Sortierung effizienter, da er ihr mehr Arbeitsspeicher zuweist. Sie sollten etwas mit diesen Zuordnungen experimentieren, wenn bei einem Auftrag Fehler im Zusammenhang mit dem Heapspeicher auftreten.

2. **DFS-Blockgröße**: Mit diesem Parameter wird die kleinste Dateneinheit festgelegt, die das Dateisystem speichert. Wenn beispielsweise die DFS-Blockgröße 128 MB beträgt, werden alle Daten bis zu einer Größe von 128 MB in einem Block gespeichert. Daten, die größer als 128 MB sind, werden zusätzliche Blöcke zugewiesen. Die Auswahl einer sehr geringen Blockgröße führt zu starkem zusätzlichem Verbrauch in Hadoop, da der Namensknoten viel mehr Anforderungen verarbeiten muss, um den entsprechenden Block für die Datei zu finden. Eine empfohlene Einstellung bei der Arbeit mit Daten im Gigabytebereich (oder größer) ist:

		set dfs.block.size=128m;

3. **Optimieren von Join-Vorgängen in Hive**: Join-Vorgänge im Map/Reduce-Framework erfolgen meist in der Reduce-Phase. In einigen Fällen können jedoch enorme Vorteile erzielt werden, indem die Join-Vorgänge in die Map-Phase verlegt werden (so genannte „mapjoins“). Damit Hive dies möglichst häufig durchführt, legen Sie Folgendes fest:

		set hive.auto.convert.join=true;

4. **Angeben der Anzahl von Mappern für Hive**: Hadoop erlaubt das Festlegen der Anzahl von Reducern. Die Anzahl der Mapper kann aber in der Regel nicht vom Benutzer festgelegt werden. Mit einem Trick erhalten Sie ein gewisses Maß an Kontrolle über diese Anzahl: Wählen Sie die Hadoop-Variablen *mapred.min.split.size* und *mapred.max.split.size* aus, da die Größe der einzelnen Map-Aufgaben durch Folgendes bestimmt wird:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Der Standardwert von *mapred.min.split.size* ist 0, der von *mapred.max.split.size* ist **Long.MAX** und der von *dfs.block.size* ist 64 MB. Bei entsprechender Datengröße können Sie durch Festlegen dieser Parameter die Anzahl der verwendeten Mapper optimieren.

5. Einige andere **weiterführende Optionen** zur Optimierung der Hive-Leistung finden Sie unten. Diese ermöglichen das Festlegen des zugeordneten Speichers für "map"- und "reduce"-Aufgaben, die beim Optimieren der Leistung nützlich sein können. Beachten Sie aber, dass *mapreduce.reduce.memory.mb* nicht größer sein darf als die Größe des physischen Speichers der einzelnen Workerknoten im Hadoop-Cluster.

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
 

<!----HONumber=AcomDC_0211_2016-->