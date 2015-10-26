<properties 	
	pageTitle="Azure Data Factory Editor – Beispiele" 
	description="Bietet Informationen zu Beispielen, die zum Funktionsumfang des Azure Data Factory-Diensts gehören." 
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
	ms.date="10/13/2015" 
	ms.author="spelluru"/>

# Azure Data Factory Editor – Beispiele

## Beispiele im Azure-Portal
Auf dem Blatt **Beispielpipelines** im Azure-Portal können Sie ein Azure Data Factory-Beispiel schnell bereitstellen, überprüfen und testen.

1. Erstellen Sie eine neue Data Factory, oder öffnen Sie eine vorhandene Data Factory. Unter [Erste Schritte mit Azure Data Factory][data-factory-get-started] finden Sie Schritte zum Erstellen einer Data Factory.
2. Klicken Sie auf dem Blatt **DATA FACTORY** für die Data Factory auf die Kachel **Beispielpipelines**.

	![Kachel "Beispielpipelines"](./media/data-factory-samples/SamplePipelinesTile.png)

2. Klicken Sie auf dem Blatt **Beispielpipelines** auf das **Beispiel**, das Sie bereitstellen möchten.
	
	![Blatt "Beispielpipelines"](./media/data-factory-samples/SampleTile.png)

3. Geben Sie die Konfigurationseinstellungen für das Beispiel an. Hierzu zählen beispielsweise Ihr Azure-Speicherkonto und Ihre Kontoschlüssel, der Azure SQL-Servername, die Datenbank, die Benutzer-ID, das Kennwort usw.

	![Blatt "Beispiel"](./media/data-factory-samples/SampleBlade.png)

4. Wenn Sie mit den Konfigurationseinstellungen fertig sind, klicken Sie auf **Erstellen**, um die von den Pipelines verwendeten Beispielpipelines und verknüpften Dienste/Tabellen zu erstellen bzw. bereitstellen.
5. Auf dem Blatt **Beispielpipelines** sehen Sie auf der Kachel "Beispiel", auf die Sie zuvor geklickt haben, den Status der Bereitstellung.

	![Bereitstellungsstatus](./media/data-factory-samples/DeploymentStatus.png)

6. Wenn die Meldung **Bereitstellung erfolgreich** auf der Kachel für das Beispiel angezeigt wird, schließen Sie das Blatt **Beispielpipelines**.
5. Auf dem Blatt **DATA FACTORY** erkennen Sie, dass Ihrer Data Factory verknüpfte Dienste, Datasets und Pipelines hinzugefügt wurden.  

	![Blatt "Data Factory"](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

Die folgende Tabelle enthält eine kurze Beschreibung der Beispiele, die auf der Kachel **Beispielpipelines** zur Verfügung stehen.

Name des Beispiels | description
----------- | -----------
Erstellen der Profile von Kunden eines Spieleherstellers | Contoso ist ein Spielehersteller, der Spiele für mehrere Plattformen entwickelt: Spielekonsolen, mobile Geräte und PCs. Jedes dieser Spiele erzeugt Unmengen von Protokollen. Ziel von Contoso ist es, die von diesen Spielen generierten Protokolle zu erfassen und zu analysieren, um Nutzungsinformationen zu erhalten, Up-Selling- und Cross-Selling-Möglichkeiten zu ermitteln, neue spannende Features zu entwickeln usw. Der Hersteller möchte auf diese Weise sein Geschäft und die Kundenerfahrung verbessern. Bei diesem Beispiel werden Beispielprotokolle gesammelt, verarbeitet und durch Verweisdaten ergänzt. Die Daten werden zur Bewertung der Effektivität einer vor Kurzem von Contoso gestarteten Marketingkampagne transformiert.
 
## Beispiele für GitHub
Das [Azure-DataFactory-Repository auf GitHub](https://github.com/azure/azure-datafactory) enthält mehrere Beispiele, mit deren Hilfe Sie den Azure Data Factory-Dienst rasch in Betrieb nehmen können. Sie können die Skripts auch ändern, um sie in eigenen Anwendungen zu verwenden. Der Ordner "Samples\\JSON" enthält JSON-Codeausschnitte für gängige Szenarien.

## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-samples.md).

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=Oct15_HO3-->