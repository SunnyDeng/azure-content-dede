<properties title="About Puppet and Azure Virtual Machines" pageTitle="About Puppet and Azure Virtual Machines" description="Describes installing and configuring Puppet on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Über Puppet und Virtuelle Azure-Computer

Puppet Enterprise ist eine Automatisierungssoftware für das Entwickeln, Bereitstellen und Verwalten Ihrer Infrastruktur. Sie können es zur Verwaltung Ihres IT-Lebenszyklus einsetzen, einschließlich: Ermittlung, Bereitstellung, Verwalten von OS- und Anwendungskonfiguration, Orchestrierung und Berichte.

Puppet ist ein Client-Server-System. Puppet Master und der Puppet Enterprise Agent sind über Azure installierbar:

-   Puppet Master ist als auf einem Ubuntu-Server installiertes, vorkonfiguriertes Abbild erhältlich. Sie können Puppet Enterprise auch auf einem vorhandenen Server installieren. Mit dem Abbild allerdings ist der Start am leichtesten. Sie benötigen Informationen über den Server, um den Agent einrichten zu können.
-   Puppet Enterprise Agent ist als Erweiterung für virtuelle Computer erhältlich, die Sie beim Erstellen eines virtuellen Computers oder auf einem schon existierenden installieren können.

Wenn Sie eine Anleitung benötigen, laden Sie „Erste Schritte“ herunter, zu finden auf der Seite [Microsoft Windows und Azure][Microsoft Windows und Azure].

## Zusätzliche Ressourcen

[Neue Integration von Microsoft Azure und Visual Studio][Neue Integration von Microsoft Azure und Visual Studio]

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Anmelden bei einem mit Linux betriebenen virtuellen Computer][Anmelden bei einem mit Linux betriebenen virtuellen Computer]

[Erweiterungen verwalten][Erweiterungen verwalten]

<!--Link references-->

  [Microsoft Windows und Azure]: http://puppetlabs.com/solutions/microsoft
  [Neue Integration von Microsoft Azure und Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Anmelden bei einem mit Linux betriebenen virtuellen Computer]: ../virtual-machines-linux-how-to-log-on
  [Erweiterungen verwalten]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
