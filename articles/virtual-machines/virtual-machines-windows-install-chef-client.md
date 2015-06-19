<properties 
	pageTitle="Über Chef und Azure Virtual Machines" 
	description="Beschreibt die Installation und Konfiguration von Chef auf einem virtuellen Computer in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="kathydav"/>

# Über Chef und Azure Virtual Machines

Chef bietet ein Automatisierungssystem für das Erstellen, Bereitstellen und Verwalten Ihrer Infrastruktur. Ressourcen werden mithilfe von Rezepten verwaltet. Hierbei handelt es sich um wiederverwendbare Definitionen, die Anweisungen für Aufgaben wie das Konfigurieren eines Webservers bereitstellen.   

Chef ist ein Clientserversystem. Unter [Choose your installation](http://www.getchef.com/chef/choose-your-version/) (Auswählen Ihrer Installation, in englischer Sprache) erfahren Sie mehr über Ihre Möglichkeiten für die Verwendung eines Chef-Servers. Sie benötigen Informationen über den Chef-Server, um den Client einzurichten. 

Zum Installieren des Chef-Clients auf einem virtuellen Azure-Computer haben Sie die folgenden Auswahlmöglichkeiten:

- Verwenden Sie das Verwaltungsportal zum Installieren des Chef-Clients beim Erstellen eines virtuellen Computers unter Windows Server 2012 oder Windows Server 2012 R2. Anweisungen finden Sie unter [Microsoft Azure Portal](https://docs.chef.io/azure_portal.html) (Microsoft Azure-Portal) (in englischer Sprache).
- Verwenden Sie Azure PowerShell, um den Chef-Client auf einem vorhandenen virtuellen Computer installieren. Ein Beispiel für ein [Skript](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) finden Sie auf Github.
- Verwenden Sie das Chef-Plug-In [knife-azure](http://docs.getchef.com/plugin_knife_azure.html), um eine Instanz eines virtuellen Computers zu erstellen und um den Chef-Client zu installieren.   


## Zusätzliche Ressourcen
[Chef and Microsoft Azure (Chef und Microsoft Azure, in englischer Sprache)]

[Anmelden bei einem virtuellen Computer unter Windows Server]

[Anmelden bei einem mit Linux betriebenen virtuellen Computer]

[Verwalten von Erweiterungen]

<!--Link references-->
[Chef and Microsoft Azure (Chef und Microsoft Azure, in englischer Sprache)]: http://www.getchef.com/solutions/azure/
[Anmelden bei einem virtuellen Computer unter Windows Server]: ../virtual-machines-log-on-windows-server/
[Anmelden bei einem mit Linux betriebenen virtuellen Computer]: ../virtual-machines-linux-how-to-log-on
[Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=47-->
 