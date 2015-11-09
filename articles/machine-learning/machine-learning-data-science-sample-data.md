<properties 
	pageTitle="Stichprobendaten im Cortana-Analyseprozess" 
	description="Informationen zum Durchsuchen von Daten in verschiedenen Speicherumgebungen." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Stichprobendaten im Cortana-Analyseprozess

Dieses **Menü** enthält Links zu Themen, die beschreiben, wie Datenstichproben aus verschiedenen Speicherumgebungen erstellt werden. Diese Aufgabe ist ein Teil des Cortana-Analyseprozesses (CAP).

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

## Einführung

Dieses Dokument behandelt das Erstellen von Stichproben aus Daten im Azure Blob-Speicher durch programmgesteuertes Herunterladen, um dann mit Python-Code Stichproben zu erstellen. Befolgen Sie dazu die folgenden Schritte:

Dieses Dokument beschreibt, wie Sie Stichproben von Daten entnehmen, die an drei Stellen gespeichert sind, die normalerweise im Cortana-Analyseprozess verwendet werden:

- **Azure Blob-Containerdaten**: Stichproben werden durch programmgesteuertes Herunterladen entnommen, um dann anschließend mit Python-Code Stichproben zu erstellen.
- **SQL Server-Daten**: Stichproben werden mithilfe von SQL und der Python-Programmiersprache erstellt. 
- **Hive-Tabellendaten**: Stichproben werden mit Hive-Abfragen erstellt.

<!---HONumber=Nov15_HO1-->