<properties
	pageTitle="Verwalten von Service Bus mit PowerShell | Microsoft Azure"
	description="Verwalten von Service Bus mit PowerShell-Skripts anstelle von .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Verwalten von Service Bus mit PowerShell

## Übersicht

Microsoft Azure PowerShell ist eine Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. In diesem Artikel wird beschrieben, wie Sie mithilfe von PowerShell Service Bus-Entitäten wie Namespaces, Warteschlangen und Event Hubs über eine lokale Azure PowerShell-Konsole bereitstellen und verwalten.

## Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Azure erwerben], [Spezielle Angebote] oder [Einen Monat kostenlos testen!].

- Einen Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell].

- Allgemeine Kenntnisse über PowerShell-Skripts, NuGet-Pakete und .NET Framework.

## Einschließen eines Verweises auf die .NET-Assembly für Service Bus

Es gibt eine begrenzte Anzahl von PowerShell-Cmdlets zur Verwaltung von Service Bus. Für die Bereitstellung von Entitäten, die nicht über die vorhandenen Cmdlets verfügbar gemacht werden, können Sie den .NET-Client für Service Bus im [Service Bus-NuGet-Paket] verwenden.

Sie müssen zunächst sicherstellen, dass Ihr Skript die Assembly **Microsoft.ServiceBus.dll** findet, die zusammen mit dem NuGet-Paket installiert wird. Aus Gründen der Flexibilität führt das Skript folgende Schritte aus:

1. Es bestimmt den Pfad, in dem es aufgerufen wurde.
2. Es durchläuft den Pfad, bis es einen Ordner mit dem Namen `packages` gefunden hat. Dieser Ordner wird erstellt, wenn Sie NuGet-Pakete installieren.
3. Es durchsucht den Ordner `packages` rekursiv nach einer Assembly mit der Bezeichnung **Microsoft.ServiceBus.dll**.
4. Es verweist auf die Assembly, sodass die Typen zur späteren Verwendung zur Verfügung stehen.

Diese Schritte werden in einem PowerShell-Skript folgendermaßen implementiert:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
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

Zwei PowerShell-Cmdlets unterstützen Service Bus-Namespacevorgänge. Anstelle der .NET SDK-APIs können Sie [Get-AzureSBNamespace][] und [New-AzureSBNamespace][] verwenden.

In diesem Beispiel werden einige lokale Variablen im Skript erstellt: `$Namespace` und `$Location`.

- `$Namespace` ist der Name des Service Bus-Namespace, mit dem Sie arbeiten möchten.
- `$Location` identifiziert das Rechenzentrum, in dem das Skript den Namespace bereitstellt.
- `$CurrentNamespace` speichert den Referenznamespace, den das Skript abruft (oder erstellt).

In einem tatsächlichen Skript können `$Namespace` und `$Location` als Parameter übergeben werden.

Dieser Teil des Skripts führt Folgendes aus:

1. Es wird versucht, einen Service Bus-Namespace mit dem angegebenen Namen zu finden.
2. Wenn der Namespace gefunden wird, erfolgt eine Meldung, was gefunden wurde.
3. Wenn der Namespace nicht gefunden wird, wird der Namespace erstellt und anschließend der neu erstellte Namespace abgerufen.

	```
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
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```

Um weitere Service Bus-Entitäten bereitstellen zu können, erstellen Sie eine Instanz der [NamespaceManager][]-Klasse aus dem SDK. Sie können mithilfe des Cmdlets [Get-AzureSBAuthorizationRule][] eine Autorisierungsregel abrufen, die zur Bereitstellung einer Verbindungszeichenfolge verwendet wird. Sie speichern einen Verweis auf die `NamespaceManager`-Instanz in der `$NamespaceManager`-Variablen. Sie werden `$NamespaceManager` später im Skript verwenden, um weitere Entitäten bereitzustellen.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Bereitstellen weiterer Service Bus-Entitäten

Um weitere Entitäten wie z. B. Warteschlangen, Themen und Event Hubs bereitzustellen, verwenden Sie die [.NET-API für Service Bus][]. In diesem Artikel geht es nur um Event Hubs, die Schritte sind für andere Entitäten jedoch ähnlich. Darüber hinaus finden Sie am Ende dieses Artikels Hinweise auf einige detaillierte Beispiele, auch für andere Entitäten.

In diesem Teil des Skripts werden vier weitere lokale Variablen erstellt. Diese Variablen werden zum Instanziieren eines `EventHubDescription`-Objekts verwendet. Das Skript führt Folgendes aus:

1. Überprüfen Sie zunächst mithilfe des `NamespaceManager`-Objekts, ob der durch `$Path` identifizierte Event Hub vorhanden ist.
2. Wenn dieser nicht vorhanden ist, erstellen Sie ein `EventHubDescription`-Objekt und übergeben dieses an die `CreateEventHubIfNotExists`-Methode der `NamespaceManager`-Klasse.
3. Nachdem Sie nun wissen, dass der Event Hub verfügbar ist, erstellen Sie mithilfe von `ConsumerGroupDescription` und `NamespaceManager` eine Verbrauchergruppe.

	```
	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"
		
	# Check to see if the Event Hub already exists
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

## Nächste Schritte

Dieser Artikel beschreibt die grundlegenden Schritte zur Bereitstellung von Service Bus-Entitäten mithilfe von PowerShell. Alle Aufgaben, die Sie mithilfe der .NET-Clientbibliotheken ausführen können, können Sie auch in einem PowerShell-Skript erledigen.

In folgenden Blogbeiträgen finden Sie ausführlichere Beispiele:

- [How to create Service Bus queues, topics and subscriptions using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx) (Erstellen von Service Bus-Warteschlangen, -Themen und -Abonnements mithilfe eines PowerShell-Skripts, in englischer Sprache)
- [How to create a Service Bus Namespace and an Event Hub using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx) (Erstellen eines Service Bus-Namespace und eines Event Hubs mithilfe eines PowerShell-Skripts, in englischer Sprache)

Es stehen auch einige einsatzbereite Skripts zum Download zur Verfügung: [Service Bus-PowerShell-Skripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Azure erwerben]: http://azure.microsoft.com/pricing/purchase-options/
[Spezielle Angebote]: http://azure.microsoft.com/pricing/member-offers/
[Einen Monat kostenlos testen!]: http://azure.microsoft.com/pricing/free-trial/
[Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell.md
[Service Bus-NuGet-Paket]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[.NET-API für Service Bus]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

<!---HONumber=Oct15_HO2-->