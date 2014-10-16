<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage"  title="JavaScript-Backend">JavaScript-Backend</a></div>

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern.

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit – in diesem Falle 5 Minuten –, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem Mobile Services-Schnellstart Funktionen zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure hinzu. Dieses Lernprogramm führt Sie durch die folgenden grundlegenden Schritte zur Aktualisierung des Mobile Services-Schnellstarts für das Hochladen von Bildern in den Blob-Speicherdienst:

1.  [Installation der Speicherclientbibliothek][]
2.  [Aktualisierung der Client-App zur Aufnahme von Bildern][]
3.  [Installieren des Speicherclients im Mobildienstprojekt][]
4.  [Aktualisieren der TodoItem-Definition im Datenmodell][]
5.  [Aktualisierung des Tabellen-Controllers zur Erzeugung einer SAS][]
6.  [Hochladen von Bildern zum Testen der App][]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2013 oder eine höhere Version.
-   NuGet Package Manager installiert für Microsoft Visual Studio.
-   [Azure-Speicherkonto][]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][] abschließen.

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage][]]

## <a name="install-storage-client"></a>Installieren des Speicherclients für Windows Store-Apps

Um eine SAS für das Hochladen von Bildern aus Ihrer App in den Blob-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das Client-App-Projekt und wählen Sie **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online** und **Include Prerelease** aus, suchen Sie nach **WindowsAzure.Storage-Preview**, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie dem Lizenzvertrag zu.

    ![][]

    Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

Als Nächstes aktualisieren Sie die Schnellstart-App zum Aufnehmen und Hochladen von Bildern.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage][]]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Store C\#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage "Windows Store C#"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage "Windows Phone"
  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "JavaScript-Backend"
  [Installation der Speicherclientbibliothek]: #install-storage-client
  [Aktualisierung der Client-App zur Aufnahme von Bildern]: #add-select-images
  [Installieren des Speicherclients im Mobildienstprojekt]: #storage-client-server
  [Aktualisieren der TodoItem-Definition im Datenmodell]: #update-data-model
  [Aktualisierung des Tabellen-Controllers zur Erzeugung einer SAS]: #update-scripts
  [Hochladen von Bildern zum Testen der App]: #test
  [Azure-Speicherkonto]: /de-de/documentation/articles/storage-create-storage-account/
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started
  [mobile-services-dotnet-backend-configure-blob-storage]: ../includes/mobile-services-dotnet-backend-configure-blob-storage.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-windows-store-dotnet-upload-to-blob-storage]: ../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md
