<properties title="Build an HBase application using Maven" pageTitle="Erstellen einer HBase-Anwendung mit Maven" description="Erfahren Sie, wie Sie mit Apache Maven eine Java-basierte Apache HBase-Anwendung erstellen und anschließend in Azure HDInsight bereitstellen können." metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/20/2014" ms.author="larryfr" />

##Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen

Erfahren Sie, wie Sie eine [Apache HBase]-Anwendung(http://hbase.apache.org/) in Java mit Apache Maven erstellen. Setzen Sie anschließend die Anwendung mit Azure HDInsight (Hadoop) ein.

[Maven](http://maven.apache.org/) ist ein Projektmanagement- und Vermittlungstool, mit dem Sie Software, Dokumentationen und Berichte für Java-Projekte erstellen können. In diesem Artikel erfahren Sie, wie Sie es für das Erstellen einer einfachen Java-Anwendung verwenden, die eine HBase-Tabelle auf einem Azure HDInsight-Cluster erstellt, abfragt und löscht.

##Anforderungen

* [Java Platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 oder höher

* [Maven](http://maven.apache.org/)

* [Ein Azure HDInsight-Cluster mit HBase](/de-de/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster)

##Erstellen des Projekts

1. Ändern Sie in der Befehlszeile Ihrer Entwicklungsumgebung die Verzeichnisse auf den Ort, an dem Sie das Projekt anlegen möchten. Beispiel: `cd code\hdinsight`

2. Verwenden Sie den Befehl "mvn", der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Damit wird ein neues Verzeichnis im aktuellen angelegt, das den Namen trägt, der vom Parameter "artifactID" ("hbaseapp" in diesem Beispiel) festgelegt wurde. Dieses Verzeichnis enthält die folgenden Objekte.

	* "pom.xml": Das Projektobjektmodell ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) enthält Informationen und Konfigurationsdetails, die zum Erstellen des Projekts verwendet werden.

	* "src": Das Verzeichnis, das das Verzeichnis "main\java\com\microsoft\examples" enthält, in dem Sie die Anwendung erstellen.

3. Löschen Sie die Datei "src\test\java\com\microsoft\examples\apptest.java", da sie nicht in diesem Beispiel verwendet wird.

##Aktualisieren des Projektobjektmodells

1. Bearbeiten Sie die Datei "pom.xml", und fügen Sie Folgendes im Abschnitt "<dependencies>" ein.

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	Damit wird Maven informiert, dass das Projekt den hbase-Client in der Version 0.98.4-hadoop2 benötigt. Beim Kompilieren wird er aus dem standardmäßigen Maven-Repository heruntergeladen. Sie können die [Maven-Repositorysuche] verwenden,(http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) um weitere Informationen zu dieser Abhängigkeit anzuzeigen.

2. Fügen Sie der Datei "pom.xml" folgenden Code hinzu: Dieser muss sich zwischen den Tags "<project>...</project>" in der Datei befinden, z. B. zwischen "</dependencies>" und "</project>".

		<build>
		  <sourceDirectory>src</sourceDirectory>
		  <resources>
	        <resource>
	          <directory>${basedir}/conf</directory>
	          <filtering>false</filtering>
	          <includes>
	            <include>hbase-site.xml</include>
	          </includes>
	        </resource>
	      </resources>
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

	Damit wird eine Ressource konfiguriert (conf\hbase-site.xml), die die Konfigurationsinformationen für HBase enthält.

	> [WACOM.NOTE] Sie können die Konfigurationswerte auch per Code festlegen. Wie Sie das tun, erfahren Sie in den Kommentaren im Beispiel "CreateTable" unten.

	Hiermit wird auch das  [maven-shade-plugin](http://maven.apache.org/plugins/maven-shade-plugin/)konfiguriert, das die Lizenzduplizierung in der von Maven erstellten JAR-Datei verhindert. Es wird deshalb verwendet, weil die doppelten Lizenzdateien bei der Laufzeit einen Fehler auf dem HDInsight-Cluster verursachen. Wird "maven-shade-plugin" mit der ApacheLicenseResourceTransformer-Implementierung verwendet, wird dieser Fehler verhindert.

	Das maven-shade-plugin produziert außerdem ein uberjar (oder fatjar), das alle Abhängigkeiten enthält, die von der Anwendung benötigt werden.

3. Speichern Sie die Datei "pom.xml".

4. Erstellen Sie im Verzeichnis "hbaseapp" ein neues Verzeichnis namens "conf". Erstellen Sie im Verzeichnis "conf" eine neue Datei namens "hbase-site.xml", und verwenden Sie Folgendes als Inhalt.

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0,zookeeper1,zookeeper2</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

	Diese Datei wird zum Laden der HBase-Konfiguration für einen HDInsight-Cluster verwendet.

	> [WACOM.NOTE] Das ist eine sehr minimale Datei vom Typ "hbase-site.xml", die die Mindesteinstellungen für den HDInsight-Cluster enthält. Eine Vollversion der von HDInsight verwendeten Konfigurationsdatei "hbase-site.xml" finden Sie, indem Sie [eine Remotedesktopverbindung mit dem HDInsight-Cluster herstellen].(http://azure.microsoft.com/de-de/documentation/articles/hdinsight-administer-use-management-portal/#rdp)Die Datei "hbase-site.xml" befindet sich im Verzeichnis "C:\apps\dist\hbase-&lt;version number>-hadoop2\conf". Der Dateiteil mit der Versionsnummer des Dateipfads ändert sich, wenn HBase auf dem Cluster aktualisiert wird.

3. Speichern Sie die Datei "hbase-site.xml".

##Erstellen der Anwendung

1. Wechseln Sie zum Verzeichnis "hbaseapp\src\com\microsoft\examples", und benennen Sie die Datei "app.java" in "CreateTable.java" um.

2. Öffnen Sie die Datei "CreateTable.java", und ersetzen Sie die vorhandenen Inhalte durch Folgendes.

		package com.microsoft.examples;
		import java.io.IOException;
		
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;
		import org.apache.hadoop.hbase.HTableDescriptor;
		import org.apache.hadoop.hbase.TableName;
		import org.apache.hadoop.hbase.HColumnDescriptor;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Put;
		import org.apache.hadoop.hbase.util.Bytes;
		
		public class CreateTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();
		
		    // Example of setting zookeeper values for HDInsight
			//   in code instead of an hbase-site.xml file
			//
		    // config.set("hbase.zookeeper.quorum",
		    //            "zookeepernode0,zookeepernode1,zookeepernode2");
		    //config.set("hbase.zookeeper.property.clientPort", "2181");
		    //config.set("hbase.cluster.distributed", "true");
		
		    // create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);
		
		    // create the table...
		    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
		    // ... with two column families
		    tableDescriptor.addFamily(new HColumnDescriptor("name"));
		    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
		    admin.createTable(tableDescriptor);
		
		    // define some people
		    String[][] people = {
		        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
		        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
		        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
		        { "4", "Rae", "Schroeder", "rae@contoso.com" },
		        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
		        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };
		
		    HTable table = new HTable(config, "people");
		
		    // Add each person to the table
		    //   Use the `name` column family for the name
		    //   Use the `contactinfo` column family for the email
		    for (int i = 0; i< people.length; i++) {
		      Put person = new Put(Bytes.toBytes(people[i][0]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
		      person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
		      table.put(person);
		    }
		    // flush commits and close the table
		    table.flushCommits();
		    table.close();
		  }
		}

	Das ist die "CreateTable"-Klasse, die eine Tabelle namens "people" erzeugt und sie mit einigen vordefinierten Benutzern auffüllt.

3. Speichern Sie die Datei "CreateTable.java".

4. Erstellen Sie im Verzeichnis "hbaseapp\src\main\java\com\microsoft\examples" eine Datei mit dem Namen "SearchByEmail.java". Fügen Sie Folgendes als Inhalt der Datei hinzu:

		package com.microsoft.examples;
		import java.io.IOException;
		
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.client.ResultScanner;
		import org.apache.hadoop.hbase.client.Result;
		import org.apache.hadoop.hbase.filter.RegexStringComparator;
		import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
		import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.util.GenericOptionsParser;
		
		public class SearchByEmail {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();
		
		    // Use GenericOptionsParser to get only the parameters to the class
		    // and not all the parameters passed (when using WebHCat for example)
		    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
		    if (otherArgs.length != 1) {
		      System.out.println("usage: [regular expression]");
		      System.exit(-1);
		    }
			
			// Open the table
		    HTable table = new HTable(config, "people");
		
			// Define the family and qualifiers to be used
		    byte[] contactFamily = Bytes.toBytes("contactinfo");
		    byte[] emailQualifier = Bytes.toBytes("email");
		    byte[] nameFamily = Bytes.toBytes("name");
		    byte[] firstNameQualifier = Bytes.toBytes("first");
		    byte[] lastNameQualifier = Bytes.toBytes("last");
		
			// Create a new regex filter
		    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
			// Attach the regex filter to a filter
			//   for the email column
		    SingleColumnValueFilter filter = new SingleColumnValueFilter(
		      contactFamily,
		      emailQualifier,
		      CompareOp.EQUAL,
		      emailFilter
		    );
		
			// Create a scan and set the filter
		    Scan scan = new Scan();
		    scan.setFilter(filter);
		
			// Get the results
		    ResultScanner results = table.getScanner(scan);
			// Iterate over results and print  values
		    for (Result result : results ) {
		      String id = new String(result.getRow());
		      byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
		      String firstName = new String(firstNameObj);
		      byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
		      String lastName = new String(lastNameObj);
		      System.out.println(firstName + " " + lastName + " - ID: " + id);
			  byte[] emailObj = result.getValue(contactFamily, emailQualifier);
		      String email = new String(emailObj);
			  System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
		    }
		    results.close();
			table.close();
		  }
		}

	Die "SearchByEmail"-Klasse kann zur Abfrage von Zeilen nach E-Mail-Adresse verwendet werden. Da sie einen Filter für reguläre Ausdrücke verwendet, können Sie beim Einsatz der Klasse entweder eine Zeichenfolge angeben oder einen regulären Ausdruck.

5. Speichern Sie die Datei "SearchByEmail.java".

6. Erstellen Sie im Verzeichnis "hbaseapp\src\main\hava\com\microsoft\examples" eine Datei mit dem Namen "DeleteTable.java". Fügen Sie Folgendes als Inhalt der Datei hinzu:

		package com.microsoft.examples;
		import java.io.IOException;
		
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;
		
		public class DeleteTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();
		
		    // Create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);
		
		    // Disable, and then delete the table
		    admin.disableTable("people");
		    admin.deleteTable("people");
		  }
		}

	Diese Klasse dient nur zum Aufräumen dieses Beispiels. Sie deaktiviert zunächst die von der "CreateTable"-Klasse erstellte Tabelle und legt diese dann ab.

7. Speichern Sie die Datei "DeleteTable.java".

##Erstellen und Packen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung, und ändern Sie das Verzeichnis in "hbaseapp".

2. Verwenden Sie den folgenden Befehl, um eine JAR-Datei mit der Anwendung zu erstellen.

		mvn clean package

	Damit werden etwaige frühere Erstellungsartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und dann die Anwendung erstellen und gepackt.

3. Nachdem dieser Befehl ausgeführt wurde, enthält das Verzeichnis "hbaseapp\target" eine Datei namens "hbaseapp-1.0-SNAPSHOT.jar".

	> [WACOM.NOTE] Die Datei "hbaseapp-1.0-SNAPSHOT.jar" ist eine Uberjar-Datei (manchmal auch Fatjar genannt), die alle Abhängigkeiten enthält, die für das Ausführen der Datei erforderlich sind.

##Hochladen der JAR-Datei und Starten des Auftrags

> [WACOM.NOTE] Es gibt viele Möglichkeiten, eine Datei in einen HDInsight-Cluster hochzuladen (siehe [Hochladen von Daten für Hadoop-Aufträge in HDInsight](/de-de/documentation/articles/hdinsight-upload-data/). Bei den Schritten unten kommt [Azure PowerShell] zum Einsatz(/de-de/documentation/articles/install-configure-powershell/).

1. Erstellen Sie nach dem Installieren und Konfigurieren von [Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/)eine neue Datei namens "hbase-runner.psm1". Fügen Sie Folgendes als Inhalt der Datei hinzu:

		<#
		.SYNOPSIS
		    Copies a file to the primary storage of an HDInsight cluster.
		.DESCRIPTION
		    Copies a file from a local directory to the blob container for
		    the HDInsight cluster.
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
		        -clusterName "MyHDInsightCluster"
		
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
		        -clusterName "MyHDInsightCluster"
		        -emailRegex "contoso.com"
		
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
		        -clusterName "MyHDInsightCluster"
		        -emailRegex "^r" -showErr
		#>
		
		function Start-HBaseExample {
		    [CmdletBinding(SupportsShouldProcess = $true)]
		    param(
		        #The class to run
		        [Parameter(Mandatory = $true)]
		        [String]$className,
		
		        #The name of the HDInsight cluster
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName,
		
		        #Only used when using SearchByEmail
		        [Parameter(Mandatory = $false)]
		        [String]$emailRegex,
		
		        #Use if you want to see stderr output
		        [Parameter(Mandatory = $false)]
		        [Switch]$showErr
		    )
		
		    Set-StrictMode -Version 3
		
		    # Is the Azure module installed?
		    FindAzure
		
		    # The JAR
		    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
		
		    # The job definition
		    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		      -JarFile $jarFile `
		      -ClassName $className `
		      -Arguments $emailRegex
		
		    # Get the job output
		    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		    Wait-AzureHDInsightJob -Job $job
		    if($showErr)
		    {
		        Write-Host "STDERR"
		        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		    }
		    Write-Host "Display the standard output ..." -ForegroundColor Green
		    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
		}
		
		<#
		.SYNOPSIS
		    Copies a file to the primary storage of an HDInsight cluster.
		.DESCRIPTION
		    Copies a file from a local directory to the blob container for
		    the HDInsight cluster.
		.EXAMPLE
		    Add-HDInsightFile -localPath "C:\temp\data.txt"
		        -destinationPath "example/data/data.txt"
		        -ClusterName "MyHDInsightCluster"
		.EXAMPLE
		    Add-HDInsightFile -localPath "C:\temp\data.txt"
		        -destinationPath "example/data/data.txt"
		        -ClusterName "MyHDInsightCluster"
		        -Container "MyContainer"
		#>
		
		function Add-HDInsightFile {
		    [CmdletBinding(SupportsShouldProcess = $true)]
		    param(
		        #The path to the local file.
		        [Parameter(Mandatory = $true)]
		        [String]$localPath,
		
		        #The destination path and file name, relative to the root of the container.
		        [Parameter(Mandatory = $true)]
		        [String]$destinationPath,
		
		        #The name of the HDInsight cluster
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName,
		
		        #If specified, overwrites existing files without prompting
		        [Parameter(Mandatory = $false)]
		        [Switch]$force
		    )
		
		    Set-StrictMode -Version 3
		
		    # Is the Azure module installed?
		    FindAzure
		
		    # Does the local path exist?
		    if (-not (Test-Path $localPath))
		    {
		        throw "Source path '$localPath' does not exist."
		    }
		
		    # Get the primary storage container
		    $storage = GetStorage -clusterName $clusterName
		
		    # Upload file to storage, overwriting existing files if -force was used.
		    Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
		                                -Container $storage.container `
		                                -Context $storage.context
		}
		
		function FindAzure {
		    # Is the Azure module installed?
		    if (-not(Get-Module -ListAvailable Azure))
		    {
		        throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell/"
		    }
		
		    # Is there an active Azure subscription?
		    $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		    if(-not($sub))
		    {
		        throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
		    }
		}
		
		function GetStorage {
		    param(
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName
		    )
		    $hdi = Get-AzureHDInsightCluster -name $clusterName
		    # Does the cluster exist?
		    if (!$hdi)
		    {
		        throw "HDInsight cluster '$clusterName' does not exist."
		    }
		    # Create a return object for context & container
		    $return = @{}
		    $storageAccounts = @{}
		    # Get the primary storage account information
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		    $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
		    # Build the hash of storage account name/keys
		    $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
		    foreach($account in $hdi.StorageAccounts)
		    {
		        $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
		    }
		    # Get the storage context, as we can't depend
		    # on using the default storage context
		    $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		    # Get the container, so we know where to
		    # find/store blobs
		    $return.container = $hdi.DefaultStorageAccount.StorageContainerName
		    # Return storage accounts to support finding all accounts for
		    # a cluster
		    $return.storageAccounts = $storageAccounts
		
		    return $return
		}
		# Only export the verb-phrase things
		export-modulemember *-*

	Diese Datei enthält zwei Module:

	* Add-HDInsightFile: Wird für das Hochladen von Dateien in HDInsight verwendet

	* Start-HBaseExample: Wird für das Ausführen der bereits erstellten Klassen verwendet

2. Speichern Sie die Datei "hbase-runner.psm1".

3. Öffnen Sie ein neues Azure PowerShell-Fenster, ändern Sie das Verzeichnis in "hbaseapp", und führen Sie dann den folgenden Befehl aus.

		PS C:\ Import-Module c:\path\to\hbase-runner.psm1

	Ändern Sie den Pfad in den Speicherort der zuvor erstellten Datei "hbase-runner.psm1". Damit wird das Modul für diese PowerShell-Sitzung registriert.

2. Verwenden Sie den folgenden Befehl zu Hochladen von "hbaseapp-1.0-SNAPSHOT.jar" in den HDInsight-Cluster.

		Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

	Ersetzen Sie "hdinsightclustername" durch den Namen Ihres HDInsight-Clusters. Der Befehl lädt dann "hbaseapp-1.0-SNAPSHOT.jar" in das Verzeichnis "example/jars" im Hauptspeicher Ihres HDInsight-Clusters hoch.

3. Verwenden Sie nach dem Hochladen der Dateien Folgendes, um eine neue Tabelle mithilfe von "hbaseapp" zu erstellen.

		Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

	Ersetzen Sie "hdinsightclustername" durch den Namen Ihres HDInsight-Clusters.

	Dieser Befehl erstellt eine neue Tabelle namens "people" in Ihrem HDInsight-Cluster. Bei diesem Befehl erfolgt keine Ausgabe im Konsolenfenster.

2. Verwenden Sie den folgenden Befehl, um nach Einträgen in die Tabelle zu suchen.

		Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

	Ersetzen Sie "hdinsightclustername" durch den Namen Ihres HDInsight-Clusters.

	Damit wird die "SearchByEmail"-Klasse für die Suche nach etwaigen Zeilen verwendet, in denen sie Spaltengruppe "contactinformation", "column" und "email" die Zeichenfolge "contoso.com" enthält. Daraufhin sollten Sie folgende Ergebnisse erhalten:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

	Wird "fabrikam.com" als "-emailRegex"-Wert verwendet, werden Benutzer zurückgegeben, bei denen "fabrikam.com" im Feld E-Mail enthalten ist. Da diese Suche mithilfe eines auf regulären Ausdrücken basierenden Filters implementiert wurde, können Sie auch reguläre Ausdrücke wie etwa "^r" eingeben, die Einträge zurückgeben, deren E-Mail-Adresse mit dem Buchstaben "r" beginnt.

##Löschen der Tabelle

Wenn Sie mit dem Beispiel fertig sind, verwenden Sie den folgenden Befehl aus der PowerShell-Sitzung zum Löschen der Tabelle "people", die darin verwendet wurde.

	Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Ersetzen Sie "hdinsightclustername" durch den Namen Ihres HDInsight-Clusters.

##Problembehandlung

###Keine oder unerwartete Ergebnisse bei Verwenden von "Start-HBaseExample"

Verwenden Sie den Parameter "-showErr", wenn Sie den Fehler sehen möchten, der während der Ausführung des Auftrags erzeugt wurde wurde.

<!--HONumber=35.1-->
