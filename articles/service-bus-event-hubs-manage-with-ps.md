<properties
   pageTitle="Verwenden von PowerShell zum Verwalten von Service Bus- und Event Hub-Ressourcen"
   description="Verwenden von PowerShell zum Erstellen und Verwalten von Service Bus- und Event Hub-Ressourcen"
   services="service-bus"
   documentationCenter=".NET"
   authors="sethmanheim"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="04/14/2015"
   ms.author="sethm"/>

# Verwenden von PowerShell zum Verwalten von Service Bus- und Event Hub-Ressourcen

Microsoft Azure PowerShell ist eine Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung von Azure-Diensten steuern und automatisieren können. In diesem Artikel wird beschrieben, wie Sie mithilfe von PowerShell Service Bus-Entitäten wie Namespaces, Warteschlangen und Event Hubs über eine lokale Azure PowerShell-Konsole bereitstellen und verwalten.

## Voraussetzungen

Bevor Sie beginnen, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Azure erwerben], [Spezielle Angebote] oder [Einen Monat kostenlos testen!].

- Einen Computer mit Azure PowerShell. Anweisungen zur Einrichtung finden Sie im nächsten Abschnitt.

- Allgemeine Kenntnisse über PowerShell-Skripts, NuGet-Pakete und .NET Framework.

## Einrichten von PowerShell

[AZURE.INCLUDE [powershell-setup](../includes/powershell-setup.md)]

## Einfügen eines Verweises auf die .NET-Assembly für Service Bus

Es gibt eine begrenzte Anzahl von PowerShell-Cmdlets zur Verwaltung von Service Bus. Für die Bereitstellung von Entitäten, die nicht über die vorhandenen Cmdlets verfügbar gemacht werden, können Sie den .NET-Client für Service Bus aus PowerShell heraus verwenden, indem Sie auf das [Service Bus-NuGet-Paket] verweisen.

Sie müssen zunächst sicherstellen, dass Ihr Skript die Assembly **Microsoft.ServiceBus.dll** findet, die zusammen mit dem NuGet-Paket installiert wird. Aus Gründen der Flexibilität führt das Skript folgende Schritte aus:

1. Es bestimmt den Pfad, aus dem es aufgerufen wurde.
2. Es durchläuft den Pfad, bis es einen Ordner mit dem Namen `packages` gefunden hat. Dieser Ordner wird erstellt, wenn Sie NuGet-Pakete installieren.
3. Es durchsucht den Ordner `packages` rekursiv nach einer Assembly mit der Bezeichnung **Microsoft.ServiceBus.dll**.
4. Es verweist auf die Assembly, sodass die Typen zur späteren Verwendung zur Verfügung stehen.

Diese Schritte werden in einem PowerShell-Skript folgendermaßen implementiert:

	```powershell
	
	try
	{
	    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
	    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
	    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
	    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
	    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
	    Add-Type -Path $assembly.FullName
	
	    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
	}
	
	catch [System.Exception]
	{
	    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
	}
	
	```

## Bereitstellen eines Service Bus-Namespace

Bei der Arbeit mit Service Bus-Namespaces können Sie zwei Cmdlets anstelle des .NET SDK verwenden: [Get-AzureSBNamespace] und [New-AzureSBNamespace].

In diesem Beispiel werden einige lokale Variablen im Skript erstellt: `$Namespace` und `$Location`.

- `$Namespace` ist der Name des Service Bus-Namespace, mit dem Sie arbeiten möchten.
- `$Location` identifiziert das Rechenzentrum, in dem Sie den Namespace bereitstellen.
- `$CurrentNamespace` speichert den Referenznamespace, den Sie abrufen (oder erstellen).

In einem tatsächlichen Skript können `$Namespace` und `$Location` als Parameter übergeben werden.

Dieser Teil des Skripts führt Folgendes aus:

1. Es wird versucht, einen Service Bus-Namespace mit dem angegebenen Namen zu finden.
2. Wenn der Namespace gefunden wird, erfolgt eine Meldung, was gefunden wurde.
3. Wenn der Namespace nicht gefunden wird, wird der Namespace erstellt und anschließend der neu erstellte Namespace abgerufen.

		``` powershell
		
		$Namespace = "MyServiceBusNS"
		$Location = "West US"
		
		# Query to see if the namespace currently exists
		$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

		# Check if the namespace already exists or needs to be created
		if ($CurrentNamespace)
		{
		    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
		}
		else
		{
		    Write-Host "The [$Namespace] namespace does not exist."
		    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
		    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
		    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
		    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
		}
		```
Um weitere Service Bus-Entitäten bereitstellen zu können, erstellen Sie eine Instanz des `NamespaceManager`-Objekts aus dem SDK. Sie können mithilfe des Cmdlets [Get-AzureSBAuthorizationRule] eine Autorisierungsregel abrufen, die zur Bereitstellung einer Verbindungszeichenfolge verwendet wird. In diesem Beispiel wird ein Verweis auf die `NamespaceManager`-Instanz in der `$NamespaceManager`-Variable gespeichert. Das Skript verwendet später `$NamespaceManager` zur Bereitstellung weiterer Entitäten.

		``` powershell
		$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
		# Create the NamespaceManager object to create the Event Hub
		Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
		$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
		Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
		```
## Bereitstellen weiterer Service Bus-Entitäten

Um weitere Entitäten wie z. B. Warteschlangen, Themen und Event Hubs bereitzustellen, können Sie die [.NET-API für Service Bus] verwenden. Am Ende dieses Artikels finden Sie einige ausführlichere Beispiele, einschließlich anderer Entitäten.

### Erstellen eines Ereignis-Hubs

In diesem Teil des Skripts werden vier weitere lokale Variablen erstellt. Diese Variablen werden zum Instanziieren eines `EventHubDescription`-Objekts verwendet. Das Skript führt Folgendes aus:

1. Überprüfen Sie zunächst mithilfe des `NamespaceManager`-Objekts, ob der durch `$Path` identifizierte Event Hub vorhanden ist.
2. Wenn dieser nicht vorhanden ist, erstellen Sie ein `EventHubDescription`-Objekt und übergeben dieses an die `CreateEventHubIfNotExists`-Methode der `NamespaceManager`-Klasse.
3. Nachdem Sie nun wissen, dass der Event Hub verfügbar ist, erstellen Sie mithilfe von `ConsumerGroupDescription` und `NamespaceManager` eine Verbrauchergruppe.

		``` powershell
		
		$Path  = "MyEventHub"
		$PartitionCount = 12
		$MessageRetentionInDays = 7
		$UserMetadata = $null
		$ConsumerGroupName = "MyConsumerGroup"
		
		# Check if the Event Hub already exists
		if ($NamespaceManager.EventHubExists($Path))
		{
		    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
		}
		else
		{
		    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
		    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
		    $EventHubDescription.PartitionCount = $PartitionCount
		    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
		    $EventHubDescription.UserMetadata = $UserMetadata
		    $EventHubDescription.Path = $Path
		    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
		    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
		}
		
		# Create the consumer group if it doesn't exist
		Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
		$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
		$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
		$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
		Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
		```

### Erstellen einer Warteschlange

Führen Sie zum Erstellen einer Warteschlange oder eines Themas wie im vorherigen Abschnitt beschrieben eine Namespaceüberprüfung durch.

	if ($NamespaceManager.QueueExists($Path))
	{
	    Write-Output "The [$Path] queue already exists in the [$Namespace] namespace." 
	}
	else
	{
	    Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
	    $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
	    $QueueDescription.EnableExpress = $EnableExpress
	    $QueueDescription.EnablePartitioning = $EnablePartitioning
	    $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
	    $QueueDescription.ForwardTo = $ForwardTo
	    $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    if ($LockDuration -gt 0)
	    {
	        $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
	    }
	    $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
	    $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    $QueueDescription.RequiresSession = $RequiresSession
	    if ($EnablePartitioning)
	    {
	        $QueueDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $QueueDescription.SupportOrdering = $SupportOrdering
	    }
	    $QueueDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateQueue($QueueDescription);
	}

### Erstellen eines Themas

	if ($NamespaceManager.TopicExists($Path))
	{
	    Write-Output "The [$Path] topic already exists in the [$Namespace] namespace." 
	}
	else
	{
	    Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
	    $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $TopicDescription.EnableExpress = $EnableExpress
	    $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
	    $TopicDescription.EnablePartitioning = $EnablePartitioning
	    $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    if ($EnablePartitioning)
	    {
	        $TopicDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $TopicDescription.SupportOrdering = $SupportOrdering
	    }
	    $TopicDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateTopic($TopicDescription);
	}

## Nächste Schritte

Dieser Artikel beschreibt die grundlegenden Schritte zur Bereitstellung von Service Bus-Entitäten mithilfe von PowerShell. Auch wenn es nur eine begrenzte Anzahl von PowerShell-Cmdlets zur Verwaltung von Service Bus-Nachrichtenentitäten gibt, können durch Verweise auf die Assembly "Microsoft.Servicebus.dll" über die .NET-Clientbibliotheken praktisch alle Vorgänge auch in einem PowerShell-Skript ausgeführt werden.

In folgenden Blogbeiträgen finden Sie ausführlichere Beispiele:

- [How to create Service Bus queues, topics and subscriptions using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx) (Erstellen von Service Bus-Warteschlangen, -Themen und -Abonnements mithilfe eines PowerShell-Skripts, in englischer Sprache)
- [How to create a Service Bus Namespace and an Event Hub using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx) (Erstellen eines Service Bus-Namespace und eines Event Hubs mithilfe eines PowerShell-Skripts, in englischer Sprache)

Es stehen auch einige einsatzbereite Skripts zum Download zur Verfügung: [Service Bus-PowerShell-Skripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[Azure erwerben]: http://azure.microsoft.com/pricing/purchase-options/
[Spezielle Angebote]: http://azure.microsoft.com/pricing/member-offers/
[Einen Monat kostenlos testen!]: http://azure.microsoft.com/pricing/free-trial/
[Service Bus-NuGet-Paket]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[.NET-API für Service Bus]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!---HONumber=58-->