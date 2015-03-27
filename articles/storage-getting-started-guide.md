<properties 
	pageTitle="Erste Schritte mit Azure-Blobs, -Tabellen und -Warteschlangen in 5 Minuten" 
	description="Erfahren Sie, wie Sie mithilfe von Azure-Schnellstarts und Visual Studio schnell mit Azure-Blobs, -Tabellen und -Warteschlangen arbeiten können." 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# Erste Schritte mit Azure-Blobs, -Tabellen und -Warteschlangen in 5 Minuten 

In diesem Lernprogramm wird gezeigt, wie Sie **Azure Storage-Blobs**, **-Tabellen** und **-Warteschlangen** durch die Entwicklung einer einfachen Azure-Anwendung in Visual Studio schnell bei der Programmierung verwenden können. 

Das Lernprogramm beinhaltet zwei Hauptszenarien für einen schnellen Einstieg in Azure Storage:

- Ausführen Ihrer ersten Azure Storage-Anwendung auf dem Azure-Speicheremulator
- Ausführen Ihrer ersten Azure Storage-Anwendung über den Azure Storage-Dienst

Wenn Sie mehr über Azure Storage erfahren möchten, bevor Sie am Code arbeiten, finden Sie die entsprechenden Informationen unter [Nächste Schritte][].

## Ausführen Ihrer ersten Azure Storage-Anwendung auf dem Azure-Speicheremulator

In diesem Abschnitt wird durch die Entwicklung einer Beispielanwendung über den [Azure-Speicheremulator](https://msdn.microsoft.com/library/azure/hh403989.aspx) veranschaulicht, wie Sie **Azure Storage-Blobs**, **-Tabellen** und **-Warteschlangen** bei der Programmierung einbinden können. Der Microsoft Azure-Speicheremulator bietet eine lokale Umgebung, die die Azure-Blob-, -Warteschlangen- und -Tabellendienste für Entwicklungszwecke emuliert. Durch Verwendung des Speicheremulators können Sie die Anwendung bezüglich der Speicherdienste lokal testen, ohne Kosten zu verursachen.

Führen Sie zum Abschließen dieses Abschnitts folgende notwendige Aufgaben durch:

1. Zum Kompilieren und Erstellen der Anwendung muss [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) auf Ihrem Computer installiert sein. Wenn Visual Studio nicht installiert ist, können Sie Visual Studio Express for Web bei der Installation des [Azure-SDK 2.5 für Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) (oder höher) installieren. 
2. Stellen Sie sicher, dass das [Azure-SDK 2.5 für Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) oder höher auf Ihrem Computer installiert ist, da es die Azure-Schnellstart-Beispielprojekte sowie die [Azure Storage-Clientbibliothek für .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) enthält.  
3. Überprüfen Sie, ob [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) auf Ihrem Computer installiert ist, da die Azure-Schnellstart-Beispielprojekte dieses benötigen. Wenn Sie nicht sicher sind, welche Version von .NET Framework auf dem Computer installiert ist, finden Sie entsprechende Informationen unter [ Feststellen, welche Versionen von .NET Framework installiert sind](https://msdn.microsoft.com/vstudio/hh925568.aspx). Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die Windows-Taste, und geben Sie **Systemsteuerung** ein. Klicken Sie auf **Programme** > **Programme und Funktionen**. Überprüfen Sie, ob .NET Framework 4.5 in der Liste installierter Programme enthalten ist.

Nun erstellen wir eine einfache Azure Storage-Anwendung mithilfe eines der Azure-Schnellstart-Beispielprojekte in Visual Studio. Dieses Lernprogramm konzentriert sich auf Beispielprojekte mit **Azure-Blob-Speicher**, **Azure-Tabellenspeicher** und **Azure Storage-Warteschlangen**. Die folgenden Anweisungen gelten für jedes Beispielprojekt. Sie unterscheiden sich lediglich in der Auswahl unterschiedlicher Vorlagen bei Schritt 3.a:

1. Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die Windows-Taste, und geben Sie "Visual Studio 2013" oder "Visual Studio Express 2013 for Web" ein. Klicken Sie auf die Anwendung, um sie zu starten.
2. Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3. Klicken Sie im Dialogfeld **Neues Projekt** auf **Installiert** > **Vorlagen** > **Visual C#** > **Cloud** > **Schnellstarts** > **Datendienste**.
	- 3.a.  Wählen Sie eine der folgenden Vorlagen aus: Azure-Blob-Speicher, Azure-Tabellenspeicher oder Azure Storage-Warteschlangen. 
	- 3.b. Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.	
	- 3.c. Geben Sie je nach ausgewählter Vorlage den Namen der Anwendung ein (z. B. **DataBlobStorage**, **DataTableStorage** oder **DataStorageQueue**). Klicken Sie auf **OK**. Daraufhin wird eine neue Visual Studio-Projektmappe erstellt. Im Folgenden finden Sie einen Screenshot als Beispiel:
	
	![Azure QuickStarts][Image1]

Wir empfehlen Ihnen, den Quellcode vor dem Ausführen der Anwendung zu überprüfen, um zu erfahren, wie die Programmierung mit Azure Storage funktioniert. Um den Code zu anzuzeigen, wählen Sie im Menü **Ansicht** in Visual Studio **Projektmappen-Explorer** aus. Doppelklicken Sie auf die Datei "Program.cs". 

Führen Sie die Beispielanwendung nun mithilfe des [Azure-Speicheremulators](https://msdn.microsoft.com/library/azure/hh403989.aspx) aus, der als Teil des Azure-SDKs installiert wurde:

1.	Starten Sie den Azure-Speicheremulator: Klicken Sie auf die Schaltfläche **Start**, drücken Sie die Windows-Taste, und geben Sie "Azure-Speicheremulator" ein. Wählen Sie ihn aus der Liste von Anwendungen aus, um ihn zu starten.
2.	Wählen Sie in Visual Studio im Menü **Build** die Option **Projektmappe bereitstellen** aus. 
3.	Drücken Sie im Menü **Debuggen** auf **F11**, um die Projektmappe Schritt für Schritt auszuführen, oder **F5**, um die Projektmappe auszuführen.

## Ausführen Ihrer ersten Azure Storage-Anwendung über den Azure Storage-Dienst
In diesem Abschnitt wird durch die Entwicklung einer Beispielanwendung über den [Azure Storage-Dienst](http://azure.microsoft.com/documentation/services/storage/) veranschaulicht, wie Sie **Azure Storage-Blobs**, **-Tabellen** und **-Warteschlangen** bei der Programmierung einbinden können.

Führen Sie zum Abschließen dieses Abschnitts folgende notwendige Aufgaben durch:

1. Zum Kompilieren und Erstellen der Anwendung muss [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) auf Ihrem Computer installiert sein. Wenn Visual Studio nicht installiert ist, können Sie Visual Studio Express for Web bei der Installation des [Azure-SDK 2.5 für Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) (oder höher) installieren. 
2. Stellen Sie sicher, dass das [Azure-SDK 2.5 für Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) oder höher auf Ihrem Computer installiert ist, da es die Azure-Schnellstart-Beispielprojekte sowie die [Azure Storage-Clientbibliothek für .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) enthält.  
3. Überprüfen Sie, ob [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) auf Ihrem Computer installiert ist, da die Azure-Schnellstart-Beispielprojekte dieses benötigen. Wenn Sie nicht sicher sind, welche Version von .NET Framework auf dem Computer installiert ist, finden Sie entsprechende Informationen unter [ Feststellen, welche Versionen von .NET Framework installiert sind](https://msdn.microsoft.com/vstudio/hh925568.aspx). Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die Windows-Taste, und geben Sie **Systemsteuerung** ein. Klicken Sie auf **Programme** > **Programme und Funktionen**. Überprüfen Sie, ob .NET Framework 4.5 in der Liste installierter Programme enthalten ist.
4.	Legen Sie ein Azure-Abonnement an (wenn Sie nicht bereits über eins verfügen), und erstellen Sie darüber hinaus ein **Standardspeicher**-Konto:
	- Informationen zum Erhalt eines Azure-Abonnements finden Sie unter [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/), [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/) und [Spezielle Angebote](http://azure.microsoft.com/pricing/member-offers/) (für Mitglieder von MSDN, Microsoft Partner Network, BizSpark und anderen Microsoft-Programmen).
	- Informationen zum Erstellen eines **Standardspeicher**-Kontos in Azure finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](./storage-create-storage-account.md). **Hinweis:** Zwei Typen von Speicherkonten stehen in Azure zur Verfügung: Standardspeicherkonto und Premium-Speicherkonto. Ein Standardspeicherkonto enthält Zugriff auf Azure-Blob-, Tabellen- und Warteschlangenspeicher. Das Premium-Speicherkonto steht zurzeit nur zum Speichern von Daten auf Datenträgern zur Verfügung, die von virtuellen Azure-Maschinen verwendet werden. Weitere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](./storage-premium-storage-preview-portal.md).

Nun erstellen wir eine einfache Azure Storage-Anwendung mithilfe eines der Azure-Schnellstart-Beispielprojekte in Visual Studio. Dieses Lernprogramm konzentriert sich auf Beispielprojekte mit **Azure-Blob-Speicher**, **Azure-Tabellenspeicher** und **Azure Storage-Warteschlangen**. Die folgenden Anweisungen gelten für jedes Beispielprojekt. Sie unterscheiden sich lediglich in der Auswahl unterschiedlicher Vorlagen bei Schritt 3.a:

1. Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die Windows-Taste, und geben Sie "Visual Studio 2013" oder "Visual Studio Express 2013 for Web" ein. Klicken Sie auf die Anwendung, um sie zu starten.
2. Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3. Klicken Sie im Dialogfeld **Neues Projekt** auf **Installiert** > **Vorlagen** > **Visual C#** > **Cloud** > **Schnellstarts** > **Datendienste**.
	- 3.a. Wählen Sie eine der folgenden Vorlagen aus: Azure-Blob-Speicher, Azure-Tabellenspeicher oder Azure Storage-Warteschlangen. 
	- 3.b. Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.
	- 3.c. Geben Sie je nach ausgewählter Vorlage den Namen der Anwendung ein (z. B. **DataBlobStorage**, **DataTableStorage** oder **DataStorageQueue**). Klicken Sie auf **OK**. Daraufhin wird eine neue Visual Studio-Projektmappe erstellt. 

Wir empfehlen Ihnen, den Quellcode vor dem Ausführen der Anwendung zu überprüfen, um zu erfahren, wie die Programmierung mit Azure Storage funktioniert. Um den Code zu anzuzeigen, wählen Sie im Menü **Ansicht** in Visual Studio **Projektmappen-Explorer** aus. Doppelklicken Sie auf die Datei "Program.cs". 

Führen Sie nun die Beispielanwendung aus:

1.	Wählen Sie in Visual Studio im Menü **Ansicht** die Option **Projektmappen-Explorer** aus. Doppelklicken Sie auf die Datei "App.config", und kommentieren Sie die Verbindungszeichenfolge für den Azure-SDK-Speicheremulator aus: 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Kommentieren Sie die Verbindungszeichenfolge für den Azure Storage-Dienst aus, und geben Sie den Speicherkontonamen und den Zugriffsschlüssel in der App.config-Datei an:
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	Informationen zum Ermitteln des Speicherkontonamens und des Zugriffsschlüssels finden Sie unter [Was ist ein Speicherkonto](./storage-whatis-account.md). 

3.	Nach Angabe des Speicherkontonamens und des Zugriffsschlüssels in der App.config-Datei klicken Sie im Menü **Datei** auf **Alle speichern**, um die Projektdateien zu speichern. 
4.	Klicken Sie im Menü **Build** auf **Projektmappe erstellen**. 
5.	Drücken Sie im Menü **Debuggen** auf **F11**, um die Projektmappe Schritt für Schritt auszuführen, oder **F5**, um die Projektmappe auszuführen.


## Nächste Schritte
In diesem Lernprogramm haben Sie gelernt, wie die Programmierung mit Azure-Blob-Speicher, Azure-Tabellenspeicher und Azure Storage-Warteschlangen funktioniert. 

Weitere Informationen hierzu finden Sie unter folgenden Links:

* [Einführung in Microsoft Azure Storage](./storage-introduction.md)
* [Verwenden des Blob-Speichers mit .NET](./storage-dotnet-how-to-use-blobs.md)
* [Verwenden des Tabellenspeichers mit .NET](./storage-dotnet-how-to-use-tables.md)
* [Verwenden des Warteschlangenspeichers mit .NET](./storage-dotnet-how-to-use-queues.md)
* [Azure Storage-Dokumentation](http://azure.microsoft.com/documentation/services/storage/)
* [MSDN-Referenz für Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Azure Storage-Clientbibliothek](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure Storage-REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


<!--HONumber=47-->
