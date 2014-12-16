<properties pageTitle="Verwenden von Mobile Services zum Hochladen von Daten in Blob-Speicher (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="glenga" />

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern. 

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit - in diesem Falle 5 Minuten -, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem [GetStartedWithData-Beispiel-App-Projekt](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure Funktionen hinzu. Dieses Lernprogramm führt Sie durch die folgenden grundlegenden Schritte zur Aktualisierung des Mobile Services-Schnellstarts für das Hochladen von Bildern in den Blob-Speicherdienst:

1. [Installation der Speicherclientbibliothek]
2. [Aktualisierung der Client-App zur Aufnahme von Bildern]
3. [Installieren des Speicherclients im Mobildienstprojekt]
4. [Aktualisieren der TodoItem-Definition im Datenmodell]
5. [Aktualisierung des Tabellen-Controllers zur Erzeugung einer SAS]
6. [Hochladen von Bildern zum Testen der App]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2013 oder eine höhere Version
+ [Windows Phone SDK 8.0] oder höher
+ NuGet Package Manager installiert für Microsoft Visual Studio.
+ [Azure-Speicherkonto][So erstellen Sie ein Speicherkonto]
+ Schließen Sie das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen Anwendung](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)   ab.

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installieren des Speicherclients für Windows Store-Apps

Um eine SAS für das Hochladen von Bildern aus Ihrer App in den Blob-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** und **Vorabversion einschließen** aus, suchen Sie nach **WindowsAzure.Storage-Preview**, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie dem Lizenzvertrag zu. 

  	![][2]

  	Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Installation der Speicherclientbibliothek]: #install-storage-client
[Aktualisierung der Client-App zur Aufnahme von Bildern]: #add-select-images
[Installieren des Speicherclients im Mobildienstprojekt]: #storage-client-server
[Aktualisieren der TodoItem-Definition im Datenmodell]: #update-data-model
[Aktualisierung des Tabellen-Controllers zur Erzeugung einer SAS]: #update-scripts
[Hochladen von Bildern zum Testen der App]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Senden von E-Mails in Mobile Services mit SendGrid]: /de-de/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Planen von Back-End-Aufträgen in Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-phone-get-started

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Erstellen eines Speicherkontos]: /de-de/documentation/articles/storage-create-storage-account/
[Azure-Speicher-Clientbibliothek für Store-Apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/de-de/download/details.aspx?id=35471


