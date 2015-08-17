<properties
	pageTitle="Über Chef und virtuelle Azure-Computer"
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
	ms.date="05/20/2015"
	ms.author="kathydav"/>

#Über Chef und virtuelle Azure-Computer

Chef bietet ein Automatisierungssystem für Aufbau, Anwendung und Verwaltung Ihrer Infrastruktur. Ressourcen werden mithilfe von „Rezepten“ verwaltet. Dabei handelt es sich um wiederverwendbare Definitionen, die Befehle für Prozesse, wie die Konfiguration eines Webservers, liefern.

Chef ist ein Client-Server-System. Weitere Informationen zu Ihren Möglichkeiten, einen Chef-Server zu nutzen, finden Sie in [Wählen Sie Ihre Installation](http://www.getchef.com/chef/choose-your-version/). Sie benötigen Informationen über den Chef-Server, um den Client einzurichten.

Zum Installieren des Chef-Clients auf einem virtuellen Azure-Computer haben Sie die folgenden Auswahlmöglichkeiten:

- Verwenden Sie das Azure-Portal zum Installieren des Chef-Clients beim Erstellen eines virtuellen Computers unter Windows Server 2012 oder Windows Server 2012 R2. Hinweise finden Sie im [Azure-Portal](https://docs.chef.io/azure_portal.html).
- Verwenden Sie Azure PowerShell, um den Chef-Client auf einem vorhandenen virtuellen Computer installieren. Ein [Beispielskript](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) finden Sie auf GitHub.
- Verwenden Sie das Chef-Plugin, [Knife-Azure](http://docs.getchef.com/plugin_knife_azure.html), um eine Instanz eines virtuellen Computers zu erstellen und den Chef-Client zu installieren.


##Zusätzliche Ressourcen
[Chef und Microsoft Azure]

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Anmelden bei einem mit Linux betriebenen virtuellen Computer]

[Verwalten von Erweiterungen]

<!--Link references-->
[Chef und Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: virtual-machines-log-on-windows-server.md
[Anmelden bei einem mit Linux betriebenen virtuellen Computer]: virtual-machines-linux-how-to-log-on.md
[Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=August15_HO6-->