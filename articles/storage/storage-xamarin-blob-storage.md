<properties 
	pageTitle="Verwenden des Blob-Speichers mit Xamarin (Vorschau) | Microsoft Azure" 
	description="Die Azure Storage-Clientbibliothek für Xamarin ermöglicht Entwicklern das Erstellen von iOS-, Android- und Windows Store-Apps mit ihren systemeigenen Benutzeroberflächen. In diesem Lernprogramm wird erläutert, wie mithilfe von Xamarin eine Android-Anwendung erstellt wird, in der der Azure-Blob-Speicher verwendet wird." 
	services="storage" 
	documentationCenter="xamarin" 
	authors="micurd" 
	manager="" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="tamram"/>

# Verwenden des Blob-Speichers mit Xamarin (Vorschau)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

Xamarin ermöglicht Entwicklern die Verwendung einer gemeinsamen C#-Codebasis zum Erstellen von iOS-, Android- und Windows Store-Apps mit ihren systemeigenen Benutzeroberflächen. Die Azure Storage-Clientbibliothek für Xamarin ist eine Vorschaubibliothek. Beachten Sie, dass sie sich in Zukunft ändern kann.

In diesem Lernprogramm wird erläutert, wie Sie den Azure-Blob-Speicher mit einer Xamarin Android-Anwendung verwenden können. Wenn Sie mehr über Azure Storage erfahren möchten, bevor Sie sich eingehend mit dem Code beschäftigen, finden Sie entsprechende Informationen unter [Nächste Schritte](#next-steps) am Ende dieses Dokuments.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Generieren einer SAS (Shared Access Signature)

Bei der Entwicklung mit der Azure Storage-Clientbibliothek für Xamarin können Sie den Zugriff auf ein Azure-Speicherkonto nicht mit Ihren Speicherkonto-Zugriffsschlüsseln authentifizieren. Dadurch wird verhindert, dass Ihre Kontoanmeldeinformationen an Benutzer verteilt werden, die Ihre App herunterladen. Stattdessen empfehlen wir die Verwendung von SAS (Shared Access Signatures), die Ihre Anmeldeinformationen nicht preisgeben.

In dieser Anleitung verwenden wir Azure PowerShell zum Generieren eines SAS-Tokens. Anschließend erstellen wir eine Xamarin-App, in der die generierte SAS verwendet wird.

Zunächst müssen Sie Azure PowerShell installieren. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md#Install).

Öffnen Sie dann Azure PowerShell, und führen Sie die folgenden Befehle aus. Denken Sie daran, `ACCOUNT_NAME` und `ACCOUNT_KEY== ` durch Ihre Anmeldeinformationen für das Speicherkonto zu ersetzen. Ersetzen Sie `CONTAINER_NAME` durch einen Namen Ihrer Wahl.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

Der SAS-URI für den neuen Container sollte dem folgenden ähneln:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

Die für den Container erstellte SAS ist für den nächsten Tag gültig. Die Signatur gewährt uneingeschränkte Berechtigungen (*z. B.* Lese-, Schreib-, Lösch- und Listenberechtigung) für Blobs im Container.

Weitere Informationen zu SAS finden Sie im [SAS-Lernprogramm für .NET](storage-dotnet-shared-access-signature-part-2.md).

## Erstellen einer neuen Xamarin-Anwendung

In diesem Lernprogramm erstellen wir die Xamarin-Anwendung in Visual Studio. Führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Laden Sie [Visual Studio](https://www.visualstudio.com/) herunter, und installieren Sie diese Software.
2. Laden Sie [Xamarin](http://xamarin.com/platform) herunter, und installieren Sie diese Software.
3. Öffnen Sie Visual Studio, und wählen Sie **Datei > Neu > Projekt > Android > Leere App (Android)** aus.
4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie dann nach **Azure Storage**, und installieren Sie **Azure Storage 4.4.0-preview**.

Es sollte nun eine Anwendung verfügbar sein, in der Sie auf eine Schaltfläche klicken und einen Indikator schrittweise erhöhen können.

## Ausführen von Containervorgängen mithilfe der SAS

Fügen Sie als Nächstes Code hinzu, um mithilfe des generierten SAS-URI eine Reihe von Containervorgängen auszuführen.

Fügen Sie zunächst die folgenden **using**-Anweisungen hinzu:

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


Fügen Sie dann eine Zeile für das SAS-Token hinzu. Ersetzen Sie die Zeichenfolge `"SAS_URI"` durch den SAS-URI, den Sie in Azure PowerShell generiert haben. Fügen Sie anschließend eine Zeile für einen Aufruf der `UseContainerSAS`-Methode ein, die wir hier unten erstellen. Beachten Sie, dass das Schlüsselwort **async** vor dem Delegaten hinzugefügt wurde.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

Fügen Sie die neue `UseContainerSAS`-Methode unter der `OnCreate`-Methode ein.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## Ausführen der Anwendung

Sie können jetzt diese Anwendung in einem Emulator oder Android-Gerät ausführen.

Obwohl sich diese ersten Schritte auf Android beziehen, können Sie die `UseContainerSAS`-Methode auch in iOS- oder Windows Store-Anwendungen verwenden. Mit Xamarin können Entwickler auch Windows Phone-Apps erstellen. Allerdings wird dies von unserer Bibliothek noch nicht unterstützt.

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie den Azure-Blob-Speicher und SAS mit einer Xamarin-Anwendung verwenden können. Als weitere Übung kann ein ähnliches Muster angewendet werden, um ein SAS-Token für eine Azure-Tabelle oder eine Azure-Warteschlange zu generieren.

Weitere Informationen zu Blobs, Tabellen und Warteschlangen finden Sie über die folgenden Links:

[Einführung in Microsoft Azure Storage](storage-introduction.md) [Verwenden des Blob-Speichers mit .NET](storage-dotnet-how-to-use-blobs.md) [Verwenden des Tabellenspeichers mit .NET](storage-dotnet-how-to-use-tables.md) [Verwenden des Warteschlangenspeichers mit .NET](storage-dotnet-how-to-use-queues.md)
 

<!---HONumber=AcomDC_1203_2015-->