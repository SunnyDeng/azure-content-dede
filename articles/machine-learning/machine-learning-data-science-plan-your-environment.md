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
	ms.date="05/29/2015" 
	ms.author="mohabib;bradsev" />


# Planen einer erweiterten Azure Machine Learning-Analyseumgebung

Sie müssen beim Einrichten einer erweiterten Azure Machine Learning-Analyseumgebung verschiedene Entscheidungen treffen. Die getroffene Auswahl sollte auf der Art, der Größe und dem Speicherort Ihrer Daten sowie dem Ziel für diese Daten in der Cloud beruhen. Der erweiterte Analyseprozess umfasst alle Aufgaben und führt von den ursprünglichen Daten in einer beliebigen Quelle bis zur Erstellung und Veröffentlichung eines Modells als Azure-Webdienst für die Nutzung in Anwendungen.

Der erweiterte Analyseprozessworkflow wird unter [Erstellen von erweiterten Analyselösungen in Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) gezeigt. Klicken Sie im Leitfaden auf die entsprechenden Artikel, um weitere Informationen zu den einzelnen Schritten im Leitfaden "Erweiterter Analyseprozess und Technologie" zu erhalten.

In diesem Artikel werden Fragen erläutert, die Sie beim Einrichten Ihrer erweiterten Analyseumgebung beachten sollten. Außerdem werden Ressourcen und Tools aufgelistet, die bei diesem Prozess hilfreich sind. Darüber hinaus enthält der Artikel Anleitungen zum Verwenden des Leitfadens "Erweiterter Analyseprozess und Technologie".

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Zu berücksichtigende Fragen

Bevor Sie mit dem Erstellen Ihrer erweiterten Analyseumgebung beginnen, sollten Sie die folgenden Fragen klären.

1. **Wo befindet sich Ihre Daten?** (Dieser Speicherort wird als die ***Datenquelle*** bezeichnet.) Beispiel:
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
5. **Planen Sie, alle Daten nach Azure verschieben? Sind Sie in der Lage dazu?**
    - Ja, ich plane, das gesamte DataSet zur Verarbeitung in die Cloud zu kopieren.
	- Nein, ich werde nur einen Teil der Daten nach Azure kopieren.
6. **Was ist Ihr bevorzugtes Azure-Cloudziel?** Beispiel:
	- Verschieben von Daten in Azure-Speicher-Blobs
	- Speichern von Daten in bereitstellbaren virtuellen Azure-Festplatten
	- Laden von Daten in eine SQL Server-Datenbank auf einem virtuellen Azure-Computer
	- Zuordnen von Daten zu Azure HDInsight Hive-Tabellen

## Erweiterte Analyseressourcen in Azure

Abhängig von Ihrem Szenario benötigen Sie außerdem Folgendes:

1.  Azure Tools: [Azure PowerShell SDK](../install-configure-powershell.md), [Azure Storage-Explorer](http://azurestorageexplorer.codeplex.com/), [AzCopy](../storage-use-azcopy.md) u. a.
2.  Virtuelle Azure-Computer mit SQL Server
3.  Azure HDInsight (Hadoop)
4.  Virtuelle Azure-Netzwerke für das Freigeben lokaler Dateien in Azure
5.  Azure Data Factory für das Planen von Datenverschiebungen


## Verwenden des Leitfadens "Erweiterter Analyseprozess und Technologie"

Der unter [Erstellen von erweiterten Analyselösungen in Azure ](machine-learning-data-science-how-to-create-machine-learning-service.md) bereitgestellte Leitfaden bietet eine Vielzahl von Big Data-Übungen. Die Übersicht zeigt die wichtigsten Schritte in einem typischen erweiterten Analyseworkflow. Nicht alle Schritte sind bei jeder Data Science-Aufgabe erforderlich. Darüber hinaus ist der Ablaufplan fortlaufend, während die Abfolge der Schritte in bestimmten Szenarios abweichen kann. Anhand Ihrer Antworten auf die Fragen oben können Sie entscheiden, welche Schritte für Ihr Szenario relevant sind, wann sie im Ablauf durchgeführt werden müssen und unter welchen Bedingungen mehrere Durchläufe der Schritte erforderlich sind.

Unter [Szenarien für den erweiterten Analyseprozess in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md) finden Sie auf der ursprünglichen Datengröße basierende Beispielszenarien, den Speicherort von Datenquellen und das Zielrepository in Azure.


 

<!---HONumber=July15_HO2-->