<properties 
	pageTitle="Bereitstellen und Verwalten von Notification Hubs mit PowerShell" 
	description="Erstellen und Verwalten von Notification Hubs mit PowerShell für Automation" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Bereitstellen und Verwalten von Notification Hubs mit PowerShell

##Übersicht

In diesem Artikel wird das Erstellen und Verwalten von Azure Notification Hubs mithilfe von PowerShell veranschaulicht. Die folgenden allgemeinen Automatisierungsaufgaben werden in diesem Thema dargestellt.

+ Erstellen eines Notification Hubs
+ Festlegen von Anmeldeinformationen

Wenn Sie auch einen neuen Servicebus-Namespace für Ihe Notification Hubs erstellen müssen, finden Sie Informationen dazu unter [Verwalten von Service Bus mit PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

Das Verwalten von Notification Hubs wird nicht direkt von den in Azure PowerShell enthaltenen Cmdlets unterstützt. Der beste Ansatz für PowerShell ist der Verweis auf die Microsoft.ServiceBus.dll-Assembly. Die Assembly wird zusammen mit dem [Service Bus NuGet-Paket](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) verteilt.


## Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Azure erwerben], [Spezielle Angebote] oder [Einen Monat kostenlos testen!].

- Einen Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell].

- Allgemeine Kenntnisse über PowerShell-Skripts, NuGet-Pakete und .NET Framework.


## Einschließen eines Verweises auf die .NET-Assembly für Service Bus

Das Verwalten von Azure Notification Hubs ist noch nicht in den PowerShell-Cmdlets in Azure PowerShell enthalten. Für die Bereitstellung von Notification Hubs und anderen Service Bus-Entitäten, die nicht über die vorhandenen Cmdlets verfügbar gemacht werden, können Sie den .NET-Client für Service Bus im [Service Bus-NuGet-Paket](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) verwenden.

Sie müssen zunächst sicherstellen, dass Ihr Skript die Assembly **Microsoft.ServiceBus.dll** findet, die als NuGet-Paket in einem Visual Studio-Projekt installiert wird. Aus Gründen der Flexibilität führt das Skript folgende Schritte aus:

1. Es bestimmt den Pfad, in dem es aufgerufen wurde.
2. Es durchläuft den Pfad, bis es einen Ordner mit dem Namen `packages` gefunden hat. Dieser Ordner wird erstellt, wenn Sie NuGet-Pakete für Visual Studio-Projekte installieren.
3. Es durchsucht den Ordner `packages` rekursiv nach einer Assembly mit der Bezeichnung **Microsoft.ServiceBus.dll**.
4. Es verweist auf die Assembly, sodass die Typen zur späteren Verwendung zur Verfügung stehen.

Diese Schritte werden in einem PowerShell-Skript folgendermaßen implementiert:

``` powershell

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

## Erstellen der NamespaceManager-Klasse

Zum Bereitstellen von Notification Hubs und anderen Service Bus-Entitäten erstellen Sie eine Instanz der [NamespaceManager](http://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx)-Klasse aus dem SDK.

Sie können mithilfe des in Azure Power Shell enthaltenen Cmdlets [Get-AzureSBAuthorizationRule] eine Autorisierungsregel abrufen, die zur Bereitstellung einer Verbindungszeichenfolge verwendet wird. Sie speichern einen Verweis auf die `NamespaceManager`-Instanz in der `$NamespaceManager`-Variable. Wir verwenden `$NamespaceManager`, um einen Notification Hub bereitzustellen.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## Bereistellung eines neuen Notification Hubs 

Verwenden Sie zum Bereitstellen eines neuen Notification Hubs die [.NET-API für Service Bus]. Dieser Artikel konzentriert sich nur auf Notification Hubs. Um mit anderen Service Bus-Entitäten zu arbeiten, lesen Sie [Verwalten von Service Bus mit PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

In diesem Teil des Skripts erstellen Sie vier lokale Variablen.

1. `$Namespace` : Legen Sie diese auf den Namen des Namespace fest, in dem Sie einen Notification Hub erstellen möchten.
2. `$Path` : Legen Sie diesen Pfad auf den Namen des neuen Notification Hubs fest. Beispiel: "MyHub".    
3. `$WnsPackageSid` : Legen Sie diese Variable auf die Paket-SID für Ihre Windows-App aus dem [Windows-Entwicklungscenter](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) fest.
4. `$WnsSecretkey`: Legen Sie diese auf den geheimen Schlüssel für Ihre Windows-App aus dem [Windows-Entwicklungscenter](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) fest.

Diese Variablen werden verwendet, um eine Verbindung mit dem Service Bus-Namespace herzustellen und einen neuen Notification Hub zu erstellen, damit Benachrichtigungen von Windows Notification Services (WNS) mit WNS-Anmeldeinformationen für eine Windows-App verarbeitet werden können. Weitere Informationen zum Abrufen der Paket-SID und des geheimen Schlüssels finden Sie im Lernprogramm [Erste Schritte mit Notification Hubs](notification-hubs-windows-store-dotnet-get-started.md).

+ Der Codeausschnitt verwendet das `NamespaceManager`-Objekt, um zu prüfen, ob der von `$Path` erkannte Notification Hub vorhanden ist.

+ Falls er nicht vorhanden ist, erstellt das Skript eine `NotificationHubDescription` mit WNS-Anmeldeinformationen, und übergibt diese an die `NamespaceManager`-Klasse, `CreateNotificationHub`-Methode.

``` powershell

$Namespace = "<Enter your namespace>
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.ServiceBus.Notifications.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.ServiceBus.Notifications.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## Zusätzliche Ressourcen

- [Verwalten von Service Bus mit PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
- [How to create Service Bus queues, topics and subscriptions using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx) (Erstellen von Service Bus-Warteschlangen, -Themen und -Abonnements mithilfe eines PowerShell-Skripts, in englischer Sprache)
- [How to create a Service Bus Namespace and an Event Hub using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx) (Erstellen eines Service Bus-Namespace und eines Event Hubs mithilfe eines PowerShell-Skripts, in englischer Sprache)

Es stehen auch einige einsatzbereite Skripts zum Download zur Verfügung: [Service Bus-PowerShell-Skripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Azure erwerben]: http://azure.microsoft.com/pricing/purchase-options/
[Spezielle Angebote]: http://azure.microsoft.com/pricing/member-offers/
[Einen Monat kostenlos testen!]: http://azure.microsoft.com/pricing/free-trial/
[Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell.md
[.NET-API für Service Bus]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 

<!---HONumber=July15_HO4-->