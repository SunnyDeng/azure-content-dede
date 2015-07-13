<properties
	pageTitle="Szenarien für die Advanced Analytics Process and Technology in Azure Machine Learning | Microsoft Azure"
	description="Wählen Sie die geeigneten Szenarien für den erweiterten Predictive Analytics-Prozess in Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="msolhab;bradsev" />


# Szenarien für die Advanced Analytics Process and Technology in Azure Machine Learning

Dieser Artikel beschreibt die verschiedenen Beispieldatenquellen und -zielszenarien, die mit der Advanced Analytics Process and Technology (ADAPT) in Azure Machine Learning verarbeitet werden können. Das Beispiel veranschaulicht einige der Optionen in den Verarbeitungssequenzen, die von den Datenmerkmalen, Quellspeicherorten und Zielrepositorys in Azure abhängen.

Die **Entscheidungsstruktur**, mit deren Hilfe Sie die für Ihre Daten und Ziele geeigneten Beispielszenarien auswählen, wird im letzten Abschnitt vorgestellt.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


In jedem der folgenden Abschnitte wird ein Beispielszenario vorgestellt. Für jedes Szenario werden ein möglicher Ablauf für Datenwissenschaft oder erweiterte Analyse sowie die unterstützenden Azure-Ressourcen aufgeführt.

>[AZURE.NOTE]**Für alle folgenden Szenarien müssen Sie diese Schritte ausführen:**

*   [Erstellen eines Speicherkontos](storage-whatis-account.md)
*   [Erstellen eines Azure ML-Arbeitsbereichs](machine-learning/machine-learning-create-workspace.md)




## <a name="smalllocal"></a>Szenario 1: Kleines bis mittelgroßes tabellarisches Dataset in lokalen Dateien

![Kleine bis mittelgroße lokale Dateien][1]

#### Zusätzliche Azure-Ressourcen: keine

1.  Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

2.  Laden Sie ein Dataset hoch.

3.  Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren hochgeladenen Datasets beginnt.

## <a name="smalllocalprocess"></a>Szenario 2: Kleines bis mittelgroßes Dataset lokaler Dateien, die verarbeitet werden müssen

![Kleine bis mittelgroße lokale Dateien mit Verarbeitung][2]

#### Zusätzliche Azure-Ressourcen: Azure-VM (IPython Notebook-Server)

1.  Erstellen Sie eine Azure-VM, auf der IPython Notebook ausgeführt wird.

2.  Laden Sie Daten in einen Azure-Speichercontainer hoch.

3.  Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus dem Azure-Speichercontainer zugreifen.

4.  Transformieren Sie die Daten in eine bereinigte Tabellenform.

5.  Speichern Sie die transformierten Daten in Azure-Blobs.

6.  Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

7.  Lesen Sie die Daten aus den Azure-Blobs mithilfe des [Reader][reader]-Moduls.

8. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren aufgenommenen Datasets beginnt.

## <a name="largelocal"></a>Szenario 3: Großes Dataset lokaler Dateien, die auf Azure-Blobs ausgerichtet sind

![Große lokale Dateien][3]

#### Zusätzliche Azure-Ressourcen: Azure-VM (IPython Notebook-Server)

1.  Erstellen Sie eine Azure-VM, auf der IPython Notebook ausgeführt wird.

2.  Laden Sie Daten in einen Azure-Speichercontainer hoch.

3.  Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus Azure-Blobs zugreifen.

4.  Transformieren Sie die Daten bei Bedarf in eine bereinigte Tabellenform.

5.  Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf.

6.  Extrahieren Sie ein kleines bis mittelgroßes Datenbeispiel.

7.  Speichern Sie die Beispieldaten in Azure-Blobs.

8. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

9. Lesen Sie die Daten aus den Azure-Blobs mithilfe des [Reader][reader]-Moduls.

10. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren aufgenommenen Datasets beginnt.


## <a name="smalllocaltodb"></a>Szenario 4: Kleines bis mittelgroßes Dataset lokaler Dateien, das auf SQL Server in einer Azure-VM ausgerichtet ist

![Kleine bis mittelgroße lokale Dateien in SQL DB in Azure][4]

#### Zusätzliche Azure-Ressourcen: Azure-VM (SQL Server / IPython Notebook-Server)

1.  Erstellen Sie eine Azure-VM, auf der SQL Server und IPython Notebook ausgeführt werden.

2.  Laden Sie Daten in einen Azure-Speichercontainer hoch.

3.  Bereiten Sie die Daten in einem Azure-Speichercontainer mithilfe von IPython Notebook vor, und bereinigen Sie sie.

4.  Transformieren Sie die Daten bei Bedarf in eine bereinigte Tabellenform.

5.  Speichern Sie Daten in VM-lokalen Dateien (IPython Notebook wird auf dem virtuellen Computer ausgeführt, lokale Laufwerke beziehen sich auf VM-Laufwerke).

6.  Laden Sie Daten in einer SQL Server-Datenbank, die auf einem virtuellen Azure-Computer ausgeführt wird.

    a. Option 1: Verwenden von SQL Server Management Studio

		i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b. Option 2: Verwenden von IPython Notebook – für mittlere und größere Datasets nicht empfehlenswert

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf. Beachten Sie, dass die Features nicht in den Datenbanktabellen materialisiert werden müssen. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.

8. Legen Sie eine Größe für das Datenbeispiel fest, falls dies erforderlich und/oder gewünscht ist.

9. Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

10. Lesen Sie die Daten mithilfe des [Reader][reader]-Moduls direkt vom SQL Server. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die [Reader][reader]-Abfrage ein.

11. Erstellen Sie einen Azure Machine Learning-Versuchsablauf, der mit einem oder mehreren aufgenommenen Datasets beginnt.

## <a name="largelocaltodb"></a>Szenario 5: Großes Dataset in lokalen Dateien, Ziel-SQL Server in Azure VM

![Große lokale Dateien in SQL DB in Azure][5]

#### Zusätzliche Azure-Ressourcen: Azure-VM (SQL Server / IPython Notebook-Server)

1.  Erstellen Sie eine Azure-VM, auf der SQL Server und der IPython Notebook-Server ausgeführt werden.

2.  Laden Sie Daten in einen Azure-Speichercontainer hoch.

3.  (Optional) Bereiten Sie Daten vor, und bereinigen Sie sie.

    a. Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus Azure-Blobs zugreifen.

    b. Transformieren Sie die Daten bei Bedarf in eine bereinigte Tabellenform.

    c. Speichern Sie Daten in VM-lokalen Dateien (IPython Notebook wird auf dem virtuellen Computer ausgeführt, lokale Laufwerke beziehen sich auf VM-Laufwerke).

4.  Laden Sie Daten in einer SQL Server-Datenbank, die auf einem virtuellen Azure-Computer ausgeführt wird.

    a. Melden Sie sich beim virtuellen SQL Server-Computer an.

    b. Wenn Daten noch nicht gespeichert wurden, laden Sie Datendateien aus dem Azure-Speichercontainer in den lokalen VM-Ordner herunter.

    c. Führen Sie SQL Server Management Studio aus.

    d. Erstellen Sie eine Datenbank und Zieltabellen.

	e. Verwenden Sie eine Massenimportmethoden zum Laden der Daten.

    f. Wenn Tabellenverknüpfungen erforderlich sind, erstellen Sie Indizes, um Verknüpfungen zu beschleunigen.

 >[AZURE.NOTE]Zum schnelleren Laden großer Datenmengen empfiehlt es sich, die Erstellung partitionierter Tabellen und den Massenimport der Daten parallel durchzuführen. Weitere Informationen finden Sie unter [Paralleler Datenimport in partitionierte SQL-Tabellen](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf. Beachten Sie, dass die Features nicht in den Datenbanktabellen materialisiert werden müssen. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.

6.  Legen Sie eine Größe für das Datenbeispiel fest, falls dies erforderlich und/oder gewünscht ist.

7.  Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

8. Lesen Sie die Daten mithilfe des [Reader][reader]-Moduls direkt vom SQL Server. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die [Reader][reader]-Abfrage ein.

9. Einfacher Azure Machine Learning-Versuchsablauf, der mit einem hochgeladenen Dataset beginnt.

## <a name="largedbtodb"></a>Szenario 6: Großes Dataset in einer lokalen SQL Server-Datenbank, das auf SQL Server auf einem virtuellen Azure-Computer ausgerichtet ist

![Große lokale SQL DB in SQL DB in Azure][6]

#### Zusätzliche Azure-Ressourcen: Azure-VM (SQL Server / IPython Notebook-Server)

1.  Erstellen Sie eine Azure-VM, auf der SQL Server und der IPython Notebook-Server ausgeführt werden.

2.  Verwenden Sie eine der Datenexportmethoden, um die Daten aus SQL Server in Dumpdateien zu exportieren.

    a. Hinweis: Wenn Sie sich dazu entschließen, alle Daten aus der lokalen Datenbank zu verschieben, wählen Sie eine alternative (schnellere) Methode zum Verschieben der vollständigen Datenbank in die SQL Server-Instanz in Azure. Überspringen Sie die Schritte zum Exportieren von Daten, zum Erstellen der Datenbank und zum Laden/Importieren der Daten in die Zieldatenbank, und führen Sie die alternative Methode durch.

3.  Laden Sie Dumpdateien in den Azure-Speichercontainer hoch.

4.  Laden Sie die Daten in eine SQL Server-Datenbank auf einem virtuellen Azure-Computer.

    a. Melden Sie sich beim virtuellen SQL Server-Computer an.

    b. Laden Sie Datendateien aus einem Azure-Speichercontainer in den lokalen VM-Ordner herunter.

    c. Führen Sie SQL Server Management Studio aus.

    d. Erstellen Sie eine Datenbank und Zieltabellen.

	e. Verwenden Sie eine Massenimportmethoden zum Laden der Daten.

	f. Wenn Tabellenverknüpfungen erforderlich sind, erstellen Sie Indizes, um Verknüpfungen zu beschleunigen.

> [AZURE.NOTE]Zum schnelleren Laden großer Datenmengen führen Sie die Erstellung partitionierter Tabellen und den Massenimport der Daten parallel durch. Weitere Informationen finden Sie unter [Paralleler Datenimport in partitionierte SQL-Tabellen](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Erkunden Sie die Daten, und erstellen Sie Features nach Bedarf. Beachten Sie, dass die Features nicht in den Datenbanktabellen materialisiert werden müssen. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.

6.  Legen Sie eine Größe für das Datenbeispiel fest, falls dies erforderlich und/oder gewünscht ist.

7.  Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

8. Lesen Sie die Daten mithilfe des [Reader][reader]-Moduls direkt vom SQL Server. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die [Reader][reader]-Abfrage ein.

9. Einfacher Azure Machine Learning-Versuchsablauf, der mit einem hochgeladenen Dataset beginnt.

### Alternative Methode zum Kopieren einer vollständigen Datenbank aus einem lokalen SQL Server in Azure SQL-Datenbank

![Trennen lokaler DB und Anfügen an SQL DB in Azure][7]

#### Zusätzliche Azure-Ressourcen: Azure-VM (SQL Server / IPython Notebook-Server)

Um die gesamte SQL Server-Datenbank auf dem virtuellen SQL Server-Computer zu replizieren, kopieren Sie eine Datenbank von einem Speicherort/Server auf einen anderen, vorausgesetzt, dass die Datenbank vorübergehend offline geschaltet werden kann. Hierzu verwenden Sie die Objekt-Explorer-Benutzeroberfläche von SQL Server Management Studio oder die entsprechenden Transact-SQL-Befehle.

1. Trennen Sie die Datenbank am Quellspeicherort. Weitere Informationen finden Sie unter [Trennen einer Datenbank](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Kopieren Sie im Windows-Explorer oder in der Windows-Eingabeaufforderung die getrennten Datenbankdateien und Protokolldateien in den Zielspeicherort auf dem virtuellen SQL Server-Computer in Azure.
3. Fügen Sie die kopierten Dateien an die SQL Server-Zielinstanz an. Weitere Informationen finden Sie unter [Anfügen einer Datenbank](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Verschieben einer Datenbank durch Trennen und Anfügen (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Szenario 7: Big Data in lokalen Dateien, Zielstrukturdatenbank in Azure HDInsight Hadoop-Clustern

![Big Data in lokalen Zielstruktur][9]

#### Zusätzliche Azure-Ressourcen: Azure HDInsight Hadoop-Cluster und Azure-VM (IPython Notebook-Server)

1.  Erstellen Sie einen virtuellen Azure-Computer, auf dem der IPython Notebook-Server ausgeführt wird.

2.  Erstellen Sie einen Azure HDInsight Hadoop-Cluster.

3.  (Optional) Bereiten Sie Daten vor, und bereinigen Sie sie.

    a. Bereiten Sie die Daten in IPython Notebook vor, und bereinigen Sie sie, indem Sie auf Daten aus Azure-Blobs zugreifen.

    b. Transformieren Sie die Daten bei Bedarf in eine bereinigte Tabellenform.

    c. Speichern Sie Daten in VM-lokalen Dateien (IPython Notebook wird auf dem virtuellen Computer ausgeführt, lokale Laufwerke beziehen sich auf VM-Laufwerke).

4.  Laden Sie Daten in den Standardcontainer des in Schritt 2 ausgewählten Hadoop-Clusters.

5.  Laden Sie Daten in die Strukturdatenbank im Azure HDInsight Hadoop-Cluster.

    a. Melden Sie sich beim Hauptknoten des Hadoop-Clusters an.

    b. Öffnen Sie die Hadoop-Befehlszeile.

    c. Geben Sie das Strukturstammverzeichnis über den Befehl `cd %hive_home%\bin` in der Hadoop-Befehlszeile ein.

    d. Führen Sie die Strukturabfragen aus, um die Datenbank und die Tabellen zu erstellen, und laden Sie Daten aus dem Blobspeicher in die Strukturtabellen.

 	> [AZURE.NOTE]Wenn es sich um große Datenmengen handelt, können Benutzer die Strukturtabelle mit Partitionen erstellen. Benutzer können dann mithilfe einer `for`-Schleife in der Hadoop-Befehlszeile auf dem Hauptknoten Daten in die partitionierte Strukturtabelle laden.

6.  Erkunden Sie die Daten, und erstellen Sie in der Hadoop-Befehlszeile Features nach Bedarf. Beachten Sie, dass die Features nicht in den Datenbanktabellen materialisiert werden müssen. Beachten Sie nur die erforderliche Abfrage, um sie zu erstellen.

	a. Melden Sie sich beim Hauptknoten des Hadoop-Clusters an.

    b. Öffnen Sie die Hadoop-Befehlszeile.

    c. Geben Sie das Strukturstammverzeichnis über den Befehl `cd %hive_home%\bin` in der Hadoop-Befehlszeile ein.

	d. Führen Sie die Strukturabfragen in der Hadoop-Befehlszeile auf dem Hauptknoten des Hadoop-Clusters aus, um die Daten zu durchsuchen und Features nach Bedarf zu erstellen.

7.  Falls erforderlich und/oder gewünscht, entnehmen Sie passende Beispieldaten für Azure Machine Learning Studio.

8.  Melden Sie sich beim [Azure Machine Learning Studio](https://studio.azureml.net/) an.

9. Lesen Sie die Daten mithilfe des [Reader][reader]-Moduls direkt aus den `Hive Queries`. Fügen Sie die erforderliche Abfrage zum Extrahieren von Feldern, zum Erstellen von Features und zum Entnehmen von Datenproben bei Bedarf direkt in die [Reader][reader]-Abfrage ein.

10. Einfacher Azure Machine Learning-Versuchsablauf, der mit einem hochgeladenen Dataset beginnt.

## <a name="decisiontree"></a>Entscheidungsstruktur zur Auswahl des Szenarios
------------------------

Das folgende Diagramm fasst die oben beschriebenen Szenarien sowie die Advanced Analytics Process and Technology-Entscheidungen zusammen, die Sie zu jedem einzelnen Szenario führen. Beachten Sie, dass das Verarbeiten und Durchsuchen von Daten, die Entwicklung von Funktionen und das Erstellen von Stichproben in einer oder mehreren Methoden/Umgebungen – in der Quell-, Zwischen- und/oder Zielumgebung – erfolgen und nach Bedarf wiederholt durchgeführt werden kann. Das Diagramm dient nur zur Veranschaulichung einiger möglicher Abläufe und stellt keine erschöpfende Auflistung dar.

![Exemplarische Vorgehensweisen für den DS-Prozess][8]

### Erweiterte Analysen in Aktion – Beispiele

Umfassende exemplarische Vorgehensweisen zu Azure Machine Learning, die die Advanced Analytics Process and Technology (ADAPT) mit öffentlichen DataSets verwenden, finden Sie hier:


* [Advanced Analytics Process and Technology in Aktion: Verwenden von SQL Server](machine-learning/machine-learning-data-science-process-sql-walkthrough.md)
* [Advanced Analytics Process and Technology in Aktion – Verwenden von HDInsight-Hadoop-Clustern](machine-learning/machine-learning-data-science-process-hive-walkthrough.md)


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=July15_HO1-->