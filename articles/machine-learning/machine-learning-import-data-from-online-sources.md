<properties
	pageTitle="Importieren von Daten in Machine Learning Studio aus Onlinedatenquellen | Microsoft Azure"
	description="Informationen zum Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio."
	keywords="Importieren von Daten,Datenformat,Datentypen,Datenquellen,Trainingsdaten"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="bradsev;garye;gopitk" />


# Importieren von Trainingsdaten aus verschiedenen Onlinedatenquellen mit dem "Reader"-Modul in Azure Machine Learning Studio

In diesem Dokument werden die Unterstützung des Importierens von Onlinedaten aus verschiedenen Datenquellen und die erforderlichen Informationen zum Verschieben von Daten aus diesen Quellen in ein Azure Machine Learning-Experiment beschrieben.

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## Einführung

Sie können in Azure Machine Learning Studio auf Daten aus einer von mehreren Onlinedatenquellen zugreifen, während Ihr Experiment mithilfe des [Reader][reader]-Moduls ausgeführt wird:

- Web-URL über HTTP
- Hadoop über HiveQL
- Azure-Blobspeicher
- Azure-Tabelle
- Azure SQL-Datenbank oder SQL Server in Azure VM
- Datenfeedanbieter (derzeit OData)
 

Der Workflow für Ihre Experimente in Azure Machine Learning Studio besteht aus dem Ziehen und Ablegen von Komponenten auf die Canvas. Für den Zugriff auf Onlinedatenquellen fügen Sie Ihrem Experiment das [Reader][reader]-Modul hinzu, wählen **Data source** aus und geben dann die für den Zugriff auf die Daten erforderlichen Parameter an. In der folgenden Tabelle werden die unterstützten Onlinedatenquellen beschrieben. Diese Tabelle enthält auch die unterstützten Dateiformate und die Parameter für den Zugriff auf die Daten.

> [AZURE.NOTE] Dieser Artikel bietet allgemeine Informationen über das [Reader][reader]-Modul. Ausführlichere Informationen zu den Datentypen, auf die Sie zugreifen können, zu Formaten, Parametern und Antworten auf häufige Fragen finden Sie im Modulreferenzthema zum [Reader][reader]-Modul.

> [AZURE.NOTE] Da auf diese Trainingsdaten zugegriffen wird, während das Experiment läuft, stehen sie nur in diesem Experiment zur Verfügung. Im Vergleich dazu stehen Daten, die in Datasetmodulen gespeichert sind, allen Experimente in Ihrem Arbeitsbereich zur Verfügung.


## Unterstützte Onlinedatenquellen
Das **Reader**-Modul von Azure Machine Learning unterstützt die folgenden Datenquellen:

Datenquelle | Beschreibung | Parameter |
---|---|---|
Web URL via HTTP |Liest Daten im CSV-Format (mit Trennzeichen getrennte Werte), im TSV-Format (mit Tabulatoren getrennte Werte), im ARFF-Format (Attribute-Relation File Format) und SVM-light-Format (Support Vector Machines) aus allen Web-URLs, die HTTP verwenden. | <b>URL</b>: Gibt den vollständigen Namen der Datei (einschließlich Website-URL und Dateiname) mit beliebiger Erweiterung an. <br/><br/><b>Data format</b>: Gibt eines der unterstützten Formate an: CSV, TSV, ARFF oder SVM-light. Wenn die Daten eine Kopfzeile haben, wird sie für das Zuweisen von Spaltennamen verwendet.|
Hadoop/HDFS|Liest Daten aus verteiltem Speicher in Hadoop. Sie geben die gewünschten Daten an, indem Sie HiveQL, eine SQL-ähnliche Abfragesprache, verwenden. HiveQL kann auch für die Aggregation von Daten und die Datenfilterung verwendet werden, ehe Sie die Daten Machine Learning Studio hinzufügen.|<b>Hive database query</b>: Gibt die Hive-Abfrage zum Generieren der Daten an.<br/><br/><b>HCatalog server URI</b> : Der angegebene Name des Clusters im Format *&lt;NameIhresClusters&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop user account name</b>: Gibt den Namen des Hadoop-Benutzerkontos an, mit dem der Cluster bereitgestellt wurde.<br/><br/><b>Hadoop user account password</b>: Gibt die Anmeldeinformationen an, die bei der Clusterbereitstellung verwendet wurden. Weitere Informationen finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight](article-hdinsight-provision-clusters).<br/><br/><b>Location of output data</b>: Gibt an, ob die Daten in einem HDFS (Hadoop Distributed File System) oder in Azure gespeichert werden. <br/><ul>Wenn Sie die Ausgabedaten in HDFS speichern, geben Sie den HDFS-Server-URI an. (Achten Sie darauf, den Namen des HDInsight-Clusters ohne das Präfix HTTPS:// zu verwenden). <br/><br/>Wenn Sie Ausgabedaten in Azure speichern, müssen Sie den Namen des Azure-Speicherkontos, den Speicherzugriffsschlüssel und Speichercontainernamen angeben.</ul>|
SQL database |Liest Daten, die in einer Azure SQL-Datenbank oder einer SQL Server-Datenbank gespeichert sind, die in einer Azure-VM ausgeführt wird. | <b>Database server name</b>: Gibt den Namen des Servers an, auf dem die Datenbank ausgeführt wird.<br/><ul>Geben Sie bei einer Azure SQL-Datenbank den Servernamen ein, der generiert wird. Dieser hat meist das Format *&lt;Generierter\_Bezeichner&gt;.database.windows.net*. <br/><br/>Geben Sie bei einer SQL Server-Datenbank, die auf einem Azure VM gehostet wird, *tcp:&lt;DNS-Name der VM&gt;, 1433*</ul> ein. <br/><b>Database name </b>: Gibt den Namen der Datenbank auf dem Server an. <br/><br/><b>Server user account name</b>: Gibt einen Benutzernamen für ein Konto an, das über Zugriffsberechtigungen für die Datenbank verfügt. <br/><br/><b>Server user account password</b>: Gibt das Kennwort für das Benutzerkonto an.<br/><br/><b>Accept any server certificate</b>: Verwenden Sie diese Option (weniger sicher), wenn Sie möchten, dass das Überprüfen des Websitezertifikats übersprungen wird, bevor Sie die Daten lesen.<br/><br/><b>Database query</b>: Geben Sie eine SQL-Anweisung ein, mit der die Daten beschrieben werden, die Sie lesen möchten.|
Azure Table|Liest Daten aus dem Tabellenspeicherdienst in Azure Storage.<br/><br/>Wenn große Mengen von Daten selten gelesen werden, verwenden Sie den Azure-Tabellenspeicherdienst. Dieser Dienst bietet eine flexible, nicht relationale (NoSQL), überaus skalierbare, kostengünstige und hochverfügbare Speicherlösung.| Die Optionen im **Reader**-Modul ändern sich je nachdem, ob Sie auf öffentliche Informationen oder ein privates Speicherkonto zugreifen, das Anmeldeinformationen erfordert. Dies wird anhand der Option <b>Authentication Type</b> bestimmt, die den Wert „PublicOrSAS“ oder „Account“ haben kann, der jeweils über einen eigenen Satz von Parametern verfügen kann. <br/><br/><b>Public or Shared Access Signature (SAS) URI</b>: Die Parameter lauten wie folgt:<br/><br/><ul><b>Table URI</b>: Gibt die öffentliche oder SAS-URL für die Tabelle an.<br/><br/><b>Gibt die nach Eigenschaftennamen zu durchsuchenden Zeilen an</b>: Die Werte lauten <i>TopN</i> zum Überprüfen der angegebenen Anzahl von Zeilen oder <i>ScanAll</i> zum Abrufen aller Zeilen in der Tabelle. <br/><br/>Wenn die Daten homogen und vorhersagbar sind, wird empfohlen, *TopN* auszuwählen und einen Wert für N einzugeben. Bei großen Tabellen kann dies zu schnelleren Lesezeiten führen. <br/><br/>Wenn die Daten mit Sätzen von Eigenschaften strukturiert sind, die abhängig von der Tiefe und Position der Tabelle variieren, wählen Sie die Option *ScanAll*, um alle Zeilen zu überprüfen. Dadurch wird die Integrität der resultierenden Eigenschafts- und Metadatenkonvertierung sichergestellt.<br/><br/></ul><b>Private Storage Account</b>: Die Parameter sind: <br/><br/><ul><b>Account name</b>: Gibt den Namen des Kontos mit der zu lesenden Tabelle an.<br/><br/><b>Account key</b>: Gibt den mit dem Konto verknüpften Speicherschlüssel an.<br/><br/><b>Table name</b> : Gibt den Namen der Tabelle an, die die zu lesenden Daten enthält.<br/><br/><b>Rows to scan for property names</b>: Die Werte lauten <i>TopN</i> zum Überprüfen der angegebene Anzahl von Zeilen oder <i>ScanAll</i> zum Abrufen aller Zeilen in der Tabelle. <br/><br/>Wenn die Daten homogen und vorhersagbar ist, wird empfohlen, *TopN* auszuwählen und einen Wert für N einzugeben. <br/><br/>Wenn die Daten mit Sätzen von Eigenschaften strukturiert sind, die abhängig von der Tiefe und Position der Tabelle variieren, wählen Sie die Option *ScanAll*, um alle Zeilen zu überprüfen. Dadurch wird die Integrität der resultierenden Eigenschafts- und Metadatenkonvertierung sichergestellt.<br/><br/>|
Azure Blob Storage | Liest im Blob-Dienst in Azure Storage gespeicherte Daten, z. B. Bilder, unstrukturierten Text oder Binärdaten.<br/><br/>Mithilfe des Blob-Diensts können Sie Daten öffentlich verfügbar machen oder Anwendungsdaten privat speichern. Über HTTP oder HTTPS-Verbindungen können Sie von überall auf Ihre Daten zugreifen. | Die Optionen im **Reader**-Modul ändern sich je nachdem, ob Sie auf öffentliche Informationen oder ein privates Speicherkonto zugreifen, das Anmeldeinformationen erfordert. Dies wird anhand der Option <b>Authentication Type</b> bestimmt, die den Wert „PublicOrSAS“ oder „Account“ haben kann.<br/><br/><b>Public or Shared Access Signature (SAS) URI</b>: Die Parameter sind:<br/><br/><ul><b>URI</b>: Gibt die öffentliche oder SAS-URL des Speicherblobs an.<br/><br/><b>File Format</b>: Gibt das Format der Daten im Blob-Dienst an. Die unterstützten Formate sind CSV, TSV und ARFF.<br/><br/></ul><b>Private Storage Account</b>: Die Parameter sind: <br/><br/><ul><b>Account name</b>: Gibt den Namen des Kontos mit dem Blob an, das Sie lesen möchten.<br/><br/><b>Account key</b>: Gibt den mit dem Konto verknüpften Speicherschlüssel an.<br/><br/><b>Path to container, directory, or blob</b> : Gibt den Namen des Blobs an, das die zu lesenden Daten enthält.<br/><br/><b>Blob file format</b>: Gibt das Format der Daten im Blob-Dienst an. Die unterstützten Datenformate sind CSV, TSV, ARFF, CSV mit einer angegebenen Codierung und Excel. <br/><br/><ul>Wenn das Format CSV oder TSV ist, müssen Sie angeben, ob die Datei eine Kopfzeile enthält.<br/><br/>Sie können die Excel-Option zum Lesen von Daten aus Excel-Arbeitsmappen verwenden. Bei der Option <i>Excel data format</i> geben Sie an, ob die Daten in einem Excel-Arbeitsblattbereich oder einer Excel-Tabelle enthalten sind. Bei der Option <i>Excel sheet or embedded table</i> geben Sie den Namen des Blatts oder der Tabelle an, das/die Sie lesen möchten.</ul><br/>|
Data Feed Provider | Liest Daten aus einem unterstützten Feedanbieter. Derzeit wird nur das OData-Format (Open Data Protocol) unterstützt. | <b>Data content type</b>: Gibt das OData-Format an.<br/><br/><b>Source URL</b>: Gibt die vollständige URL des Datenfeeds an. <br/>Beispielsweise liest die folgende URL Daten aus der Beispieldatenbank Northwind: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0211_2016-->