<properties
			pageTitle="Verwenden des Azure-Dateispeichers mit PowerShell und .NET | Microsoft Azure"
            description="Erfahren Sie, wie Sie den Azure-Dateispeicher zum Erstellen von Cloud-Dateifreigaben und zum Verwalten des Dateiinhalts verwenden. Der Dateispeicher ermöglicht Unternehmen, von SMB-Dateifreigaben abhängige Anwendungen nach Azure zu verschieben. Beibehalten Ihrer Speicherkonto-Anmeldeinformationen auf dem virtuellen Computer, sodass die Verbindung mit der Dateifreigabe beim Neustart des Computers hergestellt wird."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="07/06/2015"
      ms.author="tamram" />

# Verwenden des Azure-Dateispeichers mit PowerShell und .NET

## Übersicht

Der Azure-Dateidienst stellt Dateifreigaben mit dem SMB 2.1-Standardprotokoll bereit. Anwendungen, die in Azure ausgeführt werden, können nun ganz einfach über standardmäßige und bekannte Dateisystem-APIs wie ReadFile und WriteFile Dateien zwischen virtuellen Computern freigeben. Darüber hinaus kann der Dateizugriff gleichzeitig über eine REST-Schnittstelle erfolgen, die eine Vielzahl von Hybridszenarien ermöglicht. Schließlich beruhen Azure-Dateien auf der gleichen Technologie wie BLOB-, Tabellen-, und Warteschlangendienste, was bedeutet, dass Azure-Dateien die vorhandenen Verfügbarkeit, Dauerhaftigkeit, Skalierbarkeit und geografische Redundanz nutzen, die in die Plattform integriert ist.

## Informationen zu diesem Lernprogramm

In diesem Lernprogramm für die ersten Schritte veranschaulichen wir die Grundlagen der Verwendung des Microsoft Azure-Dateispeichers. In diesem Lernprogramm wird Folgendes beschrieben:

- Verwenden von PowerShell, um die Erstellung einer neuen Azure-Dateifreigabe, das Hinzufügen eines Verzeichnisses, das Hochladen einer lokalen Datei in die Freigabe und das Auflisten der Dateien in dem Verzeichnis zu veranschaulichen.
- Bereitstellen der Dateifreigabe von einem virtuellen Azure-Computer, genau wie bei einer SMB-Freigabe.

Für Benutzer, die auf Dateien in einer Freigabe sowohl von einer lokalen Anwendung als auch von einem virtuellen Azure-Computer oder einem Cloud-Dienst aus zugreifen möchten, zeigen wir, wie Sie die Azure-Speicherclientbibliothek für .NET verwenden, um mit der Dateifreigabe von einer Desktopanwendung aus zu arbeiten.

> [AZURE.NOTE]Zur Ausführung der .NET-Codebeispiele in dieser Anleitung ist die Azure .NET Storage Client Library 4.x oder höher erforderlich. Die Speicherclientbibliothek steht über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) zur Verfügung.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Erstellen eines Azure-Speicherkontos

Der Azure-Dateispeicher befindet sich derzeit noch in der Vorschau. Navigieren Sie zur Anforderung des Zugriffs auf die Vorschau zur [Microsoft Azure-Vorschauseite](/services/preview/), und fordern Sie den Zugriff auf **Azure-Dateien** an. Nachdem Ihre Anforderung genehmigt wurde, werden Sie benachrichtigt, dass Sie auf die Dateispeichervorschau zugreifen können. Anschließend können Sie ein Speicherkonto für den Zugriff auf den Dateispeicher erstellen.

> [AZURE.NOTE]Dateispeicher ist derzeit nur für neue Speicherkonten verfügbar. Nachdem Sie in Ihrem Abonnement Zugriff auf den Dateispeicher erhalten haben, erstellen Sie ein neues Speicherkonto zur Verwendung in dieser Anleitung.
>
> Beachten Sie, dass der Azure-Dateispeicher derzeit keine Shared Access Signatures unterstützt.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Dateifreigabe mithilfe von PowerShell

Als Nächstes verwenden wir Azure PowerShell, um eine Dateifreigabe zu erstellen. Sobald die Dateifreigabe erstellt wurde, können Sie sie von einem beliebigen Dateisystem aus einbinden, das SMB 2.1 unterstützt.

### Installieren der PowerShell-Cmdlets für den Azure-Speicher

Laden Sie die Azure PowerShell-Cmdlets herunter und installieren Sie diese anschließend, um die Verwendung von PowerShell vorzubereiten. Informationen zum Installationspunkt und zu Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

> [AZURE.NOTE]Die PowerShell-Cmdlets für den Dateidienst sind nur im aktuellen Azure PowerShell-Modul, Version 0.8.5 oder höher, verfügbar. Sie sollten daher das neueste Azure PowerShell-Modul herunterladen und installieren bzw. ein Upgrade durchführen.

Öffnen Sie ein Azure PowerShell-Fenster, indem Sie auf **Start** klicken und **Azure PowerShell** eingeben. Im Azure PowerShell-Fenster wird das Azure Powershell-Modul geladen.

### Erstellen von Kontexten für Speicherkonten und -schlüssel

Erstellen Sie nun den Speicherkonto-Kontext. Der Kontext kapselt den Speicherkontonamen und den Kontoschlüssel. Anweisungen zum Kopieren Ihres Kontoschlüssels im Azure-Portal finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Ersetzen Sie im folgenden Beispiel `storage-account-name` und `storage-account-key` durch Ihren Speicherkontonamen und -schlüssel :

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Erstellen einer neuen Dateifreigabe

Erstellen Sie nun die neue Freigabe namens `logs` in diesem Beispiel:

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

Nun haben Sie eine Dateifreigabe im Dateispeicher. Als Nächstes fügen Sie ein Verzeichnis und eine Datei hinzu.

> [AZURE.IMPORTANT]Der Name der Dateifreigabe darf nur Kleinbuchstaben enthalten. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Erstellen eines Verzeichnisses in der Dateifreigabe

Erstellen Sie als Nächstes ein Verzeichnis in der Freigabe. Im folgenden Beispiel lautet der Verzeichnisname `CustomLogs`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Hochladen einer lokalen Datei in das Verzeichnis

Laden Sie nun eine lokale Datei in das Verzeichnis hoch. Im folgenden Beispiel wird eine Datei aus `C:\temp\Log1.txt` hochgeladen. Bearbeiten Sie den Dateipfad so, dass er auf eine gültige Datei auf Ihrem lokalen Computer verweist:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Auflisten der Dateien im Verzeichnis

Sie können die Dateien im Verzeichnis auflisten, um die Datei im Verzeichnis anzuzeigen. Mit diesem Befehl werden außerdem die Unterverzeichnisse aufgelistet, in diesem Beispiel ist jedoch kein Unterverzeichnis vorhanden, daher wird nur die Datei angezeigt.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Einbinden der Freigabe von einem virtuellen Azure-Computer unter Windows aus

Um zu veranschaulichen, wie eine Azure-Dateifreigabe eingebunden wird, erstellen wir nun einen virtuellen Azure-Computer unter Windows und greifen zum Einbinden der Freigabe remote darauf zu.

1. Erstellen Sie zunächst einen neuen virtuellen Azure-Computer, indem Sie die Anweisungen in [Erstellen eines virtuellen Windows Server-Computers](../virtual-machines-windows-tutorial.md) befolgen.
2. Als nächstes greifen Sie remote auf den virtuellen Computer zu, indem Sie die Anweisungen in [Anmeldung auf einem virtuellen Windows Server-Computer](../virtual-machines-log-on-windows-server.md) befolgen.
3. Öffnen Sie ein PowerShell-Fenster auf dem virtuellen Computer.

### Fortbestehen der Anmeldeinformationen Ihres Speicherkontos für den virtuellen Computer

Bevor die Bereitstellung für die Dateifreigabe erfolgt, bestätigen Sie zunächst die Anmeldeinformationen für Ihr Speicherkonto auf dem virtuellen Computer. Durch diesen Schritt kann Windows bei einem Neustart des virtuellen Computers automatisch erneut eine Verbindung zur Dateifreigabe herstellen. Um Ihre Anmeldeinformationen beizubehalten, führen Sie im PowerShell-Fenster auf dem virtuellen Computer den Befehl `cmdkey` aus. Ersetzen Sie `<storage-account-name>` durch den Namen Ihres Speicherkontos und `<storage-account-key>` durch den Schlüssel des Speicherkontos:

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows stellt nun bei einem Neustart des virtuellen Computers erneut eine Verbindung zur Dateifreigabe her. Sie können überprüfen, ob die Freigabe erneut verbunden wurde, indem Sie den Befehl `net use` in einem PowerShell-Fenster ausführen.

### Bereitstellen der Dateifreigabe mithilfe der dauerhaften Anmeldeinformationen

Nachdem Sie eine Remoteverbindung zu dem virtuellen Computer hergestellt haben, können Sie den Befehl `net use` mit folgender Syntax ausführen, um die Dateifreigabe bereitzustellen. Ersetzen Sie `<storage-account-name>` durch den Namen Ihres Speicherkontos und `<share-name>` durch den Namen Ihrer Dateispeicher-Freigabe:

    net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

> [AZURE.NOTE]Da Sie die Speicherkonto-Anmeldeinformationen im vorherigen Schritt dauerhaft gespeichert haben, müssen Sie diese nicht mit dem Befehl `net use` angeben. Wenn Sie Ihre Anmeldeinformationen jedoch nicht dauerhaft gespeichert haben, fügen Sie sie als Parameter hinzu, der an den Befehl `net use` übergeben wird.

    net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Sie können nun mit der Dateispeicher-Freigabe vom virtuellen Computer aus arbeiten, wie von jedem anderen Laufwerk auch. Sie können die Standarddateibefehle über die Eingabeaufforderung eingeben oder die bereitgestellte Freigabe und deren Inhalt im Datei-Explorer anzeigen. Außerdem können Sie Code auf dem virtuellen Computer ausführen, der über standardmäßige Windows-E/A-APIs auf die Dateifreigabe zugreift, die z. B. über die \[System.IO-Namespaces\]\(http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx\) in .NET Framework bereitgestellt werden.

Sie können die Dateifreigabe auch über eine im Azure-Clouddienst ausgeführte Rolle bereitstellen, indem Sie eine Remoteverbindung zur Rolle herstellen.

## Erstellen einer lokalen Anwendung für den Zugriff auf den Dateispeicher

Sie können die Dateifreigabe von einem virtuellen Computer aus oder von einem in Azure ausgeführten Cloud-Dienst einbinden, wie oben gezeigt. Sie können die Dateifreigabe jedoch nicht von einer lokalen Anwendung aus einbinden. Zum Zugriff auf freigegebene Daten von einer lokalen Anwendung aus verwenden Sie die Dateispeicher-API. In diesem Beispiel wird gezeigt, wie Sie über die [Azure .NET-Speicherclientbibliothek](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409) mit einer Dateifreigabe arbeiten.

Um die Verwendung der API von einer lokalen Anwendung aus zu veranschaulichen, erstellen wir eine einfache Konsolenanwendung, die auf dem Desktop ausgeführt wird.

### Erstellen der Konsolenanwendung und Abrufen der Assembly

So erstellen Sie eine neue Konsolenanwendung in Visual Studio und installieren das Azure Storage NuGet-Paket:

1. Wählen Sie in Visual Studio **Datei** -\> **Neues Projekt**, und wählen Sie dann **Windows** -\> **Konsolenanwendung** aus der Liste der Visual C\#-Vorlagen aus.
2. Stellen Sie einen Namen für die Konsolenanwendung bereit, und klicken Sie dann auf **OK**.
3. Nachdem Ihr Projekt erstellt wurde, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf das Projekt und wählen **NuGet-Pakete verwalten** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

### Speichern Ihrer Speicherkonto-Anmeldeinformationen in der Datei „app.config“

Als Nächstes speichern Sie Ihre Anmeldeinformationen in der Datei „app.config“ des Projekts. Bearbeiten Sie die Datei "app.config" ähnlich wie im folgenden Beispiel, indem Sie `myaccount` durch den Namen Ihres Speicherkontos und `mykey` durch den Schlüssel Ihres Speicherkontos ersetzen:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]Die neueste Version des Azure-Speicheremulators bietet keine Unterstützung für Dateispeicher. Die Verbindungszeichenfolge muss auf ein Azure-Speicherkonto in der Cloud mit Zugriff auf die Files-Vorschau verweisen.


### Hinzufügen von Namespace-Deklarationen

Öffnen Sie die Datei „program.cs“ über den Projektmappen-Explorer, und fügen Sie die folgenden Namespace-Deklarationen am Anfang der Datei hinzu:

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### Programmgesteuertes Abrufen der Verbindungszeichenfolge

Sie können Ihre gespeicherten Anmeldeinformationen aus der Datei „app.config“ entweder mit der Klasse `Microsoft.WindowsAzure.CloudConfigurationManager` oder der Klasse `System.Configuration.ConfigurationManager ` abrufen. In diesem Beispiel wird dargestellt, wie Sie Ihre Anmeldeinformationen mithilfe der Klasse `CloudConfigurationManager` abrufen und dann mit der Klasse `CloudStorageAccount` kapseln. Fügen Sie den folgenden Code der `Main()`-Methode in "program.cs" hinzu:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Programmgesteuerter Zugriff auf die Dateispeicher-Freigabe

Fügen Sie als Nächstes den folgenden Code der `Main()`-Methode nach dem oben angegebenen Code hinzu, um die Verbindungszeichenfolge abzurufen. Dieser Code ruft einen Verweis auf die zuvor erstellte Datei ab und gibt dessen Inhalt im Konsolenfenster an.

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        //Ensure that the file exists.
	        if (file.Exists())
	        {
	            //Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Führen Sie die Konsolenanwendung aus, um die Ausgabe zu sehen.

## Einbinden der Freigabe von einem virtuellen Azure-Computer unter Linux aus

Bei der Erstellung eines virtuellen Azure-Computers können Sie ein Ubuntu-Image aus der Galerie der Datenträgerimages angeben, um die Unterstützung für SMB 2.1 sicherzustellen. Allerdings kann jede Linux-Distribution, die SMB 2.1 unterstützt, eine Azure-Dateifreigabe einbinden.

Eine Demonstration, wie eine Azure-Dateifreigabe unter Linux eingebunden wird, finden Sie unter [Freigegebener Speicher unter Linux mithilfe der Vorschau der Azure-Dateien - Teil 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) auf Channel 9.

## Nächste Schritte

Weitere Informationen zum Azure-Dateispeicher erhalten Sie über diese Links.

### Referenz

- [Referenz zur Storage-Clientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Referenz zur REST-API des Dateidiensts](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Blogbeiträge

- [Einführung in den Microsoft Azure-Dateidienst](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Beibehalten von Verbindungen zu Microsoft Azure-Dateien](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
 

<!---HONumber=July15_HO5-->