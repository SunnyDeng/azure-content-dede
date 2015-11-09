<properties
   pageTitle="Integrieren von Data Lake-Speicher in andere Azure-Dienste | Azure"
   description="Erfahren Sie, wie Sie Data Lake-Speicher in andere Azure-Dienste integrieren."
   documentationCenter=""
   services="data-lake-store" 
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Integrieren von Data Lake-Speicher in andere Azure-Dienste

Azure Data Lake-Speicher kann zusammen mit anderen Azure-Diensten verwendet werden, um mehr Szenarios zu ermöglichen. Im folgende Artikel werden die Dienste aufgeführt, in die Data Lake-Speicher integriert werden kann.

## Verwenden von Data Lake-Speicher mit Azure HDInsight

Sie können einen [Azure HDInsight](https://azure.microsoft.com/de-DE/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)-Cluster bereitstellen, der Data Lake-Speicher als HDFS-kompatiblen Speicher verwendet. In dieser Version können Sie für Hadoop- und Storm-Cluster unter Windows und Linux Data Lake-Speicher nur als Zusatzspeicher verwenden. Solche Cluster verwenden weiterhin Azure Storage (WASB) als Standardspeicher. Sie können jedoch für HBase-Cluster unter Windows und Linux Data Lake-Speicher als Standardspeicher oder als Zusatzspeicher verwenden.

Anleitungen zum Bereitstellen eines HDInsight-Clusters mit Data Lake-Speicher finden Sie unter:

* [Bereitstellen eines HDInsight-Clusters mit Data Lake-Speicher mithilfe des Azure-Vorschauportals](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Bereitstellen eines HDInsight-Clusters mit Data Lake-Speicher mithilfe von Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


## Verwenden von Data Lake-Speicher mit Azure Data Lake Analytics

[Azure Data Lake Analytics](data-lake-analytics/data-lake-analytics-overview.md) ermöglicht das Arbeiten mit Big Data in der Cloud. Es stellt Ressourcen dynamisch bereit und ermöglicht Ihnen die Durchführung von Analysen von Terabytes oder sogar Exabytes an Daten, die in einer Reihe von unterstützten Datenquellen gespeichert sein können, auch in Data Lake-Speicher. Data Lake-Analytics ist speziell für die Arbeit mit Azure Data Lake-Speicher optimiert – so erzielen Sie Höchstwerte bei Leistung, Durchsatz und Parallelisierung bei Big Data-Workloads.

Anleitung zum Verwenden von Data Lake Analytics mit Data Lake-Speicher finden Sie unter [Erste Schritte mit Data Lake Analytics unter Verwendung von Data Lake-Speicher](data-lake-analytics/data-lake-analytics-get-started-portal.md).


## Verwenden von Data Lake-Speicher mit Azure Data Factory

Sie können [Azure Data Factory](https://azure.microsoft.com/de-DE/services/data-factory/) zum Erfassen von Daten aus Azure-Tabellen, Azure SQL-Datenbank, Azure SQL Data Warehouse, Azure Storage-Blobs und lokalen Datenbanken verwenden. Als wichtige Komponente im Azure-Ökosystem kann Azure Data Factory auch zum Orchestrieren der Erfassung von Daten aus diesen Quelle in Azure Data Lake-Speicher verwendet werden.

Anleitungen zum Verwenden von Azure Data Factory mit Data Lake-Speicher finden Sie unter [Verschieben von Daten in und aus Data Lake-Speicher mit Data Factory](data-factory/data-factory-azure-datalake-connector.md).


## Weitere Informationen

- [Übersicht über Azure Data Lake-Speicher](data-lake-store-overview.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe des Portals](data-lake-store-get-started-portal.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe von PowerShell](data-lake-store-get-started-powershell.md)  

<!---HONumber=Nov15_HO1-->