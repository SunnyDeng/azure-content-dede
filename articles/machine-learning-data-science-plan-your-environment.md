<properties 
	pageTitle="Planen der Cloud Data Science-Umgebung | Azure" 
	description="Planen der Cloud Data Science-Umgebung" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev"/> 


# Planen der Azure Machine Learning Data Science-Umgebung

Sie müssen beim Einrichten einer Azure Machine Learning Data Science-Umgebung einige Entscheidungen treffen. Die getroffene Auswahl sollte auf der Art, der Größe und dem Speicherort Ihrer Daten sowie dem Ziel für diese Daten in der Cloud beruhen. Der Cloud Data Science-Ablauf umfasst alle Aufgaben von den ursprünglichen Daten in einer beliebigen Quelle bis zur Erstellung und Veröffentlichung eines Modells als Azure-Webdienst für die Nutzung in Anwendungen.

Sie finden den Cloud Data Science-Ablaufplan im **[Leitfaden: Cloud Data Science-Ablauf für das maschinelle Lernen](machine-learning-data-science-how-to-create-machine-learning-service.md)**. Weitere Informationen zu den einzelnen Schritten im Data Science-Ablauf erhalten Sie, indem Sie auf die entsprechenden Elemente im [Plan](machine-learning-data-science-how-to-create-machine-learning-service.md) klicken.

In diesem Artikel werden Fragen erläutert, die Sie beim Einrichten Ihrer Cloud Data Science-Umgebung beachten sollten. Außerdem werden Ressourcen und Tools aufgelistet, die bei diesem Vorgang hilfreich sind. Darüber hinaus enthält der Artikel Anleitungen zum Verwenden des Cloud Data Science-Ablaufplans.

## Zu berücksichtigende Fragen

Bevor Sie mit dem Erstellen Ihrer Cloud Data Science-Umgebung beginnen, sollten Sie die folgenden Fragen klären.

1. **Wo befinden sich Ihre Daten?** (Dieser Speicherort wird als die ***Datenquelle*** bezeichnet.) Beispiel:
	- Die Daten sind öffentlich über eine HTTP-Adresse verfügbar.
	- Die Daten befinden sich an einem lokalen/Netzwerkspeicherort.
	- Die Daten befinden sich in einer SQL Server-Datenbank.
	- Die Daten sind in einem Azure-Speichercontainer gespeichert.
2. **Wie sind die Daten formatiert?** Beispiel:
    - Kommagetrennte oder tabstoppgetrennte Dateien, nicht komprimiert
    - Kommagetrennte oder tabstoppgetrennte Dateien, komprimiert
	- Komprimierte oder nicht komprimierte Azure-Blobs.
	- SQL Server-Tabellen.
3. **Wie umfangreich sind Ihre Daten?**
    - Klein: Weniger als 2 GB
    - Mittel: Mehr als 2 GB, aber weniger als 10 GB
	- Groß: Mehr als 10 GB
	- Sehr groß: Hunderte GB
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

## Cloud Data Science-Ressourcen in Azure

Abhängig von Ihrem Szenario benötigen Sie außerdem Folgendes:

1.  Azure-Tools: [Azure PowerShell SDK](install-configure-powershell.md), [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/), [AzCopy](storage-use-azcopy.md) und andere
2.  Virtuelle Azure-Computer mit SQL Server
3.  Azure HDInsight (Hadoop)
4.  Virtuelle Azure-Netzwerke für das Freigeben lokaler Dateien in Azure
5.  Azure Data Factory für das Planen von Datenverschiebungen


## Verwenden des Cloud Data Science-Ablaufplans

Der [Cloud Data Science-Ablaufplan](machine-learning-data-science-how-to-create-machine-learning-service.md) stellt verschiedene Data Science-Aufgaben dar. Der Plan zeigt die wichtigsten Schritte in einem typischen Data Science-Workflow. Nicht alle Schritte sind bei jeder Data Science-Aufgabe erforderlich. Darüber hinaus ist der Ablaufplan fortlaufend, während die Abfolge der Schritte in bestimmten Szenarios abweichen kann. Anhand Ihrer Antworten auf die Fragen oben können Sie entscheiden, welche Schritte für Ihr Szenario relevant sind, wann sie im Ablauf durchgeführt werden müssen und unter welchen Bedingungen mehrere Durchläufe der Schritte erforderlich sind.

Beispielszenarios basierend auf dem Datenvolumen der Quelle, ihrem Speicherort und dem Ziel-Repository in Azure finden Sie unter [Cloud Data Science Process in Azure Machine Learning](hmachine-learning-data-science-plan-sample-scenarios.md) (in englischer Sprache).



<!--HONumber=49-->