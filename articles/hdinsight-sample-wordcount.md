<properties linkid="manage-services-hdinsight-sample-wordcount" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight WordCount sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight WordCount sample" authors="bradsev" />

Das HDInsight Wortzählungs-Beispiel
===================================

Dieses Beispiel beschreibt die Ausführung eines MapReduce-Programms, das die Auftretenshäufigkeit von Wörtern in einem Text mit Azure HDInsight und Azure PowerShell schätzt. Das Wortzählungs-MapReduce-Programm ist in Java geschrieben und läuft auf einem HDInsight-Cluster. Bei dem hier analysierten Text handelt es sich um die Project Gutenberg eBook-Auflage der Notizbücher von Leonardo Da Vinci.

Das Hadoop MapReduce-Programm liest die Textdatei ein und zählt, wie oft jedes einzelne Wort vorkommt. Die Ausgabe erfolgt in einer Textdatei mit Zeilen, von denen jede einzelne ein Wort und dessen Häufigkeit (ein durch Tabulator getrenntes Schlüssel/Wert-Paar) im Dokument. Der Prozess besteht aus zwei Phasen. Der Mapper verwendet die einzelnen Zeilen des Eingabetexts als Eingabe und unterteilt diese in Wörter. Er gibt bei jedem Vorkommen eines Worts ein Schlüssel/Wert-Paar des Worts gefolgt von einer 1 aus. Der Reducer addiert die einzelnen Zählwerte jedes Worts und gibt ein einziges Schlüssel/Wert-Paar aus, das aus dem Wort gefolgt von der Summe seiner Vorkommen besteht.

**Sie erhalten Informationen zu folgenden Themen:**

-   Verwenden der Azure PowerShell zur Ausführung eines MapReduce-Programms in einem Azure HDInsight-Cluster.
-   Erstellen von MapReduce-Programmen in Java.

**Voraussetzungen**:

-   Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Sie benötigen ein bereitgestelltes HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Erste Schritte mit Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) oder [Bereitstellen eines HDInsight-Clusters](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)

-   Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)

Themen in diesem Artikel
------------------------

In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:

1.  [Ausführen des Beispiels mit Azure PowerShell](#run-sample)
2.  [Der Java-Code für das Wortzählungs MapReduce-Programm](#java-code)
3.  [Zusammenfassung](#summary)
4.  [Nächste Schritte](#next-steps)

Ausführen des Beispiels mit Azure PowerShell
--------------------------------------------

**So übermitteln Sie den MapReduce-Job**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"   # Azure-Abonnementname
         $clusterName = "<ClusterName>"             # HDInsight-Clustername

3.  Führen Sie den folgenden Befehl aus, um eine MapReduce-Jobdefinition zu erstellen:

         # MapReduce-Job definieren
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.

    Die Datei "hadoop-examples.jar" ist im HDInsight-Cluster enthalten. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Die Quelldatei ist im HDInsight-Cluster enthalten, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

4.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu übermitteln:

         # Job übermitteln
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten.

5.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:

         # Job-Ausgabe abrufen
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**So rufen Sie die Ergebnisse des MapReduce-Jobs ab**

1.  Öffnen Sie **Azure PowerShell**.
2.  Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"       # Azure-Abonnementname
            
         $storageAccountName = "<StorageAccountName>"   # Azure-Speicherkontoname
         $containerName = "<ContainerName>"              # Blob-Speichercontainername

    Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

3.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

         # Aktuelles Abonnement auswählen
         Select-AzureSubscription $subscriptionName

         # Speicherkonto-Kontextobjekt erstellen
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* wird verwendet, um das aktuelle Abonnement festzulegen, falls sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

4.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe aus dem Blob-Container auf die Arbeitsstation herunterzuladen:

         # Job-Ausgabe auf den Arbeitsplatz herunterladen
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Der Ordner *example/data/WordCountOutput* ist der Ausgabeordner, den Sie bei der Ausführung des MapReduce-Jobs angegeben haben. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur in den lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner *C:\\example\\data\\WordCountOutput* heruntergeladen.

5.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Der MapReduce-Job produziert eine Datei namens *part-r-00000*, welche die Wörter und deren Anzahl enthält. Das Skript verwendet den Befehl "findstr", um alle Wörter aufzulisten, die *there* enthalten.

Die Ausgabe des WordCount-Skripts wird im Befehlsfenster angezeigt:

![HDI.Sample.WordCount.Output](./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png)

Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

Der Java-Code für das Wortzählungs MapReduce-Programm
-----------------------------------------------------

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
    System.exit(job.waitForCompletion(true) 
     0 : 1);

    } }

Zusammenfassung
---------------

Dieses Beispiel beschreibt die Ausführung eines MapReduce-Programms, das die Auftretenshäufigkeit von Wörtern in einem Text mit Azure HDInsight und Azure PowerShell zählt.

Nächste Schritte
----------------

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

-   [Erste Schritte mit Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Beispiel: 10-GB-Graysort](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Beispiel: Pi-Schätzung](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Beispiel: C\# Streaming](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Verwenden von Pig mit HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

