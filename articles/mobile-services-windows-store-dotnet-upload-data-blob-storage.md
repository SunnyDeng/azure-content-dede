<properties 
	pageTitle="Verwenden von Mobile Services zum Hochladen von Daten in Blob-Speicher (Windows Store) | Mobile Services" 
	description="Erfahren Sie, wie Sie mithilfe von Mobile Services Bilder in den Azure-Blob-Speicher hochladen und von Ihrer Windows Store-App auf die Bilder zugreifen." 
	services="mobile-services, storage" 
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
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern. 

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit - in diesem Falle fünf Minuten -, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem Mobile Services-Schnellstart Funktionen zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure hinzu. Dieses Lernprogramm führt Sie durch die folgenden grundlegenden Schritte zur Aktualisierung des Mobile Services-Schnellstarts für das Hochladen von Bildern in den Blob-Speicherdienst:

1. [Installieren der Speicherclientbibliothek]
2. [Aktualisieren des Einfügeskripts zum Generieren einer SAS]
3. [Aktualisieren der Client-App zur Aufnahme von Bildern]
4. [Hochladen von Bildern zum Testen der App]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2012 Express für Windows 8 oder eine höhere Version
+ [Azure-Speicherkonto][So erstellen Sie ein Speicherkonto]
+ Eine Kamera oder ein anderes an Ihren Computer angeschlossenes Bildaufnahmegerät

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

## <a name="install-storage-client"></a>Installieren des Speicherclients für Windows Store-Apps

Um eine SAS für das Hochladen von Bildern auf den Blob-Speicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Store-Apps installiert.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** aus, suchen Sie nach `WindowsAzure.Storage`, klicken Sie auf **Installieren** für das Paket **Azure Storage**, und akzeptieren Sie die Lizenzbedingungen. 

  	![][2]

  	Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

Als Nächstes aktualisieren Sie die Schnellstart-App zum Aufnehmen und Hochladen von Bildern.

## <a name="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

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

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Senden von E-Mails in Mobile Services mit SendGrid]: /de-de/develop/mobile/tutorials/send-email-with-sendgrid/
[Planen von Back-End-Aufträgen in Mobile Services]: /de-de/documentation/articles/mobile-services-schedule-recurring-tasks
[Senden von Pushbenachrichtigungen an Windows Store-Apps mithilfe von Service Bus aus einem .NET-Back-End]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[So erstellen Sie ein Speicherkonto]: /de-de/manage/services/storage/how-to-create-a-storage-account
[Azure-Speicherclientbibliothek für Store-Apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
[App-Einstellungen]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7



<!--HONumber=42-->
