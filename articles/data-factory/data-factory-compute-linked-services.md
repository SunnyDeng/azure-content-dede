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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Verknüpfte Computedienste

In diesem Artikel werden verschiedene Compute-Umgebungen beschrieben, mit denen Sie Daten verarbeiten oder transformieren können. Darüber hinaus werden Einzelheiten zu verschiedenen Konfigurationen beschrieben (bedarfsgesteuerte Compute-Umgebung im Vergleich zu einer eigenen Compute-Umgebung). Diese beiden Konfigurationen werden von Data Factory unterstützt, wenn Sie verknüpfte Dienste konfigurieren, um diese Compute-Umgebungen mit Azure Data Factory zu verknüpfen.

## Bedarfsgesteuerte Compute-Umgebung

Bei dieser Konfiguration wird die Compute-Umgebung vollständig vom Azure Data Factory-Dienst verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Sie können einen verknüpften Dienst für die bedarfsgesteuerte Compute-Umgebung erstellen, diesen konfigurieren und differenzierte Einstellungen für Auftragsausführung, Clusterverwaltung und Bootstrappingaktionen festlegen.

> [AZURE.NOTE]Die bedarfsgesteuerte Konfiguration wird gegenwärtig nur für Azure HDInsight-Cluster unterstützt.

## Bedarfsgesteuerter verknüpfter Azure HDInsight-Dienst

Der bedarfsgesteuerte HDInsight-Cluster wird vom Azure Data Factory-Dienst automatisch zum Verarbeiten von Daten erstellt. Der Cluster wird in derselben Region erstellt wie das Speicherkonto (Eigenschaft "linkedServiceName" in JSON), das dem Cluster zugeordnet ist.

Beachten Sie die folgenden **wichtigen** Hinweise zum bedarfsgesteuerten verknüpften HDInsight-Dienst:

- Der bedarfsgesteuerte HDInsight-Cluster wird nicht in Ihrem Azure-Abonnement angezeigt, sondern vom Azure Data Factory-Dienst verwaltet.
- Die Protokolle für Aufträge, die in einem bedarfsgesteuerten HDInsight-Cluster ausgeführt werden, werden in das mit dem HDInsight-Cluster verknüpfte Speicherkonto kopiert. Der Zugriff auf diese Protokolle erfolgt über das Azure-Portal auf dem Blatt **Aktivitätsausführung – Details**. Einzelheiten finden Sie im Artikel [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md). 
- Ihnen wird nur die Zeit in Rechnung gestellt, in der der HDInsight-Cluster verfügbar ist und Aufträge ausführt.

> [AZURE.IMPORTANT]Die bedarfsgesteuerte Bereitstellung eines Azure HDInsight-Clusters dauert üblicherweise länger als **15 Minuten**.

### Beispiel

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### Eigenschaften

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Legen Sie die Typeigenschaft auf **HDInsightOnDemand** fest. | Ja
clusterSize | Die Größe des bedarfsgesteuerten Clusters. Geben Sie an, über wie viele Knoten dieser bedarfsgesteuerte Cluster verfügen soll. | Ja 
jobscontainer | Der Blobcontainer, in dem die von Pig-/Hive-/Paketaufträgen verwendeten Daten sowie die Clusterprotokolle gespeichert werden. | Ja
timetolive | <p>Die zulässige Leerlaufzeit für den bedarfsgesteuerten HDInsight-Cluster. Gibt an, wie lange der bedarfsgesteuerte HDInsight-Cluster nach Abschluss einer Aktivitätsausführung beibehalten wird, wenn der Cluster über keine weiteren aktiven Aufträge verfügt.</p><p>Wenn eine Aktivitätsausführung z. B. 6 Minuten dauert und "timetolive" auf 5 Minuten festgelegt ist, wird der Cluster nach den 6 Minuten, in denen die Aktivitätsausführung verarbeitet wird, weitere 5 Minuten beibehalten. Wenn innerhalb dieser 6 Minuten eine weitere Aktivitätsausführung verarbeitet wird, wird diese vom selben Cluster verarbeitet.</p><p>Da das Erstellen eines bedarfsgesteuerten HDInsight-Clusters ein kostenintensiver Vorgang ist, der mit einem recht hohen Zeitaufwand einhergehen kann, sollten Sie diese Einstellung gegebenenfalls nutzen, um die Data Factory-Leistung durch die Wiederverwendung eines bedarfsgesteuerten HDInsight-Clusters zu verbessern.</p><p>Wenn Sie den timetolive-Wert auf 0 festlegen, wird der Cluster gelöscht, sobald die Aktivitätsausführung verarbeitet wurde. Wenn Sie jedoch einen hohen Wert festlegen, wird der Cluster möglicherweise für einen zu langen Zeitraum im Leerlauf beibehalten, was hohe Kosten verursachen kann. Daher ist es wichtig, basierend auf Ihren individuellen Anforderungen einen geeigneten Wert festzulegen.</p><p>Wenn der Wert der Eigenschaft "timetolive" ordnungsgemäß festgelegt wird, können mehrere Pipelines dieselbe Instanz des bedarfsgesteuerten HDInsight-Clusters verwenden</p> | Ja
Version | Version des HDInsight-Clusters | Nein
linkedServiceName | Der Blobspeicher, den der bedarfsgesteuerte Cluster zum Speichern und Verarbeiten von Daten nutzt. | Ja
additionalLinkedServiceNames | Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese für Sie registrieren kann. | Nein

### Erweiterte Eigenschaften

Für eine präzisere Konfiguration des bedarfsgesteuerten HDInsight-Clusters können Sie die folgenden Eigenschaften festlegen.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
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
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

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
location | Geben Sie den Standort des HDInsight-Clusters an (z. B. "WestUS"). | Ja
linkedServiceName | Der Name des verknüpften Diensts für den Blobspeicher, der von diesem HDInsight-Cluster verwendet wird. | Ja

## Verknüpfter Azure Batch-Dienst

Sie können einen verknüpften Azure Batch-Dienst erstellen, um einen Batch-Pool mit virtuellen Computern für Data Factory zu registrieren. Benutzerdefinierte .NET-Aktivitäten können mit Azure Batch oder Azure HDInsight ausgeführt werden.

Lesen Sie die folgenden Themen, wenn Sie noch nicht mit dem Azure Batch-Dienst vertraut sind:
 

- Unter [Azure Batch – Technische Übersicht](../batch/batch-technical-overview.md) finden Sie eine Übersicht über den Azure Batch-Dienst.
- Erstellen Sie ein Azure Batch-Konto mit dem Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) oder über das [Azure-Verwaltungsportal](../batch/batch-technical-overview.md). Im Blog [Using Azure PowerShell to Manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) ist die Verwendung dieses Cmdlets im Detail beschrieben.
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

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

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


## Mit Azure SQL verknüpfter Dienst

Sie erstellen einen mit Azure SQL verknüpften Dienst und verwenden ihn mit der [Aktivität "Gespeicherte Prozedur"](data-factory-stored-proc-activity.md) zum Aufrufen einer gespeicherten Prozedur in einer Data Factory-Pipeline. Im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) finden Sie weitere Informationen zu diesem verknüpften Dienst.


  



     
 
   

<!---HONumber=Oct15_HO3-->