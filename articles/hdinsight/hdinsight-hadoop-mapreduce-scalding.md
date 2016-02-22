<properties
 pageTitle="Entwickeln von Scalding-MapReduce-Aufträgen mit Maven | Microsoft Azure"
 description="Erfahren Sie, wie Sie Maven verwenden können, um einen Scalding-MapReduce-Auftrag zu erstellen und den Auftrag anschließend in einem Hadoop-Cluster in HDInsight bereitzustellen und auszuführen."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/05/2016"
 ms.author="larryfr"/>

# Entwickeln Sie Scalding MapReduce-Aufträge mit Apache Hadoop in HDInsight

Scalding ist eine Scala-Bibliothek, die das einfache Erstellen von Hadoop MapReduce-Aufträgen ermöglicht. Sie bietet eine kurze Syntax sowie die enge Integration mit Scala.

Erfahren Sie in diesem Dokument, wie Maven verwendet wird, um einen einfachen MapReduce-Auftrag zum Zählen von Wörtern zu erstellen, der in Scalding geschrieben wird. Anschließend erfahren Sie, wie dieser Auftrag in einem HDInsight-Cluster bereitgestellt und ausgeführt wird.

## Voraussetzungen

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Einen Windows- oder Linux-basierten Hadoop-Cluster in HDInsight**. Weitere Informationen finden Sie unter [Bereitstellung von Linux-basierten Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) oder [Bereitstellung von Windows-basierten Hadoop in HDInsight](hdinsight-provision-clusters.md).

* **[Maven](http://maven.apache.org/)**

* **[Java-Plattform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 oder höher**

## Erstellen Sie das Projekt

1. Verwenden Sie den folgenden Befehl, um ein neues Maven-Projekt zu erstellen:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Dieser Befehl erstellt ein neues Verzeichnis namens **scaldingwordcount** und das Gerüst für eine Scala-Anwendung.

2. Öffnen Sie die Datei **pom.xml** im Verzeichnis **scaldingwordcount**, und ersetzen Sie den Inhalt durch Folgendes:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Diese Datei beschreibt das Projekt, Abhängigkeiten und Plug-Ins. Wichtige Einträge sind hier:

    * **maven.compiler.source** und **maven.compiler.target**: Legt die Java-Version für dieses Projekt fest.

    * **repositories**: Die Bibliotheken, die die von diesem Projekt verwendeten Abhängigkeitsdateien enthalten.

    * **scalding-core\_2.11** und **hadoop-core**: Dieses Projekts hängt sowohl von den Scalding- und als auch den Hadoop-Core-Paketen ab.

    * **maven-scala-plugin**: Plug-In zur Kompilierung von Scala-Anwendungen.

    * **maven-shade-plugin**: Plug-In zum erstellen von Shade-Jars (Fatjars). Dieses Plug-In verwendet Filter und Transformationen, im Besonderen:

        * **Filter**: Durch die angewendeten Filter ändern sich die in der JAR-Datei enthalten Metainformationen. Um zu verhindern, dass Ausnahmen zur Laufzeit signiert werden, schließen diese Filter verschiedene Signaturdateien aus, die andernfalls mit Abhängigkeiten enthalten wären.

        * **Ausführungen**: Die Ausführungskonfiguration für die Paketierungsphase legt die Klasse **com.twitter.scalding.Tool** als Hauptklasse für das Paket fest. Andernfalls müssten Sie "com.twitter.scalding.Tool" wie auch die Klasse mit der Anwendungslogik bei der Ausführung des Auftrags mit dem Hadoop-Befehl angeben.

3. Löschen Sie das **Src/Test**-Verzeichnis, da Sie keine Tests in diesem Beispiel erstellen.

4. Öffnen Sie die Datei **src/main/scala/com/microsoft/example/app.scala** und ersetzen Sie den Inhalt durch Folgendes:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Implementiert einen einfachen Wortzählauftrag.

5. Speichern und schließen Sie die Dateien.

6. Verwenden Sie den folgenden Befehl im **scaldingwordcount**-Verzeichnis zum Erstellen und Verpacken der Anwendung:

        mvn package

    Wenn dieser Auftrag abgeschlossen ist, finden Sie das Paket mit der WordCount-Anwendung unter **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## Führen Sie den Auftrag auf einem Linux-basierten Cluster aus

> [AZURE.NOTE] Die folgenden Schritte verwenden den SSH- und den Hadoop-Befehl. Weitere Methoden zur Ausführung von MapReduce-Aufträgen finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md).

1. Verwenden Sie den folgenden Befehl zum Hochladen des Pakets zum HDInsight-Cluster:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Dadurch werden die Dateien aus dem lokalen System auf den Stammknoten kopiert.

    > [AZURE.NOTE] Wenn Sie zum Schutz Ihres SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel verwendet haben, müssen Sie möglicherweise den `-i`-Parameter und den Pfad zum privaten Schlüssel angeben. Zum Beispiel, `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Verwenden Sie den folgenden Befehl für die Verbindung zum Hauptknoten des Clusters:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Wenn Sie zum Schutz Ihres SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel verwendet haben, müssen Sie möglicherweise den `-i`-Parameter und den Pfad zum privaten Schlüssel angeben. Zum Beispiel, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Sobald es eine Verbindung zum Hauptknoten gibt, verwenden Sie den folgenden Befehl zum Ausführen des Wortzählauftrags

        hadoop jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasb:///example/data/gutenberg/davinci.txt --output wasb:///example/wordcountout

    Dadurch wird die zuvor implementierte WordCount-Klasse ausgeführt. `--hdfs` weist den Auftrag an, HDFS zu verwenden. `--input` Gibt die Eingabetextdatei an, während `--output` den Ausgabespeicherort festlegt.

4. Wenn der Auftrag abgeschlossen ist, können Sie die Ausgabe wie folgt anzeigen.

        hadoop fs -text wasb:///example/wordcountout/part-00000

    Die angezeigten Informationen sehen in etwa folgendermaßen aus:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Führen Sie den Auftrag auf einem Windows-basierten Cluster aus

> [AZURE.NOTE] Die folgenden Schritte geschehen mithilfe von Windows PowerShell. Weitere Methoden zur Ausführung von MapReduce-Aufträgen finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md).

1. [Installieren und konfigurieren Sie Azure PowerShell](../powershell-install-configure.md).

2. Starten Sie Azure PowerShell, und melden Sie sich bei Ihrem Azure-Konto an. Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl die Informationen zu Ihrem Konto zurück.

		Add-AzureRMAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

		Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Sie können `Get-AzureRMSubscription` zum Abrufen einer Liste aller Ihrem Konto zugeordneten Abonnements verwenden, einschließlich der Abonnement-ID jedes Abonnements.

4. Verwenden Sie das folgende Skript, um den WordCount-Auftrag hochzuladen und auszuführen. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres HDInsight-Clusters, und stellen Sie sicher, dass `$fileToUpload` der richtige Pfad zur Datei __scaldingwordcount-1.0-SNAPSHOT.jar__ ist.

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = "CLUSTERNAME"
        $fileToUpload = "scaldingwordcount-1.0-SNAPSHOT.jar"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"
        
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasb:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                       "--input", `
                       "wasb:///example/data/gutenberg/davinci.txt", `
                       "--output", `
                       "wasb:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Wenn Sie das Skript ausführen, werden Sie aufgefordert, den Benutzernamen und das Kennwort für den HDInsight-Clusteradministrator einzugeben. Fehler, die auftreten, während der Auftrag ausgeführt wird, werden in der Konsole protokolliert.
     
6. Wenn der Auftrag abgeschlossen ist, wird die Ausgabe in die Datei __output.txt__ im aktuellen Verzeichnis heruntergeladen. Führen Sie den folgenden Befehl aus, um die Ausgabe anzuzeigen:

        cat output.txt

    Die Datei sollte Werte ähnlich den folgenden enthalten:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Nächste Schritte

Nachdem Sie erfahren haben, wie mit Scalding MapReduce-Aufträge für HDInsight erstellt werden, können Sie mithilfe der folgenden Links weitere Möglichkeiten entdecken, mit Azure HDInsight zu arbeiten.

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0211_2016-->