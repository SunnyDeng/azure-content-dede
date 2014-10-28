<properties title="About Chef and Azure Virtual Machines" pageTitle="About Chef and Azure Virtual Machines" description="Describes installing and configuring Chef on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Über Chef und virtuelle Azure-Computer

Chef bietet ein Automatisierungssystem für Aufbau, Anwendung und Verwaltung Ihrer Infrastruktur. Ressourcen werden mithilfe von „Rezepten“ verwaltet. Dabei handelt es sich um wiederverwendbare Definitionen, die Befehle für Prozesse, wie die Konfiguration eines Webservers, liefern.

Chef ist ein Client-Server-System. Weitere Informationen zu Ihren Möglichkeiten, einen Chef-Server zu nutzen, finden Sie in [Wählen Sie Ihre Installation][Wählen Sie Ihre Installation]. Sie benötigen Informationen über den Chef-Server, um den Client einzurichten. Für die Installation des Chef-Clients auf einem virtuellen Azure-Computer haben Sie folgende Möglichkeiten:

-   Verwenden Sie das Verwaltungsportal, um den Chef-Client zu installieren, wenn Sie einen virtuellen Computer mit Windows Server 2012 oder Windows Server 2012 R2 erstellen. Hinweise finden Sie im [Microsoft Azure-Portal][Microsoft Azure-Portal].
-   Verwenden Sie Windows PowerShell, um den Chef-Client auf einem bestehenden virtuellen Computer zu installieren. Ein [Beispielskript][Beispielskript] ist auf Github verfügbar.
-   Verwenden Sie das Chef-Plugin, [Knife-Azure][Knife-Azure], um eine Instanz eines virtuellen Computers zu erstellen und den Chef-Client zu installieren.

## Zusätzliche Ressourcen

[Chef und Microsoft Azure][Chef und Microsoft Azure]

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Anmelden bei einem mit Linux betriebenen virtuellen Computer][Anmelden bei einem mit Linux betriebenen virtuellen Computer]

[Erweiterungen verwalten][Erweiterungen verwalten]

<!--Link references-->

  [Wählen Sie Ihre Installation]: http://www.getchef.com/chef/choose-your-version/
  [Microsoft Azure-Portal]: http://docs.opscode.com/azure_portal.html
  [Beispielskript]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [Knife-Azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef und Microsoft Azure]: http://www.getchef.com/solutions/azure/
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Anmelden bei einem mit Linux betriebenen virtuellen Computer]: ../virtual-machines-linux-how-to-log-on
  [Erweiterungen verwalten]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
