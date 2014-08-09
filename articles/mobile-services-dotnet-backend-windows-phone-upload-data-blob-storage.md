<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" />

Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage
========================================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )] - [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md) - [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-upload-data-blob-storage.md) - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage.md) - [(Windows Phone | JavaScript)](mobile-services-windows-phone-upload-data-blob-storage.md)

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern.

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit – in diesem Falle 5 Minuten –, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem Mobile Services-Schnellstart Funktionen zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure hinzu. Dieses Lernprogramm führt Sie durch die folgenden grundlegenden Schritte zur Aktualisierung des Mobile Services-Schnellstarts für das Hochladen von Bildern in den Blob-Speicherdienst:

1.  [Installation der Speicherclientbibliothek](#install-storage-client)
2.  [Aktualisierung des Tabellen-Controllers zur Erzeugung einer SAS](#update-scripts)
3.  [Aktualisierung der Client-App zur Aufnahme von Bildern](#add-select-images)
4.  [Hochladen von Bildern zum Testen der App](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows 8 oder eine höhere Version
-   [Windows Phone SDK 8.0](http://www.microsoft.com/en-us/download/details.aspx?id=35471) oder höher
-   NuGet Package Manager installiert für Microsoft Visual Studio.
-   [Azure-Speicherkonto](/de-de/manage/services/storage/how-to-create-a-storage-account)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-windows-phone-get-started) abschließen.

Installieren des Speicherclients für Windows Store-Apps
-------------------------------------------------------

Um eine SAS für das Hochladen von Bildern auf den Blob-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online** und **Include Prerelease** aus, suchen Sie nach **WindowsAzure.Storage-Preview**, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie dem Lizenzvertrag zu.

	![](./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png) 

	Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

Als Nächstes aktualisieren Sie die Schnellstart-App zum Aufnehmen und Hochladen von Bildern.

Aktualisieren des TodoItem-Controllers zur Erzeugung einer Shared Access Signature
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]

Nächste Schritte
----------------

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Dienstes in den Blob-Dienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Back-End-Dienste und Integration zuwenden:

-   [Senden von E-Mails in Mobile Services mit SendGrid](/de-de/develop/mobile/tutorials/send-email-with-sendgrid/)

    Lernen Sie, wie Sie mithilfe des E-Mail-Dienstes SendGrid eine E-Mail-Funktion zu Ihrem mobilen Dienst hinzufügen können. In diesem Thema wird erläutert, wie serverseitige Skripts zum Senden von E-Mails mithilfe von SendGrid hinzugefügt werden

-   [Planen von Back-End-Aufträgen in Mobile Services](/de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks)

    Lernen Sie, wie Sie die Auftragsplanerfunktion in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library)

    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET


