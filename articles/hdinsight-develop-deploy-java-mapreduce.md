<properties 
	pageTitle="Entwickeln von Java MapReduce-Programmen für Hadoop | Microsoft Azure" 
	description="Erfahren Sie, wie Sie MapReduce-Programme in Java im HDInsight-Emulator entwickeln und in HDInsight bereitstellen können." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/01/2015" 
	ms.author="nitinme"/>

# Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight
In diesem Lernprogramm werden Sie schrittweise durch ein End-to-End-Szenario für die Entwicklung eines Hadoop MapReduce-Auftrags zum Zählen von Wörtern in Java mithilfe von Apache Maven geführt. Sie erfahren außerdem, wie Sie die Anwendung im HDInsight-Emulator für Azure testen und anschließend in einem Azure HDInsight-Cluster bereitstellen und ausführen können.

##<a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, benötigten Sie Folgendes:

- Installieren des HDInsight-Emulators Informationen dazu finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][hdinsight-emulator]. Stellen Sie sicher, dass alle erforderlichen Dienste ausgeführt werden. Starten Sie auf dem Computer, auf dem HDInsight-Emulator installiert ist, die Hadoop-Befehlszeile über die Desktopverknüpfung, navigieren Sie zu **C:\\hdp**, und führen Sie den Befehl **start_local_hdp_services.cmd** aus.	
- Installieren Sie Azure PowerShell auf dem Emulator-Computer. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].
- Installieren Sie die Java-Plattform JDK 7 oder eine neuere Version auf dem Emulator-Computer. Diese Software ist auf dem Emulator-Computer bereits verfügbar.
- Installieren und konfigurieren Sie [Apache Maven](http://maven.apache.org/).
- Schließen Sie ein Azure-Abonnement ab. Informationen dazu finden Sie unter [Erste Schritte mit Azure][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose einmonatige Testversion][azure-free-trial].


##<a name="develop"></a>Erstellen eines MapReduce-Programms in Java mit Apache Maven

Erstellen Sie eine MapReduce-Anwendung zum Zählen von Wörtern. Diese einfache Anwendung zählt das Auftreten von Wörtern in den Eingabedaten. In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

1. Erstellen eines Projekts mit Apache Maven
2. Aktualisieren des Projektobjektmodells (POM)
3. Erstellen einer MapReduce-Anwendung zum Zählen von Wörtern
4. Erstellen und Packen der Anwendung

**So erstellen Sie ein Projekt mit Maven**

1. Erstellen Sie das Verzeichnis **C:\\Tutorials\\WordCountJava**. 2. Wechseln Sie in der Befehlszeile Ihrer Entwicklungsumgebung in das neu erstellte Verzeichnis.
3. Verwenden Sie den Befehl __mvn__, der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Daraufhin wird im aktuellen Verzeichnis ein neues Verzeichnis mit dem im __artifactID__-Parameter (in diesem Beispiel **wordcountjava**) angegebenen Namen erstellt. Dieses Verzeichnis enthält die folgenden Elemente:

	* __pom.xml__ – Das [Projektobjektmodell (POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), das die Informationen und Konfigurationsdetails für das Erstellen des Projekts enthält.

	* __src__ – Das Verzeichnis, das das __main\\java\\org\\apache\\hadoop\\examples__-Verzeichnis enthält, in dem Sie die Anwendung erstellen werden.
3. Löschen Sie die Datei __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__, da diese in diesem Beispiel nicht verwendet wird.

**So aktualisieren Sie das POM**

1. Bearbeiten Sie die Datei __pom.xml__, und fügen Sie Folgendes im Abschnitt `<dependencies>` ein.

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>                                                                                     
      	  <groupId>org.apache.hadoop</groupId>                                                                                                       
      	  <artifactId>hadoop-common</artifactId>                                                                                                         
      	  <version>2.5.1</version>                                                                                            
    	</dependency>

	Damit teilen Sie Maven mit, dass das Projekt die Bibliotheken (aufgelistet in <artifactId>) in bestimmten Versionen (aufgelistet in <version>) benötigt. Beim Kompilieren wird er aus dem standardmäßigen Maven-Repository heruntergeladen. Sie können die [Maven-Repositorysuche](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) verwenden, um weitere Komponenten anzuzeigen.

2. Fügen Sie der Datei __pom.xml__ folgenden Code hinzu: Dieser muss sich in der Datei innerhalb der `<project>...</project>`-Tags befinden, z. B. zwischen `</dependencies>` und `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>       
  		  </plugins>
	    </build>

	Dadurch konfigurieren Sie das [Maven Shade-Plug-In](http://maven.apache.org/plugins/maven-shade-plugin/), das eine Lizenzduplizierung in der von Maven erstellten JAR-Datei verhindert. Es wird deshalb verwendet, weil die doppelten Lizenzdateien bei der Laufzeit einen Fehler auf dem HDInsight-Cluster verursachen. Sie können diesen Fehler verhindern, indem Sie das Maven Shade-Plug-In mit der `ApacheLicenseResourceTransformer`-Implementierung verwenden.

	Das Maven Shade-Plug-In produziert außerdem ein uberjar (oder fatjar), das alle Abhängigkeiten enthält, die von der Anwendung benötigt werden.

3. Speichern Sie die Datei __pom.xml__.

**So erstellen Sie die Anwendung zum Zählen von Wörtern**

1. Wechseln Sie in das Verzeichnis __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ und benennen Sie die Datei __app.java__ in __WordCount.java__ um.
2. Öffnen Sie Notepad.
2. Kopieren Sie das folgende Programm, und fügen Sie es in den Editor ein:

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

	Bitte beachten Sie, dass der Paketname **org.apache.hadoop.examples** und der Klassenname **WordCount** lautet. Diese Namen werden beim Übermitteln des MapReduce-Auftrags verwendet
	
3. Speichern Sie die Datei.

**So erstellen und verpacken Sie die Anwendung**

1. Öffnen Sie eine Eingabeaufforderung und wechseln Sie in das Verzeichnis __wordcountjava__.

2. Verwenden Sie den folgenden Befehl, um eine JAR-Datei mit der Anwendung zu erstellen:

		mvn clean package

	Damit werden etwaige frühere Buildartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und die Anwendung erstellt und verpackt.

3. Nachdem der Befehl ausgeführt wurde, enthält das Verzeichnis __wordcountjava\\target__ eine Datei mit dem Namen __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE]Die Datei __wordcountjava-1.0-SNAPSHOT.jar__ ist eine Uberjar.


##<a name="test"></a>Testen des Programms auf dem Emulator

Das Testen des MapReduce-Jobs im Emulator umfasst die folgenden Schritte:

1. Hochladen der Datendateien in das Hadoop Distributed File System (HDFS) auf dem Emulator
2. Erstellen einer lokalen Benutzergruppe
3. Ausführen eines MapReduce-Auftrags zum Zählen von Wörtern
4. Rufen Sie die Jobergebnisse ab

Der HDInsight-Emulator verwendet standardmäßig HDFS als Dateisystem. Optional können Sie angeben, dass der HDInsight-Emulator einen Azure-Blobspeicher verwenden soll. Informationen dazu finden Sie unter [Get Started with HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).][hdinsight-emulator-wasb].

In diesem Lernprogramm verwenden Sie den HDFS-Befehl **copyFromLocal**, um die Datendateien in HDFS hochzuladen. Im nächsten Abschnitt erfahren Sie, wie Sie Dateien mithilfe von Azure PowerShell in den Azure-Blobspeicher hochladen können. Weitere Methoden zum Hochladen von Dateien in den Azure-Blobspeicher finden Sie unter [Hochladen von Daten zu HDInsight][hdinsight-upload-data].

In diesem Lernprogramm wird die folgende HDFS-Ordnerstruktur verwendet:

<table border="1">
<tr><td>Ordner</td><td>Hinweis</td></tr>
<tr><td>/WordCount</td><td>Der Stammordner für das Wortzählungsprojekt. </td></tr>
<tr><td>/WordCount/Apps</td><td>Der Ordner für die Mapper- und Reducer-Programme.</td></tr>
<tr><td>/WordCount/Input</td><td>Der Ordner für die MapReduce-Quelldateien.</td></tr>
<tr><td>/WordCount/Output</td><td>Der Ordner für die MapReduce-Ausgabedateien.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>Der Ordner für die Jobergebnisse.</td></tr>
</table>

In diesem Lernprogramm werden die .txt-Dateien im Verzeichnis %hadoop_home% als Datendateien verwendet.

> [AZURE.NOTE]Bei den Hadoop-HDFS-Befehlen muss die Groß- und Kleinschreibung beachtet werden.

**So kopieren Sie die Datendateien in das Emulator-HDFS**

1. Öffnen Sie die Hadoop-Befehlszeile auf Ihrem Desktop. Die Hadoop-Befehlszeile wird mit dem Emulator-Installer installiert.
2. Führen Sie im Hadoop-Befehlszeilenfenster den folgenden Befehl aus, um ein Verzeichnis für die Eingabedateien anzulegen:

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	Der hier verwendete Pfad ist der relative Pfad. Er ist gleichwertig mit folgendem Pfad:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. Führen Sie den folgenden Befehl aus, um einige Textdateien in den Eingabeordner im HDFS zu kopieren:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	Der MapReduce-Job zählt die Wörter in diesen Dateien.

4. Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten und zu prüfen:

		hadoop fs -ls /WordCount/Input

**So erstellen Sie eine lokale Benutzergruppe**

Um den MapReduce-Auftrag auf dem Cluster erfolgreich auszuführen, müssen Sie eine Benutzergruppe mit dem Namen "hdfs" erstellen. Anschließend müssen Sie dieser Gruppe einen Benutzer mit dem Namen "hadoop" sowie den lokalen Benutzer, mit dem Sie sich beim Emulator anmelden, hinzufügen. Führen Sie die folgenden Befehle in einer Eingabeaufforderung mit erhöhten Rechten aus:

		# Add a user group called hdfs		
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**So führen Sie den MapReduce-Auftrag in der Hadoop-Befehlszeile aus**

1. Öffnen Sie die Hadoop-Befehlszeile auf Ihrem Desktop.
2. Führen Sie den folgenden Befehl aus, um die Ordnerstruktur "/WordCount/Output" aus dem HDFS zu löschen. "/ WordCount/Output" ist der Ausgabeordner des MapReduce-Auftrags zum Zählen von Wörtern. Der MapReduce-Job schlägt fehl, falls der Ordner bereits existiert. Dieser Schritt ist notwendig, wenn Sie diesen Auftrag zum zweiten Mal ausführen.

		hadoop fs -rm - r /WordCount/Output

2. Führen Sie den folgenden Befehl aus:

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	Nachdem der Auftrag erfolgreich abgeschlossen wurde, sollte die Ausgabe ungefähr folgendermaßen aussehen:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	Auf dem Screenshot können Sie sehen, dass die Map- und Reduce-Funktionen zu 100 % abgeschlossen sind. Außerdem wird die Job-ID aufgeführt. Derselbe Bericht kann auch durch Öffnen der **Hadoop MapReduce status**-Verknüpfung auf Ihrem Desktop und durch die Suche nach der Auftrags-ID abgerufen werden.

Die andere Option zur Ausführung eines MapReduce-Jobs ist die Verwendung von Azure PowerShell. Informationen dazu finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][hdinsight-emulator].

**So zeigen Sie die Ausgabe über das HDFS an**

1. Öffnen Sie die Hadoop-Befehlszeile.
2. Führen Sie den folgenden Befehl aus, um die Ergebnisse anzuzeigen:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	Sie können auch "|more" an das Ende der Befehlszeile anhängen, um eine Seitenansicht zu öffnen. Verwenden Sie alternativ den Befehl **findstr**, um nach einem Zeichenfolgemuster zu suchen:

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

Damit ist die Entwicklung des MapReduce-Auftrags zum Zählen von Wörtern abgeschlossen und wurde erfolgreich auf dem Emulator getestet. Der nächste Schritt ist, diesen Job auf Azure HDInsight bereitzustellen und auszuführen.



##<a id="upload"></a>Hochladen von Daten und der Anwendung in den Azure-Blobspeicher
Azure HDInsight verwendet zur Datenspeicherung Azure-Blobspeicher. Wenn ein HDInsight-Cluster bereitgestellt wird, werden die Systemdateien in einem Azure-Blobspeichercontainer gespeichert. Zum Speichern der Dateien können Sie entweder diesen Standardcontainer oder einen anderen Container verwenden (entweder in demselben Azure-Speicherkonto oder in einem anderen Speicherkonto in demselben Datencenter, in dem sich auch der Cluster befindet).

In diesem Lernprogramm erstellen Sie einen Container in einem separaten Speicherkonto für die Datendateien und die MapReduce-Anwendung. Als Datendateien werden die Textdateien im Verzeichnis **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** auf Ihrer Emulator-Arbeitsstation verwendet.

**So erstellen Sie ein Blobspeicherkonto und einen Container**

1. Öffnen Sie Azure PowerShell.
2. Legen Sie die Variablen fest, und führen Sie dann folgende Befehle aus:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	Die Variable **$subscripionName** ist Ihrem Azure-Abonnement zugeordnet. Sie müssen den Variablen **$storageAccountName_Data** und **$containerName_Data** einen Namen geben. Informationen zu Einschränkungen bei der Benennung finden Sie unter [Benennen von Containern, BLOBs und Metadaten und Verweisen auf diese](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx).

3. Führen Sie die folgenden Befehle aus, um für dieses Konto ein Speicherkonto und einen Blobspeichercontainer zu erstellen:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Führen Sie die folgenden Befehle aus, um das Speicherkonto und den Container zu überprüfen:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**So laden Sie die Datendateien hoch**

1. Öffnen Sie Azure PowerShell.
2. Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Die Variablen **$storageAccountName_Data** und **$containerName_Data** stimmen mit den im letzten Vorgang definierten Variablen überein.

	Bitte beachten Sie: Der Quellordner ist **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**, und der Zielordner ist **WordCount/Input**.

3. Führen Sie die folgenden Befehle aus, um eine Liste der .txt-Dateien im Quellordner abzurufen:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Führen Sie die folgenden Befehle aus, um ein Speicherkontextobjekt zu erstellen:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Führen Sie den folgenden Befehl aus, um die Dateien zu kopieren:

		# Copy the files from the local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Führen Sie die folgenden Befehle aus, um die hochgeladenen Dateien aufzulisten:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	Sie sollten nun die hochgeladenen Text-Datendateien sehen.

**So laden Sie die Anwendung zum Zählen von Wörtern hoch**

1. Öffnen Sie Azure PowerShell.
2. Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	Die Variablen **$storageAccountName_Data** und **$containerName_Data** stimmen mit den im letzten Vorgang definierten Variablen überein. Dies bedeutet, dass Sie sowohl die Datendatei als auch die Anwendung in denselben Container desselben Speicherkontos hochladen.

	Bitte beachten Sie: Der Zielordner ist **WordCount/jars**.

4. Führen Sie die folgenden Befehle aus, um ein Speicherkontextobjekt zu erstellen:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Führen Sie den folgenden Befehl aus, um die Anwendungen zu kopieren:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Führen Sie die folgenden Befehle aus, um die hochgeladenen Dateien aufzulisten:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	Die JAR-Datei sollte nun hier aufgelistet werden.

##<a name="run"></a>Ausführen des MapReduce-Auftrags in Azure HDInsight

In diesem Abschnitt erstellen Sie ein Azure PowerShell-Skript, mit dem die folgenden Aufgaben ausgeführt werden:

1. Bereitstellen eines HDInsight-Clusters
	
	1. Erstellen eines Speicherkontos, das als Standarddateisystem für das HDInsight-Cluster verwendet wird
	2. Erstellen eines Blobspeicher-Containers 
	3. Erstellen eines HDInsight-Clusters

2. Übermitteln des MapReduce-Jobs

	1. Erstellen einer MapReduce-Jobdefinition
	2. Übermitteln eines MapReduce-Jobs
	3. Warten auf den Abschluss des Auftrags
	4. Anzeigen von Standardfehlern
	5. Anzeigen der Standardausgabe

3. Löschen des Clusters

	1. Löschen des HDInsight-Clusters
	2. Löschen des Speicherkontos, das als Standarddateisystem für den HDInsight-Cluster verwendet wurde


**So führen Sie das Azure PowerShell-Skript aus**

1. Öffnen Sie Notepad.
2. Kopieren und fügen Sie den folgenden Code ein:
		
		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container used as the default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
		
		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
		
		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
				
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  
		
		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Legen Sie die ersten sechs Variablen in dem Skript fest. Die Variable **$stringPrefix** wird den angegebenen Zeichenfolgen für die Namen des HDInsight-Clusters, des Speicherkontos und des Blobspeichercontainers vorangestellt. Da diese Namen zwischen 3 und 24 Zeichen enthalten müssen, müssen Sie sicherstellen, dass die angegebene Zeichenfolge und die im Skript verwendeten Namen zusammen diese Längeneinschränkung nicht überschreiten. Verwenden Sie für **$stringPrefix** nur Kleinbuchstaben.
 
	Die Variablen **$storageAccountName_Data** und **$containerName_Data** stehen für das Speicherkonto und den Container, die zum Speichern der Datendateien und der Anwendung verwendet werden. Die Variable **$location** muss mit dem Speicherort des Datenspeicherkontos übereinstimmen.

4. Prüfen Sie auch die restlichen Variablen.
5. Speichern Sie die Skript-Datei.
6. Öffnen Sie Azure PowerShell.
7. Führen Sie den folgenden Befehl aus, um die Ausführungsrichtlinie auf "RemoteSigned" festzulegen:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Geben Sie nach Aufforderung den Benutzernamen und das Kennwort für den HDInsight-Cluster ein. Da Sie den Cluster am Ende des Skripts löschen und den Benutzernamen und das Kennwort dann nicht mehr benötigen, können beliebige Zeichenfolgen für den Benutzername und das Kennwort verwendet werden. Wenn keine Aufforderung zur Eingabe der Anmeldeinformationen angezeigt werden soll, finden Sie weitere Informationen unter [Working with Passwords, Secure Strings and Credentials in Windows PowerShell][powershell-PSCredential].


##<a name="retrieve"></a>Abrufen der Ergebnisse des MapReduce-Auftrags
In diesem Abschnitt wird erläutert, wie Sie die Ergebnisse herunterladen und anzeigen. Informationen zur Anzeige der Ergebnisse in Excel finden Sie unter [Verbinden von Excel mit HDInsight mithilfe des Microsoft Hive ODBC-Treibers][hdinsight-ODBC] und [Verbinden von Excel mit HDInsight mithilfe von Power Query][hdinsight-power-query].


**So rufen Sie die Ausgabe ab**

1. Öffnen Sie das Azure PowerShell-Fenster.
2. Wechseln Sie zum Verzeichnis **C:\\Tutorials\\WordCountJava**. Das Standardverzeichnis für Azure PowerShell ist **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Die Cmdlets, die Sie ausführen, laden die Ausgabedatei in den aktuellen Ordner herunter. Sie haben nicht die Berechtigung zum Herunterladen der Dateien in die Systemordner.
2. Führen Sie die folgenden Befehle aus, um die Werte festzulegen:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Führen Sie die folgenden Befehle aus, um die Ergebnisse herunterzuladen und anzuzeigen:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

Sobald der Job abgeschlossen ist, haben Sie die Möglichkeit, die Daten in Excel oder mithilfe von [Sqoop][hdinsight-use-sqoop] in eine SQL Server- oder Azure SQL-Datenbank zu exportieren.

##<a id="nextsteps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie gelernt, wie Sie einen Java-MapReduce-Auftrag entwickeln können, wie die Anwendung mit dem HDInsight-Emulator getestet wird und wie ein Azure PowerShell-Skript geschrieben wird, um einen HDInsight-Cluster bereitzustellen und den MapReduce-Auftrag im Cluster auszuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Entwickeln von Hadoop-Streaming-MapReduce-Programmen in C# für HDInsight][hdinsight-develop-streaming]
- [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
- [Erste Schritte mit dem HDInsight-Emulator][hdinsight-emulator]
- [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage]
- [Verwalten von HDInsight mit Azure PowerShell][hdinsight-admin-powershell]
- [Hochladen von Daten zu HDInsight][hdinsight-upload-data]
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Verbinden von Excel mit HDInsight mithilfe von Power Query][hdinsight-power-query]
- [Verbinden von Excel mit HDInsight mithilfe des Microsoft Hive ODBC-Treibers][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-emulator]: hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png


<!--HONumber=54-->