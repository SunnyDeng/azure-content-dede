<properties 
	pageTitle="Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen | Azure" 
	description="Downsampling von Daten in Azure HDInsight Hive-Tabellen" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="hangzh;bradsev" />

# Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen 

Wenn das DataSet, das Sie analysieren möchten, groß ist, sollten Sie i. d. R. ein Downsampling der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Dies erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Data Science-Ablaufs darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.

In diesem Artikel wird das Downsampling von Daten in Azure HDInsight Hive-Tabellen mithilfe von Hive-Abfragen beschrieben. Dabei werden drei häufig verwendete Samplingmethoden behandelt: Einheitliche Stichproben, Stichproben nach Gruppen und geschichtete Stichproben.

Sie sollten die Hive-Abfragen über die Hadoop-Befehlszeile auf dem Hauptknoten des Hadoop-Clusters übermitteln. Dazu melden Sie sich auf dem Hauptknoten des Hadoop-Clusters an, öffnen die Hadoop-Befehlszeile und übermitteln die Hive-Abfragen von dort aus. Informationen zum Übermitteln von Hive-Abfragen an der Hadoop-Befehlszeile finden Sie unter [Übermitteln von Hive-Abfragen](machine-learning-data-science-process-hive-tables.md#submit). 

## <a name="uniform"></a> Einheitliche Stichproben ##
Bei einheitlichen Stichproben hat jede Zeile im DataSet die gleiche Wahrscheinlichkeit, als Stichprobe verwendet zu werden. Dies kann durch das Hinzufügen des zusätzlichen Felds "rand()" zum DataSet in der inneren "select"-Abfrage und in der äußeren "select"-Abfrage, die von diesem Zufallsfeld abhängt, implementiert werden. 

Dies ist eine Beispielabfrage:

	SET sampleRate=<sample rate, 0-1>;
	select
		field1, field2, ..., fieldN
	from
		(
		select
			field1, field2, ..., fieldN, rand() as samplekey 
		from <hive table name>
		)a
	where samplekey<='${hiveconf:sampleRate}'

Dabei gibt `<sampling rate, 0-1>` den Anteil von Datensätzen an, den die Benutzer erfassen möchten. 

## <a name="group"></a> Zufällige Stichproben nach Gruppen ##

Beim Sampling kategorischer Daten sollten Sie entweder alle Instanzen eines bestimmten Werts einer kategorischen Variable ein- oder ausschließen. Das ist mit "Stichprobe nach Gruppe" gemeint.
Wenn Sie z. B. die kategorische Variable "State" haben, die als Werte "NY", "MA", "CA", "NJ", "PA" usw. hat, möchten Sie natürlich, dass Datensätze desselben Bundesstaates immer zusammenbleiben, ob als Stichprobe oder auch sonst. 

Dies ist eine Beispielabfrage für das Erstellen einer Stichprobe nach Gruppe:

	SET sampleRate=<sample rate, 0-1>;
    select 
		b.field1, b.field2, ..., b.catfield, ..., b.fieldN
	from
		(
		select 
			field1, field2, ..., catfield, ..., fieldN
		from <table name>
		)b
	join
		(
		select
			catfield
		from
			(
			select 
				catfield, rand() as samplekey
			from <table name>
			group by catfield
			)a
		where samplekey<='${hiveconf:sampleRate}'
		)c
	on b.catfield=c.catfield

## <a name="stratified"></a>Geschichtete Stichproben

Stichproben sind in Bezug auf eine kategorische Variable geschichtet, wenn die abgerufenen Beispieldaten Werte dieser Kategorie enthalten, und zwar im gleichen Verhältnis wie in der übergeordneten Datenmenge, aus der die Stichproben abgerufen wurden. Bei dem oben genannten Beispiel besitzen Ihre Daten Teilmengen nach Bundesstaat, "NJ" hat z. B. 100 Vorkommen, "NY" 60 und "WA" hat 300. Wenn Sie die Rate für die geschichtete Stichprobe auf 0,5 festlegen, sollte die abgerufene Stichprobe ungefähr 50, 30 und 150 Vorkommen für "NJ", "NY" und "WA" aufweisen.

Dies ist eine Beispielabfrage:

	SET sampleRate=<sample rate, 0-1>;
    select 
		field1, field2, field3, ..., fieldN, state 
	from
		(
		select 
			field1, field2, field3, ..., fieldN, state,
			count(*) over (partition by state) as state_cnt, 
      		rank() over (partition by state order by rand()) as state_rank
      	from <table name>
		) a 
	where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Informationen zu komplexeren Samplingmethoden, die in Hive verfügbar sind, finden Sie unter [LanguageManual Sampling (in englischer Sprache)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

<!--HONumber=49--> 