<properties 
	pageTitle="Planen einer erweiterten Azure Machine Learning-Analyseumgebung | Microsoft Azure" 
	description="Planen Sie Ihre erweiterte Analyseumgebung unter Berücksichtigung von Kernfragen." 
	services="machine-learning" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Planen einer erweiterten Azure Machine Learning-Analyseumgebung

Welches Szenario entspricht Ihrem Analyseproblem, wenn Sie sich darauf vorbereiten, eine Umgebung für erweiterte Analysen mit Azure Machine Learning einzurichten? Ihre Entscheidungen hinsichtlich der benötigten Ressourcen basieren auf der Art, der Größe und dem Quellspeicherort Ihrer Daten sowie dem Zielspeicherort dieser Daten in der Cloud. In diesem Artikel werden die Fragen behandelt, die Ihnen helfen sollen, Ihr Szenario zu bestimmen.

Nachdem Sie das relevante Szenario ermittelt haben, begleitet Sie der ADAPT-Workflow \(Advanced Analytics Process and Technology\), der im [Lernpfad: Erstellen von erweiterten Analyselösungen in Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) vorgestellt wurde, durch eine Reihe von Aufgaben. Dazu zählen das Abrufen eines Datasets über die Erstellung und Veröffentlichung eines Modells als Azure-Webdienst, den Anwendungen nutzen können.

In diesem Thema werden auch einige der Ressourcen und Tools aufgelistet, die bei diesem erweiterten Analyseprozess verwendet werden.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Beantworten Sie diese Fragen
Beantworten Sie diese Fragen, um zu bestimmen, mit welchem Szenario Sie arbeiten, bevor Sie Ihre Umgebung für erweiterte Analysen erstellen.

1. **Wo befindet sich Ihre Daten?** \(Dieser Speicherort wird als die ***Datenquelle*** bezeichnet.\) Beispiel:
	- Die Daten sind öffentlich über eine HTTP-Adresse verfügbar.
	- Die Daten befinden sich an einem lokalen/Netzwerkspeicherort.
	- Die Daten befinden sich in einer SQL Server-Datenbank.
	- Die Daten sind in einem Azure-Speichercontainer gespeichert.
2. **Wie sind Ihre Daten formatiert?** Beispiel:
    - Kommagetrennte oder tabstoppgetrennte Dateien, nicht komprimiert
    - Kommagetrennte oder tabstoppgetrennte Dateien, komprimiert
	- Komprimierte oder nicht komprimierte Azure-Blobs.
	- SQL Server-Tabellen.
3. **Wie umfangreich sind Ihre Daten?**
    - Klein: Weniger als 2 GB
    - Mittel: Mehr als 2 GB, aber weniger als 10 GB
	- Groß: Mehr als 10 GB
	- Sehr groß: Mehrere 100 GB
4. **Wie vertraut sind Sie mit den Daten?**
    - Müssen Sie zum Durchsuchen der Daten deren Schema, die Variablenverteilungen, fehlende Werte usw. ermitteln? 
	- Erfordern die Daten eine Vorverarbeitung oder Bereinigung, bevor sie in eine Tabellendarstellung umgewandelt werden können? 
5. **Planen Sie, alle Daten in Azure-Speicher zu verschieben? Sind Sie in der Lage dazu?**
    - Ja, ich plane, das gesamte Dataset zur Verarbeitung in die Cloud zu kopieren.
	- Nein, ich werde nur einen Teil der Daten nach Azure kopieren.
6. **Was ist Ihr bevorzugtes Azure-Cloudziel?** Beispiel:
	- Verschieben von Daten in Azure-Speicher-Blobs
	- Speichern von Daten in bereitstellbaren virtuellen Azure-Festplatten
	- Laden von Daten in eine SQL Server-Datenbank auf einem virtuellen Azure-Computer
	- Zuordnen von Daten zu Azure HDInsight Hive-Tabellen

## Wie sieht Ihr Szenario aus?
Nachdem Sie die Fragen im vorherigen Abschnitt beantwortet haben, können Sie bestimmen, welches Szenario für Ihren Fall am besten geeignet ist. Die Beispielszenarien werden unter [Szenarien für die erweiterte Analyse in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md) erläutert.

## Erweiterte Analyseressourcen in Azure
Je nach Szenario benötigen Sie möglicherweise einige der folgenden Tools und Ressourcen.

1.  Azure-Tools: 
	* 	[Azure PowerShell SDK](../install-configure-powershell.md) 
	* 	[Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  Virtuelle Azure-Computer mit SQL Server
3.  Azure HDInsight \(Hadoop\)
4.  Virtuelle Azure-Netzwerke für das Freigeben lokaler Dateien in Azure
5.  Azure Data Factory für das Planen von Datenverschiebungen






 

<!---HONumber=July15_HO5-->