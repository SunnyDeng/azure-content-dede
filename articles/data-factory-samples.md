<properties 	
	pageTitle="Azure Data Factory - Beispiele" 
	description="Bietet Informationen zu Beispielen, die ausgeliefert mit dem Azure Data Factory-Dienst." 
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
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Beispiele

## Beispiele in Azure-Portal
Sie können schnell bereitstellen, überprüfen und Testen einer Azure Data Factory Beispiel mithilfe der **Pipelines (Beispiel)** Blade im Azure-Portal.

1. Erstellen Sie eine neue Data Factory, oder öffnen Sie eine Factory für die vorhandenen Daten. Finden Sie unter [Erste Schritte mit Azure Data Factory][data-factory-get-started] Schritte zum Erstellen einer Factory Daten.
2. In der **DATA FACTORY** Blade für die Factory Daten klicken Sie auf die **Beispiel-Pipelines** Kachel.

	![Beispiel-Pipelines-Kachel](./media/data-factory-samples/SamplePipelinesTile.png)

2. In der **Beispiel-Pipelines** Blade, klicken Sie auf die **Beispiel** die Sie bereitstellen möchten.
	
	![Beispiele für Pipelines blade](./media/data-factory-samples/SampleTile.png)

3. Geben Sie die Konfigurationseinstellungen für das Beispiel. Beispielsweise Ihre Azure-Speicher und Konto Zugriffsschlüssel, Azure SQL-Servername, Datenbank, Benutzer-ID und Kennwort usw....

	![Beispiel-blade](./media/data-factory-samples/SampleBlade.png)

4. Wenn Sie fertig sind, mit die Konfigurationseinstellungen angeben, klicken Sie auf **Erstellen** Erstellen/der Beispiel-Pipelines und verknüpfte von Pipelines verwendeten Dienste/Tabellen bereitstellen.
5. Sehen Sie den Status der Bereitstellung auf der Beispielkachel geklickt haben weiter oben auf der **Beispiel-Pipelines** Blade.

	![Bereitstellungsstatus](./media/data-factory-samples/DeploymentStatus.png)

6. Wenn angezeigt wird, die **Bereitstellung erfolgreich** Nachricht auf der Kachel für das Beispiel schließen die **Beispiel-Pipelines** Blade.
5. Auf **DATA FACTORY** Blade, werden Sie feststellen, dass verknüpften Dienste, Datasets und Pipelines Ihrer Factory Daten hinzugefügt werden.  

	![Daten-Factory-blade](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

Die folgende Tabelle enthält eine kurze Beschreibung der Beispiele zur Verfügung, in dem **Beispiel-Pipelines** Kachel.

Name des Beispiels | description
----------- | -----------
Gaming-Kunden, die Profilerstellung | Contoso ist ein Spiele-Unternehmen, das Spielen für mehrere Plattformen erstellt: game-Konsolen, tragbare Geräte und Computer (PCs). Jedes dieser Spiele erzeugt Unmengen von Protokollen. Contoso Ziel besteht darin, erfassen und Analysieren der durch diese Spiele erhalten Informationen zur Verwendung, Up-Selling und Cross-Selling-Möglichkeiten identifizieren, entwickeln neue überzeugende Features usw. erzeugten Protokolle... Verbesserung der geschäftlichen und Kunden eine bessere Benutzerfreundlichkeit bieten. In diesem Beispiel erfasst Beispielprotokolle, Prozesse und erweitert diese mit Verweisdaten und transformiert die Daten, um die Wirksamkeit einer Marketingkampagne zu evaluieren, die Contoso zuletzt gestartet wurde.
 
## Beispiele für GitHub
Die [GitHub Azure-DataFactory Repository](https://github.com/azure/azure-datafactory) enthält mehrere Beispiele, mit denen Sie schnell auf dem neuesten Stand mit Azure Data Factory-Dienst (oder) ändern Sie die Skripts und in eigenen Anwendung verwenden. Der Ordner Samples\JSON enthält JSON-Ausschnitte für gängige Szenarios.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir-->