<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

Entwickeln von Java MapReduce-Programmen für HDInsight
======================================================

In diesem Lernprogramm werden Sie durch ein End-to-End-Szenario vom Entwickeln und Testen eines MapReduce-Wortzähljobs mit dem HDInsight Emulator zur Bereitstellung und Ausführung auf Azure HDInsight geführt.

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Installieren Sie den Azure HDInsight Emulator. Informationen dazu finden Sie unter [Get Started using HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).
-   Installieren Sie Azure PowerShell auf dem Emulator-Computer. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).
-   Schließen Sie ein Azure-Abonnement ab. Informationen dazu finden Sie unter [Erste Schritte mit Azure](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose einmonatige Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).

Themen in diesem Artikel
------------------------

-   [Entwickeln eines MapReduce-Wortzählprogramms in Java](#develop)
-   [Testen des Programms auf dem Emulator](#test)
-   [Hochladen von Datendateien und der Anwendung in den Azure-Blob-Speicher](#upload)
-   [Ausführen des MapReduce-Programms auf Azure HDInsight](#run)
-   [Abrufen der MapReduce-Ergebnisse](#retrieve)
-   [Nächste Schritte](#nextsteps)

Entwickeln eines MapReduce-Wortzählprogramms in Java
----------------------------------------------------

Wortzählung ist eine einfache Anwendung, mit der das Auftreten eines jeden Worts in einem bestimmten eingegebenen Text gezählt wird.

**Schreiben des MapReduce-Wortzähljobs in Java**

1.  Öffnen Sie Notepad.
2.  Kopieren Sie den folgenden Text, und fügen Sie ihn in Notepad ein.

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

    Bitte beachten Sie, dass der Paketname **org.apache.hadoop.examples** und der Klassenname **WordCount** lautet. Diese Namen werden verwendet, wenn Sie den MapReduce-Job übermitteln.

3.  Speichern Sie die Datei unter **c:\\Tutorials\\WordCountJava\\WordCount.java**. Falls diese Ordnerstruktur nicht existiert, erstellen Sie sie.

Der HDInsight Emulator wird mit dem Compiler *javac* geliefert.

**Kompilieren des MapReduce-Programms**

1.  Öffnen Sie eine Eingabeaufforderung.
2.  Wechseln Sie zum Verzeichnis **c:\\Tutorials\\WordCountJava**. Dies ist der Ordner für das MapReduce-Wortzählprogramm.
3.  Führen Sie den folgenden Befehl aus, um zu prüfen, ob die beiden jar-Dateien vorhanden sind:

         dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
         dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  Führen Sie den folgenden Befehl aus, um das Programm zu kompilieren:

         C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    javac befindet sich im Ordner C:\\Hadoop\\java\\bin. Der letzte Parameter ist das Java-Programm, das sich im aktuellen Ordner befindet. Der Compiler erstellt 3 Klassendateien im aktuellen Ordner.

5.  Führen Sie den folgenden Befehl aus, um eine jar-Datei zu erstellen:

         C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    Mit diesem Befehl wird eine Datei namens WordCount.jar im aktuellen Ordner erstellt.

    ![HDI.EMulator.WordCount.Compile](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png)

Testen des Programms auf dem Emulator
-------------------------------------

Das Testen des MapReduce-Jobs auf dem Emulator umfasst Folgendes:

1.  Hochladen der Datendateien in das HDFS auf dem Emulator
2.  Übermitteln eines MapReduce-Wortzähljobs
3.  Prüfen des Jobstatus
4.  Abrufen der Ergebnisse des Jobs

Standardmäßig verwendet der HDInsight Emulator als Dateisystem HDFS. Optional können Sie den HDInsight Emulator für eine Verwendung von Azure-Blob-Speicher konfigurieren. Informationen dazu finden Sie unter [Get Started with HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage).

In diesem Lernprogramm verwenden Sie den HDFS-Befehl *copyFromLocal*, um die Datendateien in HDFS hochzuladen. Im nächsten Abschnitt lernen Sie, wie Sie Dateien mithilfe von Azure PowerShell in den Azure-Blob-Speicher hochladen. Weitere Methoden zum Hochladen von Dateien in den Azure-Blob-Speicher finden Sie unter [Hochladen von Daten zu HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

In diesem Lernprogramm wird die folgende HDFS-Ordnerstruktur verwendet:

<table border="1">
<tr><td>Ordner</td><td>Hinweis</td></tr>
<tr><td>/WordCount</td><td>Der Stammordner für das Wortzählungsprojekt. </td></tr>
<tr><td>/WordCount/Apps</td><td>Der Ordner für die Mapper- und Reducer-Programme</td></tr>
<tr><td>/WordCount/Input</td><td>Der Ordner für die MapReduce-Quelldateien.</td></tr>
<tr><td>/WordCount/Output</td><td>Der Ordner für die MapReduce-Ausgabedateien.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>Der Ordner für die Jobergebnisse.</td></tr>
</table>

In diesem Lernprogramm werden die .txt-Dateien im Verzeichnis %hadoop\_home% als Datendateien verwendet.

> [WACOM.NOTE] Bei den Hadoop-HDFS-Befehlen muss die Groß- und Kleinschreibung beachtet werden.

**Kopieren der Datendateien in das Emulator-HDFS**

1.  Öffnen Sie die Hadoop-Befehlszeile auf Ihrem Desktop. Die Hadoop-Befehlszeile wird vom Emulator-Installer installiert.
2.  Führen Sie im Hadoop-Befehlszeilenfenster den folgenden Befehl aus, um ein Verzeichnis für die Eingabedateien anzulegen:

         hadoop fs -mkdir /WordCount/Input

    Der hier verwendete Pfad ist der relative Pfad. Er ist gleichwertig mit folgendem Pfad:

         hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Führen Sie den folgenden Befehl aus, um einige Textdateien in den Eingabeordner im HDFS zu kopieren:

         hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    Der MapReduce-Job zählt die Wörter in diesen Dateien.

4.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten und zu prüfen:

         hadoop fs -ls /WordCount/Input

    Es werden ca. acht .txt-Dateien angezeigt.

**Ausführen des MapReduce-Jobs in der Hadoop-Befehlszeile**

1.  Öffnen Sie die Hadoop-Befehlszeile auf Ihrem Desktop.
2.  Führen Sie den folgenden Befehl aus, um die Ordnerstruktur /WordCount/Output aus HDFS zu löschen. /WordCount/Output ist der Ausgabeordner des MapReduce-Wortzähljobs. Der MapReduce-Job schlägt fehl, falls der Ordner bereits existiert. Dieser Schritt ist notwendig, wenn Sie diesen Job zum zweiten Mal ausführen.

         hadoop fs -rmr /WordCount/Output

3.  Führen Sie den folgenden Befehl aus:

         hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    Wenn der Job erfolgreich abgeschlossen ist, sollte Ihr Bildschirm ungefähr folgendermaßen aussehen:

    ![HDI.EMulator.WordCount.Run](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png)

    Auf dem Screenshot können Sie sehen, dass die Map- und Reduce-Funktionen zu 100% abgeschlossen sind. Außerdem wird die Job-ID (job\_201312092021\_0002) aufgeführt. Derselbe Bericht kann auch durch Öffnen des Shortcuts **Hadoop MapReduce Status** auf Ihrem Desktop und Suchen nach der Job-ID abgerufen werden.

Die andere Option zur Ausführung eines MapReduce-Jobs ist die Verwendung von Azure PowerShell. Informationen dazu finden Sie unter [Get Started with HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).

**Anzeigen der Ergebnisse im HDFS**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Führen Sie den folgenden Befehl aus, um die Ergebnisse anzuzeigen:

         hadoop fs -ls /WordCount/Output/
         hadoop fs -cat /WordCount/Output/part-00000

    Sie können auch "|more" an das Ende der Befehlszeile anhängen, um eine Seitenansicht zu öffnen. Oder verwenden Sie den findstr-Befehl, um nach einem Zeichenfolgemuster zu suchen:

         hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

Bis jetzt haben Sie einen MapReduce-Wortzähljob entwickelt und erfolgreich auf dem Emulator getestet. Der nächste Schritt ist, diesen Job auf Azure HDInsight bereitzustellen und auszuführen.

Hochladen von Daten in Azure-Blob-Speicher
------------------------------------------

Azure HDInsight verwendet zur Datenspeicherung Azure-Blob-Speicher. Wenn ein HDInsight-Cluster bereitgestellt wird, werden die Systemdateien in einem Azure-Blob-Speichercontainer gespeichert. Sie können entweder diesen Standard- oder einen anderen Container (entweder auf demselben Azure-Speicherkonto oder auf einem anderen Speicherkonto im selben Rechenzentrum, in dem sich auch das Cluster befindet) zur Speicherung der Dateien verwenden.

In diesem Lernprogramm erstellen Sie einen Container auf einem separaten Speicherkonto für die Datendateien und die MapReduce-Anwendung. Die Datendateien sind die Textdateien im Verzeichnis %hadoop\_home% auf Ihrer Arbeitsstation.

**Erstellen eines Blob-Speichers und eines Containers**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"
         $location = "<MicrosoftDataCenter>"  # For example, "East US"

    Der **$subscripionName** wird ihrem Azure-Abonnement zugeordnet. Sie müssen den Variablen **$storageAccountName_Data** und **$containerName_Data** Namen geben. Informationen zu Einschränkungen bei der Benennung finden Sie unter [Benennen von Containern, BLOBs und Metadaten und Verweisen auf diese](http://msdn.microsoft.com/en-us/library/windowsazure/dd135715.aspx).

3.  Führen Sie den folgenden Befehl aus, um ein Speicherkonto und einen Blob-Speichercontainer auf diesem Konto zu erstellen.

         # Azure-Abonnement auswählen
         Select-AzureSubscription $subscriptionName
            
         # Ein Speicherkonto erstellen
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
                    
         # Einen Blob-Speichercontainer erstellen
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
         New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Führen Sie die folgenden Befehle aus, um das Speicherkonto und den Container zu prüfen:

         Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
         Get-AzureStorageContainer -Context $destContext

**Hochladen der Datendateien**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
         $destFolder = "WordCount/Input"

    Die Variablen **$storageAccountName_Data** und **$containerName_Data** entsprechen den Definitionen im letzten Verfahren.

    Bitte beachten Sie: Der Quellordner ist **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**, und der Zielordner ist **WordCount/Input**.

3.  Führen Sie die folgenden Befehle aus, um eine Liste der .txt-Dateien im Quelldateiordner abzurufen:

         # Eine Liste der txt-Dateien abrufen
         $filesAll = Get-ChildItem $localFolder
         $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Führen Sie die folgenden Befehle aus, um ein Speicherkontextobjekt zu erstellen:

         # Ein Speicherkontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  Führen Sie den folgenden Befehl aus, um die Dateien zu kopieren:

         # Datei von der lokalen Arbeitsstation in den Blob-Container kopieren        
         foreach ($file in $filesTxt){
             
             $fileName = "$localFolder\$file"
             $blobName = "$destFolder/$file"
            
             write-host "Copying $fileName to $blobName"
            
             Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
         }

6.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

         # In den Blob-Speichercontainer hochgeladene Dateien auflisten
         Write-Host "The Uploaded data files:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    Es sollten nun ca. 8 Textdatendateien angezeigt werden.

**Hochladen der Wortzählanwendung**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
         $blobFolder = "WordCount/jars"

    Die Variablen **$storageAccountName_Data** und **$containerName_Data** entsprechen Ihrer Definition im letzten Verfahren. Dies bedeutet, dass Sie sowohl die Datendatei als auch die Anwendung in denselben Container auf demselben Speicherkonto hochladen.

    Bitte beachten Sie: Der Zielordner ist **WordCount/jars**.

3.  Führen Sie die folgenden Befehle aus, um ein Speicherkontextobjekt zu erstellen:

         # Ein Speicherkontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  Führen Sie die folgenden Befehle aus, um die Anwendungen zu kopieren:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

         # In den Blob-Speichercontainer hochgeladene Dateien auflisten
         Write-Host "The Uploaded application file:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    Die jar-Datei sollte nun hier aufgelistet werden.

Ausführen des MapReduce-Jobs auf Azure HDInsight
------------------------------------------------

Das folgende PowerShell-Skript führt die folgenden Aufgaben aus:

1.  Bereitstellen eines HDInsight-Clusters

    1.  Erstellen eines Speicherkontos, das als Standard-Dateisystem für das HDInsight-Cluster verwendet wird
    2.  Erstellen eines Blob-Speichercontainers
    3.  Erstellen eines HDInsight-Clusters

2.  Übermitteln des MapReduce-Jobs

    1.  Erstellen einer MapReduce-Jobdefinition
    2.  Übermitteln eines MapReduce-Jobs
    3.  Warten auf den Abschluss des Jobs
    4.  Anzeigen von Standardfehlern
    5.  Anzeigen der Standardausgabe

3.  Löschen des Clusters

    1.  Löschen des HDInsight-Clusters
    2.  Löschen des Speicherkontos, das als Standarddateisystem für das HDInsight-Cluster verwendet wird

**Ausführen des PowerShell-Skripts**

1.  Öffnen Sie Notepad.
2.  Kopieren und fügen Sie den folgenden Code ein:

         # Das Speicherkonto und die HDInsight-Cluster-Variablen
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $serviceNameToken = "<ServiceNameTokenString>"
         $location = "<MicrosoftDataCenter>"     ### müssen mit dem Speicherort des Datenspeicherkontos übereinstimmen
         $clusterNodes = <NumberOFNodesInTheCluster>

         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
            
         $clusterName = $serviceNameToken + "hdicluster"
            
         $storageAccountName_Default = $serviceNameToken + "hdistore"
         $containerName_Default =  $serviceNameToken + "hdicluster"

         # Die MapReduce-Job-Variablen
         $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
         $className = "org.apache.hadoop.examples.WordCount"
         $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
         $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
         $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
            
         # Ein PSCredential-Objekt erstellen. Der Benutzername und das Kennwort sind hartcodiert.  Bei Bedarf können Sie sie ändern.
         $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
            
         Select-AzureSubscription $subscriptionName
            
         #=============================
         # Ein Speicherkonto erstellen, das als Standard-Dateisystem verwendet wird
         Write-Host "Create a storage account" -ForegroundColor Green
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
            
         #=============================
         # Einen Blob-Speichercontainer erstellen, der als Standard-Dateisystem verwendet wird
         Write-Host "Create a Blob storage container" -ForegroundColor Green
         $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
            
         New-AzureStorageContainer -Name $containerName_Default -Context $destContext
            
         #=============================
         # Ein HDInsight-Cluster erstellen
         Write-Host "Create an HDInsight cluster" -ForegroundColor Green
         $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
            
         $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
             Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
             Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
            
         New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
            
         #=============================
         # Eine MapReduce-Jobdefinition erstellen
         Write-Host "Create a MapReduce job definition" -ForegroundColor Green
         $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
            
         #=============================
         # Den MapReduce-Job ausführen
         Write-Host "Run the MapReduce job" -ForegroundColor Green
         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
         Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
            
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
                    
         #=============================
         # Das HDInsight-Cluster löschen
         Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
         Remove-AzureHDInsightCluster -Name $clusterName  
            
         # Das Speicherkonto, das als Standard-Dateisystem verwendet wird, löschen
         Write-Host "Delete the storage account" -ForegroundColor Green
         Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Legen Sie die ersten sechs Variablen in dem Skript fest. **$serviceNameToken** wird als Name des HDInsight-Clusters, als Name des Standard-Speicherkontos und als Name des Standard-Blob-Speichercontainers verwendet. Da der Dienstname zwischen 3 und 24 Zeichen lang sein muss und das Skript Zeichenfolgen von bis zu 10 Zeichen Länge an die Namen anhängt, müssen Sie die Zeichenfolge auf höchstens 14 Zeichen begrenzen. Verwenden Sie für $serviceNameToken ausschließlich Kleinbuchstaben. **$storageAccountName_Data** und **$containerName_Data** sind das Speicherkonto und der Container, die zur Speicherung der Datendateien und der Anwendung verwendet werden. **$location** muss mit dem Speicherort des Datenspeicherkontos übereinstimmen.
4.  Prüfen Sie auch die restlichen Variablen.
5.  Speichern Sie die Skript-Datei.
6.  Öffnen Sie Azure PowerShell.
7.  Führen Sie den folgenden Befehl aus, um die Ausführungsrichtlinie auf "RemoteSigned" zu setzen:

         PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Wenn Sie dazu aufgefordert werden, geben Sie den Benutzernamen und das Kennwort für das HDInsight-Cluster ein. Da Sie das Cluster am Ende des Skripts löschen und den Benutzernamen und das Kennwort dann nicht mehr benötigen, können Benutzername und Kennwort beliebige Zeichenfolgen sein. Wenn Sie die Eingabe der Anmeldeinformationen überspringen möchten, siehe [Working with Passwords, Secure Strings and Credentials in Windows PowerShell (Arbeiten mit Kennwörtern, sicheren Zeichenfolgen und Anmeldeinformationen in Windows PowerShell, in englischer Sprache)](http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx)

Abrufen der Ergebnisse des MapReduce-Jobs
-----------------------------------------

In diesem Abschnitt wird erläutert, wie Sie die Ergebnisse herunterladen und anzeigen. Informationen zur Anzeige der Ergebnisse in Excel finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/) und [Verbinden von Excel mit HDInsight über Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/).

**Abrufen der Ergebnisse**

1.  Öffnen Sie das Azure PowerShell-Fenster.
2.  Wechseln Sie zum Verzeichnis **c:\\Tutorials\\WordCountJava**. Das Standardverzeichnis für Azure PowerShell ist **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Die Cmdlets, die Sie ausführen, laden die Ausgabedatei in den aktuellen Ordner herunter. Sie haben nicht die Berechtigung zum Herunterladen der Dateien in die Systemordner.
3.  Führen Sie die folgenden Befehle aus, um die Werte festzulegen:

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
         $blobName = "WordCount/Output/part-r-00000"

4.  Führen Sie die folgenden Befehle aus, um ein Azure-Speicherkontextobjekt zu erstellen:

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  Führen Sie die folgenden Befehle aus, um die Ergebnisse herunterzuladen und anzuzeigen:

         Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
         cat "./$blobName" | findstr "there"

Wenn der Job abgeschlossen ist, haben Sie die Möglichkeit, die Daten in Excel oder – mithilfe von [Sqoop](../hdinsight-use-sqoop/) – in eine SQL Server- oder Azure SQL-Datenbank zu exportieren.

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie gelernt, wie ein Java MapReduce-Job entwickelt wird, wie die Anwendung auf dem HDInsight Emulator getestet wird und wie ein PowerShell-Skript geschrieben wird, um ein HDInsight-Cluster bereitzustellen und den MapReduce-Job auf dem Cluster aufzuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Entwickeln von Hadoop-Streaming-MapReduce-Programmen in C\# für HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Erste Schritte mit Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Erste Schritte mit dem HDInsight Emulator](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)
-   [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/)
-   [Verwalten von HDInsight mit PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Hochladen von Daten zu HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Verwenden von Pig mit HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verbinden von Excel mit HDInsight über Power Query](/en-us/documentation/articles/hdinsight-connect-excel-power-query/)
-   [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight](../hdinsight-connect-excel-hive-ODBC-driver/)

