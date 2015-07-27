<properties 
	pageTitle="Erste Schritte mit Azure Storage – Einstieg in fünf Minuten | Microsoft Azure" 
	description="So finden Sie schnell den Einstieg in Microsoft Azure-Blobs, -Tabellen und -Warteschlangen mithilfe von Azure-Schnellstarts und Visual Studio und dem Speicheremulator. Führen Sie innerhalb von fünf Minuten Ihre erste Azure Storage-Anwendung aus." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="05/28/2015" 
	ms.author="tamram;selcint"/>

# Erste Schritte mit Azure Storage – Einstieg in fünf Minuten 

Der Einstieg in die Entwicklung mit Azure Storage ist einfach. In diesem Lernprogramm erfahren Sie, wie Ihre Anwendungen in Azure Storage in kürzester Zeit betriebsbereit sind. Wir zeigen Ihnen zwei Szenarien für einen einfachen Einstieg in Azure Storage:

- [Lokales Ausführen Ihrer ersten Azure Storage-Anwendung im Azure-Speicheremulator](#run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator)
- [Ausführen Ihrer ersten Azure Storage-Anwendung über Azure Storage in der Cloud](#run-your-first-azure-storage-application-against-azure-storage-in-the-cloud)

Wenn Sie mehr über Azure Storage erfahren möchten, bevor Sie am Code arbeiten, finden Sie die entsprechenden Informationen unter [Nächste Schritte](#next-steps).

## Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie beginnen:

1. Zum Kompilieren und Erstellen der Anwendung muss [Visual Studio](https://www.visualstudio.com/) auf Ihrem Computer installiert sein. 
2. Installieren Sie die neueste Version von [Azure SDK für .NET](http://azure.microsoft.com/downloads/). Das SDK enthält die Azure-Schnellstart-Beispielprojekte, den Azure-Speicheremulator und die [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).
3. Stellen Sie sicher, dass [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) auf Ihrem Computer installiert ist, da es von den Azure-Schnellstart-Beispielprojekte benötigt wird, die wir in diesem Lernprogramm verwenden. Wenn Sie nicht sicher sind, welche Version von .NET Framework auf dem Computer installiert ist, finden Sie entsprechende Informationen unter [Gewusst wie: Feststellen, welche Versionen von .NET Framework installiert sind](https://msdn.microsoft.com/vstudio/hh925568.aspx). Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die Windows-Taste, und geben Sie **Systemsteuerung** ein. Klicken Sie auf **Programme** > **Programme und Funktionen**, und ermitteln Sie, ob .NET Framework 4.5 unter den installierten Programme aufgeführt ist.

Die neueste Version der Azure Storage Client Library-Binärdateien erhalten Sie bei [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).


## Lokales Ausführen Ihrer ersten Azure Storage-Anwendung im Azure-Speicheremulator

Wenn Sie eine Anwendung entwickeln, die Azure Storage verwendet, können Sie sie im [Azure-Speicheremulator](storage-use-emulator.md) ausführen. Der Speicheremulator bietet eine lokale Umgebung, die die Azure-Blob-, -Warteschlangen- und -Tabellenspeicherdienste für Entwicklungszwecke emuliert. Sie können den Speicheremulator verwenden, um Ihre Anwendung lokal zu testen, ohne ein Azure-Abonnement oder ein Speicherkonto zu benötigen und ohne dass Kosten anfallen.

Um dies auszuprobieren, erstellen wir nun eine einfache Azure Storage-Anwendung mithilfe eines der Azure-Schnellstart-Beispielprojekte in Visual Studio. Dieses Lernprogramm konzentriert sich auf Beispielprojekte mit **Azure-Blobspeicher**, **Azure-Tabellenspeicher** und **Azure-Warteschlangenspeicher**.

1. Starten Sie Visual Studio.
2. Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3. Klicken Sie im Dialogfeld **Neues Projekt** auf **Installiert** > **Vorlagen** > **Visual C#** > **Cloud** > **Schnellstarts** > **Data Services**.
	- 3\.a. Wählen Sie eine der folgenden Vorlagen aus: Azure-Blobspeicher, Azure-Tabellenspeicher oder Azure-Warteschlangenspeicher. 
	- 3\.b. Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.	
	- 3\.c. Geben Sie einen Namen für das Projekt an, und erstellen Sie die neue Visual Studio-Projektmappe, wie hier zu sehen:
	
	![Azure-Schnellstarts][Image1]

Vor dem Ausführen der Anwendung sollten Sie den Quellcode überprüfen. Um den Code zu anzuzeigen, wählen Sie in Visual Studio im Menü **Ansicht** die Option **Projektmappen-Explorer** aus. Doppelklicken Sie auf die Datei "Program.cs".

Führen Sie nun die Beispielanwendung im Azure-Speicheremulator aus:

1.	Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die Windows-Taste, suchen Sie nach *Azure-Speicheremulator* und starten Sie die Anwendung.
2.	Wählen Sie in Visual Studio im Menü **Build** die Option **Projektmappe erstellen** aus. 
3.	Drücken Sie im Menü **Debuggen** auf **F11**, um die Projektmappe Schritt für Schritt auszuführen, oder auf **F5**, um die Projektmappe vollständig auszuführen.

## Ausführen Ihrer ersten Azure Storage-Anwendung über Azure Storage in der Cloud

Für die Ausführung über Azure Storage in der Cloud benötigen Sie ein Azure-Abonnement und ein Speicherkonto, Sie, sofern noch nicht vorhanden:

- Informationen zum Erhalt eines Azure-Abonnements finden Sie unter [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/), [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/) und [Angebote für Mitglieder](http://azure.microsoft.com/pricing/member-offers/) (für Mitglieder von MSDN, Microsoft Partner Network, BizSpark und anderen Microsoft-Programmen).
- Informationen zum Erstellen eines Speicherkontos in Azure finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](storage-create-storage-account.md).

Sobald das Konto eingerichtet ist, erstellen wir eine einfache Azure Storage-Anwendung mithilfe eines der Azure-Schnellstart-Beispielprojekte in Visual Studio. Dieses Lernprogramm konzentriert sich auf Beispielprojekte mit **Azure-Blobspeicher**, **Azure-Tabellenspeicher** und **Azure-Warteschlangenspeicher**:

1. Starten Sie Visual Studio.
2. Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3. Klicken Sie im Dialogfeld **Neues Projekt** auf **Installiert** > **Vorlagen** > **Visual C#** > **Cloud** > **Schnellstarts** > **Data Services**.
	- 3\.a. Wählen Sie eine der folgenden Vorlagen aus: Azure-Blobspeicher, Azure-Tabellenspeicher oder Azure-Warteschlangenspeicher.
	- 3\.b. Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.
	- 3\.c. Geben Sie einen Namen für das Projekt an, und erstellen Sie die neue Visual Studio-Projektmappe. 

Vor dem Ausführen der Anwendung sollten Sie den Quellcode überprüfen. Um den Code zu anzuzeigen, wählen Sie in Visual Studio im Menü **Ansicht** die Option **Projektmappen-Explorer** aus. Doppelklicken Sie auf die Datei "Program.cs".

Führen Sie nun die Beispielanwendung aus:

1.	Wählen Sie in Visual Studio im Menü **Ansicht** die Option **Projektmappen-Explorer** aus. Doppelklicken Sie auf die Datei "App.config", und kommentieren Sie die Verbindungszeichenfolge für den Azure-SDK-Speicheremulator aus:

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Kommentieren Sie die Verbindungszeichenfolge für den Azure Storage-Dienst aus, und geben Sie den Speicherkontonamen und den Zugriffsschlüssel in der Datei "App.config" an: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	Informationen zum Ermitteln des Speicherkontonamens und des Zugriffsschlüssels finden Sie unter [Was ist ein Speicherkonto](storage-whatis-account.md).

3.	Nach Angabe des Speicherkontonamens und des Zugriffsschlüssels in der App.config-Datei klicken Sie im Menü **Datei** auf **Alle speichern**, um die Projektdateien zu speichern.
4.	Klicken Sie im Menü **Build** auf **Projektmappe erstellen**.
5.	Drücken Sie im Menü **Debuggen** auf **F11**, um die Projektmappe Schritt für Schritt auszuführen, oder **F5**, um die Projektmappe auszuführen.


## Nächste Schritte

Weitere Informationen zu Azure Storage finden Sie in den folgenden Ressourcen:

* [Einführung in Microsoft Azure Storage](storage-introduction.md)
* [Verwenden des Blob-Speichers mit .NET](storage-dotnet-how-to-use-blobs.md)
* [Verwenden des Tabellenspeichers mit .NET](storage-dotnet-how-to-use-tables.md)
* [Verwenden des Warteschlangenspeichers mit .NET](storage-dotnet-how-to-use-queues.md)
* [Azure-Speicherdokumentation](http://azure.microsoft.com/documentation/services/storage/)
* [Azure Storage Client Library](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure Storage-REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 

<!---HONumber=July15_HO3-->