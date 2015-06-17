<properties 
	pageTitle="Verwenden des Azure-Dateispeichers | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Microsoft Azure-Dateispeicher zum Erstellen von Dateifreigaben und zum Verwalten des Dateiinhalts verwenden. Die Beispiele sind in PowerShell und C# geschrieben." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tamram"/>

# Verwenden des Azure-Dateispeichers

## Übersicht

In diesem Leitfaden für die ersten Schritte veranschaulichen wir die Grundlagen der Verwendung des Microsoft Azure-Dateispeichers. Zuerst erstellen wir mithilfe von PowerShell eine neue Azure-Dateifreigabe, fügen ein Verzeichnis hinzu, laden eine lokale Datei in die Freigabe hoch und listen die Dateien im Verzeichnis auf. Anschließend zeigen wir, wie die Dateifreigabe von einem virtuellen Azure-Computer bereitgestellt wird, genau wie bei einer SMB-Freigabe.

Für Benutzer, die auf Dateien in einer Freigabe sowohl von einer lokalen Anwendung als auch von einem virtuellen Azure-Computer oder einem Cloud-Dienst aus zugreifen möchten, zeigen wir, wie Sie die Azure-Speicherclientbibliothek für .NET verwenden, um mit der Dateifreigabe von einer Desktopanwendung aus zu arbeiten.

> [AZURE.NOTE] Zur Ausführung der .NET-Codebeispiele in dieser Anleitung ist die Azure .NET Storage Client Library 4.x oder höher erforderlich. Die Storage-Clientbibliothek kann unter [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) abgerufen werden.

## Was ist der Azure-Dateispeicher?

Der Dateispeicher bietet einen gemeinsam genutzten Speicher für Anwendungen und verwendet dabei das SMB 2.1-Protokoll. Virtuelle Microsoft Azure-Computer und Clouddienste können Dateidaten in verschiedenen Anwendungskomponenten über eingebundene Freigaben teilen, und lokale Anwendungen können über die Dateispeicher-API auf freigegebene Dateien zugreifen.

Anwendungen in virtuellen Azure-Computern oder Cloud-Diensten können eine Dateispeicherfreigabe bereitstellen, um auf Dateidaten zuzugreifen, ebenso wie eine Desktopanwendung eine typische SMB-Freigabe bereitstellen würde. Die Dateispeicher-Freigaben können von beliebig vielen virtuellen Azure-Computern oder -Rollen gleichzeitig eingebunden und abgerufen werden.

Da es sich bei Dateispeicher-Freigaben um gewöhnliche SMB 2.1-Freigaben handelt, können Anwendungen in Azure über die E/A-APIs auf die Freigaben zugreifen. Entwickler können daher ihren vorhandenen Code und bereits erlernte Fertigkeiten für die Migration vorhandener Anwendungen verwenden. IT-Fachkräfte können PowerShell-Cmdlets verwenden, um Dateispeicher-Freigaben im Rahmen der Administration von Azure-Anwendungen zu erstellen, einzubinden und zu verwalten. Dieser Leitfaden enthält Beispiele für beide Varianten.

Dateispeicher werden hauptsächlich für folgende Zwecke verwendet:

- Migration von lokalen Anwendungen, die Dateifreigaben aus virtuellen Azure-Computern oder Clouddiensten ohne teure Umschreibungen verwenden
- Speichern von gemeinsam genutzten Anwendungseinstellungen, z. B. in Konfigurationsdateien
- Speichern von Diagnosedaten wie Protokolle, Metriken und Absturzabbilder in einem freigegebenen Verzeichnis 
- Speichern von Tools und Dienstprogrammen für das Entwickeln und Verwalten von virtuellen Azure-Computern oder Clouddiensten

## Dateispeicherkonzepte

Der Dateispeicher umfasst die folgenden Komponenten:

![files-concepts][files-concepts]


-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx) erhalten Sie ausführliche Informationen über die Speicherkapazität des Kontos.

-   **Freigabe:** Eine Dateispeicherfreigabe ist eine SMB 2.1-Dateifreigabe in Azure.
    Alle Verzeichnisse und Dateien müssen in der übergeordneten Freigabe erstellt werden. Ein Konto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze des Speicherkontos erreicht ist.

-   **Verzeichnis:** Eine optionale Hierarchie von Verzeichnissen.

-	**Datei:** Eine Datei in der Freigabe. Eine Datei kann bis zu 1 TB groß sein.

-   **URL-Format:** Dateien sind über die folgende URL adressierbar
    format:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    Mit der folgenden Beispiel-URL kann eine der Dateien im obigen Diagramm adressiert werden:  
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



Ausführliche Informationen zur Benennung von Freigaben, Verzeichnissen und Dateien finden Sie unter [Benennen von und Verweisen auf Freigaben, Verzeichnisse, Dateien und Metadaten](http://msdn.microsoft.com/library/azure/dn167011.aspx).

## Erstellen eines Azure-Speicherkontos

Der Azure-Dateispeicher befindet sich derzeit noch in der Vorschau. Um Zugriff auf die Vorschau zu erhalten, navigieren Sie zur [Microsoft Azure-Vorschauseite](/services/preview/), und fordern Sie den Zugriff auf **Azure-Dateien** an. Sobald Ihre Anfrage genehmigt wurde, werden Sie benachrichtigt, dass Sie die Dateispeicher-Vorschau verwenden können. Anschließend können Sie ein Speicherkonto für den Zugriff auf den Dateispeicher erstellen.

> [AZURE.NOTE] Dateispeicher ist derzeit nur für neue Speicherkonten verfügbar. Nachdem Sie in Ihrem Abonnement Zugriff auf den Dateispeicher erhalten haben, erstellen Sie ein neues Speicherkonto zur Verwendung in dieser Anleitung.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Dateifreigabe mithilfe von PowerShell

Als Nächstes verwenden wir Azure PowerShell, um eine Dateifreigabe zu erstellen. Sobald die Dateifreigabe erstellt wurde, können Sie sie von einem beliebigen Dateisystem aus einbinden, das SMB 2.1 unterstützt. 

### Installieren der PowerShell-Cmdlets für den Azure-Speicher

Zur Vorbereitung laden Sie PowerShell herunter und installieren die Azure PowerShell-Cmdlets. Unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md) finden Sie den Installationspunkt und Anweisungen zur Installation.

> [AZURE.NOTE] Die PowerShell-Cmdlets für den Dateidienst sind nur im aktuellen Azure PowerShell-Modul, Version 0.8.5 oder höher, verfügbar. Sie sollten daher das neueste Azure PowerShell-Modul herunterladen und installieren bzw. ein Upgrade durchführen.

Öffnen Sie ein Azure PowerShell-Fenster, indem Sie auf **Start** klicken und **Azure PowerShell** eingeben. Im Azure PowerShell-Fenster wird das Azure Powershell-Modul geladen.

### Erstellen eines Kontexts für das Speicherkonto und den Schlüssel

Erstellen Sie nun den Speicherkonto-Kontext. Der Kontext kapselt den Speicherkontonamen und den Kontoschlüssel. Ersetzen Sie im folgenden Beispiel `account-name` und `account-key` durch Ihren Kontonamen und den Schlüssel:

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key
    
### Erstellen einer neuen Dateifreigabe

Erstellen Sie nun die neue Freigabe namens `sampleshare` in diesem Beispiel:

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

Nun haben Sie eine Dateifreigabe im Dateispeicher. Als Nächstes fügen Sie ein Verzeichnis und eine Datei hinzu.

### Erstellen eines Verzeichnisses in der Dateifreigabe

Erstellen Sie als Nächstes ein Verzeichnis in der Freigabe. Im folgenden Beispiel lautet der Verzeichnisname `sampledir`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### Hochladen einer lokalen Datei in das Verzeichnis

Laden Sie nun eine lokale Datei in das Verzeichnis hoch. Im folgenden Beispiel wird eine Datei aus `C:\temp\samplefile.txt` hochgeladen. Bearbeiten Sie den Dateipfad so, dass er auf eine gültige Datei auf Ihrem lokalen Computer verweist: 
    
    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### Auflisten der Dateien im Verzeichnis

Um die Datei im Verzeichnis anzuzeigen, können Sie die Verzeichnisdateien auflisten. Mit diesem Befehl werden außerdem die Unterverzeichnisse aufgelistet, in diesem Beispiel ist jedoch kein Unterverzeichnis vorhanden, daher wird nur die Datei angezeigt.  

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## Einbinden der Freigabe von einem virtuellen Azure-Computer unter Windows aus

Um zu veranschaulichen, wie eine Azure-Dateifreigabe eingebunden wird, erstellen wir nun einen virtuellen Azure-Computer unter Windows und greifen zum Einbinden der Freigabe remote darauf zu. 

1. Erstellen Sie zuerst einen neuen virtuellen Azure-Computer, indem Sie die Anleitung unter [Erstellen eines virtuellen Windows Server-Computers] befolgen(virtual-machines-windows-tutorial.md).
2. Stellen Sie anschließend eine Remoteverbindung mit dem virtuellen Computer her. Eine Beschreibung finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](../virtual-machines-log-on-windows-server.md).
3. Öffnen Sie ein PowerShell-Fenster auf dem virtuellen Computer. 

### Beibehalten Ihrer Speicherkonto-Anmeldeinformationen auf dem virtuellen Computer

Bevor Sie die Dateifreigabe bereitstellen, speichern Sie zuerst dauerhaft Ihre Speicherkonto-Anmeldeinformationen auf dem virtuellen Computer. Durch diesen Schritt kann Windows bei einem Neustart des virtuellen Computers automatisch erneut eine Verbindung zur Dateifreigabe herstellen. Um Ihre Anmeldeinformationen beizubehalten, führen Sie im PowerShell-Fenster auf dem virtuellen Computer den Befehl `cmdkey` aus. Ersetzen Sie `<storage-account>` durch den Namen Ihres Speicherkontos und `<account-key>` durch den Schlüssel des Speicherkontos:

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

Windows stellt nun bei einem Neustart des virtuellen Computers erneut eine Verbindung zur Dateifreigabe her. Sie können überprüfen, ob die Freigabe erneut verbunden wurde, indem Sie den Befehl `net use` in einem PowerShell-Fenster ausführen.

### Bereitstellen der Dateifreigabe mithilfe der dauerhaften Anmeldeinformationen

Nachdem Sie eine Remoteverbindung zum virtuellen Computer hergestellt haben, können Sie den Befehl  `net use` mit folgender Syntax ausführen, um die Dateifreigabe bereitzustellen. Ersetzen Sie `<storage-account>` durch den Namen Ihres Speicherkontos und `<share-name>` durch den Namen der Dateispeicherfreigabe.

	net use z: <storage-account>.file.core.windows.net<share-name>

> [AZURE.NOTE] Da Sie die Speicherkonto-Anmeldeinformationen im vorherigen Schritt dauerhaft gespeichert haben, müssen Sie diese nicht mit dem Befehl  `net use` angeben. Wenn Sie Ihre Anmeldeinformationen jedoch nicht dauerhaft gespeichert haben, fügen Sie sie als Parameter hinzu, der an den Befehl  `net use` übergeben wird. Ersetzen Sie `<storage-account>` durch den Namen Ihres Speicherkontos, `<share-name>` durch den Namen der Dateispeicher-Freigabe und `<account-key>` durch den Speicherkontoschlüssel:
	   
	net use z: <storage-account>.file.core.windows.net<share-name> /u:<storage-account> <account-key>

Sie können nun mit der Dateispeicher-Freigabe vom virtuellen Computer aus arbeiten wie von jedem anderen Laufwerk auch. Sie können die Standarddateibefehle über die Eingabeaufforderung eingeben oder die bereitgestellte Freigabe und deren Inhalt im Datei-Explorer anzeigen. Außerdem können Sie Code auf dem virtuellen Computer ausführen, der über standardmäßige Windows-E/A-APIs auf die Dateifreigabe zugreift, die z. B. über die [System.IO-Namespaces](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) in .NET Framework bereitgestellt werden. 

Sie können die Dateifreigabe auch über eine im Azure-Clouddienst ausgeführte Rolle bereitstellen, indem Sie eine Remoteverbindung zur Rolle herstellen.

## Erstellen einer lokalen Anwendung für den Zugriff auf den Dateispeicher

Sie können die Dateifreigabe von einem virtuellen Computer aus oder von einem in Azure ausgeführten Cloud-Dienst einbinden, wie oben gezeigt. Sie können die Dateifreigabe jedoch nicht von einer lokalen Anwendung aus einbinden. Zum Zugriff auf freigegebene Daten von einer lokalen Anwendung aus verwenden Sie die Dateispeicher-API. In diesem Beispiel wird gezeigt, wie Sie über die [Azure-Speicherclientbibliothek für .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409) mit einer Dateifreigabe arbeiten. 

Um die Verwendung der API von einer lokalen Anwendung aus zu veranschaulichen, erstellen wir eine einfache Konsolenanwendung, die auf dem Desktop ausgeführt wird.

### Erstellen der Konsolenanwendung und Abrufen der Assembly

So erstellen Sie eine neue Konsolenanwendung in Visual Studio und installieren das Azure Storage NuGet-Paket:

1. Wählen Sie in Visual Studio **Datei** -> **Neues Projekt**, und wählen Sie **Windows** -> **Konsolenanwendung** aus der Liste der Visual C#-Vorlagen.
2. Geben Sie einen Namen für die Konsolenanwendung an, und klicken Sie auf **OK**.
3. Nachdem das Projekt erstellt wurde, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt und wählen **NuGet-Pakete verwalten** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie dann auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

### Speichern Ihrer Speicherkonto-Anmeldeinformationen in der Datei "app.config"

Als Nächstes speichern Sie Ihre Anmeldeinformationen in der Datei "app.config" des Projekts. Bearbeiten Sie die Datei "app.config" ähnlich wie im folgenden Beispiel, indem Sie  `myaccount` durch den Namen Ihres Speicherkontos und  `mykey` durch den Schlüssel Ihres Speicherkontos ersetzen:

    <?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [AZURE.NOTE] Die neueste Version des Azure-Speicheremulators bietet keine Unterstützung für Dateispeicher. Die Verbindungszeichenfolge muss auf ein Azure-Speicherkonto in der Cloud mit Zugriff auf die Files-Vorschau verweisen.


### Hinzufügen von Namespace-Deklarationen
Öffnen Sie die Datei "program.cs" im Projektmappen-Explorer, und fügen Sie die folgenden Namespace-Deklarationen zu Beginn der Datei hinzu:

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### Programmgesteuertes Abrufen der Verbindungszeichenfolge
Sie können Ihre gespeicherten Anmeldeinformationen aus der Datei "app.config" entweder mit der Klasse `Microsoft.WindowsAzure.CloudConfigurationManager` oder mit der Klasse `System.Configuration.ConfigurationManager ` abrufen. In diesem Beispiel wird dargestellt, wie Sie Ihre Anmeldeinformationen mithilfe der Klasse`CloudConfigurationManager` abrufen und dann mit der Klasse `CloudStorageAccount` kapseln. Fügen Sie den folgenden Code in die Methode  `Main()` in "program.cs" ein:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Programmgesteuerter Zugriff auf die Dateispeicher-Freigabe

Als Nächstes fügen Sie den folgenden Code zur Methode  `Main()` nach dem oben angegebenen Code hinzu, um die Verbindungszeichenfolge abzurufen. Dieser Code ruft einen Verweis auf die zuvor erstellte Datei ab und gibt dessen Inhalt im Konsolenfenster an.

	//Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

	//Ensure that the share exists.
    if (share.Exists())
    {
		//Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

		//Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

		//Ensure that the directory exists.
        if (sampleDir.Exists())
        {
			//Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

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

Eine Demonstration, wie eine Azure-Dateifreigabe unter Linux eingebunden wird, finden Sie unter [Shared storage on Linux via Azure Files Preview - Part 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) (in englischer Sprache) auf Channel 9.

## Nächste Schritte

Nachdem Sie nun die Grundlagen des Dateispeichers kennen gelernt haben, folgen Sie diesen Links, um
ausführlichere Informationen abzurufen.
<ul>
<li>In der Referenzdokumentation für den Dateispeicherdienst finden Sie alle Details zu verfügbaren APIs:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Speicherclientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dn167006.aspx">REST-API-Referenz des Dateidiensts</a></li>
  </ul>
</li>
<li>Lesen Sie die Blogbeiträge des Azure Storage-Teams zum Thema Dateidienste:
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Einführung in den Microsoft Azure-Dateidienst</a>
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">Beibehalten der Verbindung zu Microsoft Azure Files</a></li>
  </ul>
</li><li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Blobspeicher</a> zum Speichern unstrukturierter Daten.</li>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Tabellenspeicher</a> zum Speichern strukturierter Daten.</li>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Warteschlangenspeicher</a> zum zuverlässigen Speichern von Nachrichten.</li>
    <li>Verwenden Sie eine <a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten.</li>
  </ul>
</li>
</ul>

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png


<!--HONumber=49--> 

<!--HONumber=49--> 