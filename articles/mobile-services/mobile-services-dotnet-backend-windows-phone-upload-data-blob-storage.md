<properties 
	pageTitle="Verwenden von Mobile Services zum Hochladen von Daten in Blob-Speicher (Windows Phone) | Mobile Services" 
	description="Erfahren Sie, wie Sie Mobile Services zum Hochladen von Bildern in den Azure Blob-Speicher verwenden." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Übersicht
In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, in Ihrer Windows Phone 8- oder Windows Phone 8.1 Silverlight-App das Hochladen und Speichern von von Benutzern erzeugten Bildern in Azure Storage zu ermöglichen. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern.

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit &mdash; in diesem Falle 5 Minuten –, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem [GetStartedWithData-Beispiel-App-Projekt](mobile-services-dotnet-backend-windows-phone-get-started-data.md) zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure Funktionen hinzu.

##Voraussetzungen 

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2013 oder eine höhere Version
+ [Windows Phone SDK 8.0] oder höher
+ NuGet Package Manager installiert für Microsoft Visual Studio.
+ [Azure-Speicherkonto][How To Create a Storage Account]
+ Schließen Sie das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen Anwendung](mobile-services-dotnet-backend-windows-phone-get-started-data.md) ab.  

>[AZURE.NOTE]Dieses Lernprogramm unterstützt Apps für Windows Phone 8 und Windows Phone 8.1 Silverlight. Windows Phone Store 8.1- oder universelle Windows 8.1-Apps werden nicht unterstützt.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installieren des Speicherclients für Windows Store-Apps

Um eine SAS für das Hochladen von Bildern aus Ihrer App in den Blob-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das Client-App-Projekt und wählen Sie **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** und **Include Prerelease** aus, suchen Sie nach **WindowsAzure.Storage-Preview**, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie dem Lizenzvertrag zu.

  	![][2]

  	Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=July15_HO1-->