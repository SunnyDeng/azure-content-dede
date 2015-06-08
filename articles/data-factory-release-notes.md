<properties 
	pageTitle="Data Factory - Versionshinweise | Azure" 
	description="Daten-Factory-Anmerkungen zu dieser Version" 
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

## Anmerkungen zu dieser Daten Factory von 04/10/2015
Sehen Sie die **kürzlich aktualisierte Segmente** und **Slices einer vor kurzem fehlschlug** Listen auf der **Tabelle** Blade jetzt. Diese Listen werden durch die Aktualisierungszeit des Segments sortiert. Der Zeitpunkt des Updates eines Segments wird in den folgenden Situationen geändert.

-  Sie den Status eines Segments manuell aktualisieren, z. B. mit der **Set AzureDataFactorySliceStatus** (oder), indem Sie auf **Ausführen** auf der **SLICE** Blade für das Segment.
-  Das Segment ändert sich der Status aufgrund einer Ausführung (z. B. eine Ausführung gestartet, eine Ausführung beendet wurde und fehlgeschlagen ist, eine Ausführung beendet wurde und erfolgreich war, usw.).

Klicken Sie auf den Titel der Listen oder **... (Ellipsen)** die Liste die größere Segmente anzeigen zu können. Klicken Sie auf **Filter** auf der Symbolleiste, um Segmente zu filtern.
 
Sie können weiterhin Segmente, die durch Klicken auf die Slice-Faktor sortiert anzeigen **Datenslices (von Slice-Zeit)** Kachel. Die Segmente in diesen Auflistungen werden nach Segment Zeit sortiert. Beispielsweise ist es einem Zeitplan, der Slices wäre: - 4, 4/2015 17.00 Uhr läuft - 4, 4/2015 4 Uhr Succeeded - 4, 4/2015 3 Uhr fehlgeschlagen

Aber, wenn eine ältere Segment erneut ausgeführt wird, würde nicht angezeigt oben in dieser Liste, obwohl es wahrscheinlich ist der Benutzer mehr interessiert.

## Anmerkungen zu dieser Daten Factory von 3/31/2015
- Aktualisiert **Datenverwaltungsgateway** Installationspaket an gesendet wurde [Microsoft Download Center][adf-gateway-download].
- Kopieren von **lokale Dateisystem in Azure-Blob** wird jetzt unterstützt. Finden Sie unter den folgenden Themen Weitere Informationen.
	-  [Lokale Dateisystem Dienst verknüpft.](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [OnPremisesFileSystemLocation-Eigenschaften in einer Tabelle JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Unterstützt Ereignisquellen und-senken](https://msdn.microsoft.com/library/dn894007.aspx). Sehen Sie sich die aktualisierte Kopie Matrix und **FileSystemSource** Eigenschaften. 
-  Kopieren von **lokale Oracle-Datenbank in Azure-Blob** wird jetzt unterstützt. Finden Sie unter den folgenden Themen Weitere Informationen. 
	-  [Lokale Oracle verknüpft den Dienst](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [OnPremisesOracleTableLocation-Eigenschaften in einer Tabelle JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Unterstützt Ereignisquellen und-senken](https://msdn.microsoft.com/library/dn894007.aspx). Sehen Sie sich die aktualisierte Kopie Matrix und **OracleSource** Eigenschaften.
-  Sie können die Codierung für Textdateien in Azure-Blob angeben. Finden Sie im neuen [EncodingName Eigenschaft](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Beim Kopieren in die Senke SQL können Sie eine gespeicherte Prozedur mit zusätzlichen Parametern aufrufen. Finden Sie unter [Aufrufen der gespeicherten Prozedur zum Auffangen von SQL][adf-copy-advanced] Details.   

Finden Sie das Blob buchen: [Azure Data Factory-Update – neue Datenspeicher](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) Weitere Informationen sowie Beispiele.

## Anmerkungen zu dieser Daten Factory von 2/27/2015

### Neue Verbesserungen
- **Azure Data Factory-Editor**. Data Factory-Editor, das Azure Preview Portal gehört, können Sie erstellen, bearbeiten und Bereitstellen von JSON-Dateien, die verknüpften Dienste, Datasets und Pipelines definieren. Das wichtigste Ziel des Editors finden Sie eine schnelle und leichte Benutzeroberfläche (UI) Azure Data Factory-Artefakte zu erstellen, ohne dass Sie Azure PowerShell und Ramp oben zur Verwendung von PowerShell-Cmdlets installieren. Finden Sie unter der [Azure Data Factory-Editor – ein Light Weight Web Editor][adf-editor-blog] Blogbeitrag für einen raschen Überblick sowie ein Video auf Data Factory-Editor. Ausführliche Übersicht des Editors finden Sie die [Data Factory-Editor][adf-editor] Artikel.          

### Änderungen

## Anmerkungen zu dieser Daten Factory von 1/26/2015 ##

### Änderungen
- Aktualisiert **Datenverwaltungsgateway** Installationspaket an gesendet wurde [Microsoft Download Center][adf-gateway-download]. Ab dieser Version, finden Sie die neuesten Data Management Gateway mit Azure Data Factory an diesem Speicherort herunterladen. Dieses Installationspaket dient Azure Data Factory und Power BI für Office 365-Diensten. Wenn Sie beide Dienste verwenden, beachten Sie, dass Gateways für Daten-Factory und Power BI müssen werden auf verschiedenen Computern installiert unterschiedlich konfiguriert gemäß den Anleitungen aus der Daten-Factory oder Power BI-Dokumentation.
- Die **Kopie Aktivität** unterstützt jetzt kopieren von Daten zwischen lokalen SQL Server-Datenbank und einer Azure SQL-Datenbank. Finden Sie unter [Kopie Aktivität][adf-copy-activity] Details und [GitHub][adf-github-samples] für JSON-Beispiele.
- **SqlSink** unterstützt eine neue Eigenschaft: **WriteBatchTimeout**. Diese Eigenschaft bietet Ihnen die Flexibilität, wie lange Wartezeit für den Einfügevorgang Batch ausführen, bevor ein Timeout für der Vorgang konfigurieren. Für eine Hybrid-Kopie (Copy-Vorgang, der eine lokale Datenquelle und einer Cloud-Datenquelle umfasst), benötigen Sie das Gateway-Version 1.4 oder höher, um diese Eigenschaft verwenden. 
- **SQL Server verknüpft Service** unterstützt jetzt das **Windows-Authentifizierung**. 
	- Beim Erstellen einer SQL Server-Dienst mithilfe des Portals verknüpft, können Sie jetzt Windows-Authentifizierung verwenden, und legen Sie die entsprechende Anmeldeinformationen. Dazu müssen Sie das Gateway-Version 1.4 oder höher aufweisen. 
	- Beim Erstellen einer SQL Server-Dienst mithilfe von Azure PowerShell verknüpft, können Sie das Angeben von Verbindungsinformationen im nur-Text oder Verschlüsseln der Verbindung mit aktualisiert [Cmdlet "New-AzureDataFactoryEncryptValue"][adf-encrypt-value-cmdlet] und verwenden Sie dann die verschlüsselte Zeichenfolge für die Eigenschaft der Verbindungszeichenfolge in der verknüpften Dienst JSON-Nutzlast. Finden Sie unter [verknüpften Dienste][adf-msdn-linked-services] ausführliche Informationen zum Definieren von verknüpften Diensts im JSON-Format. Die Verschlüsselungsfunktion wird durch das Cmdlet "New-AzureDataFactoryEncryptValue" noch nicht unterstützt. 

## Anmerkungen zu dieser Daten Factory von 12/11/2014 ##

### Neue Verbesserungen

- Azure Machine Learning-Integration
	- Diese Version von Azure Data Factory-Dienst können Sie Azure Data Factory mit Azure Machine Learning (ML) mit Integration **AzureMLLinkedService** und **AzureMLBatchScoringActivity**. Finden Sie unter [predictive Pipelines, die mithilfe von Data Factory und Azure Computerlernen erstellen][adf-azure-ml] Details. 
- Der Status der Gatewayversion wird bereitgestellt.
	- Der Status "NewVersionAvailable" wird im Azure Vorschauportal und in der Ausgabe des Cmdlet "Get-AzureDataFactoryGateway" angezeigt, wenn eine neuere Version des Gateways als die derzeit installierte Version verfügbar ist. Sie können dann den Portalanweisungen folgen, um die neue Installationsdatei (MSI) herunterzuladen und auszuführen, um das neueste Gateway zu installieren. Es ist keine zusätzliche Konfiguration erforderlich.

### Änderungen

- "JobsContainer" in "HdInsightOnDemandLinkedService" wird entfernt.
	- In der JSON-Definition für eine HDInsightOnDemandLinkedService, Sie müssen nicht angeben **JobsContainer** -Eigenschaft nicht mehr. Wenn Sie die Eigenschaft für einen verknüpften, bedarfsgesteuerten Dienst angegeben haben, wird die Eigenschaft ignoriert. Sie können die Eigenschaft aus der JSON-Definition für den verknüpften Dienst entfernen und die Definition des verknüpften Diensts mithilfe des Cmdlet "New-AzureDataFactoryLinkedService" aktualisieren.
- Optionale Konfigurationsparameter für "HDInsightOnDemandLinkedService"
	- Diese Version führt die Unterstützung für einige optionale Konfigurationsparameter für "HDInsightOnDemandLinked" (bedarfsgesteuerter HDInsight-Cluster) ein. Finden Sie unter [ClusterCreateParameters Eigenschaften][on-demand-hdi-parameters] Details.
- Speicherort für Gateway wird entfernt.
	- Wenn Sie ein Azure Data Factory-Gateway über das Portal oder PowerShell (New-AzureDataFactoryGateway) erstellen, müssen Sie den Speicherort für das Gateway nicht länger angeben. Die Region für die Data Factory wird übernommen. Ebenso wird zum Konfigurieren eines verknüpften SQL Server-Diensts mithilfe von JSON die Eigenschaft "gatewayLocation" nicht länger benötigt. Das Data Factory .NET SDK wird ebenfalls aktualisiert, um diese Änderungen widerzuspiegeln.
	- Wenn Sie eine ältere Version des SDK und von Azure PowerShell verwenden, müssen Sie weiterhin die Standorteinstellung bereitstellen.
 
     

#### Wichtige Änderungen
	
- CustomActivity zu DotNetActivity
	- **ICustomActivity** Schnittstelle wird umbenannt, um **IDotNetActivity**. Sie müssen Data Factory NuGet-Pakete aktualisieren und "ICustomActivity" im Quellcode für Ihre benutzerdefinierte Aktivität in "IDotNetActivity" ändern.  
	- Der Typ der benutzerdefinierten Aktivität in der JSON-Definition für die benutzerdefinierte Aktivität muss geändert werden, von **CustomActivity**  **DotNetActivity**. 
	- Die **CustomActivity** und **CustomActivityProperties** -Klasse wurden umbenannt **DotNetActivity** und **DotNetActivityProperties** mit den gleichen Satz von Eigenschaften.

		Wenn Sie die ältere Version des SDK und von Azure PowerShell verwenden, können Sie weiterhin "CustomActivity" statt "DotNetActivity" verwenden.
    
  		Finden Sie unter [Verwenden Sie benutzerdefinierte Aktivitäten in einer Pipeline Azure Data Factory][adf-custom-activities] eine exemplarische Vorgehensweise zum Erstellen einer benutzerdefinierten Aktivität und in einer Pipeline Azure Data Factory verwenden.

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

<!---HONumber=GIT-SubDir-->