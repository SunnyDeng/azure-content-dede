<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage and access the images from your Windows Store app." metaCanonical="" services="mobile-services,storage" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title=".NET-Backend" >.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern.

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit – in diesem Falle 5 Minuten –, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem Mobile Services-Schnellstart Funktionen zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure hinzu. Dieses Lernprogramm führt Sie durch die folgenden grundlegenden Schritte zur Aktualisierung des Mobile Services-Schnellstarts für das Hochladen von Bildern in den Blob-Speicherdienst:

1.  [Installation der Speicherclientbibliothek][Installation der Speicherclientbibliothek]
2.  [Aktualisierung des Einfügeskripts zur Erzeugung einer SAS][Aktualisierung des Einfügeskripts zur Erzeugung einer SAS]
3.  [Aktualisierung der Client-App zur Aufnahme von Bildern][Aktualisierung der Client-App zur Aufnahme von Bildern]
4.  [Hochladen von Bildern zum Testen der App][Hochladen von Bildern zum Testen der App]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows 8 oder eine höhere Version
-   [Azure-Speicherkonto][Azure-Speicherkonto]
-   Eine Kamera oder ein anderes an Ihren Computer angeschlossenes Bildaufnahmegerät

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <a name="install-storage-client"></a>Installieren des Speicherclients für Windows Store-Apps

Um eine SAS für das Hochladen von Bildern auf den Blob-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online**, suchen Sie nach `WindowsAzure.Storage`, klicken Sie auf **Installieren** für das Paket **Azure Storage**, und akzeptieren Sie die Lizenzbedingungen.

    ![][0]

    Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

Als Nächstes aktualisieren Sie die Schnellstart-App zum Aufnehmen und Hochladen von Bildern.

## <a name="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Dienstes in den Blob-Dienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Back-End-Dienste und Integration zuwenden:

-   [Senden von E-Mails in Mobile Services mit SendGrid][Senden von E-Mails in Mobile Services mit SendGrid]

Lernen Sie, wie Sie mithilfe des E-Mail-Dienstes SendGrid eine E-Mail-Funktion zu Ihrem mobilen Dienst hinzufügen können. In diesem Thema wird erläutert, wie serverseitige Skripts zum Senden von E-Mails mithilfe von SendGrid hinzugefügt werden

-   [Planen von Back-End-Aufträgen in Mobile Services][Planen von Back-End-Aufträgen in Mobile Services]

Lernen Sie, wie Sie die Auftragsplanerfunktion in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]

Referenzthemen zur Verwendung von Serverskripts zur Ausführung serverseitiger Tasks sowie zur Integration in andere Azure-Komponenten und externe Ressourcen.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]

Lernen Sie mehr über die Verwendung von Mobile Services mit .NET



  [Installation der Speicherclientbibliothek]: #install-storage-client
  [Aktualisierung des Einfügeskripts zur Erzeugung einer SAS]: #update-scripts
  [Aktualisierung der Client-App zur Aufnahme von Bildern]: #add-select-images
  [Hochladen von Bildern zum Testen der App]: #test
  [Azure-Speicherkonto]: /de-de/manage/services/storage/how-to-create-a-storage-account
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [Senden von E-Mails in Mobile Services mit SendGrid]: /de-de/develop/mobile/tutorials/send-email-with-sendgrid/
  [Planen von Back-End-Aufträgen in Mobile Services]: /de-de/documentation/articles/mobile-services-schedule-recurring-tasks
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
