<properties
	pageTitle="Hochladen von Bildern in Azure-Blobspeicher über eine universelle Windows-App | Microsoft Azure"
	description="Erfahren Sie, wie mithilfe eines mobilen .NET Back-End-Dienstes Bilder in Azure-BLOB-Speicher hochladen und dann über Ihre universelle Windows-App darauf zugreifen."
	documentationCenter="windows"
	authors="ggailey777"
	services="mobile-services,storage"
	manager="dwrede"
	editor=""/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="glenga"/>

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Übersicht
In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern.

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit &mdash; in diesem Falle 5 Minuten –, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie der Mobile Services-Schnellstartapp Funktionen zum Aufnehmen von Bildern und Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure hinzu.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2013 Update 3 oder eine höhere Version.
+ [Azure-Speicherkonto](../storage-create-storage-account.md)
+ Eine Kamera oder ein anderes an Ihren Computer angeschlossenes Bildaufnahmegerät

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##Nächste Schritte

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Dienstes in den Blob-Dienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Backend-Dienste und Integration zuwenden:

+ [Planen von Back-End-Aufträgen in Mobile Services](../mobile-services-dotnet-backend-schedule-recurring-tasks.md)

     Lernen Sie, wie Sie die Auftragsplanerfunktion in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

+ [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](../mobile-services-windows-dotnet-how-to-use-client-library.md)

     Lernen Sie mehr über die Verwendung von Mobile Services mit .NET

<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: ../mobile-services-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866

<!---HONumber=AcomDC_1210_2015-->