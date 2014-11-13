<properties title="&Uuml;ber Chef und Azure Virtual Machines" pageTitle="&Uuml;ber Chef und Azure Virtual Machines" description="Beschreibt die Installation und Konfiguration von Chef auf einem virtuellen Computer in Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="07/29/2014" ms.author="kathydav" />

# Über Chef und Azure Virtual Machines

Chef bietet ein Automatisierungssystem für das Erstellen, Bereitstellen und Verwalten Ihrer Infrastruktur. Ressourcen werden mithilfe von Rezepten verwaltet. Hierbei handelt es sich um wiederverwendbare Definitionen, die Anweisungen für Aufgaben wie das Konfigurieren eines Webservers bereitstellen.

Chef ist ein Clientserversystem. Unter [Choose your installation][Choose your installation] (Auswählen Ihrer Installation) (in englischer Sprache) erfahren Sie mehr über Ihre Möglichkeiten für die Verwendung eines Chef-Servers. Sie benötigen Informationen über den Chef-Server, um den Client einzurichten. Zum Installieren des Chef-Clients auf einem virtuellen Azure-Computer haben Sie die folgenden Auswahlmöglichkeiten:

-   Verwenden Sie das Verwaltungsportal zum Installieren des Chef-Clients beim Erstellen eines virtuellen Computers unter Windows Server 2012 oder Windows Server 2012 R2. Anweisungen finden Sie unter [Microsoft Azure Portal][Microsoft Azure Portal] (Microsoft Azure-Portal) (in englischer Sprache).
-   Verwenden Sie die Windows PowerShell zum Installieren des Chef-Clients auf einem vorhandenen virtuellen Computer. Ein [Beispielskript][Beispielskript] finden Sie auf Github.
-   Verwenden Sie das Chef-Plug-In [knife-azure][knife-azure], um eine Instanz eines virtuellen Computers zu erstellen und um den Chef-Client zu installieren.

## Zusätzliche Ressourcen

[Chef and Microsoft Azure (Chef und Microsoft Azure) (in englischer Sprache)][Chef and Microsoft Azure (Chef und Microsoft Azure) (in englischer Sprache)]

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Anmelden bei einem mit Linux betriebenen virtuellen Computer][Anmelden bei einem mit Linux betriebenen virtuellen Computer]

[Verwalten von Erweiterungen][Verwalten von Erweiterungen]

<!--Link references-->

  [Choose your installation]: http://www.getchef.com/chef/choose-your-version/
  [Microsoft Azure Portal]: http://docs.opscode.com/azure_portal.html
  [Beispielskript]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [knife-azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef and Microsoft Azure (Chef und Microsoft Azure) (in englischer Sprache)]: http://www.getchef.com/solutions/azure/
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Anmelden bei einem mit Linux betriebenen virtuellen Computer]: ../virtual-machines-linux-how-to-log-on
  [Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
