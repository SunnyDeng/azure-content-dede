<properties 
	pageTitle="Tutorial: Kopieren von Daten aus einem Azure-Blob in Azure SQL" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Beispieldatenpipeline erstellen, die Daten von einem Blob an eine Azure SQL-Datenbankinstanz kopiert." 
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
	ms.date="07/27/2015" 
	ms.author="spelluru"/>


# Lernprogramm: Erstellen und Überwachen einer Data Factory mit Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##Inhalt dieses Lernprogramms
In diesem Lernprogramm erstellen Sie zunächst mithilfe des Azure-Vorschauportals eine Azure Data Factory mit dem Namen **ADFTutorialDataFactoryVS** und verwenden dann Visual Studio 2013 für folgende Vorgänge:

1. Erstellen von zwei verknüpften Diensten: **AzureStorageLinkedService1** und **AzureSqlinkedService1**. Der AzureStorageLinkedService1 verknüpft einen Azure-Speicher, und AzureSqlLinkedService1 verknüpft eine Azure SQL-Datenbank mit der Data Factory: **ADFTutorialDataFactoryVS**. Die Eingabedaten für die Pipeline befinden sich in einem Blobcontainer im Azure-Blobspeicher. Ausgabedaten werden in einer Tabelle in der Azure SQL-Datenbank gespeichert. Daher fügen Sie diese beiden Datenspeicher als verknüpfte Dienste der Data Factory hinzu.
2. Erstellen von zwei Data Factory-Tabellen: **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten darstellen, die in den Datenspeichern gespeichert sind. Für die Tabelle "EmpTableFromBlob" geben Sie den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Für die Tabelle "EmpSQLTable" geben Sie die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden sollen. Sie können auch andere Eigenschaften wie z. B. die Struktur der Daten, die Verfügbarkeit von Daten usw. angeben.
3. Erstellen einer Pipeline mit dem Namen **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. Die Pipeline weist eine **Kopieraktivität** auf, die zum Kopieren von Eingabedaten aus dem Azure-Blob in die Azure SQL-Tabelle verwendet wird 


## <a name="CreateDataFactory"></a>Erstellen einer Azure Data Factory
In diesem Schritt erstellen Sie im Azure-Vorschauportal eine Azure Data Factory mit dem Namen **ADFTutorialDataFactoryVS**.

1.	Nach der Anmeldung beim [Azure-Vorschauportal](http://portal.azure.com) klicken Sie links unten auf **NEU**, wählen **Datenanalyse** auf dem Blatt **Erstellen** aus und klicken auf dem Blatt **Datenanalyse** auf **Data Factory**. 

	![Neu->DataFactory](./media/data-factory-get-started-using-vs/NewDataFactoryMenu.png)

6. Gehen Sie auf dem Blatt **Neue Data Factory** wie folgt vor:
	1. Geben Sie **ADFTutorialDataFactoryVS** als **Namen** ein. 
	
  		![Blatt "Neue Data Factory"](./media/data-factory-get-started-using-vs/getstarted-new-data-factory.png)
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und führen Sie die folgenden Schritte aus:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie **ADFTutorialResourceGroup** auf dem Blatt **Ressourcengruppe erstellen** als **Namen** der Ressourcengruppe ein, und klicken Sie auf **OK**. 

			![Ressourcengruppe erstellen](./media/data-factory-get-started-using-vs/CreateNewResourceGroup.png)

		Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-overview.md).  
7. Auf dem Blatt **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.
8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

	Der Name der Azure Data Factory muss global eindeutig sein. Wenn die Fehlermeldung **Data factory name "ADFTutorialDataFactoryVS" is not available** angezeigt wird, ändern Sie den Namen der Data Factory (z. B.in "IhrNameADFTutorialDataFactoryVS") und wiederholen den Vorgang. Verwenden Sie diesen Namen beim Ausführen der restlichen Schritte in diesem Lernprogramm anstelle von "ADFTutorialFactory". Im Thema [Data Factory – Benennungsregeln][data-factory-naming-rules] finden Sie Benennungsregeln für Data Factory-Artefakte.
	 
	![Data Factory-Name nicht verfügbar](./media/data-factory-get-started-using-vs/getstarted-data-factory-not-available.png)

9. Klicken Sie auf der linken Seite auf **BENACHRICHTIGUNGEN**, und suchen Sie nach Benachrichtigungen zum Erstellungsprozess. Klicken Sie auf **X**, um das Blatt **Benachrichtigungen** zu schließen, falls es geöffnet ist.
10. Nach Abschluss der Erstellung wird das Blatt **DATA FACTORY** wie unten dargestellt angezeigt:

    ![Data Factory-Startseite](./media/data-factory-get-started-using-vs/getstarted-data-factory-home-page.png)

## Erstellen und Bereitstellen von Daten Factory-Entitäten mit Visual Studio 

### Voraussetzungen
Sie müssen Folgendes auf Ihrem Computer installiert haben: – Visual Studio 2013 – Herunterladen von Azure SDK für Visual Studio 2013. Navigieren Sie zur [Azure-Downloadseite](http://azure.microsoft.com/downloads/), und klicken Sie auf **VS 2013 Installieren** im Abschnitt **.NET**.

### Exemplarische Vorgehensweise

#### Erstellen des Visual Studio-Projekts 
1. Starten Sie **Visual Studio 2013**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** sollte angezeigt werden.  
2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Empty Data Factory Project**. Wenn die Vorlage DataFactory nicht angezeigt wird, schließen Sie Visual Studio, installieren Sie Azure SDK für Visual Studio 2013, und öffnen Sie Visual Studio erneut.  

	![Dialogfeld "Neues Projekt"](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Füllen Sie für das Projekt die Felder **Name**, **Speicherort** und **Lösung** aus, und klicken Sie auf**OK**.

	![Projektmappen-Explorer](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. Ein Datenspeicher kann ein Azure-Speicher, eine Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank sein.

In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **AzureStorageLinkedService1** und **AzureSqlLinkedService1**. Der verknüpfte Dienst "AzureStorageLinkedService1" verknüpft ein Azure-Speicherkonto und der Dienst "AzureSqlLinkedService" eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory**.

##### Erstellen des mit Azure Storage verknüpften Diensts

4. Klicken Sie mit der rechten Maustaste auf **verknüpfte Dienste** im Projektmappen-Explorer, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.      
5. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure Storage Linked Service** aus der Liste aus, und klicken Sie auf **Hinzufügen**. 

	![Neuer verknüpfter Dienst](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Ersetzen Sie **accountname** und **accountkey** durch den Namen Ihres Azure-Speicherkontos und den entsprechenden Schlüssel.

	![Mit Azure-Speicher verknüpfter Dienst](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Speichern Sie die Datei **AzureStorageLinkedService1.json**.

#### Erstellen des mit Azure SQL verknüpften Diensts

5. Klicken Sie mit der rechten Maustaste erneut auf den Knoten **Verknüpfte Dienste** im **Projektmappen-Explorer**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
6. Wählen Sie dieses Mal **Azure SQL Linked Service** aus, und klicken Sie dann auf **Hinzufügen**. 
7. Ersetzen Sie in der Datei **AzureSqlLinkedService1.json** die Werte **servername**, **databasename**, \*\***username@servername** und **password** durch die betreffenden Angaben für Azure SQL-Server, -Datenbank, -Benutzerkonto und -Kennwort.
8.  Speichern Sie die Datei **AzureSqlLinkedService1.json**. 


### Erstellen von Eingabe- und Ausgabetabellen
Im vorherigen Schritt haben Sie die verknüpften Dienste **AzureStorageLinkedService1** und **AzureSqlLinkedService1** erstellt, um ein Azure-Speicherkonto und eine Azure SQL-Datenbank mit der Data Factory **ADFTutorialDataFactory** zu verknüpfen. In diesem Schritt definieren Sie die beiden Data Factory-Tabellen **EmpTableFromBlob** und **EmpSQLTable**, die Ein- und Ausgabedaten in den Datenspeichern darstellen, auf die "AzureStorageLinkedService1" und "AzureSqlLinkedService1" verweisen. Für die Tabelle "EmpTableFromBlob" geben Sie den Blobcontainer an, der ein Blob mit den Quelldaten enthält. Für die Tabelle "EmpSQLTable" geben Sie die SQL-Tabelle an, in der die Ausgabedaten gespeichert werden sollen.

#### Erstellen der Eingabetabelle

9. Klicken Sie mit der rechten Maustaste auf **Tabellen** im **Projektmappen-Explorer**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
10. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure-Blob** aus, und klicken Sie auf **Hinzufügen**.   
10. Ersetzen Sie den JSON-Text durch den folgenden Text, und speichern Sie die **AzureBlobLocation1.json**-Datei. 

		{
    		"name": "EmpTableFromBlob",
        	"properties":
        	{
            	"structure":  
                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "AzureStorageLinkedService1"
	            },
	            "availability": 
	            {
	                "frequency": "Hour",
	                "interval": 1,
	                "waitOnExternal": {}
                }
	        }
		}

#### Erstellen der Ausgabetabelle

11. Klicken Sie mit der rechten Maustaste erneut auf **Tabellen** im **Projektmappen-Explorer**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
12. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Azure SQL** aus, und klicken Sie auf **Hinzufügen**. 
13. Ersetzen Sie den JSON-Text durch den folgenden JSON-Text, und speichern Sie die Datei **AzureSqlTableLocation1.json**.

		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSqlTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService1"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

#### Erstellen der Pipeline 
Sie haben bisher verknüpfte Ein-/Ausgabe-Dienste und -Tabellen erstellt. Nun erstellen Sie eine Pipeline mit einer **Kopieraktivität**, um Daten aus dem Azure-Blob in Azure SQL-Datenbank zu kopieren.


1. Klicken Sie mit der rechten Maustaste auf **Pipelines** im **Projektmappen-Explorer**, zeigen Sie auf **Hinzufügen** und klicken Sie auf**Neues Element**.  
15. Wählen Sie **Copy Data Pipeline** im Dialogfeld **Neues Element hinzufügen**, und klicken Sie auf **Hinzufügen**. 
16. Ersetzen Sie den JSON-Text durch den folgenden JSON-Text, und speichern Sie die Datei **CopyActivity1.json**.
			
		 {
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from a blob to Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink",
		                        "writeBatchSize": 10000,
		                        "writeBatchTimeout": "60:00:00"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		
		        "start": "2015-07-12T00:00:00Z",
		        "end": "2015-07-13T00:00:00Z",
		        "isPaused": false
		    }
		} 

#### Veröffentlichen/Bereitstellen der Data Factory-Entitäten
  
18. Wählen Sie im Symbolleistenbereich durch Klicken mit der rechten Maustaste die Option **Data Factory** aus, um die Symbolleiste "Data Factory" zu aktivieren, falls diese noch nicht aktiviert ist. 
19. Klicken Sie in der Symbolleiste **Data Factory** auf das **Dropdownlistenfeld**, um alle Data Factorys in Ihrem Azure-Abonnement anzuzeigen. Wenn das Dialogfeld **Anmelden bei Visual Studio** angezeigt wird: 
	20. Geben Sie das **E-Mail-Konto** und das **Kennwort** für das Azure-Abonnement ein, in dem die Data Factory erstellt werden soll, und klicken Sie auf **Anmelden**.
	21. Nachdem die Anmeldung abgeschlossen wurde, sollten alle Data Factorys des betreffenden Azure-Abonnements angezeigt werden. In diesem Lernprogramm erstellen Sie eine neue Data Factory.       
22. Wählen Sie in der Dropdownliste die Option **ADFTutorialFactoryVS** aus, und klicken Sie auf die Schaltfläche **Veröffentlichen**, um die verknüpften Dienste und Datasets und die Pipeline bereitzustellen bzw. zu veröffentlichen.    

	![Schaltfläche "Veröffentlichen"](./media/data-factory-get-started-using-vs/publish.png)

23. Der Status der Veröffentlichung sollte im oben gezeigten Fenster "Data Factory Task List" angezeigt werden. Vergewissern Sie sich, dass die Veröffentlichung erfolgreich war.

## Verwenden Sie Server-Explorer, um Data Factory-Entitäten zu überprüfen

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht** und dann auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster erst die Option **Azure** und dann **Data Factory**. Wenn **Anmelden bei Visual Studio** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen über alle Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt. ![Server-Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Durch Klicken mit der rechten Maustaste auf eine Data Factory und Auswählen von "Export Data Factory to New Project" können Sie anhand einer vorhandenen Data Factory ein Visual Studio-Projekt erstellen. ![Exportieren einer Data Factory in ein Visual Studio-Projekt](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Aktualisieren von Data Factory-Tools für Visual Studio
Um die Azure Data Factory-Tools für Visual Studio zu aktualisieren, führen Sie folgende Schritte aus:

1. Klicken Sie im Menü auf **Tools**, und wählen Sie**Erweiterungen und Updates** aus. 
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
4. Wählen Sie **Azure Data Factory tools for Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools. 

Unter [Überwachen von Datasets und Pipelines](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline) finden Sie eine Anleitung zum Überwachen der in dieser Anleitung erstellten Pipeline und Datasets über das Azure-Vorschauportal.

<!---HONumber=August15_HO6-->