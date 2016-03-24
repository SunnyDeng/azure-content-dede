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
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Azure Data Factory Editor – Beispiele

## Beispiele für GitHub
Das [Azure-DataFactory-Repository auf GitHub](https://github.com/azure/azure-datafactory) enthält mehrere Beispiele, mit deren Hilfe Sie den Azure Data Factory-Dienst rasch in Betrieb nehmen können. Sie können die Skripts auch ändern, um sie in eigenen Anwendungen zu verwenden. Der Ordner "Samples\\JSON" enthält JSON-Codeausschnitte für gängige Szenarien.

## Beispiele im Azure-Portal
Sie können die Kachel mit den **Beispielpipelines** auf der Startseite Ihrer Data Factory zum Bereitstellen von Beispielpipelines mit den zugehörigen Entitäten (Datasets und verknüpften Dienste) in Ihrer Data Factory verwenden.

1. Erstellen Sie eine neue Data Factory, oder öffnen Sie eine vorhandene Data Factory. Unter [Erste Schritte mit Azure Data Factory](data-factory-get-started.md#CreateDataFactory) finden Sie Schritte zum Erstellen einer Data Factory.
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
   
## Beispiele in Visual Studio

### Voraussetzungen

Folgendes muss auf Ihrem Computer installiert sein:

- Visual Studio 2013 oder Visual Studio 2015
- Laden Sie das Azure-SDK für Visual Studio 2013 oder Visual Studio 2015 herunter. Navigieren Sie zur [Azure-Downloadseite](https://azure.microsoft.com/downloads/), und klicken Sie auf **VS 2013** oder**VS 2015** im Abschnitt **.NET**.
- Laden Sie das neueste Azure Data Factory-Plug-In für Visual Studio herunter: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) oder [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Bei Verwendung von Visual Studio 2013 können Sie das Plug-In auch wie folgt aktualisieren: Klicken Sie im Menü auf **Tools** -> **Erweiterungen und Updates** -> **Online** -> **Visual Studio-Katalog** -> **Microsoft Azure Data Factory-Tools für Visual Studio** -> **Aktualisieren**.

### Verwenden von Data Factory-Vorlagen

1. Klicken Sie im Menü auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. 
2. Führen Sie im Dialogfeld **Neues Projekt** folgende Schritte aus: 
	1. Wählen Sie unter **Vorlagen** die Option **DataFactory** aus. 
	2. Wählen Sie im rechten Bereich **Data Factory-Vorlagen** aus. 
	3. Geben Sie einen **Namen** für das Projekt ein. 
	4. Wählen Sie einen **Speicherort** für das Projekt aus. 
	5. Klicken Sie auf **OK**. 

	![Dialogfeld "Neues Projekt"](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Wählen Sie im Dialogfeld **Data Factory-Vorlagen** die Beispielvorlage aus dem Abschnitt **Anwendungsfall-Vorlagen** aus, und klicken Sie auf **Weiter**. Die folgenden Schritte führen Sie durch die Verwendung der Vorlage **Kundenprofile**. Die Schritte sind bei den anderen Beispielen ähnlich. 

	![Dialogfeld „Data Factory-Vorlagen“](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. Klicken Sie im Dialogfeld **Data Factory-Konfiguration** auf der Seite **Data Factory-Grundeinstellungen** auf **Weiter**.
8. Führen Sie auf der Seite **Data Factory konfigurieren** folgende Schritte aus: 
	1. Wählen Sie in dieser exemplarischen Vorgehensweise **Neue Data Factory erstellen** aus. Sie können auch **Vorhandene Data Factory verwenden** auswählen.
	2. Geben Sie einen **Namen** für die Data Factory ein.
	3. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll. 
	4. Wählen Sie die **Ressourcengruppe** für die Data Factory aus.
	5. Wählen Sie **USA, Westen** oder **Europa, Norden** als **Region** aus.
	6. Klicken Sie auf **Weiter**. 
9. Geben Sie auf der Seite **Datenspeicher konfigurieren** eine vorhandene **Azure SQL-Datenbank** und ein **Azure-Speicherkonto** an (oder erstellen Sie sie), und klicken Sie dann auf „Weiter“. 
10. Übernehmen Sie auf der Seite **Compute konfigurieren** die Standardwerte, und klicken Sie auf **Weiter**. 
11. Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen, und klicken Sie auf **Weiter**. 
12. Warten Sie auf der Seite **Bereitstellungsstatus**, bis die Bereitstellung abgeschlossen ist, und klicken Sie auf **Fertig stellen**.
13. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**. 
19. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
20. Das folgende Dialogfeld sollte angezeigt werden:

	![Dialogfeld „Veröffentlichen“](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Führen Sie auf der Seite **Data Factory konfigurieren** folgende Schritte aus:
	1. Aktivieren Sie die Option **Vorhandene Data Factory verwenden**.
	2. Wählen Sie die **Data Factory** aus, die Sie für die oben verwendete Vorlage ausgewählt hatten. 
	6. Klicken Sie auf **Weiter**, um zur Seite **Publish Items** zu wechseln. (Drücken Sie die **TAB-TASTE**, um das Feld „Name“ zu verlassen, wenn die Schaltfläche **Weiter** deaktiviert ist.) 
23. Stellen Sie auf der Seite **Publish Items** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.     
24. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
25. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist. 

Einzelheiten zur Verwendung von Visual Studio zum Erstellen von Data Factory-Entitäten und ihrer Veröffentlichung in Azure finden Sie unter [Erstellen Ihrer ersten Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=AcomDC_0309_2016-->