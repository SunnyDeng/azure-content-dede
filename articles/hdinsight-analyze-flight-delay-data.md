<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analysieren von Daten zu Flugversp&auml;tungen mit Hadoop in HDInsight | Azure" metaKeywords="" description="Hier erfahren Sie, wie Sie Daten zu HDInsight hochladen, die Daten mit Hive bearbeiten und dann mit Sqoop die Ergebnisse in eine SQL-Datenbank exportieren k&ouml;nnen." metaCanonical="" services="hdinsight" documentationCenter="" title="Analysieren von Daten zu Flugversp&auml;tungen mit Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analysieren von Daten zu Flugverspätungen mit Hadoop in HDInsight

Hive ermöglicht die Ausführung eines Hadoop MapReduce-Jobs über eine SQL-ähnliche Skriptsprache namens *[HiveQL][HiveQL]*, die zur Zusammenfassung, Abfrage und Analyse großer Datenmengen verwendet werden kann. In diesem Lernprogramm erfahren Sie, wie Sie mit Hive durchschnittliche Verspätungen an Flughäfen berechnen und dann mit Sqoop die Ergebnisse in eine Azure SQL-Datenbank exportieren können.

Einer der größten Vorteile von HDInsight ist die Trennung von Datenspeicher und Server. HDInsight verwendet Azure-Blob-Speicher zur Datenspeicherung. Ein normaler HDInsight-Prozess besteht aus drei Teilen:

1.  Speichern von Daten im Azure-Blob-Speicher. Dies kann ein fortlaufender Prozess sein. Es werden zum Beispiel Wetterdaten, Sensordaten, Webprotokolle und in diesem Fall Daten zu Flugverspätungen im Blob-Speicher gespeichert.
2.  Ausführen von Aufträgen. Für die Bearbeitung der Daten führen Sie ein PowerShell-Skript aus (oder eine Clientanwendung), um einen HDInsight-Cluster bereitzustellen, Aufträge auszuführen und den Cluster zu löschen. Die Aufträge speichern Ausgabedaten im Azure-Blob-Speicher. Die Ausgabedaten bleiben selbst nach dem Löschen des Clusters erhalten. So bezahlen Sie nur für den tatsächlichen Verbrauch.
3.  Rufen Sie die Ausgabe vom Blob-Speicher ab, oder exportieren Sie die Daten in diesem Fall in eine Azure SQL-Datenbank.

Das folgende Diagramm veranschaulicht das Szenario und die Struktur dieses Artikels:

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

Hinweis: Die Nummern im Diagramm stehen für die Abschnittstitel.

**Geschätzter Zeitaufwand:** 30 Minuten

## Dieses Lernprogramm umfasst folgende Punkte

-   [Voraussetzungen][Voraussetzungen]
-   [Ausführen eines Hive-Auftrags auf einem neuen oder vorhandenen HDInsight-Cluster (M1)][Ausführen eines Hive-Auftrags auf einem neuen oder vorhandenen HDInsight-Cluster (M1)]
-   [Verwenden von Sqoop, um die Ausgabe zu Azure SQL-Datenbank zu exportieren (M2)][Verwenden von Sqoop, um die Ausgabe zu Azure SQL-Datenbank zu exportieren (M2)]
-   [Nächste Schritte][Nächste Schritte]
-   [Anhang A: Hochladen von Daten zu Flugverspätungen in einen Azure-Blob-Speicher (A1)][Anhang A: Hochladen von Daten zu Flugverspätungen in einen Azure-Blob-Speicher (A1)]
-   [Anhang B: Erstellen und Hochladen eines HiveQL-Skripts (A2)][Anhang B: Erstellen und Hochladen eines HiveQL-Skripts (A2)]

## <span id="prerequisite"></span></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].
-   Ein Azure-Abonnement. \*\*\*

**Grundlagen der HDInsight-Speicherung**

Hadoop-Cluster in HDInsight verwenden Azure-Blob-Speicher zur Datenspeicherung. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von *HDFS* in Azure-Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight].

Wenn Sie einen HDInsight-Cluster bereitstellen, wird ebenso wie in HDFS ein Blob-Speichercontainer eines Azure-Speicherkontos als Standarddateisystem festgelegt. Dieses Speicherkonto ist das *Standardspeicherkonto*, und der Blob-Container wird *Standard-Blob-Container* oder *Standardcontainer* genannt. Das Standardspeicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. Durch das Löschen eines HDInsight-Clusters wird der Standardcontainer oder das Standardspeicherkonto nicht gelöscht.

Zusätzlich zum Standardspeicherkonto können andere Azure-Speicherkonten während des Bereitstellungsprozesses an einen HDInsight-Cluster gebunden werden. Die Bindung erfolgt dadurch, dass das Speicherkonto und der Speicherkontoschlüssel zur Konfigurationsdatei hinzugefügt werden. Somit kann der Cluster zur Laufzeit auf diese Speicherkonten zugreifen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][Bereitstellen von Hadoop-Clustern in HDInsight].

Die WASB-Syntax lautet:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Der WASB-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight].

Auf Dateien, die im Standardcontainer gespeichert sind, kann in HDInsight über einen der folgenden URIs zugegriffen werden (als Beispiel wird hier flightdelays.hql verwendet):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Um direkt aus dem Speicherkonto auf die Datei zuzugreifen, lautet der Blob-Name für die Datei:

    tutorials/flightdelays/flightdelays.hql

Es befindet sich kein „/“ vor dem Blob-Namen.

**Verwendete Dateien in diesem Lernprogramm**

In der folgenden Tabelle sind die in diesem Lernprogramm verwendeten Dateien aufgelistet:

| Dateien                                                                   | Beschreibung                                                                                                     |
|---------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | Die HiveQL-Skriptdatei, die vom Hive-Auftrag benötigt wird, den Sie ausführen werden.                            |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Die Eingabedaten für die Hive-Aufträge.                                                                          |
| \\tutorials\\flightdelays\\output                                         | Der Ausgabepfad für den Hive-Auftrag. Der Standardcontainer wird für die Speicherung der Ausgabedaten verwendet. |
| \\tutorials\\flightdelays\\jobstatus                                      | Der Statusordner des Hive-Auftrags.                                                                              |

In diesem Lernprogramm werden Daten zur termingemäßen Leistung von Airline-Flügen von [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA) verwendet. Die Daten wurden in einen Azure-Blob-Speichercontainer mit öffentlicher Blob-Zugriffsberechtigung hochgeladen. Da es sich um einen öffentlichen Blob-Container handelt, müssen Sie dieses Speicherkonto nicht an den HDInsight-Cluster binden. Das HiveQL-Skript wird ebenfalls zum selben Blob-Container hochgeladen. Wie Sie die Daten zu Ihrem Speicherkonto hochladen und die HiveQL-Skriptdatei erstellen bzw. hochladen, erfahren Sie im [Anhang][Anhang].

**Verstehen von internen und externen Hive-Tabellen**

Folgendes sollten Sie über interne und externe Hive-Tabellen wissen:

-   Mit dem Befehl CREATE TABLE erstellen Sie eine interne Tabelle. Die Datendatei muss sich im Standardcontainer befinden.
-   Der Befehl CREATE TABLE verschiebt die Datendatei in den Ordner /hive/warehouse/<tablename>.
-   Der Befehl CREATE EXTERNAL TABLE erstellt eine externe Tabelle. Die Datendatei kann sich außerhalb des Standardcontainers befinden.
-   Der Befehl CREATE EXTERNAL TABLE verschiebt die Datendatei nicht.
-   Der Befehl CREATE EXTERNAL TABLE lässt keine Ordner im SPEICHERORT zu. Aus diesem Grund wird im Lernprogramm eine Kopie der Datei sample.log erstellt.

Weitere Informationen finden Sie unter [HDInsight: Hive Internal and External Tables Intro][HDInsight: Hive Internal and External Tables Intro] (HDInsight: Einführung in interne und externe Hive-Tabellen, in englischer Sprache).

> [WACOM.NOTE] Eine der HiveQL-Anweisungen erstellt eine externe Hive-Tabelle. Externe Hive-Tabellen erhalten die Datendatei am ursprünglichen Speicherort. Interne Hive-Tabellen verschieben die Datendate nach hive\\warehouse. Bei internen Hive-Tabellen muss die Datendatei im Standardcontainer enthalten sein. Bei Daten, die nicht im Standard-Blob-Container gespeichert sind, müssen Sie externe Hive-Tabellen verwenden.

## <span id="runjob"></span></a>Ausführen eines Hive-Auftrags auf einem neuen oder vorhandenen HDInsight-Cluster

Hadoop verwendet Stapelbearbeitung. Die kosteneffizienteste Möglichkeit, einen Hive-Auftrag auszuführen, ist, einen Cluster für den Auftrag bereitzustellen und den Auftrag nach Abschluss zu löschen. Das folgende Skript erläutert den gesamten Vorgang. Weitere Informationen zur Bereitstellung eines HDInsight-Clusters und zur Ausführung von Hive-Aufträgen finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight][Bereitstellen von Hadoop-Clustern in HDInsight] und [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight].

**Ausführen der Hive-Abfragen mit PowerShell**

1.  Öffnen Sie PowerShell ISE.
2.  Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

        *** add script here

3.  Drücken Sie **F5**, um das Skript auszuführen. Die Ausgabe sollte der Folgenden ähneln:

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

    Zwischen jeder Stadt und Verzögerungszeit befindet sich ein Trennzeichen, das im PowerShell-Ausgabefenster nicht sichtbar ist. Es lautet "\\001". Sie verwenden dieses Trennzeichen, wenn Sie den Sqoop-Export ausführen.

## <span id="exportdata"></span></a>Exportieren der Hive-Job-Ergebnisse in die Azure SQL-Datenbank

Der letzte Schritt besteht darin, den Sqoop-Export auszuführen, um die Daten in die SQL-Datenbank zu exportieren. Zuvor in diesem Lernprogramm haben Sie die SQL-Datenbank und die Tabelle "AvgDelays" erstellt.

**Exportieren von Daten in die SQL-Datenbank**

1.  Öffnen Sie Azure PowerShell.
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

        Add-AzureAccount

3.  Legen Sie die ersten fünf Variablen fest, und führen Sie dann folgende Befehle aus:

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Dies sind die Variablen und deren Beschreibung:

    <table border="1">

    <tr>
    <td>
    **Variablenname**

    </td>
    <td>
    **Hinweis**

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Dies ist der Name des HDInsight-Clusters.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Dies ist der Name des SQL-Datenbankservers, auf den Sqoop Daten exportiert.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Dies ist der Benutzername für die SQL-Datenbank.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Dies ist das Benutzerkennwort für die SQL-Datenbank.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Dies ist der Name der SQL-Datenbank, in die Sqoop Daten exportiert. Der Standardname lautet \*HDISqoop".

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Dies ist die SQL-Datenbank, in die Sqoop Daten exportiert. Der Standardname lautet AvgDelays. Dies ist die Tabelle, die Sie zuvor im Lernprogramm erstellt haben.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Dies ist der Speicherort der Hive-Ausgabedatei. Sqoop exportiert die Dateien aus diesem Speicherort in die SQL-Datenbank.

    </td>
    </tr>

    </table>
    </p>
4.  Führen Sie den folgenden Befehl aus, um Daten zu exportieren:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Stellen Sie eine Verbindung zur SQL-Datenbank her, und zeigen Sie die durchschnittlichen Flugverspätungen nach Stadt geordnet in der Tabelle *AvgDelays* an:

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>Nächste Schritte

Jetzt haben Sie erfahren, wie Sie Folgendes vornehmen können: Dateien in den Blob-Speicher hochladen, eine Hive-Tabelle mit Daten aus dem Blob-Speicher füllen, Hive-Abfragen ausführen und Sqoop verwenden, um Daten aus dem HDFS in Azure-SQL-Datenbank zu exportieren. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Getting Started with HDInsight][Getting Started with HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Verwenden von Oozie mit HDInsight][Verwenden von Oozie mit HDInsight]
-   [Verwenden von Sqoop mit HDInsight][Verwenden von Sqoop mit HDInsight]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]
-   [Entwickeln von Java MapReduce-Programmen für HDInsight][Entwickeln von Java MapReduce-Programmen für HDInsight]
-   [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight]

## <span id="appendix-a"></span></a>Anhang A – Hochladen der Daten zu Flugverspätungen in den Azure-Blob-Speicher

**Herunterladen der Flugdaten**

1.  Rufen Sie die folgende Website auf: [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA).
2.  Wählen Sie auf der Website die folgenden Werte aus:

    | Name          | Wert                                                                                                                                                                                                                                                                                                                                                                                                                   |
    |---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2013                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                                                |
    | Fields:       | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (Entfernen Sie die Häkchen bei allen anderen Feldern.) |

3.  Klicken Sie auf **Download**.
4.  Entpacken Sie die Datei im Ordner **C:\\Tutorials\\FlightDelays\\Data**. Jede Datei ist eine CSV-Datei und hat eine Größe von ungefähr 60 GB.
5.  Geben Sie der Datei den Monat, für den sie Daten enthält, als neuen Namen. Die Datei mit Daten aus dem Monat Januar hieße dann beispielsweise *January.csv*.
6.  Wiederholen Sie Schritt 2 und 5, um eine Datei für jeden der 12 Monate des Jahres 2013 herunterzuladen. Für die Ausführung des Lernprogramms benötigen Sie mindestens eine Datei.

**Hochladen der Daten zu Flugverspätungen in den Azure-Blob-Speicher**

1.  Öffnen Sie PowerShell ISE.
2.  Fügen Sie das folgende Skript in den Skriptbereich ein:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  Drücken Sie **F5**, um das Skript auszuführen.

## <span id="appendix-b"></span></a>Anhang B – Erstellen und Hochladen eines HiveQL-Skripts

Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei einfügen. Der Abschnitt zeigt, wie Sie ein HiveQL-Skript erstellen und das Skript mithilfe von PowerShell in den Azure-Blob-Speicher hochladen. Hive erfordert, dass HiveQL-Skripte auf WASB gespeichert werden.

Das HiveQL-Skript führt folgende Schritte aus:

1.  **Löschen der Tabelle delays\_raw**, wenn diese Tabelle bereits vorhanden ist.
2.  **Erstellen der externen Hive-Tabelle delays\_raw**, die auf den WASB-Speicherort mit den Daten zu Flugverspätungen verweist. Diese Abfrage legt fest, dass Felder durch "," getrennt und Zeilen mit "\\n" beendet werden. Dies stellt ein Problem dar, wenn Feldwerte Kommas *enthalten*, da Hive nicht zwischen einem Komma als Trennzeichen für Felder und einem Komma als Teil eines Feldwerts unterscheiden kann. (Letzteres ist der Fall bei Feldwerten für ORIGIN\_CITY\_NAME und DEST\_CITY\_NAME.) Zur Behebung dieses Problems erstellt die Abfrage TEMP-Spalten zur Aufbewahrung von Daten, die fehlerhaft in Spalten aufgeteilt sind.
3.  **Löschen der Tabelle delays**, wenn diese Tabelle bereits vorhanden ist.
4.  **Erstellen der Tabelle delays**. Es ist hilfreich, die Daten vor der weiteren Verarbeitung zu bereinigen. Diese Abfrage erstellt eine neue Tabelle *delays* aus der Tabelle *delays\_raw*. Beachten Sie, dass die TEMP-Spalten (wie zuvor erwähnt) nicht kopiert werden und dass die *substring*-Funktion verwendet wird, um Anführungszeichen aus den Daten zu entfernen.
5.  **Berechnen der durchschnittlichen Verspätungen aufgrund des Wetters und Gruppieren der Ergebnisse nach Name der Stadt.** Außerdem werden die Ergebnisse in den WASB ausgegeben. Beachten Sie, dass die Anfrage Apostrophe aus den Daten entfernt und Zeilen ausschließt, in denen der Wert für *weather\_dela*y *null* beträgt. Dies ist erforderlich, da Sqoop, das Sie später in diesem Lernprogramm verwenden, diese Werte standardmäßig nicht ordnungsgemäß behandelt.

Eine vollständige Liste der HiveQL-Befehle finden Sie unter [Hive Data Definition Language][HiveQL] (Hive-Datendefinitionssprache, in englischer Sprache). Jeder HiveQL-Befehl muss mit einem Semikolon enden.

**Erstellen einer HiveQL-Skriptdatei**

1.  Öffnen Sie Azure PowerShell ISE.
2.  Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Im Skript werden folgende Konstanten verwendet:

    -   **$hqlLocalFileName**: Das Skript speichert die HiveQL-Skriptdatei lokal, bevor diese zu WASB hochgeladen wird. Dies ist der Dateiname. Die Standardwerte lauten <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
    -   **$hqlBlobName**: Dies ist der Blob-Name der HiveQL-Skriptdatei, der im Azure-Blob-Speicher verwendet wird. Die Standardwerte lauten <u>tutorials/flightdelays/flightdelays.hql</u>. Da die Datei direkt zum Azure-Blob-Speicher geschrieben wird, befindet sich KEIN "/" am Anfang des Blob-Namens. Wenn Sie von WASB auf die Datei zugreifen möchten, müssen Sie "/" am Anfang des Dateinamens hinzufügen.
    -   **$srcDataFolder** und **$dstDataFolder**: = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

        Add-AzureAccount

4.  Legen Sie die ersten zwei Variablen fest, und führen Sie dann die Befehle aus.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Dies sind die Variablen und deren Beschreibung:

    <table border="1">

    <tr>
    <td>
    **Variablenname**

    </td>
    <td>
    **Beschreibung**

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Dies ist das Azure-Speicherkonto, mit dem die HiveQL-Skriptdatei gespeichert wird. Die in diesem Lernprogramm bereitgestellten PowerShell-Skripts erfordern sowohl die Flugdatendateien, als auch die Skriptdatei, die sich in demselben Azure-Speicherkonto und Blob-Speichercontainer befinden.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Dies ist der Azure-Blob-Speichercontainer, mit dem die HiveQL-Skriptdatei gespeichert wird. Die in diesem Lernprogramm bereitgestellten PowerShell-Skripts erfordern sowohl die Flugdatendateien, als auch die Skriptdatei, die sich in demselben Azure-Speicherkonto und Blob-Speichercontainer befinden.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    Dies ist der lokale Dateiname für das HiveQL-Skript, bevor es in den WASB hochgeladen wird. Zur Vereinfachung des PowerShell-Skripts schreiben Sie die Datei lokal und verwenden dann das Cmdlet "Set-AzureStorageBlobContent", um die Skriptdatei in HDInsight hochzuladen.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    Dies ist der Skriptdateiname mit dem Pfad im WASB

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    Dies ist der Ordner im WASB, aus dem das HiveQL-Skript Daten bezieht.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    Dies ist der Ordner im WASB, zu dem das HiveQL-Skript die Ergebnisse sendet. Im Verlauf des Lernprogramms verwenden Sie Sqoop, um die Daten aus diesem Ordner in Azure-SQL-Datenbank zu exportieren.

    </td>
    </tr>

    </table>
    </p>
5.  Führen Sie die folgenden Befehle aus, um die HiveQL-Anweisungen zu definieren:

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

6.  Führen Sie die folgenden Befehle aus, um die Hive-Skriptdatei auf der Arbeitsstation zu schreiben und sie dann in den WASB hochzuladen:

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    Die Ausgabe sollte der Folgenden ähneln:

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## Vorbereiten der SQL-Datenbank

**Vorbereiten der SQL-Datenbank (mit dem Sqoop-Skript zusammenfügen)**

1.  Öffnen Sie PowerShell ISE.
2.  Kopieren und fügen Sie das folgende Skript in den Skriptbereich ein:

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > WACOM.NOTE Das Skript verwendet einen REST-Dienst, <http://bot.whatismyipaddress.com>, um Ihre externe IP-Adresse abzurufen. Die IP-Adresse wird für das Erstellen einer Firewall-Regel für den SQL-Datenbankserver verwendet.

    Im Skript werden unter anderem folgende Konstanten verwendet:

    -   **$ipAddressRestService**: Der Standardwert ist <u><http://bot.whatismyipaddress.com></u>. Es handelt sich um einen REST-Dienst mit öffentlicher IP-Adresse, mit dem die externe IP-Adresse abgerufen wird. Bei Bedarf können Sie andere Dienst verwenden. Die externe IP-Adresse, die vom Dienst abgerufen wurde, wird verwendet, um eine Firewallregel für Ihren Azure SQL-Datenbankserver zu erstellen, sodass Sie von der Arbeitsstation aus auf die Datenbank zugreifen können (mit PowerShell-Skript).
    -   **$fireWallRuleName**: Dies ist der Name der Firewall des Azure SQL-Datenbankservers. Der Standardname lautet <u>FlightDelay</u>. Bei Bedarf können Sie den Namen ändern.
    -   **$sqlDatabaseMaxSizeGB**: Dieser Wert wird nur verwendet, wenn ein neuer Azure SQL-Datenbankserver erstellt wird. Der Standardwert ist <u>10 GB</u>. 10 GB reicht für dieses Lernprogramm aus.
    -   **$sqlDatabaseName**: Dieser Wert wird nur verwendet, wenn eine neue Azure SQL-Datenbank erstellt wird. Der Standardwert ist <u>HDISqoop</u>. Wenn Sie diesen umbenennen, müssen Sie das Sqoop PowerShell-Skript entsprechend aktualisieren.

3.  Drücken Sie **F5**, um das Skript auszuführen. Sie müssen die Anmeldeinformationen Ihres Azure-Abonnements sowie folgende Werte eingeben:

    -   **sqlDatabaseServer**: Geben Sie den Namen des Azure SQL-Datenbankservers ein, zu dem Sie die Hive-Ausgabe exportieren möchten. Geben Sie nichts ein, um einen neuen Datenbankserver zu erstellen.
    -   **sqlDatabaseUsername**: Geben Sie die Anmeldeinformationen für die Datenbank ein. Sie müssen angeben, ob Sie einen neuen Datenbankserver erstellen oder einen vorhanden verwenden möchten.
    -   **sqlDatabasePassword**: Geben Sie das Kennwort für die Datenbankanmeldung ein. Sie müssen angeben, ob Sie einen neuen Datenbankserver erstellen oder einen vorhanden verwenden möchten.
    -   **sqlDatabaseLocation**: Geben Sie die Region ein. Dies wird nur verwendet, wenn Sie einen neuen Datenbankserver erstellen möchten.
    -   **sqlDatabaseName**: Geben Sie den Namen der Azure SQL-Datenbank ein. Geben Sie nichts ein, um eine neue Datenbank zu erstellen. Der Standarddatenbankname ist **HDISqoop**.

4.  Validieren Sie die Skriptausgabe. Überprüfen Sie, ob das Skript erfolgreich ausgeführt wurde.

Wenn Sie für das Hochladen der Dateien eine andere Methode verwenden möchten, stellen Sie sicher, dass der Dateipfad *tutorials/flightdelays/data* lautet. Für den Zugriff auf die Dateien gilt folgende Syntax:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

Bei *tutorials/flightdelays/data* handelt es sich um den virtuellen Ordner, den Sie beim Hochladen der Dateien erstellt haben. Überprüfen Sie, dass 12 Dateien vorhanden sind, eine für jeden Monat.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [Voraussetzungen]: #prerequisite
  [Ausführen eines Hive-Auftrags auf einem neuen oder vorhandenen HDInsight-Cluster (M1)]: #runjob
  [Verwenden von Sqoop, um die Ausgabe zu Azure SQL-Datenbank zu exportieren (M2)]: #exportdata
  [Nächste Schritte]: #nextsteps
  [Anhang A: Hochladen von Daten zu Flugverspätungen in einen Azure-Blob-Speicher (A1)]: #appdendix-a
  [Anhang B: Erstellen und Hochladen eines HiveQL-Skripts (A2)]: #appendix-b
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Bereitstellen von Hadoop-Clustern in HDInsight]: ../hdinsight-provision-clusters/
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [Anhang]: #appendix
  [HDInsight: Hive Internal and External Tables Intro]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Getting Started with HDInsight]: ../hdinsight-get-started/
  [Verwenden von Oozie mit HDInsight]: ../hdinsight-use-oozie/
  [Verwenden von Sqoop mit HDInsight]: ../hdinsight-use-sqoop/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
  [Entwickeln von Java MapReduce-Programmen für HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
