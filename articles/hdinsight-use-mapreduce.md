<properties linkid="manage-services-hdinsight-howto-mapreduce" urlDisplayName="MapReduce with HDInsight " pageTitle="Use MapReduce with HDInsight | Azure" metaKeywords="" description="Learn how to use HDInsight to execute a simple Hadoop MapReduce job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use MapReduce with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden von MapReduce mit HDInsight
=====================================

Hadoop MapReduce ist ein Software-Framework zum Schreiben von Anwendungen, die riesige Datenmengen (Big Data) verarbeiten. In diesem Lernprogramm übermitteln Sie von Ihrer Arbeitsstation aus unter Verwendung von Azure PowerShell ein MapReduce-Programm, das Worthäufigkeiten in einem Text zählt, an einen HDInsight-Cluster. Das Wortzählprogramm ist in Java geschrieben und im HDInsight-Cluster enthalten.

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Einen HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters](/de-de/manage/services/hdinsight/provision-hdinsight-clusters/)

-   Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/manage/install-and-configure-windows-powershell/).

**Geschätzter Zeitaufwand:** 30 Minuten

Dieses Lernprogramm umfasst folgende Punkte
-------------------------------------------

1.  [Das Szenario](#scenario)
2.  [Ausführen des Beispiels mit Azure PowerShell](#run-sample)
3.  [Der Java-Code für das MapReduce-Wortzählprogramm](#java-code)
4.  [Nächste Schritte](#next-steps)

Das Szenario
------------

Im folgenden Diagramm wird die Funktionsweise von MapReduce im Wortzählszenario veranschaulicht:

![HDI.Wortzähldiagramm](./media/hdinsight-get-started/HDI.WordCountDiagram.gif)

Bei der Ausgabe des MapReduce-Jobs handelt es sich um einen Satz Schlüssel/Wert-Paare. Der Schlüssel ist eine Zeichenfolge, die ein Wort angibt, und der Wert ist eine ganze Zahl, die angibt, wie oft das Wort insgesamt im Text vorkommt. Der Vorgang besteht aus zwei Phasen:

-   Der Mapper verwendet die einzelnen Zeilen des Eingabetexts als Eingabe und unterteilt diese in Wörter. Er gibt bei jedem Vorkommen eines Worts ein Schlüssel/Wert-Paar des Worts gefolgt von einer 1 aus. Die Ausgabe wird sortiert und dann an den Reducer gesendet.

-   Der Reducer addiert die einzelnen Zählwerte jedes Worts und gibt ein einziges Schlüssel/Wert-Paar aus, das aus dem Wort gefolgt von der Summe seiner Vorkommen besteht.

Für die Ausführung eines MapReduce-Jobs sind folgende Elemente erforderlich:

-   Ein MapReduce-Programm. In diesem Lernprogramm verwenden Sie das in HDInsight-Clustern enthaltene Wortzählprogramm, sodass Sie kein eigenes Programm schreiben müssen. Es befindet sich in */example/jars/hadoop-examples.jar*. In HDInsight-Clustern der Version 3.0 lautet der Dateiname *hadoop-mapreduce-examples.jar*. Anweisungen zum Schreiben eines eigenen MapReduce-Jobs finden Sie unter [Entwickeln von Java MapReduce-Programmen für HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).
-   Eine Eingabedatei. Sie verwenden */example/data/gutenberg/davinci.txt* als Eingabedatei. Informationen zum Hochladen von Dateien finden Sie unter [Hochladen von Daten zu HDInsight](/de-de/documentation/articles/hdinsight-upload-data/).
-   Einen Ordner für die Ausgabedatei. Sie verwenden */example/data/WordCountOutput* als Ordner für die Ausgabedatei. Das System erstellt den Ordner, falls dieser nicht vorhanden ist. Der MapReduce-Job schlägt fehl, falls der Ordner bereits existiert. Wenn Sie den MapReduce-Job zum zweiten Mal ausführen möchten, müssen Sie entweder den vorhandenen Ausgabeordner löschen oder einen anderen Ausgabeordner angeben.

Ausführen des Beispiels mit Azure PowerShell
--------------------------------------------

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/manage/install-and-configure-windows-powershell/).

2.  Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"   # Name des Azure-Abonnements
         $clusterName = "<ClusterName>"             # Name des HDInsight-Clusters

3.  Führen Sie den folgenden Befehl aus, und geben Sie Ihre Azure-Kontoinformationen an:

         Add-AzureAccount

4.  Führen Sie die folgenden Befehle aus, um eine MapReduce-Jobdefinition zu erstellen:

         # MapReduce-Job definieren
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.

    Die Datei "hadoop-examples.jar" ist in der HDInsight-Clusterbereitstellung enthalten. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Die Quelldatei ist in der HDInsight-Clusterbereitstellung enthalten, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

5.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu übermitteln:

         # Job übermitteln
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten, sowie die Anmeldeinformationen. "Start-AzureHDInsightJob" ist ein asynchronisierter Aufruf. Verwenden Sie das Cmdlet *Wait-AzureHDInsightJob*, um zu überprüfen, ob der Job abgeschlossen ist.

6.  Führen Sie den folgenden Befehl aus, um den Abschluss des MapReduce-Jobs zu überprüfen:

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

7.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:

         # Jobausgabe abrufen
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**So rufen Sie die Ergebnisse des MapReduce-Jobs ab**

1.  Öffnen Sie **Azure PowerShell**.
2.  Führen Sie den folgenden Befehl aus, um in das Stammverzeichnis "c:\" zu wechseln:

         cd \

    Das Standardverzeichnis für Azure PowerShell ist *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. Sie haben standardmäßig keinen Schreibzugriff für diesen Ordner. Sie müssen entweder in das Stammverzeichnis "c:\\" oder einen Ordner wechseln, für den Sie über Schreibberechtigung verfügen.

3.  Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

         $subscriptionName = "<SubscriptionName>"       # Name des Azure-Abonnements
            
         $storageAccountName = "<StorageAccountName>"   # Name des Azure-Speicherkontos
         $containerName = "<ContainerName>"              # Name des Blob-Speichercontainers

         Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird.  Der Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

4.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

         # Aktuelles Abonnement auswählen
         Select-AzureSubscription $subscriptionName

         # Speicherkonto-Kontextobjekt erstellen
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* wird verwendet, um das aktuelle Abonnement festzulegen, falls sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

5.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe aus dem Blob-Container auf die Arbeitsstation herunterzuladen:

         # Jobausgabe auf die Arbeitsstation herunterladen
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Der Ordner */example/data/WordCountOutput* ist der Ausgabeordner, den Sie bei der Ausführung des MapReduce-Jobs angegeben haben. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur in den lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner "C:\\example\\data\\WordCountOutput" heruntergeladen.

6.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    Der MapReduce-Job produziert eine Datei namens *part-r-00000*, die die Wörter und deren Anzahl enthält. Das Skript verwendet den Befehl "findstr", um alle Wörter aufzulisten, die *"there"* enthalten.

Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

Der Java-Code für das MapReduce-Wortzählprogramm
------------------------------------------------

Nachfolgend ist der Quellcode für das Java-MapReduce-Wortzählprogramm abgebildet:

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

Nächste Schritte
----------------

MapReduce bietet zwar leistungsstarke Diagnosemöglichkeiten, ist jedoch ggf. etwas kompliziert in der Anwendung. Andere Sprachen wie Pig und Hive sind benutzerfreundlichere Alternativen für die Arbeit mit HDInsight-Daten. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Entwickeln von Hadoop-Streaming-MapReduce-Programmen in C\# für HDInsight](/de-de/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Verwenden von Hive mit HDInsight](/de-de/documentation/articles/hdinsight-use-hive/)
-   [Verwenden von Pig mit HDInsight](/de-de/documentation/articles/hdinsight-use-pig/)
-   [Ausführen der HDInsight-Beispiele](/de-de/documentation/articles/hdinsight-run-samples/)

