<properties linkid="dev-net-how-to-file-storage" urlDisplayName="File Service" pageTitle="How to use Azure File storage | Microsoft Azure" metaKeywords="Get started Azure file  Azure file share  Azure file shares  Azure file   Azure file storage   Azure file .NET   Azure file C#   Azure file PowerShell" description="Learn how to use Microsoft Azure File storage to create file shares and manage file content. Samples are written in PowerShell and C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure File storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Verwenden des Azure-Dateispeichers

In diesem Leitfaden für die ersten Schritte werden die Grundlagen zur Verwendung des Microsoft Azure-Dateispeichers veranschaulicht. Zunächst verwenden wir PowerShell, um die Erstellung einer neuen Azure-Dateifreigabe, das Hinzufügen eines Verzeichnisses, das Hochladen einer lokalen Datei in die Freigabe und das Auflisten der Dateien im Verzeichnis anzuzeigen. Dann zeigen wir, wie die Dateifreigabe wie bei jeder anderen SMB-Freigabe über einen virtuellen Azure-Computer bereitgestellt wird.

Für Benutzer, die auf Dateien einer Freigabe von einer lokalen Anwendung sowie von einem virtuellen Azure-Computer bzw. Clouddienst zugreifen möchten, wird gezeigt, wie mit der Azure .NET-Speicherclientbibliothek über eine Desktopanwendung mit der Dateifreigabe gearbeitet wird.

> [WACOM.NOTE] Die Ausführung der .NET-Codebeispiele in dieser Anleitung erfordert Azure .NET-Speicherclientbibliothek 4.x oder höher. Die Speicherclientbibliothek steht über [NuGet][NuGet] zur Verfügung.

## Inhaltsverzeichnis

-   [Was ist der Dateispeicher?][Was ist der Dateispeicher?]
-   [Dateispeicherkonzepte][Dateispeicherkonzepte]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Verwenden von PowerShell zum Erstellen einer Dateifreigabe][Verwenden von PowerShell zum Erstellen einer Dateifreigabe]
-   [Bereitstellen der Freigabe über einen virtuellen Azure-Computer][Bereitstellen der Freigabe über einen virtuellen Azure-Computer]
-   [Erstellen einer lokalen Anwendung für den Zugriff auf den Dateispeicher][Erstellen einer lokalen Anwendung für den Zugriff auf den Dateispeicher]
-   [Nächste Schritte][Nächste Schritte]

## <a name="what-is-file-storage"></a><span class="short-header">Was ist der Azure-Dateispeicher?</span>Was ist der Azure-Dateispeicher?

Der Dateispeicher bietet einen freigegebenen Speicher für Anwendungen, die das standardmäßige SMB 2.1-Protokoll verwenden. Virtuelle Microsoft Azure-Computer und Clouddienste können Dateidaten in verschiedenen Anwendungskomponenten über eingebundene Freigaben teilen, und lokale Anwendungen können über die Dateispeicher-API des Dateidiensts auf freigegebene Dateien zugreifen.

Anwendungen in virtuellen Azure-Computern oder Cloud-Diensten können eine Dateispeicher-Freigabe einbinden, um auf Dateidaten zuzugreifen, ebenso wie eine Desktopanwendung eine typische SMB-Freigabe einbinden würde. Die Dateispeicherfreigabe kann von einer beliebigen Anzahl von virtuellen Azure-Computern oder -Rollen gleichzeitig bereitgestellt und verwendet werden.

Da eine Dateispeicherfreigabe eine standardmäßige SMB 2.1-Dateifreigabe ist, können in Azure ausgeführte Anwendungen über Datei-E/A-APIs auf Daten der Freigabe zugreifen. Entwickler können daher ihren vorhandenen Code und bereits erlernte Fertigkeiten für die Migration vorhandener Anwendungen verwenden. IT-Fachkräfte können PowerShell-Cmdlets verwenden, um Dateispeicher-Freigaben im Rahmen der Administration von Azure-Anwendungen zu erstellen, einzubinden und zu verwalten. Diese Anleitung zeigt jeweils Beispiele dazu.

Der Dateispeicher wird hauptsächlich für folgende Zwecke verwendet:

-   Migrieren von lokalen Anwendungen, die für die Ausführung auf virtuellen Azure-Computern oder Clouddiensten auf Dateifreigaben beruhen, ohne aufwendige Umschreibevorgänge zu erfordern.
-   Speichern freigegebener Anwendungseinstellungen, z. B. in Konfigurationsdateien.
-   Speichern von Diagnosedaten wie Protokolle, Metriken und Absturzabbilder an einem freigegebenen Speicherort.
-   Speichern von Tools und Dienstprogrammen zur Entwicklung und Verwaltung von virtuellen Azure-Computern und Clouddiensten.

## <a name="file-storage-concepts"></a><span class="short-header">Dateispeicherkonzepte</span>Dateispeicherkonzepte

Der Dateispeicher enthält die folgenden Komponenten:

![files-concepts][files-concepts]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen
    über ein Speicherkonto. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets][Azure Storage Scalability and Performance Targets] (Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher, in englischer Sprache).

-   **Freigabe:** Eine Dateispeicherfreigabe ist eine SMB 2.1-Dateifreigabe in Azure.
    Alle Verzeichnisse und Dateien müssen in einer übergeordneten Freigabe erstellt werden. Ein Konto kann eine
    unbegrenzte Anzahl von Freigaben enthalten, während eine Freigabe wiederum eine unbegrenzte
    Anzahl von Dateien bis zur Kapazitätsgrenze des Speicherkontos aufnehmen kann.

-   **Directory:** Eine optionale Hierarchie von Verzeichnissen.

-   **Datei:** Eine Datei in der Freigabe. Die maximale Größe für eine Datei beträgt 1 TB.

-   **URL-Format:** Dateien sind mithilfe des folgenden
    URL-Formats adressierbar:
    <https://>`<storage account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`

    Die folgende Beispiel-URL könnte dazu verwendet werden, eine der Dateien im
    obigen Diagramm zu adressieren:
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`

Informationen zur Benennung von Freigaben, Verzeichnissen und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten][Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten].

## <a name="create-account"></a><span class="short-header">Erstellen eines Azure-Speicherkontos</span>Erstellen eines Azure-Speicherkontos

Der Azure-Dateispeicher befindet sich derzeit in der Vorschauphase. Navigieren Sie zur Anforderung des Zugriffs auf die Vorschau zur [Microsoft Azure-Vorschauseite][Microsoft Azure-Vorschauseite], und fordern Sie den Zugriff auf **Azure-Dateien** an. Nachdem Ihre Anforderung genehmigt wurde, werden Sie benachrichtigt, dass Sie auf die Dateispeichervorschau zugreifen können. Sie können dann ein Speicherkonto für den Zugriff auf den Dateispeicher erstellen.

> [WACOM.NOTE] Der Dateispeicher ist derzeit nur für neue Speicherkonten verfügbar. Nachdem Ihrem Abonnement der Zugriff auf den Dateispeicher gewährt wurde, erstellen Sie ein neues Speicherkonto, das für diese Anleitung verwendet wird.

[WACOM.INCLUDE [create-storage-account][create-storage-account]]

## <a name="use-cmdlets"></a><span class="short-header">Verwenden von PowerShell zum Erstellen einer Dateifreigabe</span>Verwenden von PowerShell zum Erstellen einer Dateifreigabe

### Installieren der PowerShell-Cmdlets für den Azure-Speicher

Laden Sie die Azure PowerShell-Cmdlets herunter und installieren Sie diese anschließend, um die Verwendung von PowerShell vorzubereiten. Informationen zum Installationspunkt und zu Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

> [WACOM.NOTE] Die PowerShell-Cmdlets für den Dateidienst sind nur im neuesten Azure PowerShell-Modul, Version 0.8.5 oder höher, verfügbar. Es wird empfohlen, dass Sie das neueste Azure PowerShell-Modul herunterladen und installieren oder ein entsprechendes Upgrade ausführen.

Öffnen Sie ein Azure PowerShell-Fenster, indem Sie auf **Start** klicken und **Windows Azure PowerShell** eingeben. Das Azure PowerShell-Fenster lädt das Azure PowerShell-Modul für Sie.

### Erstellen von Kontexten für Speicherkonten und -schlüssel

Jetzt erstellen Sie den Speicherkontokontext. Der Kontext kapselt den Kontonamen und den Kontoschlüssel. Ersetzen Sie im folgenden Beispiel `account-name` und `account-key` durch Ihren Kontonamen und -schlüssel:

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key

### Erstellen einer neuen Dateifreigabe

Als nächstes erstellen Sie die neue Freigabe, die in diesem Beispiel die Bezeichnung `sampleshare` erhält:

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

Sie verfügen jetzt über eine Dateifreigabe im Dateispeicher. Als nächstes werden ein Verzeichnis und eine Datei hinzugefügt.

### Erstellen eines Verzeichnisses in der Dateifreigabe

Erstellen Sie jetzt ein Verzeichnis in der Freigabe. Im folgenden Beispiel erhält das Verzeichnis die Bezeichnung `sampledir`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### Hochladen einer lokalen Datei in das Verzeichnis

Laden Sie jetzt eine lokale Datei in das Verzeichnis hoch. Im folgenden Beispiel wird eine Datei aus `C:\temp\samplefile.txt` hochgeladen. Bearbeiten Sie den Dateipfad, damit er auf eine gültige Datei auf Ihrem lokalen Computer verweist:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### Auflisten der Dateien im Verzeichnis

Sie können die Dateien im Verzeichnis auflisten, um die Datei im Verzeichnis anzuzeigen. Mit diesem Befehl werden auch Unterverzeichnisse aufgelistet, aber in diesem Beispiel sind keine Unterverzeichnisse vorhanden, daher wird nur die Datei aufgeführt.

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## <a name="mount-share"></a><span class="short-header">Bereitstellen der Freigabe von einem virtuellen Azure-Computer</span>Bereitstellen der Freigabe von einem virtuellen Azure-Computer

Zur Veranschaulichung der Bereitstellung einer Azure-Dateifreigabe erstellen wir jetzt einen virtuellen Azure-Computer und stellen die Freigabe über Remotezugriff bereit.

1.  Erstellen Sie zunächst einen neuen virtuellen Azure-Computer, indem Sie die Anweisungen in [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers] befolgen.
2.  Als nächstes greifen Sie remote auf den virtuellen Computer zu, indem Sie die Anweisungen in [Anmeldung auf einem virtuellen Windows Server-Computer][Anmeldung auf einem virtuellen Windows Server-Computer] befolgen.
3.  Öffnen Sie ein PowerShell-Fenster auf dem virtuellen Computer.

### Fortbestehen der Anmeldeinformationen Ihres Speicherkontos für den virtuellen Computer

Bevor die Bereitstellung für die Dateifreigabe erfolgt, bestätigen Sie zunächst die Anmeldeinformationen für Ihr Speicherkonto auf dem virtuellen Computer. Dieser Schritt gestattet es Windows, die Verbindung zur Dateifreigabe automatisch wiederherzustellen, wenn der virtuelle Computer neu gestartet wird. Damit die Anmeldeinformationen für Ihr Konto fortbestehen, führen Sie den Befehl `cmdkey` auf dem virtuellen Computer im PowerShell-Fenster aus. Ersetzen Sie `<storage-account>` durch den Namen Ihres Speicherkontos und `<account-key>` durch Ihren Speicherkontoschlüssel:

    cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

Windows stellt jetzt die Verbindung zu Ihrer Dateifreigabe wieder her, wenn der virtuelle Computer neu gestartet wird. Sie können überprüfen, ob die Verbindung zur Freigabe erneut hergestellt wurde, indem Sie den Befehl `net use` in einem PowerShell-Fenster ausführen.

### Bereitstellen der Dateifreigabe mithilfe der fortbestehenden Anmeldeinformationen

Nachdem eine Remoteverbindung zum virtuellen Computer besteht, können Sie den Befehl `net use` zum Bereitstellen der Dateifreigabe mithilfe der folgenden Syntax ausführen. Ersetzen Sie `<storage-account>` durch den Namen Ihres Speicherkontos und `<share-name>` durch den Namen Ihrer Dateispeicherfreigabe.

    net use z: \<storage-account>.file.core.windows.netnet use z: \\<storage-account>.file.core.windows.net\<share-name>lt;share-name>

> [WACOM.NOTE] Da Sie die Anmeldeinformationen Ihres Speicherkontos im vorherigen Schritt bestätigt haben, müssen Sie sie nicht mithilfe des Befehls `net use` bereitstellen. Wenn Sie Ihre Anmeldeinformationen noch nicht bestätigt haben, beziehen Sie diese als Parameter ein, der an den Befehl `net use` übergeben wird. Ersetzen Sie `<storage-account>` durch den Namen Ihres Speicherkontos, `<share-name>` durch den Namen Ihrer Dateispeicherfreigabe und `<account-key>` durch Ihren Speicherkontoschlüssel:

    net use z: \<storage-account>.file.core.windows.netnet use z: \\<storage-account>.file.core.windows.net\<share-name> /u:<storage-account> <account-key>lt;share-name> /u:<storage-account> <account-key>

Sie können jetzt auf dem virtuellen Computer wie bei jedem anderen Laufwerk mit der Dateispeicherfreigabe arbeiten. Sie können Standarddateibefehle über die Eingabeaufforderung ausführen oder die bereitgestellte Freigabe und ihre Inhalte über den Datei-Explorer anzeigen. Sie können auf dem virtuellen Computer auch Code ausführen, der mithilfe der standardmäßigen Datei-E/A-APIs von Windows (z. B. die von [System.IO-Namespaces][System.IO-Namespaces] im .NET Framework bereitgestellten APIs) auf die Dateifreigabe zugreift.

Sie können die Dateifreigabe auch über eine Rolle bereitstellen, die in einem Azure-Clouddienst ausgeführt wird, indem remote auf die Rolle zugegriffen wird.

## <a name="create-console-app"></a><span class="short-header">Erstellen einer lokalen Anwendung für die Arbeit mit dem Dateispeicher</span>Erstellen einer lokalen Anwendung für die Arbeit mit dem Dateispeicher

Sie können eine Dateispeicherfreigabe innerhalb eines in Azure ausgeführten virtuellen Computers oder eines Clouddiensts bereitstellen, wie oben veranschaulicht. Sie können eine Dateispeicherfreigabe jedoch nicht über eine lokale Anwendung bereitstellen. Sie müssen die Dateispeicher-API verwenden, um über eine lokale Anwendung auf die Freigabedaten zuzugreifen. In diesem Beispiel wird gezeigt, wie Sie über die [Azure .NET-Speicherclientbibliothek][Azure .NET-Speicherclientbibliothek] mit einer Dateifreigabe arbeiten.

Wir erstellen eine einfache Konsolenanwendung, die auf dem Desktop ausgeführt wird, um die Verwendung einer API über eine lokale Anwendung zu veranschaulichen.

### Erstellen der Konsolenanwendung und Erhalten der Assembly

So erstellen Sie eine neue Konsolenanwendung in Visual Studio und installieren das Azure Storage NuGet-Paket:

1.  Wählen Sie in Visual Studio **Datei** -\> **Neues Projekt**, und wählen Sie dann **Windows** -\> **Konsolenanwendung** aus der Liste der Visual C#-Vorlagen aus.
2.  Stellen Sie einen Namen für die Konsolenanwendung bereit, und klicken Sie dann auf **OK**.
3.  Nachdem Ihr Projekt erstellt wurde, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf das Projekt und wählen **NuGet-Pakete verwalten** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

### Speichern der Anmeldeinformationen Ihres Speicherkontos in der Datei „app.config“

Als nächstes speichern Sie Ihre Anmeldeinformationen in der Datei „app.config“ Ihres Projekts. Bearbeiten Sie die Datei „app.config“, sodass sie dem folgenden Beispiel ähnlich ist, wobei Sie `myaccount` durch Ihren Speicherkontonamen und `mykey` durch Ihren Speicherkontoschlüssel ersetzen:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup> 
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
        </startup>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
        </appSettings>
    </configuration>

> [WACOM.NOTE] Die neueste Version des Azure-Speicheremulators unterstützt den Dateispeicher nicht. Ihre Verbindungszeichenfolge muss auf ein Azure-Speicherkonto in der Cloud mit Zugriff auf die Dateivorschau ausgerichtet sein.

### Hinzufügen von Namespace-Deklarationen

Öffnen Sie die Datei „program.cs“ über den Projektmappen-Explorer, und fügen Sie die folgenden Namespace-Deklarationen am Anfang der Datei hinzu:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.File;

### Programmgesteuertes Abrufen Ihrer Verbindungszeichenfolge

Sie können Ihre gespeicherten Anmeldeinformationen aus der Datei „app.config“ mithilfe der Klasse `Microsoft.WindowsAzure.CloudConfigurationManager` oder `System.Configuration.ConfigurationManager`abrufen. Das Beispiel zeigt, wie Sie Ihre Anmeldeinformationen mithilfe der Klasse `CloudConfigurationManager` abrufen und sie mit der Klasse `CloudStorageAccount` kapseln. Fügen Sie den folgenden Code in „program.cs“ zur `Main()`-Methode hinzu:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Programmgesteuertes Zugreifen auf die Dateispeicherfreigabe

Als nächstes fügen Sie den folgenden Code im Anschluss an den Code, mit dem die Verbindungszeichenfolge abgerufen wurde, zur `Main()`-Methode hinzu. Dieser Code erhält einen Verweis auf die zuvor von uns erstellte Datei und gibt ihren Inhalt im Konsolenfenster aus.

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

Führen Sie die Konsolenanwendung aus, um die Ausgabe anzuzeigen.

## <a name="next-steps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Ihnen die Grundlagen des Dateispeichers vermittelt wurden, folgen Sie jetzt diesen Links,
um ausführlichere Informationen zu erhalten.

-   In der Referenzdokumentation für den Dateispeicherdienst finden Sie alle Details zu verfügbaren APIs:
    -   [Referenz zur Speicherclientbibliothek für .NET][Azure .NET-Speicherclientbibliothek]
    -   [Referenz zur REST-API des Dateidiensts][Referenz zur REST-API des Dateidiensts]

-   Zeigen Sie die Blogbeiträge des Azure-Speicherteams an, die sich auf den Dateidienst beziehen:
    -   [Einführung in den Microsoft Azure-Dateidienst][Einführung in den Microsoft Azure-Dateidienst]
    -   [Beibehalten von Verbindungen zu Microsoft Azure-Dateien][Beibehalten von Verbindungen zu Microsoft Azure-Dateien]

-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Blobspeicher][Blobspeicher] zum Speichern unstrukturierter Daten.
    -   Verwenden Sie [Tabellenspeicher][Tabellenspeicher] zum Speichern strukturierter Daten.
    -   Verwenden Sie [Warteschlangenspeicher][Warteschlangenspeicher] zum zuverlässigen Speichern von Nachrichten.
    -   Verwenden Sie eine [SQL-Datenbank][SQL-Datenbank] zum Speichern relationaler Daten.

</p>

  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Was ist der Dateispeicher?]: #what-is-file-storage
  [Dateispeicherkonzepte]: #file-storage-concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Verwenden von PowerShell zum Erstellen einer Dateifreigabe]: #use-cmdlets
  [Bereitstellen der Freigabe über einen virtuellen Azure-Computer]: #mount-share
  [Erstellen einer lokalen Anwendung für den Zugriff auf den Dateispeicher]: #create-console-app
  [Nächste Schritte]: #next-steps
  [files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png
  [Azure Storage Scalability and Performance Targets]: http://msdn.microsoft.com/de-de/library/dn249410.aspx
  [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten]: http://msdn.microsoft.com/de-de/library/azure/dn167011.aspx
  [Microsoft Azure-Vorschauseite]: /de-de/services/preview/
  [create-storage-account]: ../includes/create-storage-account.md
  [Installieren und Konfigurieren von Azure PowerShell]: /de-de/documentation/articles/install-configure-powershell/
  [Erstellen eines virtuellen Windows Server-Computers]: /de-de/documentation/articles/virtual-machines-windows-tutorial/
  [Anmeldung auf einem virtuellen Windows Server-Computer]: /de-de/documentation/articles/virtual-machines-log-on-windows-server/
  [System.IO-Namespaces]: http://msdn.microsoft.com/de-de/library/gg145019(v=vs.110).aspx
  [Azure .NET-Speicherclientbibliothek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referenz zur REST-API des Dateidiensts]: http://msdn.microsoft.com/de-de/library/azure/dn167006.aspx
  [Einführung in den Microsoft Azure-Dateidienst]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [Beibehalten von Verbindungen zu Microsoft Azure-Dateien]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx
  [Blobspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Tabellenspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-tables/
  [Warteschlangenspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL-Datenbank]: /de-de/documentation/articles/sql-database-dotnet-how-to-use/
