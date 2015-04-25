<properties 
	pageTitle="Verwenden von Mobile Services zum Hochladen von Daten in Blob-Speicher (Windows Phone) | Mobile Services" 
	description="Erfahren Sie, wie Sie Mobile Services zum Hochladen von Bildern in den Azure Blob-Speicher verwenden." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	writer="wesmc" 
	services="mobile-services, storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/06/2014" 
	ms.author="wesmc"/>

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern. 

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit &mdash;in diesem Falle fünf Minuten -, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem [GetStartedWithData-App-Beispielprojekt ](mobile-services-windows-phone-get-started-data.md) Funktionen zum Aufnehmen von Bildern und Hochladen dieser Bilder in Azure unter Verwendung einer von Mobile Services erzeugten SAS hinzu. Dieses Lernprogramm führt Sie durch die folgenden grundlegenden Schritte zum Aktualisieren der einfachen TodoList-App zum Hochladen von Bildern in den BLOB-Speicherdienst:

1. [Installieren der Speicherclientbibliothek]
2. [Aktualisieren des Einfügeskripts zum Generieren einer SAS]
3. [Aktualisieren der Client-App zur Aufnahme von Bildern]
4. [Hochladen von Bildern zum Testen der App]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2012 Express für Windows 8 oder eine höhere Version
+ [Windows Phone SDK 8.0] oder höher
+ NuGet Package Manager installiert für Microsoft Visual Studio.
+ [Azure-Speicherkonto][So erstellen Sie ein Speicherkonto]
+ Abschluss des Lernprogramms [Hinzufügen von Mobile Services zu einer vorhandenen App](mobile-services-windows-phone-get-started-data.md)  


##<a name="install-storage-client"></a>Installieren des Speicherclients für Windows Phone-Apps

Um eine SAS für das Hochladen von Bildern in den BLOB-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Phone-Apps installiert.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** und dann **Include Prerelease** aus, suchen Sie nach **WindowsAzure.Storage-Preview**, klicken Sie im Paket **Azure Storage** auf **Installieren**, und stimmen Sie dem Lizenzvertrag zu. 

  	![][2]

  	Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

Als Nächstes aktualisieren Sie die Schnellstart-App zum Aufnehmen und Hochladen von Bildern.

##<a name="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Um neue Eigenschaften zum TodoItem-Objekt hinzuzufügen, muss das dynamische Schema im mobilen Dienst aktiviert sein. Wenn das dynamische Schema aktiviert ist, werden automatisch neue Spalten in die TodoItem-Tabelle eingefügt, die auf diese neuen Eigenschaften verweisen.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Dienstes in den Blob-Dienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Back-End-Dienste und Integration zuwenden:

+ [Senden von E-Mails in Mobile Services mit SendGrid]
 
  Lernen Sie, wie Sie mithilfe des E-Mail-Dienstes SendGrid eine E-Mail-Funktion zu Ihrem mobilen Dienst hinzufügen können. In diesem Thema wird erläutert, wie serverseitige Skripts zum Senden von E-Mails mithilfe von SendGrid hinzugefügt werden

+ [Planen von Back-End-Aufträgen in Mobile Services]

  Lernen Sie, wie Sie die Auftragsplanerfunktion in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

+ [Mobile Services: Serverskriptreferenz]

  Referenzthemen zur Verwendung von Serverskripts zur Ausführung serverseitiger Tasks sowie zur Integration in andere Azure-Komponenten und externe Ressourcen.
 
+ [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]

  Lernen Sie mehr über die Verwendung von Mobile Services mit .NET
  
 
<!-- Anchors. -->
[Installieren der Speicherclientbibliothek]: #install-storage-client
[Aktualisieren der Client-App zur Aufnahme von Bildern]: #add-select-images
[Aktualisieren des Einfügeskripts zum Generieren einer SAS]: #update-scripts
[Hochladen von Bildern zum Testen der App]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[Senden von E-Mails in Mobile Services mit SendGrid]: /de-de/develop/mobile/tutorials/send-email-with-sendgrid/
[Planen von Back-End-Aufträgen in Mobile Services]: /de-de/develop/mobile/tutorials/schedule-backend-tasks/
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-phone-get-started

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Erstellen eines Speicherkontos]: /de-de/manage/services/storage/how-to-create-a-storage-account
[Azure-Speicherclientbibliothek für Store-Apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/de-de/download/details.aspx?id=35471





<!--HONumber=42-->
