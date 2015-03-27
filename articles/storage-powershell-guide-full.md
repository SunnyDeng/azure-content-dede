<properties 
	pageTitle="Verwenden von Azure PowerShell mit Azure Storage" 
	description="Erfahren Sie, wie Sie Azure PowerShell für Azure Storage verwenden." 
	services="storage" 
	documentationCenter="na" 
	authors="Selcin" 
	manager="adinah"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="selcint"/>

# Verwenden von Azure PowerShell mit Azure Storage 

Willkommen beim Azure PowerShell-Benutzerhandbuch für Azure Storage!

Sie können verschiedene Entwicklungs- und Verwaltungstools verwenden, um eine Vielzahl von Aufgaben für Azure Storage auszuführen, z. B. das [Azure-Verwaltungsportal](http://manage.windowsazure.com/), die [Speicherclientbibliothek](http://msdn.microsoft.com/library/azure/dn261237.aspx), die [REST-API](http://msdn.microsoft.com/library/azure/dd179355.aspx) und [Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx). 

In diesem Handbuch wird die Verwendung von Azure PowerShell zum Ausführen allgemeiner Aufgaben für Azure Storage untersucht. Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure durch Windows PowerShell enthält. Es handelt sich um eine aufgabenbasierte Befehlszeilenshell und Skriptsprache, die insbesondere für die Systemverwaltung konzipiert ist. Mit PowerShell können Sie die Verwaltung Ihrer Azure-Dienste und -Anwendungen ganz einfach steuern und automatisieren. Sie können z. B. dieselben Aufgaben mithilfe der Cmdlets wie über das Azure-Verwaltungsportal ausführen. 

In diesem Handbuch wird angenommen, dass Sie bereits Erfahrung mit der Verwendung von [Azure Storage](http://azure.microsoft.com/documentation/services/storage/) und [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx) haben. Das Handbuch bietet eine Reihe von Skripts, um die Verwendung von PowerShell mit Azure Storage zu veranschaulichen. Sie müssen die Skriptvariablen auf Basis Ihrer Konfiguration aktualisieren, bevor Sie die jeweiligen Skripts ausführen.

Der erste Abschnitt in diesem Handbuch bietet einen kurzen Überblick über Azure Storage und PowerShell. Beginnen Sie mit [Voraussetzungen für die Verwendung von Azure PowerShell mit Azure Storage], um ausführliche Informationen und Anweisungen zu erhalten(#pre).

## Inhaltsverzeichnis
  
 - [Erste Schritte mit Azure Storage und PowerShell in 5 Minuten][]
 - [Voraussetzungen für die Verwendung von Azure PowerShell mit Azure Storage][] 
 - [Verwalten von Speicherkonten in Azure][]
	 - [Einrichten von standardmäßigen Azure-Abonnements][]
	 - [Erstellen von neuen Azure-Speicherkonten][]
	 - [Einrichten von standardmäßigen Azure-Speicherkonten][]
	 - [Auflisten aller Azure-Speicherkonten in einem Abonnement][]
	 - [Erstellen von Azure-Speicherkontexten][]
 - [Verwalten von Azure-Blobs und Blob-Momentaufnahmen][]
	 - [Erstellen von Containern][]
	 - [Hochladen von Blobs in einen Container][]
	 - [Herunterladen von Blobs aus einem Container][]
	 - [Kopieren von Blobs aus einem Speichercontainer in einen anderen][]
	 - [Löschen von Blobs][]
	 - [Verwalten von Azure-Blob-Momentaufnahmen][]
	 	- [Erstellen von Blob-Momentaufnahmen][]
	 	- [Auflisten der Momentaufnahmen von Blobs][]
	 	- [Kopieren von Momentaufnahmen von Blobs][]
 - [Verwalten von Azure-Tabellen und Tabellenentitäten][]
	 - [Erstellen von Tabellen][]
	 - [Abrufen von Tabellen][]
	 - [Löschen von Tabellen][]
	 - [Verwalten von Tabellenentitäten][]
	 	- [Hinzufügen von Tabellenentitäten][]
	 	- [Abfragen von Tabellenentitäten][]
	 	- [Löschen von Tabellenentitäten][]
 - [Verwalten von Azure-Warteschlangen und Warteschlangennachrichten][]
	 - [Erstellen von Warteschlangen][]
	 - [Abrufen von Warteschlangen][]
	 - [Löschen von Warteschlangen][]
	 - [Verwalten von Warteschlangennachrichten][]
	 	- [Einfügen einer Nachricht in eine Warteschlange][]
	 	- [Entfernen aus der Warteschlange bei nächster Nachricht][]
 - [Verwalten von Azure-Dateifreigaben und Dateien][]
 - [Festlegen und Abfragen von Speicheranalysen][]
 - [Verwalten von Shared Access Signature (SAS) und gespeicherten Zugriffsrichtlinien][]
 - [Verwenden von Azure Storage für die US-Regierung und Azure China][]
 - [Nächste Schritte][]
 
## <a name="getstart"></a>Erste Schritte mit Azure Storage und PowerShell in 5 Minuten
In diesem Abschnitt wird veranschaulicht, wie Sie über PowerShell in 5 Minuten auf Azure Storage zugreifen können. 

**Neu bei Azure:** Erstellen Sie ein Microsoft Azure-Abonnement und ein Microsoft-Konto, das diesem Abonnement zugeordnet ist. Informationen zu Azure-Kaufoptionen finden Sie unter [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/), [Kaufoptionen](http://azure.microsoft.com/pricing/purchase-options/) und [Spezielle Angebote](http://azure.microsoft.com/pricing/member-offers/) (für Mitglieder von MSDN, Microsoft Partner Network und BizSpark sowie anderer Microsoft-Programme).

**Unschlüssig hinsichtlich der Microsoft Azure-Konten und -Abonnements:** Weitere Informationen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**Nach der Erstellung eines Microsoft Azure-Abonnements und eines Kontos:** 

1.	Laden Sie [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) auf Ihren lokalen Computer herunter, und installieren Sie es anschließend.
2.	Starten Sie die Windows PowerShell Integrated Scripting Environment (ISE): Wechseln Sie auf Ihrem lokalen Computer zum Menü **Start**. Geben Sie **Verwaltung** ein, und klicken Sie darauf, um sie auszuführen. Klicken Sie im Fenster **Verwaltung** mit der rechten Maustaste auf **Windows PowerShell ISE**, klicken Sie auf **Als Administrator ausführen**. 
3.	Klicken Sie in **Windows PowerShell ISE** auf **Datei** > **Neu**, um eine neue Skriptdatei zu erstellen. 
4.	Jetzt erhalten Sie ein einfaches Skript, das grundlegende PowerShell-Befehle für den Zugriff auf Azure Storage veranschaulicht. Sie werden zunächst vom Skript zur Eingabe Ihrer Anmeldeinformationen für das Azure-Konto aufgefordert, damit Ihr Azure-Konto zur lokalen PowerShell-Umgebung hinzugefügt werden kann. Dann legt das Skript das standardmäßige Azure-Abonnement fest und erstellt ein neues Speicherkonto in Azure. Als Nächstes erstellt das Skript in diesem neuen Speicherkonto einen neuen Container und lädt eine vorhandene Image-Datei (Blob) in diesen Container hoch. Nachdem das Skript alle Blobs in diesem Container aufgelistet hat, erstellt es ein neues Zielverzeichnis auf dem lokalen Computer und lädt dann die Image-Datei herunter.
5.	Markieren Sie im folgenden Codeabschnitt das Skript zwischen den Anmerkungen **#begin** und **#end**. Drücken Sie STRG+C, um die Auswahl in die Zwischenablage zu kopieren. 

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"
    
    	# Give a name to your new storage account. It must be lowercase! 
    	$StorageAccountName="yourstorageaccountname"
    
    	# Choose "West US" as an example.
    	$Location = "West US"
    
    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"
    
    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"
    
    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"
    
    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount
    
    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default
    
    	# Create a new storage account.
    	New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $Location
    
    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    
    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off
    
    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload 
    
    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName
    
    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName
    
    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  
    
    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder
    	#end
    
5.	Drücken Sie in **Windows PowerShell ISE** STRG+V, um das Skript einzufügen. Klicken Sie auf **Datei** > **Speichern**. Geben Sie in das Dialogfeld **Speichern unter** den Namen der Skriptdatei ein, z. B. "mystoragescript". Klicken Sie auf **Speichern**.

6.	Nun müssen Sie die Skriptvariablen auf Basis der Konfigurationseinstellungen aktualisieren. Sie müssen die Variable **$SubscriptionName** mit Ihrem eigenen Abonnement aktualisieren. Sie können die anderen Variablen im Skript übernehmen oder bei Bedarf aktualisieren.

	- **$SubscriptionName:** Sie müssen diese Variable durch Ihren eigenen Abonnementnamen ersetzen. Suchen Sie den Namen Ihres Abonnements mithilfe einer der drei folgenden Methoden: 
	
		a. Klicken Sie in **Windows PowerShell ISE** auf **Datei** > **Neu**, um eine neue Skriptdatei zu erstellen. Kopieren Sie das folgende Skript in die neue Skriptdatei, und klicken Sie auf **Debuggen** > **Ausführen**. Das folgende Skript fordert zunächst Ihre Azure-Anmeldeinformationen an, um Ihr Azure-Konto zur lokalen PowerShell-Umgebung hinzuzufügen. Anschließend werden alle Abonnements angezeigt, die mit der lokalen PowerShell-Sitzung verbunden sind. Notieren Sie den Namen des Abonnements, das Sie in diesem Lernprogramm verwenden möchten: 
	 
    		Add-AzureAccount 
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName 


		b. Azure unterstützt derzeit zwei Portale: das aktuelle [Azure-Verwaltungsportal](https://manage.windowsazure.com/) und das [Azure-Vorschauportal](https://portal.azure.com/). Wenn Sie sich beim aktuellen [Azure-Verwaltungsportal](https://portal.azure.com/) anmelden, navigieren Sie nach unten, und klicken Sie auf der linken Seite des Portals auf **Einstellungen**. Klicken Sie auf **Abonnements**. Kopieren Sie den Namen des Abonnements, das Sie während der Ausführung der in diesem Handbuch angegebenen Skripts verwenden möchten. Der folgende Screenshot stellt ein Beispiel dar.

		![Azure Management Portal][Image1]

		c. Wenn Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) anmelden, klicken Sie auf der linken Seite im Menü "Hub" auf **DURCHSUCHEN**. Klicken Sie dann auf **Alles** und anschließend auf **Abonnements**. Kopieren Sie den Namen des Abonnements, das Sie während der Ausführung der in diesem Handbuch angegebenen Skripts verwenden möchten. Der folgende Screenshot stellt ein Beispiel dar.

		![Azure Preview Portal][Image2]
 

	- **$StorageAccountName:** Verwenden Sie den im Skript angegebenen Namen, oder geben Sie einen neuen Namen für Ihr Speicherkonto ein. **Wichtig:** Der Name des Speicherkontos muss in Azure eindeutig sein. Zudem darf er nur Kleinschreibung enthalten!
	 
	- **$Location:** Verwenden Sie das im Skript angegebene "USA (West)", oder wählen Sie andere Azure-Regionen wie "USA (Ost)", "North Europe" usw.
	  
	- **$ContainerName:** Verwenden Sie den im Skript angegebenen Namen, oder geben Sie einen neuen Namen für Ihren Container ein.
	
	- **$ImageToUpload:** Geben Sie einen Pfad zu einem Bild auf dem lokalen Computer ein. Beispiel: "C:\Images\HelloWorld.png".
	
	- **$DestinationFolder:** Geben Sie einen Pfad zu einem lokalen Verzeichnis zum Speichern von Dateien ein, die aus dem Azure Storage heruntergeladen werden. Beispiel: "C:\DownloadImages".

7.	Nach dem Aktualisieren der Skriptvariablen in der Datei "mystoragescript.ps1", klicken Sie auf **Datei** > **Speichern**. Klicken Sie dann auf **Debuggen** > **Ausführen**, oder drücken Sie **F5**, um das Skript auszuführen.  

Nachdem das Skript ausgeführt wird, sollten Sie über einen lokalen Ordner verfügen, der die heruntergeladene Datei enthält. Der folgende Screenshot zeigt eine Beispielausgabe:

![Download Blobs][Image3]


> [AZURE.NOTE] Der Abschnitt "Erste Schritte mit Azure Storage und PowerShell in 5 Minuten" hat eine kurze Einführung zur Verwendung von Azure PowerShell mit Azure Storage geboten. Ausführliche Informationen und Anweisungen finden Sie in den folgenden Abschnitten.

## <a name="pre"></a>Voraussetzungen für die Verwendung von Azure PowerShell mit Azure Storage
Sie benötigen ein Azure-Abonnement und -Konto zum Ausführen der in diesem Handbuch angegebenen PowerShell-Cmdlets. Die verfügbaren Azure-Abonnementoptionen finden Sie unter [Erste Schritte mit Azure](http://azure.microsoft.com/pricing/free-trial/).

Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure durch Windows PowerShell enthält. Informationen zum Installieren und Einrichten von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Sie sollten daher das neueste Azure PowerShell-Modul herunterladen und installieren bzw. ein Upgrade durchführen, bevor Sie dieses Handbuch verwenden. 

Sie können die Cmdlets in der Azure PowerShell-Konsole, der Windows PowerShell-Standardkonsole oder der Windows PowerShell Integrated Scripting Environment (ISE) ausführen. Wechseln Sie z. B. zum Öffnen einer **Azure PowerShell-Konsole** zum Menü "Start", geben Sie "Microsoft Azure PowerShell" ein, klicken Sie mit der rechten Maustaste, und klicken Sie dann auf "Als Administrator ausführen". Wechseln Sie zum Öffnen der **Windows PowerShell ISE** zum Menü "Start", geben Sie "Verwaltung" ein, und klicken Sie dann darauf, um sie auszuführen. Klicken Sie im Fenster "Verwaltung" mit der rechten Maustaste auf "Windows PowerShell ISE", und klicken Sie dann auf "Als Administrator ausführen". Ausführliche Optionen zum Einrichten und Konfigurieren finden Sie im Abschnitt "Installieren von Azure PowerShell" im Lernprogramm [Installieren und Konfigurieren von Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

## <a name="manageaccount"></a>Verwalten von Speicherkonten in Azure
### <a name="setdefsub"></a>Einrichten von standardmäßigen Azure-Abonnements
Für die Verwaltung von Azure Storage mit Azure PowerShell müssen Sie Ihre Clientumgebung über die Azure Active Directory-Authentifizierung oder die zertifikatbasierte Authentifizierung mit Azure authentifizieren. Ausführliche Informationen finden Sie im Abschnitt [Vorgehensweise: Verbinden mit Ihrem Abonnement](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect) im Lernprogramm [Installieren und Konfigurieren von Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). In diesem Handbuch wird die Azure Active Directory-Authentifizierung verwendet.

1.	Geben Sie in der Azure PowerShell-Konsole oder in der Windows PowerShell ISE den folgenden Befehl ein, um Ihr Azure-Konto zur lokalen PowerShell-Umgebung hinzuzufügen:

    `Add-AzureAccount`

2.	Geben Sie im Microsoft Azure-Anmeldefenster die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

3.	Führen Sie dann den folgenden Befehl zum Anzeigen der Azure-Konten in Ihrer lokalen PowerShell-Umgebung aus, und stellen Sie sicher, dass Ihr Konto aufgeführt ist:
 
	`Get-AzureAccount`
  
4.	Anschließend führen Sie das folgende Cmdlet aus, um alle mit der lokalen PowerShell-Sitzung verbundenen Abonnements anzuzeigen und zu überprüfen, ob Ihr Abonnement aufgeführt wird:

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
 
5.	Führen Sie zum Festlegen eines Azure-Standardabonnements das Cmdlet "Select-AzureSubscription" aus:
 
	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default

6.	Überprüfen Sie den Namen des Standardabonnements durch Ausführen des Cmdlets "Get-AzureSubscription":

	`Get-AzureSubscription -Default`

7.	Führen Sie Folgendes aus, um alle verfügbaren PowerShell-Cmdlets für Azure Storage anzuzeigen:

	`Get-Command -Module Azure -Noun *Storage*`

### <a name="createaccount"></a>Erstellen von neuen Azure-Speicherkonten
Für Azure Storage benötigen Sie ein Speicherkonto. Nachdem Sie Ihren Computer für die Verbindung mit Ihrem Abonnement konfiguriert haben, können Sie ein neues Azure-Speicherkonto erstellen. 

1.	Führen Sie das Cmdlet "Get-AzureLocation" aus, um alle verfügbaren Datencenterstandorte zu finden:

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	Führen Sie anschließend das Cmdlet "New-AzureStorageAccount" aus, um ein neues Speicherkonto zu erstellen. Im folgenden Beispiel wird ein neues Speicherkonto im Datencenter der Region "West US" erstellt:
  
    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $location

Ausführliche Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](http://azure.microsoft.com/documentation/articles/storage-whatis-account/).

> [AZURE.IMPORTANT] Der Name für das Speicherkonto muss in Azure eindeutig sein und Kleinschreibung verwenden. Informationen zu Namenskonventionen und Einschränkungen finden Sie unter [Informationen zu Azure-Speicherkonten](http://azure.microsoft.com/documentation/articles/storage-whatis-account/), [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese](http://msdn.microsoft.com/library/azure/dd135715.aspx) sowie [Erstellen eines Speicherkontos](http://msdn.microsoft.com/library/azure/hh264518.aspx).

### <a name="defaultaccount"></a>Einrichten von standardmäßigen Azure-Speicherkonten
Sie können mehrere Speicherkonten in Ihrem Abonnement verwenden. Sie können ein Speicherkonto auswählen und als Standardspeicherkonto für alle Speicherbefehle in einer PowerShell-Sitzung festlegen. Dadurch können Sie die Azure PowerShell-Speicherbefehle ohne explizite Angabe des Speicherkontextes ausführen. 

1.	Führen Sie das Cmdlet "Set-AzureSubscription" aus, um ein Standardspeicherkonto für Ihr Abonnement festzulegen. 

		$SubscriptionName = "Your subscription name" 
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 

2.	Als Nächstes führen Sie das Cmdlet "Get-AzureSubscription" aus, um zu überprüfen, ob das Speicherkonto Ihrem Standardabonnementkonto zugeordnet ist. Mit diesem Befehl werden die Abonnementeigenschaften für das aktuelle Abonnement, einschließlich seines aktuellen Speicherkontos, zurückgegeben.

	    Get-AzureSubscription -Current

### <a name="listaccounts"></a>Auflisten aller Azure-Speicherkonten in einem Abonnement
Jedes Azure-Abonnement kann über bis zu 100 Speicherkonten verfügen. Die neuesten Informationen zu Grenzwerten finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](http://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).

Führen Sie das folgende Cmdlet aus, um die Namen und Statusangaben für die Speicherkonten des aktuellen Abonnements zu ermitteln:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="createctx"></a>Erstellen von Azure-Speicherkontexten
Azure-Speicherkontext ist ein Objekt in PowerShell zum Kapseln der Speicheranmeldeinformationen. Durch die Verwendung eines Speicherkontextes während der Ausführung nachfolgender Cmdlets können Sie Ihre Anforderung authentifizieren, ohne das Speicherkonto und seinen Zugriffsschlüssel explizit anzugeben. Es gibt verschiedene Möglichkeiten zur Erstellung eines Speicherkontextes, z. B. durch die Verwendung von Speicherkontoname und Zugriffsschlüssel, SAS-Token (Shared Access Signature), Verbindungszeichenfolge oder die anonyme Erstellung. Weitere Informationen finden Sie unter [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Verwenden Sie eine der folgenden drei Methoden zum Erstellen eines Speicherkontextes:

- Führen Sie das Cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) aus, um den primären Speicherzugriffsschlüssel für Ihr Azure-Speicherkonto zu ermitteln. Als Nächstes rufen Sie das Cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) auf, um einen Speicherkontext zu erstellen:

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generate a shared access signature token for an Azure storage container and use it to create a storage context:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	Weitere Informationen finden Sie unter [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) und [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell ](http://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

- In einigen Fällen möchten Sie möglicherweise die Dienstendpunkte angeben, wenn Sie einen neuen Speicherkontext erstellen. Dies kann erforderlich sein, wenn Sie einen benutzerdefinierten Domänennamen für Ihr Speicherkonto mit dem Blob-Dienst registriert haben oder eine SAS (Shared Access Signature) für den Zugriff auf Speicherressourcen verwenden möchten. Legen Sie die Dienstendpunkte in einer Verbindungszeichenfolge fest, und verwenden Sie diese, um einen neuen Speicherkontext zu erstellen, wie unten gezeigt:

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Weitere Informationen zum Konfigurieren einer Speicherverbindungszeichenfolge finden Sie unter [Konfigurieren von Verbindungszeichenfolgen](http://msdn.microsoft.com/library/azure/ee758697.aspx).

Sie haben jetzt Ihren Computer eingerichtet und erfahren, wie Sie Abonnements und Speicherkonten mithilfe von Azure PowerShell verwalten. Fahren Sie mit dem nächsten Abschnitt fort, um zu erfahren, wie Sie Azure-Blobs und Blob-Momentaufnahmen verwalten.

## <a name="manageblobs"></a>Verwalten von Azure-Blobs und Blob-Momentaufnahmen
Der Azure-Blob-Speicher ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. In diesem Abschnitt wird davon ausgegangen, dass Sie bereits mit den Konzepten des Azure-Blob-Speicherdiensts vertraut sind. Ausführliche Informationen finden Sie unter [Verwenden des Blob-Speichers mit .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs) und [Blob-Dienstkonzepte](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="container"></a>Erstellen von Containern
Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Sie können mithilfe des Cmdlets "New-AzureStorageContainer" einen privaten Container erstellen:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Es gibt drei Stufen des anonymen Lesezugriffs: **Aus**, **Blob** und **Container**. Legen Sie für den Parameter "Permission" den Wert **Off** fest, um den anonymen Zugriff auf Blobs zu verhindern. Der neue Container ist standardmäßig privat und der Zugriff ist ausschließlich dem Kontobesitzer gestattet. Um den anonymen öffentlichen Lesezugriff auf Blob-Ressourcen, jedoch nicht auf Containermetadaten oder die Liste der im Container enthaltenen Blobs zuzulassen, legen Sie für den Parameter "Permission" den Wert **Blob** fest. Um den vollständigen öffentlichen Lesezugriff auf Blob-Ressourcen, Containermetadaten und die Liste der im Container enthaltenen Blobs zuzulassen, legen Sie für den Parameter "Permission" den Wert **Container** fest. Weitere Informationen finden Sie unter der Enumeration [BlobContainerPublicAccessType](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storageclient.blobcontainerpublicaccesstype(v=azure.95).aspx).

### <a name="uploadblob"></a>Hochladen von Blobs in einen Container
Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs. Weitere Informationen finden Sie unter [Grundlegendes zu Block-Blobs und Seiten-Blobs](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Verwenden Sie das Cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx), um Blobs in einen Container hochzuladen. Dieser Befehl lädt standardmäßig die lokalen Dateien in einen Block-Blob hoch. Sie können den Parameter "-BlobType" verwenden, um den Blob-Typ anzugeben. 

Im folgenden Beispiel wird das Cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) ausgeführt, um alle Dateien im angegebenen Verzeichnis abzurufen und diese dann mithilfe des Pipeline-Operators an das nächste Cmdlet zu übergeben. Mit dem Cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) werden die lokalen Dateien in den Container hochgeladen:

    Get-ChildItem -Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="downblob"></a>Herunterladen von Blobs aus einem Container
Im folgenden Beispiel wird veranschaulicht, wie Blobs aus einem Container heruntergeladen werden. Im Beispiel wird zuerst mit dem Speicherkontokontext, der den Speicherkontonamen und seinen primären Zugriffsschlüssel umfasst, eine Verbindung mit dem Azure Storage hergestellt. Dann wird im Beispiel mithilfe des Cmdlets [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) ein Blob-Verweis abgerufen. Als Nächstes verwendet das Beispiel das Cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) zum Herunterladen von Blobs in den lokalen Zielordner. 

    #Define the variables.
	ContainerName = "yourcontainername" 
    $DestinationFolder = "C:\DownloadImages" 
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Download blobs from a container. 
    New-Item -Path $DestinationFolder -ItemType Directory -Force 
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="copyblob"></a>Kopieren von Blobs aus einem Speichercontainer in einen anderen
Sie können Blobs asynchron zwischen Speicherkonten und Regionen kopieren. Im folgenden Beispiel wird veranschaulicht, wie Sie Blobs in zwei unterschiedlichen Speicherkonten von einem Speichercontainer in einen anderen kopieren. Zunächst werden im Beispiel die Variablen für Quell- und Zielspeicherkonten festgelegt und dann für jedes Konto ein Speicherkontext erstellt. Als Nächstes kopiert das Beispiel die Blobs mithilfe des Cmdlets [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) aus dem Quellcontainer in den Zielcontainer. Im Beispiel wird davon ausgegangen, dass die Quell- und Zielspeicherkonten bereits vorhanden sind. 

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey
    
    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey
    
    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext
    
    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Beachten Sie, dass in diesem Beispiel ein asynchroner Kopiervorgang ausgeführt wird. Sie können den Status der einzelnen Kopiervorgänge überwachen, indem Sie das Cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) ausführen.

### <a name="deleteblob"></a>Löschen von Blobs
Um ein Blob zu löschen, rufen Sie zuerst einen Blob-Verweis ab und rufen dann das Cmdlet "Remove-AzureStorageBlob" dafür auf. Das folgende Beispiel löscht alle Blobs in einem bestimmten Container. Im Beispiel werden zunächst Variablen für ein Speicherkonto festgelegt und dann ein Speicherkontext erstellt. Als Nächstes ruft das Beispiel mithilfe des Cmdlets [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) einen Blob-Verweis ab und führt dann das Cmdlet [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) aus, um Blobs aus einem Container in Azure Storage zu entfernen. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob 

## <a name="manageshots"></a>Verwalten von Azure-Blob-Momentaufnahmen
Mit Azure können Sie eine Momentaufnahme eines Blobs erstellen. Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird. Nachdem eine Momentaufnahme erstellt wurde, kann sie zwar gelesen, kopiert oder gelöscht, aber nicht geändert werden. Momentaufnahmen bieten eine Möglichkeit, um ein Blob so zu sichern, wie es zu einem bestimmten Zeitpunkt dargestellt wird. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Blobs](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="createshot"></a>Erstellen von Blob-Momentaufnahmen
Wenn Sie eine Momentaufnahme eines Blobs erstellen möchten, rufen Sie zunächst einen Blob-Verweis ab und rufen dann die ICloudBlob.CreateSnapshot-Methode dafür auf. Im folgenden Beispiel werden zunächst Variablen für ein Speicherkonto festgelegt und dann ein Speicherkontext erstellt. Als Nächstes ruft das Beispiel mithilfe des Cmdlets [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) einen Blob-Verweis ab und führt dann das Cmdlet [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) aus, um eine Momentaufnahme zu erstellen. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName
    
    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 

### <a name="listshot"></a>Auflisten der Momentaufnahmen von Blobs
Sie können für ein Blob beliebig viele Momentaufnahmen erstellen. Sie können die einem Blob zugeordneten Momentaufnahmen auflisten, um die aktuellen Momentaufnahmen nachzuverfolgen. Im folgenden Beispiel wird ein vordefiniertes Blob verwendet und dann das Cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) aufgerufen, um die Momentaufnahmen für dieses Blob aufzulisten.  

    #Define the blob name.
    $BlobName = "yourblobname"
    
    #List the snapshots of a blob.
    Get-AzureStorageBlob -Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="copyshot"></a>Kopieren von Momentaufnahmen von Blobs
Sie können die Momentaufnahme eines Blobs kopieren, um die Momentaufnahme wiederherzustellen. Ausführliche Informationen zu Einschränkungen finden Sie unter [Erstellen einer Momentaufnahme eines Blobs](http://msdn.microsoft.com/library/azure/hh488361.aspx). Im folgenden Beispiel werden zunächst Variablen für ein Speicherkonto festgelegt und dann ein Speicherkontext erstellt. Als Nächstes werden im Beispiel die Variablen für Container und Blob-Name definiert. Das Beispiel ruft mithilfe des Cmdlets [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) einen Blob-Verweis ab und führt dann das Cmdlet [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) aus, um eine Momentaufnahme zu erstellen. Anschließend führt das Beispiel das Cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) aus, um die Momentaufnahme eines Blobs mithilfe des ICloudBlob-Objekts für das Quell-Blob zu kopieren. Vergewissern Sie sich, dass Sie die Variablen auf Grundlage Ihrer Konfiguration aktualisieren, bevor Sie das Beispiel ausführen. Beachten Sie, dass im folgenden Beispiel davon ausgegangen wird, dass die Quell- und Zielcontainer sowie das Quell-Blob bereits vorhanden sind. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName
    
    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 
    
    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy -Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Sie haben jetzt erfahren, wie Azure-Blobs und Blob-Momentaufnahmen mit Azure PowerShell verwaltet werden. Fahren Sie mit dem nächsten Abschnitt fort, um zu erfahren, wie Tabellen, Warteschlangen und Dateien verwaltet werden.

## <a name="managetables"></a>Verwalten von Azure-Tabellen und Tabellenentitäten
Der Azure-Tabellenspeicherdienst ist ein NoSQL-Datenspeicher, den Sie zum Speichern und Abfragen sehr großer Sätze von strukturierten, nicht relationalen Daten ausführen können. Die Hauptkomponenten des Diensts sind Tabellen, Entitäten und Eigenschaften. Eine Tabelle ist eine Sammlung von Entitäten. Eine Entität ist ein Satz von Eigenschaften. Jede Entität kann über bis zu 252 Eigenschaften verfügen, bei denen es sich um Name-Wert-Paare handelt. In diesem Abschnitt wird davon ausgegangen, dass Sie bereits mit den Konzepten des Azure-Tabellenspeicherdiensts vertraut sind. Ausführliche Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx) und [Verwenden des Tabellenspeichers mit .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

In den folgenden Unterabschnitten erfahren Sie, wie Azure-Speicherdienste mit Azure PowerShell verwaltet werden. Die behandelten Szenarien umfassen das **Erstellen**, **Löschen** und **Abrufen** von **Tabellen** sowie das **Hinzufügen**, **Abfragen** und **Löschen von Tabellenentitäten**.

### <a name="createtable"></a>Erstellen von Tabellen
Jede Tabelle muss sich in einem Azure-Speicherkonto befinden. Im folgenden Beispiel wird veranschaulicht, wie eine Tabelle in Azure Storage erstellt wird. Im Beispiel wird zuerst mit dem Speicherkontokontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Als Nächstes verwendet es das Cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx), um eine Tabelle in Azure Storage zu erstellen. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey 
    
    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="gettable"></a>Abrufen von Tabellen
Sie können eine oder alle Tabellen in einem Speicherkonto abfragen und abrufen. Im folgenden Beispiel wird veranschaulicht, wie eine bestimmte Tabelle mit dem Cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) abgerufen wird.

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable -Name $tabName -Context $Ctx 

Wenn Sie das Cmdlet "Get-AzureStorageTable" ohne Parameter aufrufen, ruft es alle Speichertabellen für ein Speicherkonto ab.

### <a name="remtable"></a>Löschen von Tabellen
Sie können eine Tabelle mithilfe des Cmdlets [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) aus einem Speicherkonto löschen.  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="mngentity"></a>Verwalten von Tabellenentitäten
Azure PowerShell bietet derzeit keine Cmdlets zum direkten Verwalten von Tabellenentitäten. Sie können die in der [Azure-Speicherclientbibliothek für .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) angegebenen Klassen verwenden, um Vorgänge mit Tabellenentitäten auszuführen. 

#### <a name="addentity"></a>Hinzufügen von Tabellenentitäten
Um eine Entität zu einer Tabelle hinzuzufügen, erstellen Sie zunächst ein Objekt, das die Entitätseigenschaften definiert. Eine Entität kann bis zu 255 Eigenschaften besitzen, einschließlich drei Systemeigenschaften: **PartitionKey**, **RowKey** und **Timestamp**. Sie sind für das Einfügen und Aktualisieren der Werte von **PartitionKey** und **RowKey** zuständig. Der Server verwaltet die Werte von **Timestamp**, die nicht geändert werden können. Zusammen identifizieren **PartitionKey** und **RowKey** jede Entität in einer Tabelle eindeutig. 

-	**PartitionKey**: Bestimmt die Partition, in der die Entität gespeichert ist.
-	**RowKey**: Identifiziert die Entität innerhalb der Partition eindeutig.

Sie können bis zu 252 benutzerdefinierte Eigenschaften für eine Entität definieren. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Im folgenden Beispiel wird veranschaulicht, wie Entitäten zu einer Tabelle hinzugefügt werden. Das Beispiel zeigt das Abrufen der Employee-Tabelle und das Hinzufügen mehrerer Entitäten zur Tabelle. Zunächst wird mit dem Speicherkontokontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Anschließend wird die angegebene Tabelle mithilfe des Cmdlets [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) abgerufen. Wenn die Tabelle nicht vorhanden ist, wird mithilfe des Cmdlets [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) eine Tabelle in Azure Storage erstellt. Als Nächstes definiert das Beispiel eine benutzerdefinierte Funktion, "Add-Entity", um Entitäten zur Tabelle hinzuzufügen, indem Partition und Zeilenschlüssel für jede Entität angegeben wird. Die Add-Entity-Funktion ruft das Cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) für die Klasse [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) auf, um ein Entitätsobjekt zu erstellen. Später wird im Beispiel die [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx)-Methode für dieses Entitätsobjekt aufgerufen, um es zu einer Tabelle hinzuzufügen. 

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table, 
       [String]$partitionKey, 
       [String]$rowKey,
       [String]$name, 
       [Int]$id
    )
    
      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)
    
      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore
    
    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable -Name $TableName -Context $Ctx
    }
    
    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1 
    Add-Entity $table "Partition1" "Row2" "Jessie" 2 
    Add-Entity $table "Partition2" "Row1" "Christine" 3 
    Add-Entity $table "Partition2" "Row2" "Steven" 4 


#### <a name="queryentity"></a>Abfragen von Tabellenentitäten
Verwenden Sie die [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx)-Klasse zum Abfragen einer Tabelle. Im folgende Beispiel wird davon ausgegangen, dass Sie das im Abschnitt "Hinzufügen von Entitäten" dieses Handbuchs angegebene Skript bereits ausgeführt haben. Im Beispiel wird zuerst mit dem Speicherkontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Anschließend wird versucht, die zuvor erstellte Employees-Tabelle mithilfe des Cmdlets [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) abzurufen. Durch das Aufrufen des Cmdlets [New-Object](http://technet.microsoft.com/library/hh849885.aspx) für die Microsoft.WindowsAzure.Storage.Table.TableQuery-Klasse wird ein neues Abfrageobjekt erstellt. Das Beispiel sucht nach den Entitäten, deren ID-Spalte den Wert "1" aufweist, wie im Zeichenfolgenfilter angegeben. Ausführliche Informationen finden Sie im Abschnitt "Erstellen von Filterzeichenfolgen" unter [Abfragen von Tabellen und Entitäten](http://msdn.microsoft.com/library/azure/dd894031.aspx). Wenn Sie diese Abfrage ausführen, werden alle Entitäten zurückgegeben, die den Filterkriterien entsprechen.    

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Get a reference to a table.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx
    
    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery
    
    #Define columns to select. 
    $list = New-Object System.Collections.Generic.List[string] 
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")
    
    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20
    
    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)
    
    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize 

#### <a name="deleteentity"></a>Löschen von Tabellenentitäten
Sie können eine Entität unter Verwendung ihres Partitions- und Zeilenschlüssels löschen. Im folgende Beispiel wird davon ausgegangen, dass Sie das im Abschnitt "Hinzufügen von Entitäten" dieses Handbuchs angegebene Skript bereits ausgeführt haben. Im Beispiel wird zuerst mit dem Speicherkontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Anschließend wird versucht, die zuvor erstellte Employees-Tabelle mithilfe des Cmdlets [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) abzurufen. Wenn die Tabelle vorhanden ist, ruft das Beispiel die [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx)-Methode zum Abrufen einer Entität auf Grundlage ihrer Partition und der Zeilenschlüsselwerte auf. Übergeben Sie anschließend die Entität zum Löschen an die Methode [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx). 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null) 
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="managequeues"></a>Verwalten von Azure-Warteschlangen und Warteschlangennachrichten
Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. In diesem Abschnitt wird davon ausgegangen, dass Sie bereits mit den Konzepten des Azure-Warteschlangenspeicherdiensts vertraut sind. Ausführliche Informationen finden Sie unter [Verwenden des Warteschlangenspeichers mit .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/) und [Warteschlangendienstkonzepte](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/#concepts).

In diesem Abschnitt erfahren Sie, wie Sie den Azure-Warteschlangenspeicherdienst mit Azure PowerShell verwalten. Die behandelten Szenarien umfassen das **Einfügen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen**, **Löschen** und **Abrufen von Warteschlangen**.
 
### <a name="createqueue"></a>Erstellen von Warteschlangen
Im folgenden Beispiel wird zuerst mit dem Speicherkontokontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Anschließend wird das Cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) aufgerufen, um eine Warteschlange namens "queuename" zu erstellen. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue -Name $QueueName -Context $Ctx 

Informationen zu Namenskonventionen für den Azure-Warteschlangendienst finden Sie unter [Benennen von Warteschlangen und Metadaten](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="getqueue"></a>Abrufen von Warteschlangen
Sie können eine bestimmte Warteschlange oder Liste aller Warteschlangen in einem Speicherkonto abfragen und abrufen. Im folgenden Beispiel wird veranschaulicht, wie eine angegebene Warteschlange mit dem Cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) abgerufen wird.

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $Ctx 

Wenn Sie das Cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) ohne Parameter aufrufen, ruft es eine Liste aller Warteschlangen ab.

### <a name="remqueue"></a>Löschen von Warteschlangen
Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie das Cmdlet "Remove-AzureStorageQueue" auf. Im folgenden Beispiel wird gezeigt, wie eine angegebene Warteschlange mit dem Cmdlet "Remove-AzureStorageQueue" gelöscht wird. 

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue -Name $QueueName -Context $Ctx 

### <a name="mngqueuemsg"></a>Verwalten von Warteschlangennachrichten
Azure PowerShell bietet derzeit keine Cmdlets zum direkten Verwalten von Warteschlangennachrichten. Sie können die in der [Azure-Speicherclientbibliothek für .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) angegebenen Klassen verwenden, um Vorgänge mit Warteschlangennachrichten auszuführen. 

#### <a name="addqueuemsg"></a>Einfügen einer Nachricht in eine Warteschlange
Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst eine neue Instanz der [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx)-Klasse. Anschließend rufen Sie die [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx)-Methode auf. Die CloudQueueMessage kann entweder aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Byte-Array erstellt werden.
 
Im folgenden Beispiel wird veranschaulicht, wie Nachrichten zu einer Warteschlange hinzugefügt werden. Im Beispiel wird zuerst mit dem Speicherkontokontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Als Nächstes wird die angegebene Warteschlange mithilfe des Cmdlets [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) abgerufen. Wenn die Warteschlange vorhanden ist, wird mit dem Cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) eine Instanz der [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx)-Klasse erstellt. Später wird im Beispiel die [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx)-Methode für dieses Nachrichtenobjekt aufgerufen, um es zu einer Warteschlange hinzuzufügen. Dieser Code ruft eine Warteschlange ab und fügt die Nachricht 'MessageInfo' ein:

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx 
    
    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"
    
       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    } 


#### <a name="dequeuemsg"></a>Entfernen aus der Warteschlange bei nächster Nachricht
Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie die [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx)-Methode aufrufen, erhalten Sie die nächste Nachricht in einer Warteschlange. Eine von **GetMessage** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie auch die [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx)-Methode aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers nicht durchgeführt werden kann. Der Code ruft **DeleteMessage** direkt nach der Verarbeitung der Nachricht auf.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx
    
    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)
    
    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage) 

## <a name="files"></a>Verwalten von Azure-Dateifreigaben und Dateien
Der Azure-Dateispeicher bietet einen gemeinsam genutzten Speicher für Anwendungen und verwendet dabei das SMB 2.1-Protokoll. Virtuelle Microsoft Azure-Computer und Cloud-Dienste können Dateidaten in verschiedenen Anwendungskomponenten über eingebundene Freigaben teilen, und lokale Anwendungen können über die Dateispeicher-API oder Azure PowerShell auf freigegebene Dateien zugreifen.

Ausführliche Informationen zum Azure-Dateispeicher finden Sie unter [Verwenden des Azure-Dateispeichers](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/) und [Dateidienst-REST-API](http://msdn.microsoft.com/library/azure/dn167006.aspx).

Informationen zum Verwalten von Azure-Dateispeicher mithilfe von Azure PowerShell finden Sie unter [Erstellen einer Dateifreigabe mithilfe von PowerShell](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#use-cmdlets).

## <a name="stganalytics"></a>Festlegen und Abfragen von Speicheranalysen
Mithilfe der [Azure-Speicheranalysen](http://msdn.microsoft.com/library/azure/hh343270.aspx) können Sie Metriken (Speichermetriken) von Ihren Azure-Speicherkonten und Protokolldaten (Speicherprotokollierung) zu Anforderungen erfassen, die an Ihr Speicherkonto gesendet wurden. Mithilfe von Speichermetriken können Sie die Integrität eines Speicherkontos überwachen. Mithilfe der Speicherprotokollierung können Sie Probleme mit dem Speicherkonto diagnostizieren und beheben.
Standardmäßig sind für Ihre Speicherdienste keine Speichermetriken aktiviert. Sie können die Überwachung mithilfe des Azure-Verwaltungsportals, über Windows PowerShell oder programmgesteuert über eine Speicher-API aktivieren. Die Speicherprotokollierung erfolgt serverseitig und sie ermöglicht es Ihnen, Details für erfolgreiche und fehlerhafte Anforderungen in Ihrem Speicherkonto aufzuzeichnen. Anhand dieser Protokolle können Sie Details zu Lese-, Schreib- und Löschvorgängen für Ihre Tabellen, Warteschlangen und Blobs sowie die Gründe für fehlerhafte Anforderungen anzeigen.

Informationen zum Aktivieren und Anzeigen von Speichermetrikdaten mithilfe von PowerShell finden Sie unter [Aktivieren von Speichermetriken mithilfe von PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Weitere Informationen zum Aktivieren und Abrufen von Speicherprotokolldaten mithilfe von PowerShell finden Sie unter 
[Aktivieren der Speicherprotokollierung mithilfe von PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) und [Suchen der Protokolldaten für die Speicherprotokollierung](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Ausführliche Informationen zur Verwendung von Speichermetriken und der Speicherprotokollierung zum Beheben von Speicherproblemen finden Sie unter [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).

## <a name="sas"></a>Verwalten von Shared Access Signature (SAS) und gespeicherten Zugriffsrichtlinien
Eine SAS (Shared Access Signature, Signatur für freigegebenen Zugriff) ist ein wichtiger Bestandteil des Sicherheitsmodells für alle Anwendungen, die Azure-Speicher verwenden. Shared Access Signatures sind nützlich für die Vergabe eingeschränkter Berechtigungen für Ihr Speicherkonto an Clients, die keinen Zugriff auf Ihren Kontoschlüssel haben dürfen. Standardmäßig kann nur der Besitzer eines Speicherkontos auf Blobs, Tabellen und Warteschlangen in diesem Konto zugreifen. Wenn Ihr Dienst oder Ihre Anwendung diese Ressourcen für andere Clients zur Verfügung stellen muss, ohne den Zugriffsschlüssel freizugeben, stehen Ihnen drei Optionen zum Ermöglichen des Zugriffs zur Verfügung:

- Sie können die Berechtigungen eines Containers festlegen, um anonymen Lesezugriff auf den Container und seine Blobs zuzulassen. Dies ist für Tabellen oder Warteschlangen nicht zulässig.
- Verwenden Sie eine SAS, die für ein bestimmtes Zeitintervall beschränkten Zugriff auf Container, Blobs, Warteschlangen und Tabellen gewährt.
- Verwenden Sie eine gespeicherte Zugriffsrichtlinie, um ein höheres Maß an Kontrolle über SAS für einen Container und die enthaltenen Blobs, für eine Warteschlange oder für eine Tabelle zu erhalten. Mithilfe der gespeicherten Zugriffsrichtlinie können Sie die Startzeit, die Ablaufzeit oder die Berechtigungen für eine Signatur ändern oder die Signatur aufheben, nachdem sie ausgegeben wurde.

Eine SAS kann zwei Formen aufweisen:

- **Ad-Hoc-SAS**: Beim Erstellen einer Ad-Hoc-SAS werden Startzeit, Ablaufzeit und Berechtigungen für die SAS direkt in der SAS-URI angegeben. Diese Art von SAS kann für Container, Blobs, Tabellen oder Warteschlangen erstellt werden und kann nicht aufgehoben werden.
- **SAS mit gespeicherten Zugriffsrichtlinien**: Gespeicherte Zugriffsrichtlinien werden für Ressourcencontainer (Blob-Container, Tabellen oder Warteschlangen) definiert, und Sie können sie zum Verwalten von Einschränkungen für eine oder mehrere SAS verwenden. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, erbt die SAS die Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen) dieser gespeicherten Zugriffsrichtlinie. Diese Art von SAS kann aufgehoben werden.

Weitere Informationen finden Sie unter [Shared Access Signatures],(./storage-dotnet-shared-access-signature-part-1.md/)[Teil 1: Grundlagen zum SAS-Modell],(./storage-dotnet-shared-access-signature-part-1.md/)und [Verwalten des Zugriffs auf Azure-Speicherressourcen](http://msdn.microsoft.com/library/azure/ee393343.aspx).

In den nächsten Abschnitten erfahren Sie, wie Sie ein SAS-Token und eine gespeicherte Zugriffsrichtlinie für Azure-Tabellen erstellen. Azure PowerShell stellt ähnliche Cmdlets auch für Container, Blobs und Warteschlangen bereit. Um die Skripts in diesem Abschnitt auszuführen, laden Sie [Azure PowerShell Version 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) oder höher herunter.

### <a name="sub1"></a>Erstellen eines richtlinienbasierten SAS-Tokens
Verwenden Sie das Cmdlet "New-AzureStorageTableStoredAccessPolicy", um eine neue gespeicherte Zugriffsrichtlinie zu erstellen. Rufen Sie dann das Cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) auf, um ein neues richtlinienbasiertes SAS-Token für eine Azure-Speichertabelle zu erstellen.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="sub2"></a>Erstellen eines nicht aufhebbaren Ad-Hoc-SAS-Tokens
Verwenden Sie das Cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx), um ein neues nicht aufhebbares Ad-Hoc-SAS-Tokens für eine Azure-Speichertabelle zu erstellen:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="sub3"></a>Erstellen einer gespeicherten Zugriffsrichtlinie
Verwenden Sie das Cmdlet "New-AzureStorageTableStoredAccessPolicy", um eine neue gespeicherte Zugriffsrichtlinie für eine Azure-Speichertabelle zu erstellen:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="sub4"></a>Aktualisieren einer gespeicherten Zugriffsrichtlinie
Verwenden Sie das Cmdlet "Set-AzureStorageTableStoredAccessPolicy", um eine vorhandene gespeicherte Zugriffsrichtlinie für eine Azure-Speichertabelle zu aktualisieren:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="sub5"></a>Löschen einer gespeicherten Zugriffsrichtlinie
Verwenden Sie das Cmdlet "Remove-AzureStorageTableStoredAccessPolicy", um eine gespeicherte Zugriffsrichtlinie in einer Azure-Speichertabelle zu löschen:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="gov"></a>Verwenden von Azure Storage für die US-Regierung und Azure China
Eine Azure-Umgebung ist eine unabhängige Bereitstellung von Microsoft Azure, z. B. [Azure Government für die US-Regierung](http://azure.microsoft.com/features/gov/), [AzureCloud für globales Azure](https://manage.windowsazure.com) und [AzureChinaCloud für Azure, das von 21Vianet in China betrieben wird](http://www.windowsazure.cn/). Sie können neue Azure-Umgebungen für die US-Regierung und Azure China bereitstellen. 

Um Azure Storage mit AzureChinaCloud verwenden zu können, müssen Sie einen Speicherkontext erstellen, der AzureChinaCloud zugeordnet ist. Gehen Sie zu Beginn folgendermaßen vor:

1.	Führen Sie das Cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) aus, um die verfügbaren Azure-Umgebungen anzuzeigen:

    `Get-AzureEnvironment`

2.	Fügen Sie ein Azure China-Konto zu Windows PowerShell hinzu:

    `Add-AzureAccount -Environment AzureChinaCloud`

3.	Erstellen Sie einen Speicherkontext für ein AzureChinaCloud-Konto:

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Um Azure Storage mit [Azure Government für die US-Regierung](http://azure.microsoft.com/features/gov/) verwenden zu können, sollten Sie eine neue Umgebung definieren und anschließend in dieser Umgebung einen neuen Speicherkontext erstellen:

1. Rufen Sie das Cmdlet [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) auf, um eine neue Azure-Umgebung für Ihr privates Datencenter zu erstellen. 

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. Führen Sie das Cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) aus, um einen neuen Speicherkontext für diese neue Umgebung zu erstellen, wie unten gezeigt. 
   
	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

Weitere Informationen finden Sie unter:

- [Microsoft Azure Government-Entwicklerhandbuch](../azure-government-developer-guide/). 
- [Unterschiede zwischen AzureCloud für globales Azure und AzureChinaCloud für Azure, in China von 21Vianet betrieben](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next"></a>Nächste Schritte
In diesem Handbuch haben Sie erfahren, wie Sie Azure Storage mit Azure PowerShell verwalten. In den folgenden Artikeln und Ressourcen finden Sie weitere Informationen dazu.

- [Azure Storage-Dokumentation](http://azure.microsoft.com/documentation/services/storage/)
- [MSDN-Referenz für Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [PowerShell-Cmdlets für Azure Storage](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Windows PowerShell-Referenz](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Erste Schritte mit Azure Storage und PowerShell in 5 Minuten]: #getstart
[Voraussetzungen für die Verwendung von Azure PowerShell mit Azure Storage]: #pre
[Verwalten von Speicherkonten in Azure]: #manageaccount
[Einrichten von standardmäßigen Azure-Abonnements]: #setdefsub
[Erstellen von neuen Azure-Speicherkonten]: #createaccount
[Einrichten von standardmäßigen Azure-Speicherkonten]: #defaultaccount
[Auflisten aller Azure-Speicherkonten in einem Abonnement]: #listaccounts
[Erstellen von Azure-Speicherkontexten]: #createctx
[Verwalten von Azure-Blobs und Blob-Momentaufnahmen]: #manageblobs
[Erstellen von Containern]: #container
[Hochladen von Blobs in einen Container]: #uploadblob
[Herunterladen von Blobs aus einem Container]: #downblob
[Kopieren von Blobs aus einem Speichercontainer in einen anderen]: #copyblob
[Löschen von Blobs]: #deleteblob
[Verwalten von Azure-Blob-Momentaufnahmen]: #manageshots
[Erstellen von Blob-Momentaufnahmen]: #createshot
[Auflisten der Momentaufnahmen von Blobs]: #listshot
[Kopieren von Momentaufnahmen von Blobs]: #copyshot
[Verwalten von Azure-Tabellen und Tabellenentitäten]: #managetables
[Erstellen von Tabellen]: #createtable
[Abrufen von Tabellen]: #gettable
[Löschen von Tabellen]: #remtable
[Verwalten von Tabellenentitäten]: #mngentity
[Hinzufügen von Tabellenentitäten]: #addentity
[Abfragen von Tabellenentitäten]: #queryentity
[Löschen von Tabellenentitäten]: #deleteentity
[Verwalten von Azure-Warteschlangen und Warteschlangennachrichten]: #managequeues
[Erstellen von Warteschlangen]: #createqueue
[Abrufen von Warteschlangen]: #getqueue
[Löschen von Warteschlangen]: #remqueue
[Verwalten von Warteschlangennachrichten]: #mngqueuemsg
[Einfügen einer Nachricht in eine Warteschlange]: #addqueuemsg
[Entfernen aus der Warteschlange bei nächster Nachricht]: #dequeuemsg
[Verwalten von Azure-Dateifreigaben und Dateien]: #files
[Festlegen und Abfragen von Speicheranalysen]: #stganalytics
[Verwalten von Shared Access Signature (SAS) und gespeicherten Zugriffsrichtlinien]: #sas
[Verwenden von Azure Storage für die US-Regierung und Azure China]: #gov
[Nächste Schritte]: #next

<!--HONumber=47-->
