<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Das Wortz&auml;hlungsbeispiel mit Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="Erfahren Sie, wie Sie ein einfaches MapReduce-Beispiel in HDInsight ausf&uuml;hren k&ouml;nnen." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Das Wortz&auml;hlungsbeispiel mit Hadoop in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Das Wortzählungsbeispiel mit Hadoop in HDInsight

In diesem Thema führen Sie ein Hadoop MapReduce-Programm aus, das Vorkommnisse von Wörtern in einer Textdatei zählt, indem Sie das Programm mithilfe von Azure PowerShell in HDInsight ausführen. Das MapReduce-Wortzählungsbeispiel ist in Java geschrieben und wird auf einem von HDInsight verwalteten Hadoop-Cluster ausgeführt. In diesem Beispiel werden wir die Project Gutenberg-eBook-Ausgabe der Notizbücher von Leonardo Da Vinci als Textdatei analysieren.

Das Hadoop MapReduce-Programm liest die Textdatei ein und zählt, wie oft jedes einzelne Wort vorkommt. Die Ausgabe erfolgt in einer Textdatei mit Zeilen, von denen jede einzelne ein Wort und dessen Häufigkeit (ein durch Tabulator getrenntes Schlüssel/Wert-Paar) im Dokument. Der Prozess besteht aus zwei Phasen. Der Mapper verwendet die einzelnen Zeilen des Eingabetexts als Eingabe und unterteilt diese in Wörter. IEr gibt bei jedem Vorkommen eines Worts ein Schlüssel/Wert-Paar des Worts gefolgt von einer 1 aus. Der Reducer addiert die einzelnen Zählwerte jedes Worts und gibt ein einziges Schlüssel/Wert-Paar aus, das aus dem Wort gefolgt von der Summe seiner Vorkommen besteht.

**Sie erhalten Informationen zu folgenden Themen:**

-   Verwenden der Azure PowerShell zur Ausführung eines MapReduce-Programms in einem Azure HDInsight-Cluster.
-   Erstellen von MapReduce-Programmen in Java.

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren][Azure kostenlos ausprobieren].

-   Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight] oder [Bereitstellen eines HDInsight-Clusters][Bereitstellen eines HDInsight-Clusters]

-   Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell]

## Themen in diesem Artikel

In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:

1.  [Ausführen des Beispiels mit Azure PowerShell][Ausführen des Beispiels mit Azure PowerShell]
2.  [Der Java-Code für das MapReduce-Programm zur Wortzählung][Der Java-Code für das MapReduce-Programm zur Wortzählung]
3.  [Zusammenfassung][Zusammenfassung]
4.  [Nächste Schritte][Nächste Schritte]

## <span id="run-sample"></span></a>Ausführen des Beispiels mit Azure PowerShell

**So übermitteln Sie den MapReduce-Job**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

2.  Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Führen Sie den folgenden Befehl aus, um eine MapReduce-Jobdefinition zu erstellen:

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.

    Die Datei "hadoop-examples.jar" ist im HDInsight-Cluster enthalten. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Die Quelldatei ist im HDInsight-Cluster enthalten, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

4.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu übermitteln:

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten.

5.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**So rufen Sie die Ergebnisse des MapReduce-Jobs ab**

1.  Öffnen Sie **Azure PowerShell**.
2.  Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

    Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

3.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* wird verwendet, um das aktuelle Abonnement festzulegen, falls sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

4.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe aus dem Blob-Container auf die Arbeitsstation herunterzuladen:

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Der Ordner *example/data/WordCountOutput* ist der Ausgabeordner, den Sie bei der Ausführung des MapReduce-Jobs angegeben haben. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur in den lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner *C:\\example\\data\\WordCountOutput* heruntergeladen.

5.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Der MapReduce-Job produziert eine Datei namens *part-r-00000*, die die Wörter und deren Anzahl enthält. Das Skript verwendet den Befehl "findstr", um alle Wörter aufzulisten, die *"there"* enthalten.

Die Ausgabe des WordCount-Skripts wird im Befehlsfenster angezeigt:

![HDI.Sample.WordCount.Output][HDI.Sample.WordCount.Output]

Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

## <span id="java-code"></span></a>Der Java-Code für das MapReduce-Programm zur Wortzählung

    package org.apache.hadoop.examples;
    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

    public static class TokenizerMapper 
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
      
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
      }
    }

    public static class IntSumReducer 
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
        }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
    }

## <span id="summary"></span></a>Zusammenfassung

Dieses Beispiel beschreibt die Ausführung eines MapReduce-Programms, das die Auftretenshäufigkeit von Wörtern in einem Text mit Azure HDInsight und Azure PowerShell zählt.

## <span id="next-steps"></span></a>Nächste Schritte

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]
-   [Beispiel: 10-GB-Graysort][Beispiel: 10-GB-Graysort]
-   [Beispiel: Pi-Schätzung][Beispiel: Pi-Schätzung]
-   [Beispiel: C# Streaming][Beispiel: C# Streaming]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Dokumentation zum Azure HDInsight SDK][Dokumentation zum Azure HDInsight SDK]

  [Azure kostenlos ausprobieren]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Bereitstellen eines HDInsight-Clusters]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Ausführen des Beispiels mit Azure PowerShell]: #run-sample
  [Der Java-Code für das MapReduce-Programm zur Wortzählung]: #java-code
  [Zusammenfassung]: #summary
  [Nächste Schritte]: #next-steps
  [HDI.Sample.WordCount.Output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
  [Beispiel: 10-GB-Graysort]: ../hdinsight-sample-10gb-graysort/
  [Beispiel: Pi-Schätzung]: ../hdinsight-sample-pi-estimator/
  [Beispiel: C# Streaming]: ../hdinsight-sample-csharp-streaming/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Dokumentation zum Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx
