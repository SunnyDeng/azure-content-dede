<properties 
	pageTitle="Erstellen einer HBase-Anwendung mit Maven" 
	description="Erfahren Sie, wie Sie mit Apache Maven eine Java-basierte Apache HBase-Anwendung erstellen und anschließend in Azure HDInsight bereitstellen können" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2014" 
	ms.author="larryfr"/>

##Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen

Erfahren Sie, wie Sie eine [Apache HBase](http://hbase.apache.org/)-Anwendung in Java mithilfe von Apache Maven erstellen und entwickeln. Setzen Sie anschließend die Anwendung mit Azure HDInsight (Hadoop) ein.

[Maven](http://maven.apache.org/) ist ein Projektmanagement- und Verständnistool, mit dem Sie Software, Dokumentationen und Berichte für Java-Projekte erstellen können. In diesem Artikel erfahren Sie, wie Sie es für das Erstellen einer einfachen Java-Anwendung verwenden, die eine HBase-Tabelle auf einem Azure HDInsight-Cluster erstellt, abfragt und löscht.

##Anforderungen

* [Java Platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 oder höher

* [Maven](http://maven.apache.org/)

* [Ein Azure HDInsight-Cluster mit HBase](/de-de/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster)

##Erstellen des Projekts

1. Ändern Sie in der Befehlszeile Ihrer Entwicklungsumgebung die Verzeichnisse auf den Ort, an dem Sie das Projekt anlegen möchten. Beispiel: `cd code\hdinsight`

2. Verwenden Sie den Befehl __mvn__, der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Damit wird ein neues Verzeichnis im aktuellen angelegt, das den Namen trägt, der vom Parameter __artifactID__ (in diesem Beispiel **hbaseapp**) festgelegt wurde. Dieses Verzeichnis enthält die folgenden Objekte.

	* __pom.xml__: Das Projekt-Objektmodell ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) enthält Informationen und Konfigurationsdetails für das Erstellen des Projekts.

	* __src__: Das Verzeichnis, das das Verzeichnis __main\java\com\microsoft\examples__ enthält, in dem Sie die Anwendung schreiben.

3. Löschen Sie die Datei __src\test\java\com\microsoft\examples\apptest.java__, die in diesem Beispiel nicht verwendet wird.

##Aktualisieren des Projektobjektmodells

1. Bearbeiten Sie die Datei __pom.xml__, und fügen Sie Folgendes im Abschnitt `<dependencies>` ein.

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	Damit wird Maven darüber informiert, dass das Projekt die __hbase-client__-Version __0.98.4-hadoop2__ benötigt. Beim Kompilieren wird er aus dem standardmäßigen Maven-Repository heruntergeladen. Sie können die [Maven-Repositorysuche](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) verwenden, wenn Sie weitere Informationen zu dieser Abhängigkeit erhalten möchten.

2. Fügen Sie der Datei __pom.xml__ folgenden Code hinzu: Dieser muss sich in den `<project>...</project>`-Tags in der Datei befinden, z. B. zwischen `</dependencies>` und `</project>`.

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
        	  <artifactId>maven-compiler-plugin</artifactId>
        	  <configuration>
          	    <source>1.6</source>
          	    <target>1.6</target>
        	  </configuration>
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

	Damit wird eine Ressource konfiguriert (__conf\hbase-site.xml__), die die Konfigurationsinformationen für HBase enthält.

	> [AZURE.NOTE] Sie können die Konfigurationswerte auch per Code festlegen. In den Kommentaren im Beispiel __CreateTable__  unten erhalten Sie weitere Informationen dazu.

	Damit werden auch [maven-compiler-plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) und [maven-shade-plugin](http://maven.apache.org/plugins/maven-shade-plugin/) konfiguriert. Das Compiler-Plug-In wird zum Kompilieren der Topologie verwendet. Das Shade-Plug-In wird verwendet, um die Lizenzduplizierung in dem von Maven erstellten JAR-Paket zu verhindern. Es wird deshalb verwendet, weil die doppelten Lizenzdateien bei der Laufzeit einen Fehler auf dem HDInsight-Cluster verursachen. Wird maven-shade-plugin mit der `ApacheLicenseResourceTransformer`-Implementierung verwendet, so wird dieser Fehler verhindert.

	Das maven-shade-plugin produziert außerdem ein uberjar (oder fatjar), das alle Abhängigkeiten enthält, die von der Anwendung benötigt werden.

3. Speichern Sie die Datei __pom.xml__.

4. Erstellen Sie ein neues Verzeichnis namens __conf__ im Verzeichnis __hbaseapp__. Erstellen Sie im Verzeichnis __conf__ eine neue Datei namens __hbase-site.xml__, und verwenden Sie Folgendes als Inhalt.

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

	> [AZURE.NOTE] Das ist eine sehr minimale Datei vom Typ "hbase-site.xml", die die Mindesteinstellungen für den HDInsight-Cluster enthält. Stellen Sie für eine Vollversion der von HDInsight verwendeten Konfigurationsdatei "hbase-site.xml" eine Verbindung per [Remotedesktop mit dem HDInsight-Cluster](http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp) her. Dort finden Sie die Datei "hbase-site.xml" im Verzeichnis "C:\apps\dist\hbase-&lt;versionsnummer>-hadoop2\conf". Der Dateiteil mit der Versionsnummer des Dateipfads ändert sich, wenn HBase auf dem Cluster aktualisiert wird.

3. Speichern Sie die Datei __hbase-site.xml__.

##Erstellen der Anwendung

1. Wechseln Sie zum Verzeichnis __hbaseapp\src\main\java\com\microsoft\examples__, und benennen Sie die Datei "app.java__" in __CreateTable.java__ um.

2. Öffnen Sie die Datei __CreateTable.java__, und ersetzen Sie die vorhandenen Inhalte mit Folgendem:

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

	Das ist die Klasse __CreateTable__ die eine Tabelle namens __people__ erzeugt und sie mit einigen vordefinierten Benutzern füllt.

3. Speichern Sie die Datei __CreateTable.java__.

4. Erstellen Sie im Verzeichnis __hbaseapp\src\main\java\com\microsoft\examples__ eine Datei mit dem Namen __SearchByEmail.java__. Fügen Sie Folgendes als Inhalt der Datei hinzu:

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

	Die Klasse __SearchByEmail__ kann zur Abfrage von Zeilen nach E-Mail-Adresse verwendet werden. Da sie einen Filter für reguläre Ausdrücke verwendet, können Sie beim Einsatz der Klasse entweder eine Zeichenfolge angeben oder einen regulären Ausdruck.

5. Speichern Sie die Datei __SearchByEmail.java__.

6. Erstellen Sie im Verzeichnis __hbaseapp\src\main\hava\com\microsoft\examples__ eine Datei mit dem Namen __DeleteTable.java__. Fügen Sie Folgendes als Inhalt der Datei hinzu:

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

	Diese Klasse dient nur zum Aufräumen dieses Beispiels. Sie deaktiviert zunächst die von der Klasse __CreateTable__ erstellte Tabelle und legt diese dann ab.

7. Speichern Sie die Datei __DeleteTable.java__.

##Erstellen und Packen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung, und ändern Sie das Verzeichnis in __hbaseapp__.

2. Verwenden Sie den folgenden Befehl, um ein JAR mit der Anwendung zu erstellen.

		mvn clean package

	Damit werden etwaige frühere Erstellungsartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und dann die Anwendung erstellen und gepackt.

3. Nachdem dieser Befehl ausgeführt wurde, enthält das Verzeichnis __hbaseapp\target__ eine Datei namens __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] Die Datei __hbaseapp-1.0-SNAPSHOT.jar__ ist eine Uberjar-Datei (manchmal auch Fatjar genannt), die alle Abhängigkeiten enthält, die für das Ausführen der Datei erforderlich sind.

##Hochladen der JAR-Datei und Starten des Auftrags

> [AZURE.NOTE] Es gibt viele Möglichkeiten, eine Datei in einen HDInsight-Cluster hochzuladen (siehe [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md). Bei den Schritten unten kommt [Azure PowerShell](install-configure-powershell.md) zum Einsatz.

1. Nachdem Sie [Azure PowerShell](install-configure-powershell.md) installiert und konfiguriert haben, erstellen Sie eine neue Datei namens __hbase-runner.psm1__. Fügen Sie Folgendes als Inhalt der Datei hinzu:

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
		        throw "Windows Azure PowerShell not found! For help, see http://azure.microsoft.com/documentation/articles/install-configure-powershell/"
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

	* __Add-HDInsightFile__ wird für das Hochladen von Dateien zu HDInsight verwendet.

	* __Start-HBaseExample__ wird für das Ausführen der bereits erstellter Klassen verwendet.

2. Speichern Sie die Datei __hbase-runner.psm1__.

3. Öffnen Sie ein neues Azure PowerShell-Fenster, ändern Sie das Verzeichnis in __hbaseapp__, und führen Sie dann den folgenden Befehl aus.

		PS C:\ Import-Module c:\path\to\hbase-runner.psm1

	Ändern Sie den Pfad in den Speicherort der früher erstellten Datei __hbase-runner.psm1__. Damit wird das Modul für diese PowerShell-Sitzung registriert.

2. Verwenden Sie den folgenden Befehl zum Hochladen von __hbaseapp-1.0-SNAPSHOT.jar__ in den HDInsight-Cluster.

		Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

	Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters. Der Befehl lädt dann __hbaseapp-1.0-SNAPSHOT.jar__ in __example/jars__ im Hauptspeicher Ihres HDInsight-Clusters hoch.

3. Verwenden Sie nach dem Hochladen der Dateien Folgendes, um eine neue Tabelle mithilfe von __hbaseapp__ zu erstellen.

		Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

	Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters.

	Dieser Befehl erstellt eine neue Tabelle namens __people__ im HDInsight-Cluster. Bei diesem Befehl erfolgt keine Ausgabe im Konsolenfenster.

2. Verwenden Sie den folgenden Befehl, um nach Einträgen in die Tabelle zu suchen.

		Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

	Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters.

	Damit wird die Klasse "SearchByEmail" für die Suche nach etwaigen Zeilen verwendet, in denen die Spaltenfamilie __contactinformation__, die Spalte __email__ die Zeichenfolge __contoso.com__ enthält. Daraufhin sollten Sie folgende Ergebnisse erhalten:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

	Wird __fabrikam.com__ als `-emailRegex`-Wert verwendet, werden Benutzer zurückgegeben, die __fabrikam.com__ im Feld "E-Mail" haben. Da diese Suche mithilfe eines auf regulären Ausdrücken basierenden Filters implementiert wurde, können Sie auch reguläre Ausdrücke wie etwa __^r__ eingeben, womit Einträge zurückgegeben werden, bei denen die E-Mail-Adresse mit dem Buchstaben "r" beginnt.

##Löschen der Tabelle

Wenn Sie mit dem Beispiel fertig sind, verwenden Sie den folgenden Befehl aus der PowerShell-Sitzung zum Löschen der Tabelle __people__, die in diesem Beispiel verwendet wurde.

	Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters.

##Problembehandlung

###Keine oder unerwartete Ergebnisse bei Verwenden von "Start-HBaseExample"

Verwenden Sie den Parameter `-showErr`, wenn Sie das STDERR sehen möchten, das während der Ausführung des Auftrags produziert wurde.
<!--HONumber=42-->
