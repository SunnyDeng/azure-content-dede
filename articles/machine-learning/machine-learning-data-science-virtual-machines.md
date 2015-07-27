<properties
	pageTitle="Virtuelle Data Science-Computer in Azure | Microsoft Azure"
	description="Einrichten eines virtuellen Computers für Data Science"
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
	ms.author="mohabib;xibingao;bradsev" />

# Virtuelle Data Science-Computer in Azure

Es können mehrere Arten von virtuellen Azure-Computern bereitgestellt und als Teil einer cloudbasierten Data Science-Umgebung konfiguriert werden. Die Entscheidung, welche Art von virtuellem Computer Sie verwenden, hängt von der Art und der Menge der Daten ab, die mit Machine Learning modelliert werden sollen, sowie vom Ziel für die Daten in der Cloud. Hilfe bei dieser Entscheidung finden Sie unter [Planen der Azure Machine Learning Data Science-Umgebung](machine-learning-data-science-plan-your-environment.md). Einen Katalog mit einigen Szenarien für die erweiterte Analyse finden Sie unter [Szenarien für Advanced Analytics Process and Technology (ADAPT) in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md).

Sie erhalten Anweisungen zum Einrichten einer Azure-VM und einer Azure-VM mit SQL Server als IPython Notebook-Server. Der virtuelle Windows-Computer wird mit Unterstützung von Tools wie IPython Notebook, Azure-Speicher-Explorer und AzCopy sowie anderer Hilfsprogramme, die für Data Science-Projekte hilfreich sind, konfiguriert. Azure-Speicher-Explorer und AzCopy stellen z. B. hilfreiche Möglichkeiten zum Hochladen von Daten von Ihrem lokalen Computer in den Azure-Speicher oder das Herunterladen aus dem Speicher auf Ihren lokalen Computer bereit. Es werden zwei Verfahren beschrieben:

* Unter [Einrichten eines virtuellen Azure-Computers als IPython Notebook-Server für die erweiterte Analyse](machine-learning-data-science-setup-virtual-machine.md) wird beschrieben, wie Sie mit IPython Notebook und anderen Tools einen virtuellen Azure-Computer bereitstellen, der für Data Science-Fälle eingesetzt wird, bei der die Daten in einem anderen Azure-Speicher als SQL gespeichert werden.

* Unter [Einrichten einer Azure SQL Server-VM als IPython Notebook-Server für die erweiterte Analyse](machine-learning-data-science-setup-sql-server-virtual-machine.md) wird beschrieben, wie Sie mit IPython Notebook und anderen Tools eine Azure SQL Server-VM bereitstellen, die für Data Science-Fälle eingesetzt wird, bei der die Daten in einer SQL-Datenbank gespeichert werden.

Nach der Bereitstellung und Konfiguration können diese virtuellen Computer als IPython Notebook-Server für das Untersuchen und Verarbeiten von Daten sowie für andere Aufgaben in Verbindung mit Azure Machine Learning und dem Cloud Data Science-Vorgang verwendet werden. Die nächsten Schritte im Data Science-Prozess sind unter [Leitfaden: Erweiterte Datenverarbeitung in Azure](machine-learning-data-science-advanced-data-processing.md) aufgeführt. Dazu gehören auch das Verschieben von Daten nach SQL Server oder HDInsight sowie das Verarbeiten und Extrahieren von Stichproben für die Gewinnung von Informationen aus den Daten mithilfe von Azure Machine Learning.


> [AZURE.NOTE]Virtuelle Azure-Computer werden **nach Nutzung abgerechnet**. Damit Sie nicht für ungenutzte virtuelle Computer bezahlen müssen, müssen Sie diese im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) in den Status **Angehalten (Zuordnung aufgehoben)** versetzen. Eine Schrittanleitung dazu, wie Sie die Zuordnung Ihrer virtuellen Computer aufheben, finden Sie unter [Herunterfahren und Freigeben von nicht genutzten virtuellen Computern](machine-learning-data-science-setup-virtual-machine.md#shutdown).
 

<!---HONumber=July15_HO2-->