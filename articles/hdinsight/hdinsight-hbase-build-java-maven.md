<properties
	pageTitle="Erstellen einer HBase-Anwendung mit Maven und Bereitstellen in Windows-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Apache Maven eine Java-basierte Apache HBase-Anwendung erstellen und anschließend in einem Windows-basiertem Azure HDInsight-Cluster bereitstellen können."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="larryfr"/>

#Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen

Erfahren Sie, wie Sie eine [Apache HBase](http://hbase.apache.org/)-Anwendung in Java mithilfe von Apache Maven erstellen und entwickeln. Setzen Sie anschließend die Anwendung mit Azure HDInsight (Hadoop) ein.

[Maven](http://maven.apache.org/) ist ein Projektmanagement- und Verständnistool, mit dem Sie Software, Dokumentationen und Berichte für Java-Projekte erstellen können. In diesem Artikel erfahren Sie, wie Sie es für das Erstellen einer einfachen Java-Anwendung verwenden, die eine HBase-Tabelle auf einem Azure HDInsight-Cluster erstellt, abfragt und löscht.

##Anforderungen

* [Java-Plattform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 oder höher

* [Maven](http://maven.apache.org/)

* [Ein Azure HDInsight-Cluster mit HBase](hdinsight-hbase-get-started.md#create-hbase-cluster)

##Erstellen des Projekts

1. Ändern Sie an der Befehlszeile Ihrer Entwicklungsumgebung die Verzeichnisse auf den Speicherort, an dem Sie das Projekt erstellen möchten, beispielsweise `cd code\hdinsight`.

2. Verwenden Sie den Befehl __mvn__, der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Damit wird ein neues Verzeichnis im aktuellen erstellt, das den Namen trägt, der vom Parameter __artifactID__ (in diesem Beispiel **hbaseapp**) festgelegt wurde. Dieses Verzeichnis enthält die folgenden Elemente:

	* __pom.xml__: Das Projektobjektmodell ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) enthält Informationen und Konfigurationsdetails für das Erstellen des Projekts.

	* __src__: Das Verzeichnis, das das Verzeichnis __main\\java\\com\\microsoft\\examples__ enthält, in dem Sie die Anwendung erstellen.

3. Löschen Sie die Datei __src\\test\\java\\com\\microsoft\\examples\\apptest.java__, da sie in diesem Beispiel nicht verwendet wird.

##Aktualisieren des Projektobjektmodells

1. Bearbeiten Sie die Datei __pom.xml__, und fügen Sie folgenden Code im Abschnitt `<dependencies>` ein.

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	Damit wird Maven informiert, dass das Projekt den __hbase-Client__ in der Version __0.98.4-hadoop2__ benötigt. Beim Kompilieren wird er aus dem standardmäßigen Maven-Repository heruntergeladen. Sie können die [Suche im zentralen Maven-Repository](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) verwenden, wenn Sie weitere Informationen zu dieser Abhängigkeit erhalten möchten.

2. Fügen Sie der Datei __pom.xml__ folgenden Code hinzu: Dieser muss sich in der Datei innerhalb der `<project>...</project>`-Tags befinden, z. B. zwischen `</dependencies>` und `</project>`.

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
						<version>3.3</version>
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

	Damit wird eine Ressource (__conf\\hbase-site.xml__,) konfiguriert, die die Konfigurationsinformationen für HBase enthält.

	> [AZURE.NOTE]Sie können die Konfigurationswerte auch per Code festlegen. Wie Sie hierbei vorgehen, erfahren Sie in den Kommentaren im Beispiel __CreateTable__ weiter unten.

	Damit werden auch das [Maven Compiler-Plug-In](http://maven.apache.org/plugins/maven-compiler-plugin/) und das [Maven Shade-Plug-In](http://maven.apache.org/plugins/maven-shade-plugin/) konfiguriert. Das Compiler-Plug-In wird zum Kompilieren der Topologie verwendet. Das Shade-Plug-In wird verwendet, um die Lizenzduplizierung in dem von Maven erstellten JAR-Paket zu verhindern. Es wird deshalb verwendet, weil die doppelten Lizenzdateien bei der Laufzeit einen Fehler auf dem HDInsight-Cluster verursachen. Wird maven-shade-plugin mit der `ApacheLicenseResourceTransformer`-Implementierung verwendet, so wird dieser Fehler verhindert.

	Das maven-shade-plugin erzeugt außerdem ein Uberjar (oder Fatjar), das alle Abhängigkeiten enthält, die von der Anwendung benötigt werden.

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

	> [AZURE.NOTE]Das ist eine sehr minimale Datei vom Typ "hbase-site.xml", und sie enthält Mindesteinstellungen für den HDInsight-Cluster. Eine vollständige Version der Konfigurationsdatei "hbase-site.xml", die von HDInsight verwendet wird, finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-management-portal.md#rdp). Die Datei "hbase-site.xml" befindet sich im Verzeichnis "C:\\apps\\dist\\hbase-&lt;Versionsnummer>-hadoop2\\conf". Der Dateiteil mit der Versionsnummer des Dateipfads ändert sich, wenn HBase auf dem Cluster aktualisiert wird.

3. Speichern Sie die Datei __hbase-site.xml__.

##Erstellen der Anwendung

1. Wechseln Sie zum Verzeichnis __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__, und benennen Sie die Datei "app.java" in __CreateTable.java__ um.

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

	Das ist die Klasse __CreateTable__, die eine Tabelle namens __people__ erzeugt und sie mit einigen vordefinierten Benutzern füllt.

3. Speichern Sie die Datei __CreateTable.java__.

4. Erstellen Sie im Verzeichnis __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ eine Datei mit dem Namen __SearchByEmail.java__. Fügen Sie Folgendes als Inhalt der Datei hinzu:

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

6. Erstellen Sie im Verzeichnis __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__ eine Datei mit dem Namen __DeleteTable.java__. Fügen Sie Folgendes als Inhalt der Datei hinzu:

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

	Diese Klasse dient zum Aufräumen dieses Beispiels. Sie deaktiviert die von der Klasse __CreateTable__ erstellte Tabelle und verwirft sie.

7. Speichern Sie die Datei __DeleteTable.java__.

##Erstellen und Packen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung, und ändern Sie das Verzeichnis in __hbaseapp__.

2. Verwenden Sie den folgenden Befehl, um eine JAR-Datei zu erstellen, die die Anwendung enthält:

		mvn clean package

	Damit werden etwaige frühere Erstellungsartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und dann die Anwendung erstellt und gepackt.

3. Nachdem dieser Befehl ausgeführt wurde, enthält das Verzeichnis __hbaseapp\\target__ eine Datei namens __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE]Die Datei __hbaseapp-1.0-SNAPSHOT.jar__ ist ein Uberjar (manchmal auch Fatjar genannt) mit allen Abhängigkeiten, die für das Ausführen der Anwendung erforderlich sind.

##Hochladen der JAR-Datei und Starten eines Auftrags

> [AZURE.NOTE]Es gibt viele Möglichkeiten, eine Datei in einen HDInsight-Cluster hochzuladen, wie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md) beschrieben. Die folgenden Schritte geschehen mithilfe von [Azure PowerShell](../install-configure-powershell.md).

1. Nachdem Sie Azure PowerShell installiert und konfiguriert haben, erstellen Sie eine neue Datei namens __hbase-runner.psm1__. Fügen Sie Folgendes als Inhalt der Datei hinzu:

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
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
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
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup `
                | %{ $_.Key1 }
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

	Diese Datei enthält zwei Module:

	* __Add-HDInsightFile__ – wird für das Hochladen von Dateien zu HDInsight verwendet

	* __Start-HBaseExample__ – wird für das Ausführen der bereits erstellter Klassen verwendet

2. Speichern Sie die Datei __hbase-runner.psm1__.

3. Öffnen Sie ein neues Azure PowerShell-Fenster, ändern Sie die Verzeichnis auf __hbaseapp__ und führen Sie dann den folgenden Befehl aus.

		PS C:\ Import-Module c:\path\to\hbase-runner.psm1

	Ändern Sie den Pfad auf den Speicherort der früher erstellten Datei __hbase-runner.psm1__. Dadurch wird das Modul für diese Azure PowerShell-Sitzung registriert.

2. Verwenden Sie den folgenden Befehl zu Hochladen von __hbaseapp-1.0-SNAPSHOT.jar__ zum HDInsight-Cluster.

		Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

	Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters. Der Befehl lädt dann __hbaseapp-1.0-SNAPSHOT.jar__ in __example/jars__ im Hauptspeicher Ihres HDInsight-Clusters hoch.

3. Verwenden Sie nach dem Hochladen der Dateien folgenden Code, um eine neue Tabelle mithilfe von __hbaseapp__ zu erstellen.

		Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

	Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters.

	Dieser Befehl erstellt eine neue Tabelle namens __people__ im HDInsight-Cluster. Bei diesem Befehl erfolgt keine Ausgabe im Konsolenfenster.

2. Verwenden Sie den folgenden Befehl, um nach Einträgen in die Tabelle zu suchen:

		Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

	Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters.

	Dieser Befehl verwendet die Klasse **SearchByEmail** Klasse, um nach allen Zeilen zu suchen, in denen die Spaltenfamilie __contactinformation__ und die Spalte __email__ die Zeichenfolge __contoso.com__ enthält. Daraufhin sollten Sie folgende Ergebnisse erhalten:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

	Wird __fabrikam.com__ als `-emailRegex`-Wert verwendet, werden Benutzer zurückgegeben, die __fabrikam.com__ im Feld "E-Mail" aufweisen. Da diese Suche mithilfe eines auf regulären Ausdrücken basierenden Filters implementiert wurde, können Sie auch reguläre Ausdrücke wie etwa __^r__ eingeben, womit Einträge zurückgegeben werden, bei denen die E-Mail-Adresse mit dem Buchstaben "r" beginnt.

##Löschen der Tabelle

Wenn Sie mit dem Beispiel fertig sind, verwenden Sie den folgenden Befehl aus der Azure PowerShell-Sitzung zum Löschen der Tabelle __people__, die in diesem Beispiel verwendet wurde.

	Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Ersetzen Sie __hdinsightclustername__ durch den Namen Ihres HDInsight-Clusters.

##Problembehandlung

###Keine oder unerwartete Ergebnisse bei Verwenden von "Start-HBaseExample"

Verwenden Sie den Parameter `-showErr`, wenn Sie den Standardfehler (STDERR) sehen möchten, der während der Ausführung des Auftrags erzeugt wurde.

<!---HONumber=AcomDC_1210_2015-->