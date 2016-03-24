<properties
	pageTitle="Verknüpfte Computedienste | Microsoft Azure"
	description="Erfahren Sie mehr über Compute-Umgebungen, die in Azure Data Factory-Pipelines für die Transformation und Verarbeitung von Daten verwendet werden können."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="spelluru"/>

# Verknüpfte Computedienste

In diesem Artikel werden verschiedene Compute-Umgebungen beschrieben, mit denen Sie Daten verarbeiten oder transformieren können. Darüber hinaus werden Einzelheiten zu verschiedenen Konfigurationen beschrieben (bedarfsgesteuerte Compute-Umgebung im Vergleich zu einer eigenen Compute-Umgebung). Diese beiden Konfigurationen werden von Data Factory unterstützt, wenn Sie verknüpfte Dienste konfigurieren, um diese Compute-Umgebungen mit Azure Data Factory zu verknüpfen.

## Bedarfsgesteuerte Compute-Umgebung

Bei dieser Konfiguration wird die Compute-Umgebung vollständig vom Azure Data Factory-Dienst verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Sie können einen verknüpften Dienst für die bedarfsgesteuerte Compute-Umgebung erstellen, diesen konfigurieren und differenzierte Einstellungen für Auftragsausführung, Clusterverwaltung und Bootstrappingaktionen festlegen.

> [AZURE.NOTE] Die bedarfsgesteuerte Konfiguration wird gegenwärtig nur für Azure HDInsight-Cluster unterstützt.

## Bedarfsgesteuerter verknüpfter Azure HDInsight-Dienst
Der Azure Data Factory-Dienst kann zum Verarbeiten von Daten automatisch einen bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster erstellen. Der Cluster wird in derselben Region erstellt wie das Speicherkonto (Eigenschaft "linkedServiceName" in JSON), das dem Cluster zugeordnet ist.

Beachten Sie die folgenden **wichtigen** Hinweise zum bedarfsgesteuerten verknüpften HDInsight-Dienst:

- Der bedarfsgesteuerte HDInsight-Cluster wird nicht in Ihrem Azure-Abonnement angezeigt, sondern vom Azure Data Factory-Dienst verwaltet.
- Die Protokolle für Aufträge, die in einem bedarfsgesteuerten HDInsight-Cluster ausgeführt werden, werden in das mit dem HDInsight-Cluster verknüpfte Speicherkonto kopiert. Der Zugriff auf diese Protokolle erfolgt über das klassische Azure-Portal auf dem Blatt **Aktivitätsausführung – Details**. Einzelheiten finden Sie im Artikel [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md).
- Ihnen wird nur die Zeit in Rechnung gestellt, in der der HDInsight-Cluster verfügbar ist und Aufträge ausführt.

> [AZURE.IMPORTANT] Die bedarfsgesteuerte Bereitstellung eines Azure HDInsight-Clusters dauert üblicherweise länger als **15 Minuten**.

### Beispiel
Die folgende JSON definiert einen bedarfsgesteuerten mit HDInsight verknüpften Dienst. Die Data Factory erstellt bei der Verarbeitung eines Datenslices automatisch einen **Windows-basierten** HDInsight-Cluster. Beachten Sie, dass **osType** in dieser Beispiel-JSON nicht angegeben und der Standardwert für diese Eigenschaft **Windows** ist.

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "version": "3.2",
	      "clusterSize": 1,
	      "timeToLive": "00:30:00",
	      "linkedServiceName": "StorageLinkedService"
	    }
	  }
	}


Die folgende JSON definiert einen bedarfsgesteuerten Linux-basierten mit HDInsight verknüpften Dienst. Der Data Factory-Dienst erstellt bei der Verarbeitung eines Datenslices automatisch einen **Linux-basierten** HDInsight-Cluster. Sie müssen Werte für **sshUserName** und **sshPassword** angeben.


	{
	    "name": "HDInsightOnDemandLinkedService",
	    "properties": {
	        "hubName": "getstarteddf0121_hub",
	        "type": "HDInsightOnDemand",
	        "typeProperties": {
	            "version": "3.2",
	            "clusterSize": 4,
	            "timeToLive": "00:05:00",
	            "osType": "linux",
	            "sshPassword": "MyPassword!",
	            "sshUserName": "myuser",
	            "linkedServiceName": "StorageLinkedService",
	        }
	    }
	}

> [AZURE.IMPORTANT] 
Der HDInsight-Cluster erstellt einen **Standardcontainer** im Blobspeicher, den Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dies ist beabsichtigt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss – es sei denn, ein aktiver Cluster (**timeToLive**) ist vorhanden und wird gelöscht, nachdem die Verarbeitung abgeschlossen ist.
> 
> Wenn immer mehr Slices verarbeitet werden, enthält Azure Blob Storage viele Container. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Der Name dieser Container basiert auf dem folgenden Muster: adf**ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel. Verwenden Sie Tools wie [Microsoft Storage-Explorer](http://storageexplorer.com/), um Container in Ihrer Azure Blob Storage-Instanz zu löschen.

### Eigenschaften

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
type | Legen Sie die Typeigenschaft auf **HDInsightOnDemand** fest. | Ja
clusterSize | Die Größe des bedarfsgesteuerten Clusters. Geben Sie an, über wie viele Knoten dieser bedarfsgesteuerte Cluster verfügen soll. | Ja
timetolive | Die zulässige Leerlaufzeit für den bedarfsgesteuerten HDInsight-Cluster. Gibt an, wie lange der bedarfsgesteuerte HDInsight-Cluster nach Abschluss einer Aktivitätsausführung beibehalten wird, wenn der Cluster über keine weiteren aktiven Aufträge verfügt.<br/><br/>Wenn eine Aktivitätsausführung z. B. 6 Minuten dauert und "timetolive" auf 5 Minuten festgelegt ist, wird der Cluster nach den 6 Minuten, in denen die Aktivitätsausführung verarbeitet wird, weitere 5 Minuten beibehalten. Wenn innerhalb dieser 6 Minuten eine weitere Aktivitätsausführung verarbeitet wird, wird diese vom selben Cluster verarbeitet.<br/><br/>Da das Erstellen eines bedarfsgesteuerten HDInsight-Clusters ein kostenintensiver Vorgang ist, der mit einem recht hohen Zeitaufwand einhergehen kann, sollten Sie diese Einstellung gegebenenfalls nutzen, um die Data Factory-Leistung durch die Wiederverwendung eines bedarfsgesteuerten HDInsight-Clusters zu verbessern.<br/><br/>Wenn Sie den timetolive-Wert auf 0 festlegen, wird der Cluster gelöscht, sobald die Aktivitätsausführung verarbeitet wurde. Wenn Sie jedoch einen hohen Wert festlegen, wird der Cluster möglicherweise für einen zu langen Zeitraum im Leerlauf beibehalten, was hohe Kosten verursachen kann. Daher ist es wichtig, basierend auf Ihren individuellen Anforderungen einen geeigneten Wert festzulegen.<br/><br/>Wenn der Wert der Eigenschaft "timetolive" ordnungsgemäß festgelegt wird, können mehrere Pipelines dieselbe Instanz des bedarfsgesteuerten HDInsight-Clusters verwenden. | Ja
Version | Version des HDInsight-Clusters. Der Standardwert ist 3.1 für Windows-Cluster und 3.2 für Linux-Cluster. | Nein
linkedServiceName | Der Blobspeicher, den der bedarfsgesteuerte Cluster zum Speichern und Verarbeiten von Daten nutzt. | Ja
additionalLinkedServiceNames | Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese für Sie registrieren kann. | Nein
osType | Typ des Betriebssystems. Zulässige Werte sind: „Windows“ (Standard) und „Linux“. | Nein
hcatalogLinkedServiceName | Der Name des mit Azure SQL verknüpften Diensts, der auf die HCatalog-Datenbank verweist. Der bedarfsgesteuerte HDInsight-Cluster wird mit der Azure SQL-Datenbank als Metastore erstellt. | Nein
sshUser | SSH-Benutzer für den Linux-basierten HDInsight-Cluster | Ja (nur für Linux)
sshPassword | SSH-Kennwort für den Linux-basierten HDInsight-Cluster | Ja (nur für Linux)


#### additionalLinkedServiceNames (JSON-Beispiel)

    "additionalLinkedServiceNames": [
        "otherLinkedServiceName1",
		"otherLinkedServiceName2"
  	]
 
### Erweiterte Eigenschaften

Für eine präzisere Konfiguration des bedarfsgesteuerten HDInsight-Clusters können Sie die folgenden Eigenschaften festlegen.

Eigenschaft | Beschreibung | Erforderlich
:-------- | :----------- | :--------
coreConfiguration | Gibt die wichtigsten Konfigurationsparameter (wie in "core-site.xml") für den HDInsight-Cluster an, der erstellt werden soll. | Nein
hBaseConfiguration | Gibt die HBase-Konfigurationsparameter (hbase-site.xml) für den HDInsight-Cluster an. | Nein
hdfsConfiguration | Gibt die HDFS-Konfigurationsparameter (hdfs-site.xml) für den HDInsight-Cluster an. | Nein
hiveConfiguration | Gibt die Hive-Konfigurationsparameter (hive-site.xml) für den HDInsight-Cluster an. | Nein
mapReduceConfiguration | Gibt die MapReduce-Konfigurationsparameter (mapred-site.xml) für den HDInsight-Cluster an. | Nein
oozieConfiguration | Gibt die Oozie-Konfigurationsparameter (oozie-site.xml) für den HDInsight-Cluster an. | Nein
stormConfiguration | Gibt die Storm-Konfigurationsparameter (storm-site.xml) für den HDInsight-Cluster an. | Nein
yarnConfiguration | Gibt die Yarn-Konfigurationsparameter (yarn-site.xml) für den HDInsight-Cluster an. | Nein

#### Beispiel: Konfiguration eines bedarfsgesteuerten HDInsight-Clusters mit erweiterten Eigenschaften

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "timeToLive": "01:30:00",
	      "version": "3.2",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx4000m",
	        "mapreduce.map.java.opts": "-Xmx4000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000",
	        "mapreduce.map.memory.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

### Knotengrößen
Sie können die Größe der Head-, Daten- und Zookeeper-Knoten mit den folgenden Eigenschaften angeben.

Eigenschaft | Beschreibung | Erforderlich
:-------- | :----------- | :--------
headNodeSize | Gibt die Größe des Hauptknotens an. Der Standardwert lautet „groß“. Details finden Sie unten im Abschnitt **Knotengrößen angeben**. | Nein
dataNodeSize | Gibt die Größe des Datenknotens an. Der Standardwert lautet „groß“. | Nein
zookeeperNodeSize | Gibt die Größe des Zoo Keeper-Knotens an. Der Standardwert lautet „klein“. | Nein
 
#### Knotengrößen angeben
Lesen Sie den Artikel [Größen von virtuellen Computern](../virtual-machines/virtual-machines-size-specs.md#size-tables), um Näheres zu Zeichenfolgenwerten zu erfahren, die Sie für die oben aufgeführten Eigenschaften angeben müssen. Die Werte müssen den **CMDLETs und APIs** entsprechen, auf die im Artikel verwiesen wird. Wie Sie in diesem Artikel sehen können, hat der Datenknoten „Large“ (Standard) 7 GB Arbeitsspeicher, was für Ihr Szenario möglicherweise nicht ausreichend ist.

Wenn Sie Hauptknoten der Größe D4 und Workerknoten erstellen möchten, müssen Sie **Standard\_D4** als Wert für die Eigenschaften headNodeSize und dataNodeSize angeben.

	"headNodeSize": "Standard_D4",	
	"dataNodeSize": "Standard_D4",

Wenn Sie einen falschen Wert für diese Eigenschaften angeben, erhalten Sie möglicherweise den folgenden **Fehler:** Fehler beim Erstellen des Clusters. Ausnahme: Vorgang der Clustererstellung kann nicht abgeschlossen werden. Vorgang mit Code "400" fehlgeschlagen. Cluster hinterließ folgenden Status: "Fehler". Nachricht: "PreClusterCreationValidationFailure" Wenn Sie diesen Fehler erhalten, achten Sie darauf, dass Sie den Namen der **Cmdlets und APIs** aus der Tabelle im oben genannten Artikel verwenden.



## Eigene Compute-Umgebung

Bei dieser Konfiguration können Benutzer eine bereits vorhandene Compute-Umgebung als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird vom Benutzer verwaltet und von Data Factory zum Ausführen von Aktivitäten verwendet.

Diese Art von Konfiguration wird für die folgenden Compute-Umgebungen unterstützt:

- Azure HDInsight
- Azure Batch
- Azure Machine Learning.

## Verknüpfter Azure HDInsight-Dienst

Sie können einen verknüpften Azure HDInsight-Dienst erstellen, um Ihren eigenen HDInsight-Cluster für Data Factory zu registrieren.

### Beispiel

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### Eigenschaften

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Legen Sie die Typeigenschaft auf **HDInsight** fest. | Ja
clusterUri | Der URI des HDInsight-Clusters. | Ja
username | Geben Sie den Namen des Benutzers ein, der mit einem vorhandenen HDInsight-Cluster verbunden werden soll. | Ja
password | Geben Sie ein Kennwort für das Benutzerkonto an. | Ja
location | Geben Sie den Standort des HDInsight-Clusters an (z. B. "WestUS"). | Ja
linkedServiceName | Der Name des verknüpften Diensts für den Blobspeicher, der von diesem HDInsight-Cluster verwendet wird. | Ja

## Verknüpfter Azure Batch-Dienst

Sie können einen verknüpften Azure Batch-Dienst erstellen, um einen Batch-Pool mit virtuellen Computern für Data Factory zu registrieren. Benutzerdefinierte .NET-Aktivitäten können mit Azure Batch oder Azure HDInsight ausgeführt werden.

Lesen Sie die folgenden Themen, wenn Sie noch nicht mit dem Azure Batch-Dienst vertraut sind:


- Unter [Azure Batch – Grundlagen](../batch/batch-technical-overview.md) finden Sie eine Übersicht über den Azure Batch-Dienst.
- Erstellen Sie ein Azure Batch-Konto mit dem Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) oder über das [klassische Azure-Portal](../batch/batch-account-create-portal.md). Im Blog [Using Azure PowerShell to Manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) ist die Verwendung dieses Cmdlets im Detail beschrieben.
- Verwenden Sie das Cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx), um einen Azure Batch-Pool zu erstellen.

### Beispiel

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

Erweitern Sie den Namen Ihres Batch-Kontos für die Eigenschaft **accountName** um "**.<Name der Region**". Beispiel:

			"accountName": "mybatchaccount.eastus"

Außerdem kann auch der batchUri-Endpunkt angegeben werden, wie nachfolgend gezeigt.

			"accountName": "adfteam",
			"batchUri": "https://eastus.batch.azure.com",

### Eigenschaften

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Legen Sie die Typeigenschaft auf **AzureBatch** fest. | Ja
accountName | Der Name des Azure Batch-Kontos. | Ja
accessKey | Der Zugriffsschlüssel für das Azure Batch-Konto. | Ja
poolName | Der Name des Pools mit virtuellen Computern. | Ja
linkedServiceName | Der Name des verknüpften Azure Storage-Diensts, der diesem verknüpften Azure Batch-Dienst zugeordnet ist. Dieser verknüpfte Dienst wird für Stagingdateien verwendet, die für die Ausführung der Aktivität und zum Speichern der Protokolle zur Aktivitätsausführung benötigt werden. | Ja


## Mit Azure Machine Learning verknüpfter Dienst

Sie können einen verknüpften Azure Machine Learning-Dienst erstellen, um einen Machine Learning-Batchbewertungsendpunkt für Data Factory zu registrieren.

### Beispiel

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### Eigenschaften

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Legen Sie die Typeigenschaft auf **AzureML** fest. | Ja
mlEndpoint | Die Batchbewertungs-URL. | Ja
apiKey | Die veröffentlichte API des Arbeitsbereichsmodells. | Ja


## Mit Azure Data Lake Analytics verknüpfter Dienst
Sie erstellen einen mit **Azure Data Lake Analytics** verknüpften Dienst, um einen Azure Data Lake Analytics-Computedienst mit einer Azure Data Factory zu verknüpfen, bevor Sie die [Data Lake Analytics-U-SQL-Aktivität](data-factory-usql-activity.md) in einer Pipeline verwenden.

Das folgende Beispiel enthält eine JSON-Definition für einen mit Azure Data Lake Analytics verknüpften Dienst.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>",
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der JSON-Definition verwendet werden.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Legen Sie die Typeigenschaft auf **AzureDataLakeAnalytics** fest. | Ja
accountName | Name des Azure Data Lake Analytics-Kontos. | Ja
dataLakeAnalyticsUri | URI des Azure Data Lake Analytics-Kontos. | Nein
authorization | Der Autorisierungscode wird automatisch abgerufen, nachdem Sie im Data Factory-Editor auf die Schaltfläche **Autorisieren** geklickt und die OAuth-Anmeldung abgeschlossen haben. | Ja
subscriptionId | Azure-Abonnement-ID | Nein (falls nicht angegeben, wird das Abonnement der Data Factory verwendet).
ResourceGroupName | Azure-Ressourcengruppenname | Nein (falls nicht angegeben, wird die Ressourcengruppe der Data Factory verwendet).
sessionId | Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Sie wird im Data Factory-Editor automatisch generiert. | Ja

Der von Ihnen mithilfe der Schaltfläche **Autorisieren** generierte Autorisierungscode läuft nach einer gewissen Zeit ab. Die Zeiten bis zum Ablaufen der Autorisierungscodes für die verschiedenen Benutzerkonten finden Sie in der folgenden Tabelle. Unter Umständen wird nach **Ablauf des Authentifizierungstokens** folgende Fehlermeldung angezeigt: „Fehler beim Anmeldevorgang: invalid\_grant – AADSTS70002: Fehler beim Überprüfen der Anmeldeinformationen. AADSTS70008: Die angegebene Zugriffserteilung ist abgelaufen oder wurde widerrufen. Ablaufverfolgungs-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Zeitstempel: 2015-12-15 21:09:31Z“

| Benutzertyp | Läuft ab nach |
| :-------- | :----------- | 
| Benutzerkonten, die NICHT von Azure Active Directory verwaltet werden (@hotmail.com, @live.com usw.) | 12 Stunden |
| Benutzerkonten, die von Azure Active Directory (AAD) verwaltet werden | 14 Tage nach der letzten Sliceausführung. <br/><br/>90 Tage, wenn ein Slice, das auf einem verknüpften OAuth-Dienst basiert, mindestens einmal alle 14 Tage ausgeführt wird. |
 
Um diesen Fehler zu vermeiden/beheben, müssen Sie sich durch Klicken auf die Schaltfläche **Autorisieren** erneut autorisieren, wenn das **Token abläuft**, und den verknüpften Dienst anschließend erneut bereitstellen. Sie können auch programmgesteuert Werte für die Eigenschaften „sessionId“ und „authorization“ generieren. Verwenden Sie dazu den im folgenden Abschnitt bereitgestellten Code.

### So generieren Sie programmgesteuert Werte für „sessionId“ und „authorization“ 
Der folgende Code generiert Werte für **sessionId** und **authorization**.

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Nähere Informationen zu den im Code verwendeten Data Factory-Klassen finden Sie in den Themen [AzureDataLakeStoreLinkedService-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) und [AuthorizationSessionGetResponse-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Sie müssen für die WindowsFormsWebAuthenticationDialog-Klasse einen Verweis hinzufügen auf: „Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll“.
 

## Mit Azure SQL verknüpfter Dienst

Sie erstellen einen mit Azure SQL verknüpften Dienst und verwenden ihn mit der [Aktivität "Gespeicherte Prozedur"](data-factory-stored-proc-activity.md) zum Aufrufen einer gespeicherten Prozedur in einer Data Factory-Pipeline. Im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) finden Sie weitere Informationen zu diesem verknüpften Dienst.

<!---HONumber=AcomDC_0309_2016-->