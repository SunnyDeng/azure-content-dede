<properties 
   pageTitle="Übersicht über Azure Data Lake-Speicher | Azure" 
   description="Erfahren Sie, was Azure Data Lake-Speicher ist und welche Vorteile er im Vergleich zu anderen Datenspeichern bietet." 
   services="data-lake-store" 
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
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Übersicht über Azure Data Lake-Speicher

Azure Data Lake-Speicher ist ein unternehmensweites riesiges Repository für Big Data-Analyseworkloads. Azure Data Lake bietet Ihnen die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort zu erfassen.

> [AZURE.TIP]Verwenden Sie den [Lernpfad für Data Lake-Speicher](https://azure.microsoft.com/de-DE/documentation/learning-paths/data-lake-store-self-guided-training/), um den Azure Data Lake-Speicherdienst zu erkunden.

Auf Azure Data Lake-Speicher kann über Hadoop (verfügbar mit HDInsight-Clustern) mithilfe der WebHDFS-kompatiblen REST-APIs zugegriffen werden. Er wurde speziell für Analysen der gespeicherten Daten konzipiert und für Datenanalyseszenarien leistungsoptimiert. Er enthält standardmäßig all die Funktionen auf Unternehmensniveau, die für reale Anwendungsfälle von Unternehmen erforderlich sind – Sicherheit, Verwaltbarkeit, Skalierbarkeit, Zuverlässigkeit und Verfügbarkeit.


![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Azure Data Lake bietet u. a. die folgenden wichtigen Funktionen.

### Konzipiert für Hadoop

Beim Azure Data Lake-Speicher handelt es sich um ein Apache Hadoop-Dateisystem, das mit HDFS (Hadoop Distributed File System) kompatibel ist und mit dem Hadoop-Ökosystem eingesetzt werden kann. Ihre vorhandenen HDInsight-Anwendungen oder -Dienste, die die WebHDFS-API verwenden, können problemlos in Data Lake-Speicher integriert werden. Data Lake-Speicher macht auch eine WebHDFS-kompatible REST-Schnittstelle für Anwendungen verfügbar.

In Data Lake-Speicher gespeicherte Daten können mühelos mit Hadoop-Analyseframeworks wie MapReduce oder Hive analysiert werden. Microsoft Azure HDInsight-Cluster können für den direkten Zugriff auf in Data Lake-Speicher gespeicherte Daten bereitgestellt und konfiguriert werden.

### Unbegrenzter Speicher, Dateigrößen bis in den Petabytebereich

Azure Data Lake-Speicher bietet unbegrenzten Speicher und eignet sich zum Speichern unterschiedlichster Daten für Analysezwecke. Es gelten keinerlei Einschränkungen für Kontogrößen, Dateigrößen oder die Menge an Daten, die in einem Data Lake gespeichert werden kann. Data Lake-Speicher unterstützt Dateigrößen vom Kilobyte- bis in den Petabytebereich und ist somit eine gute Wahl für die Speicherung von Daten jeglicher Art. Daten werden dauerhaft gespeichert, indem mehrere Kopien erstellt werden, und können für unbegrenzte Zeit im Data Lake gespeichert werden.

### Leistungsoptimiert für Big Data-Analysen

Azure Data Lake-Speicher ist für die Ausführung großer Analysesysteme ausgelegt, die zum Abfragen und Analysieren großer Datenmengen einen enormen Durchsatz erfordern. Der Data Lake verteilt Teile einer Datei auf mehrere einzelne Speicherserver. Dies verbessert den Lesedurchsatz, wenn die Datei zum Ausführen von Datenanalysen parallel gelesen wird.


### Geeignet für Unternehmen: Hoch verfügbar und sicher

Azure Data Lake-Speicher bietet Verfügbarkeit und Zuverlässigkeit nach Industriestandard. Ihre Datenassets werden dauerhaft gespeichert, indem zum Schutz vor unerwarteten Fehlern redundante Kopien erstellt werden. Unternehmen können Azure Data Lake in ihren Lösungen als wesentlichen Bestandteil ihrer vorhandenen Datenplattform einsetzen.

Data Lake-Speicher bietet außerdem Sicherheit auf Unternehmensniveau für die gespeicherten Daten. Weitere Informationen finden Sie unter [Sichern von Daten in Azure Data Lake-Store](#DataLakeStoreSecurity).


### Alle Daten

Azure Data Lake-Speicher kann beliebige Daten ohne vorherige Transformation im systemeigenen Format speichern. Bei Data Lake-Speicher ist es nicht erforderlich, vor dem Laden der Daten ein Schema zu definieren. Die Interpretation der Daten und Definition eines Schemas erfolgt zum Zeitpunkt der Analyse durch die einzelnen Analyseframeworks. Dank der Möglichkeit, Dateien beliebiger Größe und Formate zu speichern, kann Data Lake-Speicher strukturierte, teilweise strukturierte und unstrukturierte Daten verwalten.


## <a name="DataLakeStoreSecurity"></a>Sichern von Daten in Azure Data Lake-Speicher

Azure Data Lake-Speicher verwendet Azure Active Directory zur Authentifizierung und Zugriffssteuerungslisten (ACLs) zum Verwalten des Zugriffs auf Ihre Daten.

| Funktion | Beschreibung |
|-----------------------------------------|------------------------------------------|
| Authentifizierung | Zur Identitäts- und Zugriffsverwaltung für alle in Azure Data Lake-Speicher gespeicherten Daten ist Azure Data Lake-Speicher in Azure Active Directory (AAD) integriert. Dank dieser Integration nutzt Azure Data Lake sämtliche AAD-Funktionen, z. B. Multi-Factor Authentication, bedingter Zugriff, rollenbasierte Zugriffssteuerung, Überwachung der Anwendungsnutzung, Sicherheitsüberwachung und -warnungen usw. Azure Data Lake-Speicher unterstützt das OAuth 2.0-Protokoll für die Authentifizierung mit der REST-Schnittstelle. |
| Zugriffssteuerung | Azure Data Lake-Speicher ermöglicht eine Zugriffssteuerung durch die Unterstützung von POSIX-Berechtigungen, die vom WebHDFS-Protokoll verfügbar gemacht werden. Berechtigungen können in der aktuellen Version auf der Data Lake-Ebene angegeben werden und gelten für alle Dateien und Ordner im Data Lake. Zukünftige Updates werden eine detaillierte Zugriffssteuerung ermöglichen, bei der Berechtigungen für einzelne Dateien und Ordner angegeben werden können.|

Anweisungen zum Sichern von Daten in Data Lake-Speicher finden Sie unter [Sichern von Daten in Azure Data Lake-Speicher](data-lake-store-secure-data.md).

## Mit Azure Data Lake-Speicher kompatible Anwendungen

Eine Liste der mit Azure Data Lake-Speicher interoperablen Open-Source-Anwendungen finden Sie unter [Mit Azure Data Lake-Speicher kompatible Anwendungen und Dienste](data-lake-store-compatible-oss-other-applications.md). Informationen dazu, wie Data Lake-Speicher mit anderen Azure-Diensten verwendet werden kann, um ein breiteres Spektrum von Szenarien zu ermöglichen, finden Sie unter [Integration in andere Azure-Dienste](data-lake-store-integrate-with-other-services.md).

## Was ist das Azure Data Lake-Speicherdateisystem (adl://)?

In Hadoop-Umgebungen (verfügbar mit HDInsight-Clustern) kann über das neue Dateisystem „AzureDataLakeFilesystem“ (adl://) auf Data Lake-Speicher zugegriffen werden. Anwendungen und Dienste, die adl:// verwenden, können weitere Leistungsoptimierungen nutzen, die gegenwärtig nicht in WebHDFS verfügbar sind. Daher bietet Ihnen Data Lake-Speicher die Flexibilität, entweder mit adl:// (empfohlene Option) die optimale Leistung zu nutzen oder vorhandenen Code beizubehalten, indem Sie die WebHDFS-API weiterhin direkt verwenden. Azure HDInsight schöpft die Möglichkeiten des „AzureDataLakeFilesystem“ voll aus, um eine optimale Leistung für Data Lake-Speicher bereitzustellen.

Sie können mithilfe von `adl://<data_lake_store_name>.azuredatalakestore.net` auf Ihre Daten im Data Lake-Speicher zugreifen. Weitere Informationen zum Zugriff auf die Daten im Data Lake-Speicher finden Sie unter [Anzeigen der Eigenschaften der gespeicherten Daten](data-lake-store-get-started-portal.md#properties).

## Wie verwende ich Azure Data Lake-Speicher?

Informationen zur Bereitstellung eines Data Lake-Speichers über das Azure-Vorschauportal finden Sie unter [Erste Schritte mit Data Lake-Speicher mithilfe des Azure-Vorschauportals](data-lake-store-get-started-portal.md). Nachdem Sie Azure Data Lake bereitgestellt haben, können Sie sich darüber informieren, wie Big Data-Angebote wie Azure Data Lake Analytics oder Azure HDInsight mit Data Lake-Speicher verwendet werden. Sie können auch eine .NET-Anwendung zum Erstellen eines Azure Data Lake-Speicherkontos erstellen und Vorgänge wie das Hoch- und Herunterladen von Daten usw. durchführen.

- [Erste Schritte mit Azure Data Lake Analytics](data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Erste Schritte mit Azure Data Lake-Speicher mithilfe des .NET SDKs](data-lake-store-get-started-net-sdk.md)
  

<!---HONumber=Nov15_HO1-->