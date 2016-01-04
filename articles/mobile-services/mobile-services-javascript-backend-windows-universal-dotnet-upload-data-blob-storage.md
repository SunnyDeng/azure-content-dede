<properties 
	pageTitle="Hochladen von Bildern in Azure-Blobspeicher über eine universelle Windows-App | Microsoft Azure" 
	description="Erfahren Sie, wie mithilfe eines mobilen JavaScript-Back-End-Dienstes Bilder in Azure-BLOB-Speicher hochladen und dann über Ihre universelle Windows-App darauf zugreifen." 
	services="mobile-services,storage" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="glenga"/>

# Hochladen von Bildern in Azure-BLOB-Speicher mithilfe von Mobile Services

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

##Aktualisieren des registrierten Einfügeskripts im klassischen Azure-Portal

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##Nächste Schritte

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Dienstes in den Blob-Dienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Backend-Dienste und Integration zuwenden:

+ [Planen von Back-End-Aufträgen in Mobile Services]

  	Lernen Sie, wie Sie die Auftragsplanerfunktoin in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

+ [Mobile Services: Serverskriptreferenz]

    Referenzthemen zur Verwendung von Serverskripts zur Ausführung serverseitiger Tasks sowie zur Integration in andere Azure-Komponenten und externe Ressourcen.
 
+ [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]

    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Planen von Back-End-Aufträgen in Mobile Services]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services: Serverskriptreferenz]: mobile-services-how-to-use-server-scripts.md
[Erste Schritte mit Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=AcomDC_1203_2015-->