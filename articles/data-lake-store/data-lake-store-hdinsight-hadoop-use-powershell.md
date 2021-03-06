<properties 
   pageTitle="Konfigurieren von HDInsight-Clustern mit Azure Data Lake-Speicher mithilfe von PowerShell | Azure" 
   description="Verwenden von Azure PowerShell zum Konfigurieren und Verwenden von HDInsight Hadoop-Clustern mit Azure Data Lake" 
   services="data-lake" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/06/2015"
   ms.author="nitinme"/>

# Bereitstellen eines HDInsight-Clusters mit Data Lake-Speicher mithilfe von Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Erfahren Sie, wie Sie Azure PowerShell zum Konfigurieren eines HDInsight-Clusters (Hadoop, HBase oder Storm) verwenden, damit er mit einem Azure Data Lake-Speicher genutzt werden kann. Einige wichtige Hinweise zu dieser Version:

* **Für Hadoop- und Storm-Cluster (Windows und Linux)** kann der Data Lake-Speicher nur als zusätzliches Speicherkonto verwendet werden. Standardspeicherkonten für solche Cluster sind weiterhin Azure-Speicherblobs (WASB).

* Sie können **für HBase-Cluster (Windows und Linux)** den Data Lake-Speicher als Standardspeicher oder als Zusatzspeicher verwenden.


In diesem Artikel stellen wir einen Hadoop-Cluster mit Data Lake-Speicher als zusätzlichem Speicher bereit.

Zum Konfigurieren von HDInsight für den Data Lake-Speicher mithilfe von PowerShell sind die folgenden Schritte erforderlich:

* Erstellen eines Azure Data Lake-Speichers
* Einrichten der Authentifizierung für rollenbasierten Zugriff auf den Data Lake-Speicher
* Erstellen eines HDInsight-Clusters mit Authentifizierung für den Data Lake-Speicher
* Ausführen eines Testauftrags im Cluster

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/de-DE/pricing/free-trial/).
- **Aktivieren Ihres Azure-Abonnements** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup).
- **Windows SDK**. Sie können die Installation von [hier](https://dev.windows.com/de-DE/downloads) durchführen. Dies dient zum Erstellen eines Sicherheitszertifikats.
- **Azure PowerShell 1.0 oder höher**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).
 

## Erstellen eines Azure Data Lake-Speichers

Führen Sie folgende Schritte aus, um einen Data Lake-Speicher zu erstellen.

1. Öffnen Sie auf dem Desktop ein neues Azure PowerShell-Fenster, und geben Sie den folgenden Codeausschnitt ein. Stellen Sie bei der Aufforderung zum Anmelden sicher, dass Sie sich als einer der Administratoren bzw. Besitzer des Abonnements anmelden:

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

3. Ein Azure Data Lake-Speicherkonto wird einer Azure-Ressourcengruppe zugeordnet. Erstellen Sie zunächst eine Azure-Ressourcengruppe.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Azure-Ressourcengruppe erstellen](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Azure-Ressourcengruppe erstellen")

2. Erstellen Sie ein Azure Data Lake-Speicherkonto. Der angegebene Kontoname darf nur Kleinbuchstaben und Zahlen enthalten.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Azure Data Lake-Konto erstellen](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake-Konto erstellen")

3. Stellen Sie sicher, dass das Konto erfolgreich erstellt wurde.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	Die Ausgabe sollte **True** lauten.

4. Laden Sie einige Beispieldaten in Azure Data Lake hoch. Sie werden später in diesem Artikel verwendet, um zu überprüfen, ob auf die Daten aus einem HDInsight-Cluster zugegriffen werden kann. Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData) herunterladen.

		
		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Einrichten der Authentifizierung für rollenbasierten Zugriff auf den Data Lake-Speicher

Jedes Azure-Abonnement ist mit einem Azure Active Directory verknüpft. Benutzer und Dienste, die über das Azure-Portal oder die Azure-Ressourcen-Manager-API auf Abonnementressourcen zugreifen, müssen sich zunächst per Azure Active Directory authentifizieren. Azure-Abonnements und -Dienste erhalten Zugriff, indem ihnen die entsprechende Rolle für eine Azure-Ressource zugewiesen wird. Für Dienste identifiziert der Dienstprinzipal den Dienst in Azure Active Directory (AAD). In diesem Abschnitt wird veranschaulicht, wie Sie einem Anwendungsdienst, z. B. HDInsight, Zugriff auf eine Azure-Ressource gewähren (das zuvor erstellte Azure Data Lake-Speicherkonto), indem Sie einen Dienstprinzipal für die Anwendung erstellen und ihr über Azure PowerShell Rollen zuweisen.

Sie müssen die folgenden Aufgaben ausführen, um die Active Directory-Authentifizierung für Azure Data Lake einzurichten.

* Erstellen eines selbstsignierten Zertifikats
* Erstellen einer Anwendung in Azure Active Directory und eines Dienstprinzipals

### Erstellen eines selbstsignierten Zertifikats

Stellen Sie sicher, dass Sie das [Windows-SDK](https://dev.windows.com/de-DE/downloads) installiert haben, bevor Sie mit den Schritten in diesem Abschnitt fortfahren. Sie müssen auch ein Verzeichnis erstellt haben, z. B. **C:\\mycertdir**, in dem das Zertifikat erstellt werden soll.

1. Navigieren Sie im PowerShell-Fenster zu dem Speicherort, an dem Sie das Windows-SDK installiert haben (normalerweise `C:\Program Files (x86)\Windows Kits\10\bin\x86`), und verwenden Sie das [MakeCert][makecert]-Hilfsprogramm, um ein selbstsigniertes Zertifikat und einen privaten Schlüssel zu erstellen. Verwenden Sie die folgenden Befehle:

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	Sie werden aufgefordert, das Kennwort für den privaten Schlüssel einzugeben. Nachdem der Befehl erfolgreich ausgeführt wurde, sollten im von Ihnen angegebenen Zertifikatsverzeichnis die Dateien **CertFile.cer** und **mykey.pvk** enthalten sein.

4. Verwenden Sie das Hilfsprogramm [Pvk2Pfx][pvk2pfx], um die von MakeCert erstellten PVK- und CER-Dateien in eine PFX-Datei zu konvertieren. Führen Sie den folgenden Befehl aus:

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	Geben Sie bei Aufforderung das Kennwort für den privaten Schlüssel ein, das Sie bereits angegeben haben. Der Wert, den Sie für den Parameter **-po** angeben, ist das Kennwort, das der PFX-Datei zugeordnet ist. Nachdem der Befehl erfolgreich abgeschlossen wurde, sollte im von Ihnen angegebenen Zertifikatsverzeichnis auch die Datei „CertFile.pfx“ enthalten sein.

###  Erstellen einer Anwendung in Azure Active Directory und eines Dienstprinzipals

In diesem Abschnitt führen Sie folgende Schritte aus: Erstellen eines Dienstprinzipals für eine Azure Active Directory-Anwendung, Zuweisen einer Rolle zum Dienstprinzipal und Authentifizieren als Dienstprinzipal durch Bereitstellen eines Zertifikats. Führen Sie die folgenden Befehle zum Erstellen einer Anwendung in Azure Active Directory aus.

1. Fügen Sie die folgenden Cmdlets im PowerShell-Konsolenfenster ein. Stellen Sie sicher, dass der Wert, den Sie für die **-DisplayName**-Eigenschaft angeben, eindeutig ist. Die Werte für **-HomePage** und **-IdentiferUris** sind Platzhalterwerte und werden nicht überprüft. 

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"
		
		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file
		
		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)
		
		$rawCertificateData = $certificatePFX.GetRawCertData()
		
		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" ` 
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. Erstellen Sie einen Dienstprinzipal, indem Sie die Anwendungs-ID verwenden.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId
		
		$objectId = $servicePrincipal.Id

3. Gewähren Sie dem Dienstprinzipal Zugriff auf den Data Lake-Speicher, den Sie bereits erstellt haben.
		
		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	Geben Sie an der Aufforderung **Y** ein, um dies zu bestätigen.

## Erstellen eines HDInsight-Clusters mit Authentifizierung für den Data Lake-Speicher

In diesem Abschnitt erstellen wir einen HDInsight Hadoop-Cluster. Für diese Version müssen sich der HDInsight-Cluster und der Data Lake-Speicher an demselben Standort (USA (Osten) 2) befinden.

1. Beginnen Sie, indem Sie die Mandanten-ID für das Abonnement abrufen. Diese benötigen Sie weiter unten.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. In dieser Version kann der Data Lake-Speicher für einen Hadoop-Cluster nur als zusätzlicher Speicher für den Cluster verwendet werden. Als Standardspeicher werden weiterhin Azure Storage-BLOBS (WASB) verwendet. Wir erstellen daher zuerst das Speicherkonto und die Speichercontainer, die für den Cluster erforderlich sind.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS
 
		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. Erstellen Sie den HDInsight-Cluster. Verwenden Sie die folgenden Cmdlets:

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential
		
		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	Nachdem das Cmdlet erfolgreich abgeschlossen wurde, sollte Folgendes ausgegeben werden:

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts : 

## Ausführen von Testaufträgen im HDInsight-Cluster zur Verwendung des Data Lake-Speichers

Nachdem Sie einen HDInsight-Cluster konfiguriert haben, können Sie Testaufträge für den Cluster ausführen, um zu testen, ob der HDInsight-Cluster auf Daten im Data Lake-Speicher zugreifen kann. Hierzu führen wir einen Hive-Beispielauftrag aus. Es wird eine Tabelle aus den Beispieldaten erstellt, die Sie zuvor in Ihren Data Lake-Speicher hochgeladen haben.

Verwenden Sie die folgenden Cmdlets, um die Hive-Abfrage auszuführen. In dieser Abfrage erstellen wir eine Tabelle aus den Daten im Data Lake-Speicher und führen für die erstellte Tabelle dann eine SELECT-Abfrage aus.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"
	
	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Dies führt zur folgenden Ausgabe. Der **ExitValue** von 0 in der Ausgabe weist darauf hin, dass der Auftrag erfolgreich abgeschlossen wurde.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

Rufen Sie die Ausgabe mit dem folgenden Cmdlet aus dem Auftrag ab:

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

Die Ausgabe sieht ungefähr so aus:

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


	

## Zugreifen auf den Data Lake-Speicher mit HDFS-Befehlen

Nachdem Sie den HDInsight-Cluster für die Verwendung des Data Lake-Speichers konfiguriert haben, können Sie die HDFS-Shellbefehle verwenden, um auf den Speicher zuzugreifen.

1. Melden Sie sich am neuen [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie auf **Durchsuchen**, **HDInsight-Cluster** und dann auf den HDInsight-Cluster, den Sie erstellt haben.

3. Klicken Sie auf dem Clusterblatt auf **Remotedesktop** und dann auf dem Blatt **Remotedesktop** auf **Verbinden**.

	![Remoteverbindung mit HDI-Cluster](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Azure-Ressourcengruppe erstellen")

	Geben Sie bei Aufforderung die Anmeldeinformationen ein, die Sie für den Remotedesktopbenutzer bereitgestellt haben.

4. Starten Sie in der Remotesitzung die Windows PowerShell, und verwenden Sie die HDFS-Dateisystembefehle, um die Dateien in Azure Data Lake aufzulisten.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Hierbei sollte auch die Datei aufgeführt werden, die Sie bereits in den Data Lake-Speicher hochgeladen haben.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	Sie können auch den Befehl `hdfs dfs -put` verwenden, um Dateien in Azure Data Lake hochzuladen, und dann mit `hdfs dfs -ls` überprüfen, ob der Upload der Dateien erfolgreich war.

## Weitere Informationen

* [Portal: Erstellen eines HDInsight-Clusters für die Verwendung des Data Lake-Speichers](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/de-DE/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/de-DE/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=Nov15_HO3-->