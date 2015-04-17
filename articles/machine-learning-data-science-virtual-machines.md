<properties 
	pageTitle="Einrichten eines virtuellen Computers oder eines virtuellen Computers mit SQL Server in Azure für Data Science" 
	description="Einrichten eines virtuellen Computers für Data Science" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Einrichten eines virtuellen Computers oder eines virtuellen Computers mit SQL Server in Azure für Data Science

Es können mehrere Arten von virtuellen Azure-Computern bereitgestellt und als Teil einer Cloud-basierten Data Science-Umgebung konfiguriert werden. Die Entscheidung, welche Art von virtuellem Computer Sie verwenden, hängt von der Art und der Menge der Daten ab, die mit Machine Learning modelliert werden sollen, sowie vom Ziel für die Daten in der Cloud. Hilfe bei dieser Entscheidung finden Sie unter [Planen der Azure Machine Learning Data Science-Umgebung](machine-learning-data-science-plan-your-environment.md). In diesem Artikel werden zwei Szenarios behandelt.

Sie erhalten Anweisungen zum Einrichten einer Azure-VM und einer Azure-VM mit SQL Server als IPython Notebook-Server. Die virtuellen Computer werden unter Windows ausgeführt und mit Unterstützung von Tools wie Azure-Speicher-Explorer und AzCopy sowie anderer Pakete, die für Data Science-Projekte hilfreich sind, konfiguriert. Azure-Speicher-Explorer und AzCopy stellen z. B. hilfreiche Möglichkeiten zum Hochladen von Daten von Ihrem lokalen Computer in den Azure-Speicher oder das Herunterladen aus dem Speicher auf Ihren lokalen Computer bereit. Es werden zwei Verfahren beschrieben:

* [Einrichten eines virtuellen Azure-Computers für Data Science](machine-learning-data-science-setup-virtual-machine.md) beschreibt, wie Sie mit IPython Notebook und anderen Tools einen virtuellen Azure-Computer bereitstellen, der für Data Science verwendet wird, wenn die Daten in einem anderen Azure-Speicher als SQL gespeichert werden. 

* [Einrichten eines virtuellen Computers mit SQL Server in Azure für Data Science](machine-learning-data-science-setup-sql-server-virtual-machine.md) beschreibt, wie Sie mit IPython Notebook und anderen Tools einen virtuellen Computer mit SQL Server in Azure für Data Science bereitstellen, wenn die Daten in einer SQL-Datenbank gespeichert werden.

Nach der Bereitstellung und Konfiguration können diese virtuellen Computer als IPython Notebook-Server für das Durchsuchen und Verarbeiten von Daten und für andere Aufgaben in Verbindung mit Azure Machine Learning und dem Cloud Data Science-Vorgang verwendet werden. Dazu gehören auch das Verschieben von Daten nach HDInsight und das Verarbeiten und Extrahieren von Stichproben für das Trainieren anhand der Daten mit Azure Machine Learning.

* Informationen zum Verschieben von Daten aus dem Azure-Blob-Speicher nach HDInsight finden Sie unter [Erstellen und Laden von Daten in Hive-Tabellen aus Azure-Blob-Speicher](machine-learning-data-science-hive-tables.md).
* Weitere Informationen zum Verarbeiten von Daten in HDInsight mit Hive-Abfragen finden Sie unter [Übermitteln von Hive-Abfragen an HDInsight Hadoop-Cluster in Cloud Data Science](machine-learning-data-science-hive-queries.md).
* Informationen zum Extrahieren von Stichproben aus Daten in HDInsight finden Sie unter [Extrahieren von Stichproben aus Daten in Azure HDInsight Hive-Tabellen](machine-learning-data-science-sample-data-hive.md).


> [AZURE.NOTE] Virtuelle Azure-Computer werden **nach Nutzung abgerechnet**. Damit Sie nicht für ungenutzte virtuelle Computer bezahlen müssen, müssen Sie diese im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) in den Status **Angehalten (Freigegeben)** versetzen. Schrittweise Anweisungen zur Freigabe Ihrer virtuellen Computer finden Sie unter [Herunterfahren und Freigeben von nicht genutzten virtuellen Computern](machine-learning-data-science-setup-virtual-machine.md#shutdown). 




<!--HONumber=49-->