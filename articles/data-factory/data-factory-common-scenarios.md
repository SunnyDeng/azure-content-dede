<properties 
	pageTitle="Gängige Szenarien für die Verwendung von Azure Data Factory" 
	description="Dieser Artikel beschreibt einige der gängigen Szenarien für die Verwendung des Azure Data Factory-Diensts." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Gängige Szenarien für die Verwendung von Azure Data Factory
In diesem Abschnitt werden einige Beispielszenarien beschrieben, die Azure Data Factory heute unterstützen kann und die als Hub-Szenarios weiter wachsen werden.

> [AZURE.NOTE]Lesen Sie den Artikel [Einführung in den Azure Data Factory-Dienst][datafactory-introduction], bevor Sie diesen Artikel lesen.

##Szenario 1: Datenquellen für den Datenhub
![Quellen für den Datenhub][image-data-factory-introduction-secenario1-source-datahub]

Unternehmen verfügen über Daten unterschiedlichen Typs, die sich in unterschiedlichen Quellen befinden. Der erste Schritt bei der Erstellung eines Informationsproduktionssystems besteht darin, alle erforderlichen Quellen von Daten und Verarbeitung, z. B. SaaS-Dienste, Dateifreigaben, FTP und Webdienste, miteinander zu verbinden und die Daten wie für die weitere Verarbeitung erforderlich zu verschieben.

Ohne Data Factory müssen Unternehmen benutzerdefinierte Datenverschiebungskomponenten erstellen oder benutzerdefinierte Dienste zur Integration dieser Datenquellen und Verarbeitung schreiben. Dies ist teuer, und es ist schwierig, solche Systeme zu verwalten. Häufig fehlen dabei auch die robusten Überwachungs- und Warnfunktionen sowie die Steuerungen, die ein vollständig verwalteter Dienst bietet.
  
Bei Azure Data Factory werden Datenspeicherungs- und Verarbeitungsdienste in einem Hub-Container gesammelt, der Verarbeitungs- und Speicheraktivitäten erleichtert und optimiert, eine einheitliche Verwaltung des Ressourcenverbrauchs ermöglicht und bei Bedarf Dienste für die Datenverschiebung bereitstellt.

##Szenario 2: Operationalisieren der Informationsproduktion
Operationalisierungsszenarien sind der nächste logische Schritt nach den Datenquellenszenarien. Sobald in einem Hub Daten vorhanden sind, sollten Sie Datenpipelines erstellen und in Betrieb nehmen, um zuverlässig transformierte Daten basierend auf einem verwaltbaren und kontrollierten Zeitplan zu produzieren, damit Produktionsumgebungen mit zuverlässigen Daten versorgt werden können. Die Datentransformation in der Azure Data Factory erfolgt über Hive, Pig und benutzerdefinierte C#-Verarbeitung, die auf Hadoop (Azure HDInsight) ausgeführt wird. Diese Transformationen können dazu verwendet werden, Daten zu bereinigen, wichtige Datenfelder zu maskieren und weitere Vorgänge auf vielfältige, komplexe Weise mit den Daten auszuführen. Normalerweise wird die Operationalisierung mit einer komplexen und schwer zu verwaltenden Infrastruktur und mit benutzerdefinierten Diensten erreicht. Sie bietet hinsichtlich Implementierung, Verwaltung, Skalierung, Problembehandlung und Versionskontrolle für eine derartige Lösung einige Herausforderungen.
  
Mit Data Factory als vollständig verwalteter Dienst können Benutzer diese Pipelines in Betrieb nehmen, indem sie sie mit einem einmaligen oder komplexen wiederkehrenden Zeitplan definieren; die Orchestrierung wird direkt vom Data Factory-Dienst übernommen. Mit Hubs erfolgen die Clusterverwaltung für sämtliche Daten und die Verarbeitung innerhalb eines Hubs im Namen des Benutzers, daher können sich die Benutzer anstelle der Infrastrukturverwaltung auf Transformationsanalysen konzentrieren. Azure Data Factory beseitigt die Herausforderungen bei der Arbeit mit fehleranfälligen, benutzerdefinierten Diensten und ermöglicht es Unternehmen, vertrauenswürdige Informationen zuverlässig und reproduzierbar zu erzeugen.


##Szenario 3: Integrieren der Informationsproduktion in die Datenermittlung
Herkömmliche BI-Ansätze und -Technologien liefern zwar eine "verlässliche Wahrheitsquelle", haben jedoch fast immer eine schwerwiegende Nebenwirkung: einen konstanten Anforderungsrückstand aufgrund eines sorgfältig kontrollierten Prozesses. Für eine Anpassung an sich schnell änderende Unternehmensfragen ist eine größere Flexibilität auf Seiten der Unternehmen erforderlich, die ihre Informationsproduktionssysteme mit den Informationsnutzungssystemen verbinden müssen. Azure Data Factory hilft dabei, der Herausforderung der Verbindung dieser Systeme mit optimierten Datenpipelines für die Informationsproduktion und der Herausforderung des Informationsverbrauchs zu begegnen, indem aktuelle vertrauenswürdige Daten in einfach zu nutzenden Formen erstellt werden.
  
Azure Data Factory unterstützt die folgenden Funktionen für eine einfache Nutzung der produzierten Daten:

- Einfaches Verschieben (einmalig oder geplant) der erzeugten Datenbestände in relationale Datamarts für die Nutzung durch vorhandene BI-Tools (Excel, Tableau usw.).
- Nutzung der produzierten Datenbestände durch eine Data Factory, die Power Query in Excel verwendet.

Informationen zur Nutzung von Daten mithilfe von Power Query finden Sie in den folgenden Themen:

- [Power Query: Herstellen einer Verbindung mit Microsoft Azure-Tabellenspeicher][Power-Query-Azure-Table]
- [Power Query: Herstellen einer Verbindung mit Microsoft Azure-Blobspeicher][Power-Query-Azure-Blob]
- [Power Query: Herstellen einer Verbindung mit Microsoft Azure SQL-Datenbank][Power-Query-Azure-SQL]
- [Power Query: Herstellen einer Verbindung mit lokalem Microsoft SQL Server][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png



 

<!---HONumber=July15_HO3-->