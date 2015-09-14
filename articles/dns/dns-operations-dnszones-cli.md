<properties 
   pageTitle="Vorgänge für DNS-Zonen | Microsoft Azure"
	description="Sie können DNS-Zonen mit Azure Powershell-Cmdlets oder der -Befehlszeilenschnittstelle verwalten. Aktualisieren, Löschen und Erstellen von DNS-Zonen in Azure DNS"
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
	ms.date="09/02/2015"
	ms.author="joaoma"/>

# Verwalten von DNS-Zonen

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [Azure Powershell](dns-operations-dnszones.md)

Diese Anleitung zeigt Ihnen, wie Sie die DNS-Zone verwalten. Sie hilft Ihnen, die Reihenfolge der erforderlichen Vorgänge zu verstehen, die für die Verwaltung der DNS-Zone durchgeführt werden müssen.

## Erstellen einer neuen DNS-Zone

Um eine neue DNS-Zone zum Hosten Ihrer Domäne zu erstellen, verwenden Sie `azure network dns zone create`:

		Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

Der Vorgang erstellt eine neue DNS-Zone in Azure DNS. Optional können Sie ein Array von Azure-Ressourcen-Manager-Tags angeben. Weitere Informationen finden Sie unter [Etags und Tags](dns-getstarted-create-dnszone.md#Etags-and-tags).

Der Name der Zone innerhalb der Ressourcengruppe muss eindeutig sein, und die Zone darf noch nicht vorhanden sein, andernfalls schlägt der Vorgang fehl.

Der gleiche Zonennamen kann in einer anderen Ressourcengruppe oder einem anderen Azure-Abonnement erneut verwendet werden. Wenn mehrere Zonen denselben Namen haben, werden jeder Instanz verschiedene Namensserveradressen zugewiesen, und nur eine Instanz kann von der übergeordneten Domäne delegiert werden. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md).

## Abrufen einer DNS-Zone

Verwenden Sie zum Abrufen einer DNS-Zone `azure network dns zone show`:

	azure network dns zone show myresourcegroup contoso.com

Der Vorgang gibt eine DNS-Zone mit deren ID, der Anzahl der Datensätze und Tags zurück.


## Auflisten von DNS-Zonen

Verwenden Sie zum Abrufen von DNS-Zonen innerhalb einer Ressourcengruppe `azure network dns zone list`:

	azure network dns zone list myresourcegroup


## Aktualisieren einer DNS-Zone

Änderungen an einer DNS-Zonenressource können mithilfe von `azure network dns zone set` vorgenommen werden. Dadurch wird keine der DNS-Datensatzgruppen in der Zone aktualisiert (siehe [Verwalten von DNS-Einträgen](dns-operations-recordsets.md)). Es wird nur verwendet, um Eigenschaften der Zonenressource selbst zu aktualisieren. Dies ist derzeit auf die Azure-Ressourcen-Manager-"Tags" für die Zonenressource beschränkt. Weitere Informationen finden Sie unter [Etags und Tags](dns-getstarted-create-dnszone.md#Etags-and-tags).

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Löschen einer DNS-Zone

DNS-Zonen können mithilfe von `azure network dns zone delete` gelöscht werden.
 
Vor dem Löschen einer DNS-Zone in Azure DNS müssen Sie alle Datensatzgruppen löschen, mit Ausnahme der NS- und SOA-Einträge im Zonenstamm, die beim Erstellen der Zone automatisch erstellt wurden.

	azure network dns zone delete myresourcegroup contoso.com 

Dieser Vorgang hat einen optionalen Switch "-q", der die Eingabeaufforderung zur Bestätigung des Löschvorgangs der DNS-Zone unterdrückt.


## Nächste Schritte


[Verwalten von DNS-Einträgen](dns-operations-recordsets-cli.md)

[Automatisieren von Vorgängen mit dem .NET SDK](dns-sdk.md)

<!---HONumber=September15_HO1-->