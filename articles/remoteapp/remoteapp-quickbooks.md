<properties 
    pageTitle="Bereitstellen von QuickBooks in Azure RemoteApp | Microsoft Azure" 
    description="Erfahren Sie, wie Sie QuickBooks mit Azure RemoteApp freigeben." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# Bereitstellen von QuickBooks in Azure RemoteApp

Verwenden Sie die folgenden Informationen, um QuickBooks als App in Azure RemoteApp freizugeben.


Sie können QuickBooks 2015 Enterprise mit Azure RemoteApp in einer Hybrid- oder Cloud-Sammlung freigeben. Die Unternehmensdatei muss sich auf einem virtuellen Computer befinden, auf dem der QuickBooks-Datenbankserver ausgeführt wird, der von den Azure RemoteApp-Servern getrennt ist. Speichern Sie die Unternehmensdatei niemals auf Ihrem Azure RemoteApp-Image, da es in diesem Fall zu Datenverlusten kommen kann. Nur QuickBooks Enterprise unterstützt das Hosten der QuickBooks-Datei auf einer externen Freigabe, wobei der QuickBooks-Datenbankserver über eine standardmäßige Windows-Netzwerkumgebung zugänglich ist.

> [AZURE.IMPORTANT]Der QuickBooks-Datenbankserver, auf dem die Unternehmensdatei gehostet wird, muss sich auf einem separaten virtuellen Computer in demselben VNET wie die Azure RemoteApp-Sammlung befinden.

## Schritte zum Bereitstellen von QuickBooks

1. Erstellen Sie einen virtuellen Azure-Computer, und installieren Sie QuickBooks und den QuickBooks-Datenbankserver, und stellen Sie die Unternehmensdatei auf einem virtuellen Azure-Computer bereit. Achten Sie darauf, die Firewallregeln richtig zu konfigurieren.
2. Installieren Sie QuickBooks auf einem [benutzerdefinierten Image](remoteapp-imageoptions.md), und erstellen Sie eine [Azure RemoteApp-Sammlung](remoteapp-collections.md) (Cloud oder Hybrid) in demselben VNET, in dem sich der virtuelle Computer zum Hosten des QuickBooks-Datenbankservers mit Unternehmensdateien befindet. 
3.	[Veröffentlichen](remoteapp-publish.md) der QuickBooks-App für Benutzer
4.	Starten Sie den von Azure RemoteApp gehosteten QuickBooks-Client, navigieren Sie mit der standardmäßigen Windows-Netzwerkverbindung zum virtuellen Computer, auf dem der QuickBooks-Datenbankserver gehostet wird, und öffnen Sie die Unternehmensdatei. 

## Dokumentationsverweise

- Von QuickBooks [unterstützte Konfigurationen](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- QuickBooks-[Bereitstellungsoptionen](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

Sie können sich auch meine Ignite-Präsentation mit dem Titel [Fundamentals of Microsoft Azure RemoteApp Management and Administration](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) (in englischer Sprache) ansehen. Der QuickBooks-Teil beginnt bei 1:02:45.

## Bereitstellungsarchitektur

![QuickBooks- und Azure RemoteApp-Bereitstellung](./media/remoteapp-quickbooks/ra-quickbooks.png)

<!---HONumber=AcomDC_0114_2016-->