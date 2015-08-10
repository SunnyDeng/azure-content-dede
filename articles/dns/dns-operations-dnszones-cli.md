<properties 
   pageTitle="Vorgänge für DNS-Zonen | Microsoft Azure" 
   description="Sie können DNS-Zonen mit Azure Powershell-Cmdlets verwalten. Aktualisieren, Löschen und Erstellen von DNS-Zonen in Azure DNS" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="07/28/2015"
   ms.author="joaoma"/>

# Verwalten von DNS-Zonen

Diese Anleitung zeigt Ihnen, wie Sie die DNS-Zone verwalten. Sie hilft Ihnen, die Reihenfolge der erforderlichen Vorgänge zu verstehen, die für die Verwaltung der DNS-Zone durchgeführt werden müssen.

## Erstellen einer neuen DNS-Zone

Um eine neue DNS-Zone zum Hosten Ihrer Domäne zu erstellen, verwenden Sie das Cmdlet "New-AzureDnsZone":

		PS C:\> $zone = New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

Mit diesem Vorgang wird eine neue DNS-Zone in Azure DNS erstellt und ein lokales Objekt für diese Zone zurückgegeben. Optional können Sie ein Array von Azure-Ressourcen-Manager-Tags angeben. Weitere Informationen finden Sie unter [Etags und Tags](../dns-getstarted-create-dnszone#Etags-and-tags).

Der Name der Zone innerhalb der Ressourcengruppe muss eindeutig sein, und die Zone darf noch nicht vorhanden sein, andernfalls schlägt der Vorgang fehl.

Der gleiche Zonennamen kann in einer anderen Ressourcengruppe oder einem anderen Azure-Abonnement erneut verwendet werden. Wenn mehrere Zonen denselben Namen haben, werden jeder Instanz verschiedene Namensserveradressen zugewiesen, und nur eine Instanz kann von der übergeordneten Domäne delegiert werden. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](../dns-domain-delegation).

## Abrufen einer DNS-Zone

Verwenden Sie das Cmdlet "Get-AzureDnsZone", um eine DNS-Zone abzurufen:

		PS C:\> $zone = Get-AzureDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Der Vorgang gibt ein DNS-Zonenobjekt zurück, das einer vorhandenen Zone in Azure DNS entspricht. Dieses Objekt enthält Daten über die Zone \(z. B. die Anzahl der Datensatzgruppen\), jedoch nicht die Datensatzgruppen selbst.

## Auflisten von DNS-Zonen
Durch Auslassen des Zonennamens von Get-AzureDnsZone, können Sie alle Zonen in einer Ressourcengruppe auflisten:

	PS C:\> $zoneList = Get-AzureDnsZone -ResourceGroupName MyAzureResourceGroup
Dieser Vorgang gibt ein Array von Zonenobjekten zurück.

## Aktualisieren einer DNS-Zone
Änderungen an einer DNS-Zonenressource können mithilfe von Set-AzureDnsZone vorgenommen werden. Dadurch wird keine der DNS-Datensatzgruppen in der Zone aktualisiert \(siehe [Verwalten von DNS-Einträgen](../dns-operations-recordsets)\). Es wird nur verwendet, um Eigenschaften der Zonenressource selbst zu aktualisieren. Dies ist derzeit auf die Azure-Ressourcen-Manager-"Tags" für die Zonenressource beschränkt. Weitere Informationen finden Sie unter [Etags und Tags](../dns-getstarted-create-dnszone#Etags-and-tags).

Verwenden Sie eine der folgenden zwei Möglichkeiten, um die DNS-Zone zu aktualisieren:

### Option 1
 
Geben Sie die Zone mithilfe des Zonennamens und der Ressourcengruppe an.

	PS C:\> Set-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Option 2
Geben Sie die Zone mithilfe eines $zone-Objekts von Get-AzureDnsZone an:

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureDnsZone -Zone $zone [-Overwrite]

Wenn Sie Set-AzureDnsZone mit einem $zone-Objekt verwenden, werden Etag-Überprüfungen verwendet, um sicherzustellen, dass gleichzeitige Änderungen nicht überschrieben werden. Sie können den optionalen Switch "-Overwrite" verwenden, um diese Überprüfungen zu unterdrücken. Weitere Informationen finden Sie unter [Etags und Tags](../dns-getstarted-create-dnszone#Etags-and-tags).

## Löschen einer DNS-Zone

DNS-Zonen können mit dem Cmdlet "Remove-AzureDnsZone" gelöscht werden.
 
Vor dem Löschen einer DNS-Zone in Azure DNS müssen Sie alle Datensatzgruppen löschen, mit Ausnahme der NS- und SOA-Einträge im Zonenstamm, die beim Erstellen der Zone automatisch erstellt wurden.

Verwenden Sie eine der folgenden zwei Möglichkeiten, um die DNS-Zone zu löschen:

### Option 1

Geben Sie die Zone mithilfe des Zonennamens und des Ressourcengruppennamens an.

	PS C:\> Remove-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

Dieser Vorgang hat einen optionalen Switch "-Force", der die Eingabeaufforderung zur Bestätigung des Löschvorgangs der DNS-Zone unterdrückt.
### Option 2

Geben Sie die Zone mithilfe eines $zone-Objekts von Get-AzureDnsZone an:

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureDnsZone -Zone $zone [-Force] [-Overwrite]

Der Switch "-Force" ist derselbe wie in Option 1.

Wie bei "Set-AzureDnsZone" ermöglicht die Angabe der Zone mithilfe eines $zone-Objekts die Etag-Überprüfung, um sicherzustellen, dass gleichzeitige Änderungen nicht gelöscht werden. <BR> Das optionale Flag "-Overwrite" unterdrückt diese Überprüfungen. Weitere Informationen finden Sie unter [Etags und Tags](../dns-getstarted-create-dnszone#Etags-and-tags).

Das Zonenobjekt kann auch weitergeleitet werden, anstatt als Parameter übergeben zu werden:

	PS C:\> Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureDnsZone [-Force] [-Overwrite]

## Nächste Schritte


[Verwalten von DNS-Einträgen](../dns-operations-recordsets)

[Automatisieren von Vorgängen mit dem .NET SDK](../dns-sdk)

<!---HONumber=July15_HO5-->