<properties 
	pageTitle="Hochladen von Bildern in Azure Storage über eine Windows Phone Silverlight-App | Microsoft Azure" 
	description="Erfahren Sie, wie Sie mithilfe von Mobile Services Bilder von einer Windows Phone Silverlight-App in den Azure-Blobspeicher hochladen." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Verwenden von Mobile Services zum Hochladen von Bildern in Azure Storage

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Übersicht

In diesem Thema wird erläutert, wie Sie Azure Mobile Services dazu verwenden können, Ihre App für das Hochladen und Speichern von durch Benutzer erzeugten Bildern in Azure Storage zu aktivieren. Mobile Services verwendet zur Datenspeicherung eine SQL-Datenbank. BLOB (Binary Large Object)-Daten lassen sich allerdings effizienter im Azure-Blob-Speicherdienst speichern.

Die Anmeldeinformationen zum sicheren Hochladen von Daten in den Blob-Speicherdienst können mit der Client-App nicht sicher zugewiesen werden. Stattdessen müssen Sie diese Anmeldeinformationen in Ihrem mobilen Dienst speichern und dazu verwenden, eine Shared Access Signature (SAS) zu erstellen, die dann zum Hochladen eines neuen Bildes verwendet wird. Die SAS, eine Anmeldeinformation mit einer kurzen Laufzeit &mdash; in diesem Falle 5 Minuten –, wird durch Mobile Services sicher an die Client-App zurückgegeben. Anschließend nutzt die App diese temporäre Anmeldeinformation zum Hochladen des Bildes. In diesem Beispiel sind Downloads vom Blob-Dienst öffentlich.

In diesem Lernprogramm fügen Sie dem [GetStartedWithData-Beispiel-App-Projekt](mobile-services-windows-phone-get-started-data.md) zur Aufnahme von Bildern und zum Hochladen dieser Bilder unter Verwendung einer von Mobile Services erzeugten SAS in Azure Funktionen hinzu.


##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2012 Express für Windows 8 oder eine höhere Version
+ [Windows Phone SDK 8.0] oder höher
+ NuGet Package Manager installiert für Microsoft Visual Studio.
+ [Azure-Speicherkonto][How To Create a Storage Account]
+ Schließen Sie das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen Anwendung](mobile-services-windows-phone-get-started-data.md) ab.  


##Installieren des Speicherclients für Windows Phone-Apps

Um eine SAS für das Hochladen von Bildern in den Blobspeicher verwenden zu können, müssen Sie zuerst das NuGet-Paket hinzufügen, das die Speicherclientbibliothek für Windows Phone-Apps installiert.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** und **Include Prerelease** aus, suchen Sie nach **WindowsAzure.Storage-Preview**, klicken Sie auf **Installieren** im Paket **Azure Storage**, und stimmen Sie dem Lizenzvertrag zu.

  	![Hinzufügen von Azure Storage NuGet](./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Die Clientbibliothek für Azure-Speicherdienste wird zum Projekt hinzugefügt.

Als Nächstes aktualisieren Sie die Schnellstart-App zum Aufnehmen und Hochladen von Bildern.

##Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Um neue Eigenschaften zum TodoItem-Objekt hinzuzufügen, muss das dynamische Schema im mobilen Dienst aktiviert sein. Wenn das dynamische Schema aktiviert ist, werden automatisch neue Spalten in die TodoItem-Tabelle eingefügt, die auf diese neuen Eigenschaften verweisen.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


##Nächste Schritte

Nachdem Sie nun gelernt haben, Ihre Bilder durch die Integration Ihres mobilen Diensts in den Blobdienst sicher hochzuladen, können Sie sich einigen anderen Themen aus dem Bereich Backend-Dienste und Integration zuwenden:

+ [Senden von E-Mails in Mobile Services mit SendGrid]
 
  Lernen Sie, wie Sie mithilfe des E-Mail-Dienstes SendGrid eine E-Mail-Funktion zu Ihrem mobilen Dienst hinzufügen können. In diesem Thema wird erläutert, wie serverseitige Skripts zum Senden von E-Mails mithilfe von SendGrid hinzugefügt werden.

+ [Planen von Back-End-Aufträgen in Mobile Services]

  Lernen Sie, wie Sie die Auftragsplanerfunktion in Mobile Services zur Definition von Skriptcode verwenden können, der gemäß einem von Ihnen definierten Zeitplan ausgeführt wird.

##Weitere Informationen

+ [Mobile Services: Serverskriptreferenz]

  Referenzthemen zur Verwendung von Serverskripts zur Ausführung serverseitiger Tasks sowie zur Integration in andere Azure-Komponenten und externe Ressourcen.
 
+ [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]

  Lernen Sie mehr über die Verwendung von Mobile Services mit .NET
  
<!-- Images. -->

<!-- URLs. -->
[Senden von E-Mails in Mobile Services mit SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Planen von Back-End-Aufträgen in Mobile Services]: mobile-services-schedule-recurring-tasks.md
[Mobile Services: Serverskriptreferenz]: mobile-services-how-to-use-server-scripts.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=August15_HO7-->