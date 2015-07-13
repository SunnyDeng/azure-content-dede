<properties 
	pageTitle="Data Factory - Versionshinweise | Azure" 
	description="DataFactory – Anmerkungen zur Version" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Versionshinweise für Azure Data Factory

## Anmerkungen zur Version von Data Factory vom 10.04.2015
Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** befinden sich nun auf dem Blatt **TABELLE**. Diese Listen werden anhand der Aktualisierungszeit des Slices sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.

-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set AzureDataFactorySliceStatus** oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).

Klicken Sie auf den Titel der Listen oder auf **...** (Auslassungspunkte), um eine umfangreichere Liste mit Slices anzuzeigen. Klicken Sie auf der Symbolleiste auf **Filter**, um die Slices zu filtern.
 
Durch Klicken auf die Kachel **Datenslices (nach Slicezeit)** können Sie Slices weiterhin sortiert nach der Slicezeit anzeigen. Die Slices in diesen Auflistungen sind nach Slicezeit sortiert. Bei einem stündlichen Zeitplan sind die Slices beispielsweise wie folgt sortiert: - 04.04.2015 17:00 Uhr In Bearbeitung - 04.04.2015 16:00 Uhr Erfolg - 04.04.2015 15:00 Uhr Fehler

Wenn jedoch ein älterer Slice erneut ausgeführt wird, wird er nicht oben in dieser Liste angezeigt, obwohl dies vermutlich für den Benutzer interessant wäre.

## Anmerkungen zur Version von Data Factory vom 31.3.2015
- Das aktualisierte Installationspaket für das **Datenverwaltungsgateway** wurde im [Microsoft Download Center][adf-gateway-download] bereitgestellt.
- Das Kopieren aus dem **lokalen Dateisystem in einen Azure-Blob** wird jetzt unterstützt. Weitere Informationen finden Sie in den folgenden Themen.
	-  [Mit lokalem Dateisystem verknüpfter Dienst](https://msdn.microsoft.com/library/dn930836.aspx)
	-  ["OnPremisesFileSystemLocation"-Eigenschaften im JSON-Code einer Tabelle](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx). Siehe die aktualisierte Kopiermatrix und die aktualisierten Eigenschaften **FileSystemSource**. 
-  Das Kopieren aus einer **lokalen Oracle-Datenbank in einen Azure-Blob** wird jetzt unterstützt. Weitere Informationen finden Sie in den folgenden Themen: 
	-  [Mit lokaler Oracle-Datenbank verknüpfter Dienst](https://msdn.microsoft.com/library/dn948537.aspx)
	-  ["OnPremisesOracleTableLocation-Eigenschaften" im JSON-Code einer Tabelle](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx). Siehe die aktualisierte Kopiermatrix und die aktualisierten Eigenschaften **OracleSource**.
-  Sie können die Codierung für Textdateien in einem Azure-Blob angeben. Siehe die neue Eigenschaft [encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Beim Kopieren in die SQL-Senke können Sie eine gespeicherte Prozedur mit zusätzlichen Parametern aufrufen. Unter [Aufrufen einer gespeicherten Prozedur für SQL-Senke][adf-copy-advanced] finden Sie weitere Details.   

Siehe den Blogbeitrag [Azure Data Factory-Update – Neue Datenspeicher](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/), der weitere Informationen und Beispiele bietet.

## Anmerkungen zur Version von Data Factory vom 27.2.2015

### Neue Verbesserungen
- **Azure Data Factory-Editor**: Der Data Factory-Editor, der zum Azure-Vorschauportal gehört, dient zum Erstellen, Bearbeiten und Bereitstellen von JSON-Dateien, die verknüpfte Dienste, Datasets und Pipelines definieren. Hauptaufgabe des Editors ist es, Ihnen eine schnelle und schlanke Benutzeroberfläche zum Erstellen von Azure Data Factory-Artefakten zu bieten, ohne dass Sie Azure PowerShell installieren und PowerShell-Cmdlets einsetzen müssen. Im Blogbeitrag [Azure Data Factory-Editor – ein schlanker Web-Editor][adf-editor-blog] finden Sie eine Kurzübersicht sowie ein Video zum Data Factory-Editor. Eine ausführliche Übersicht über den Editor finden Sie im Artikel [Data Factory-Editor][adf-editor].          

### Änderungen

## Anmerkungen zur Version von Data Factory vom 26.1.2015 ##

### Änderungen
- Das aktualisierte Installationspaket für das **Datenverwaltungsgateway** wurde im [Microsoft Download Center][adf-gateway-download] bereitgestellt. Ab dieser Version finden Sie an dieser Downloadadresse das neueste Datenverwaltungsgateway für den Einsatz mit Azure Data Factory. Dieses Installationspaket umfasst sowohl Azure Data Factory als auch Power BI für Office 365-Dienste. Wenn Sie beide Dienste nutzen, beachten Sie, dass Gateways für Data Factory und Power BI auf verschiedenen Computern installiert und gemäß den Anleitungen in der Data Factory- bzw. Power BI-Dokumentation unterschiedlich konfiguriert werden müssen.
- Die **Kopieraktivität** unterstützt jetzt das Kopieren von Daten zwischen einer lokalen SQL Server-Datenbank und einer Azure SQL-Datenbank. Unter [Kopieraktivität][adf-copy-activity] finden Sie Details und auf [GitHub][adf-github-samples] JSON-Beispiele.
- **SqlSink** unterstützt die neue Eigenschaft **WriteBatchTimeout**. Mit dieser Eigenschaft können Sie flexibel festlegen, wie lange auf den Abschluss des Batcheinfügevorgangs gewartet werden soll, bis ein Timeout erfolgt. Für einen Hybridkopiervorgang (der eine lokale und eine Clouddatenquelle umfasst) benötigen Sie Version 1.4 oder höher des Gateways, um diese Eigenschaft verwenden zu können. 
- Der **mit SQL Server verknüpfte Dienst** unterstützt jetzt die **Windows-Authentifizierung**. 
	- Beim Erstellen eines mit SQL Server verknüpften Diensts im Portal können Sie jetzt die Windows-Authentifizierung wählen und entsprechende Anmeldeinformationen festlegen. Dazu müssen Sie Version 1.4 oder höher des Gateways verwenden. 
	- Beim Erstellen eines mit SQL Server verknüpften Diensts mit Azure PowerShell können Sie Verbindungsinformationen unverschlüsselt angeben oder die Verbindung mit dem aktualisierten [Cmdlet "New-AzureDataFactoryEncryptValue"][adf-encrypt-value-cmdlet] verschlüsseln. Anschließend verwenden Sie die verschlüsselte Zeichenfolge für die Eigenschaft mit der Verbindungszeichenfolge in der JSON-Nutzlast des verknüpften Diensts. Unter [Verknüpfte Dienste][adf-msdn-linked-services] finden Sie Details zum Definieren eines verknüpften Diensts in JSON. Die Verschlüsselungsfunktion wird vom Cmdlet "New-AzureDataFactoryEncryptValue" noch nicht unterstützt. 

## Anmerkungen zur Version von Data Factory vom 12.11.2014 ##

### Neue Verbesserungen

- Azure Machine Learning-Integration
	- Mit dieser Version des Azure Data Factory-Diensts können Sie Azure Data Factory mit Azure Machine Learning (ML) mithilfe von **AzureMLLinkedService** und **AzureMLBatchScoringActivity** integrieren. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Data Factory und Azure Machine Learning][adf-azure-ml]. 
- Der Status der Gatewayversion wird bereitgestellt.
	- Der Status "NewVersionAvailable" wird im Azure Vorschauportal und in der Ausgabe des Cmdlet "Get-AzureDataFactoryGateway" angezeigt, wenn eine neuere Version des Gateways als die derzeit installierte Version verfügbar ist. Sie können dann den Portalanweisungen folgen, um die neue Installationsdatei (MSI) herunterzuladen und auszuführen, um das neueste Gateway zu installieren. Es ist keine zusätzliche Konfiguration erforderlich.

### Änderungen

- "JobsContainer" in "HdInsightOnDemandLinkedService" wird entfernt.
	- In der JSON-Definition für "HDInsightOnDemandLinkedService" müssen Sie die Eigenschaft **jobsContainer** nicht länger angeben. Wenn Sie die Eigenschaft für einen verknüpften, bedarfsgesteuerten Dienst angegeben haben, wird die Eigenschaft ignoriert. Sie können die Eigenschaft aus der JSON-Definition für den verknüpften Dienst entfernen und die Definition des verknüpften Diensts mithilfe des Cmdlet "New-AzureDataFactoryLinkedService" aktualisieren.
- Optionale Konfigurationsparameter für "HDInsightOnDemandLinkedService"
	- Diese Version führt die Unterstützung für einige optionale Konfigurationsparameter für "HDInsightOnDemandLinked" (bedarfsgesteuerter HDInsight-Cluster) ein. Weitere Informationen finden Sie unter [ClusterCreateParameters-Eigenschaften][on-demand-hdi-parameters].
- Speicherort für Gateway wird entfernt.
	- Wenn Sie ein Azure Data Factory-Gateway über das Portal oder PowerShell (New-AzureDataFactoryGateway) erstellen, müssen Sie den Speicherort für das Gateway nicht länger angeben. Die Region für die Data Factory wird übernommen. Ebenso wird zum Konfigurieren eines verknüpften SQL Server-Diensts mithilfe von JSON die Eigenschaft "gatewayLocation" nicht länger benötigt. Das Data Factory .NET SDK wird ebenfalls aktualisiert, um diese Änderungen widerzuspiegeln.
	- Wenn Sie eine ältere Version des SDK und von Azure PowerShell verwenden, müssen Sie weiterhin die Standorteinstellung bereitstellen.
 
     

#### Wichtige Änderungen
	
- CustomActivity zu DotNetActivity
	- Die Schnittstelle **ICustomActivity** wurde in **IDotNetActivity** umbenannt. Sie müssen Data Factory NuGet-Pakete aktualisieren und "ICustomActivity" im Quellcode für Ihre benutzerdefinierte Aktivität in "IDotNetActivity" ändern.  
	- Der Typ der benutzerdefinierten Aktivität muss in deren JSON-Definition von **CustomActivity** in **DotNetActivity** geändert werden. 
	- Die Klassen **CustomActivity** und **CustomActivityProperties** wurden mit dem gleichen Eigenschaftensatz in **DotNetActivity** und **DotNetActivityProperties** umbenannt.

		Wenn Sie die ältere Version des SDK und von Azure PowerShell verwenden, können Sie weiterhin "CustomActivity" statt "DotNetActivity" verwenden.
    
  		Eine exemplarische Vorgehensweise zum Erstellen einer benutzerdefinierten Aktivität und zu deren Verwendung in einer Azure Data Factory-Pipeline finden Sie unter [Verwenden benutzerdefinierter Aktivitäten in einer Azure Data Factory-Pipeline][adf-custom-activities].

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=62-->