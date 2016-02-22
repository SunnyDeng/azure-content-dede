<properties 
	pageTitle="Entnehmen von Datenstichproben in Azure-Blobcontainern, SQL Server und Hive-Tabellen | Microsoft Azure" 
	description="Informationen zum Durchsuchen von Daten, die in verschiedenen Azure-Umgebungen gespeichert sind." 
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
	ms.date="02/07/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Entnehmen von Datenstichproben in Azure-Blobcontainern, SQL Server und Hive-Tabellen

## Einführung

Dieses Dokument beschreibt, wie Sie Stichproben von an drei Stellen gespeicherten Daten entnehmen, die normalerweise zum Analysieren und Modellieren von Daten im Cortana Analytics-Prozess verwendet werden:

- **Azure Blob-Containerdaten**: Stichproben werden durch programmgesteuertes Herunterladen entnommen, um dann anschließend mit Python-Code Stichproben zu erstellen.
- **SQL Server-Daten**: Stichproben werden mithilfe von SQL und der Python-Programmiersprache erstellt. 
- **Hive-Tabellendaten**: Stichproben werden mit Hive-Abfragen erstellt.

Das nachstehende **Menü** enthält Links zu Themen, die das Entnehmen von Datenstichproben aus den verschiedenen Azure Storage-Umgebungen beschreiben.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Diese Datenstichprobenaufgabe ist ein Teil des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Warum Datenstichproben entnehmen?

Wenn das Dataset, das Sie analysieren möchten, groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Dies erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Cortana-Analyseprozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.

<!---HONumber=AcomDC_0211_2016-->