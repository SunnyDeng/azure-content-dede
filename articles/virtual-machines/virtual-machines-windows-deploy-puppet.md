<properties
	pageTitle="Über Puppet und Azure Virtual Machines | Microsoft Azure"
	description="Beschreibt die Installation und Konfiguration von Puppet auf einem virtuellen Computer in Azure"
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

#Über Puppet und Azure Virtual Machines

<p>Puppet Enterprise ist eine Automatisierungssoftware für das Erstellen, Bereitstellen und Verwalten Ihrer Infrastruktur. Sie können damit den Lebenszyklus Ihrer IT-Infrastruktur verwalten, einschließlich: Ermittlung, Bereitstellung, Konfigurationsverwaltung für Betriebssystem und Anwendungen, Orchestrierung und Berichterstellung.

Puppet ist ein Clientserversystem. Puppet Master und der Puppet Enterprise Agent können über Microsoft Azure installiert werden:

- Puppet Master ist als auf einem Ubuntu-Server installiertes, vorkonfiguriertes Abbild erhältlich. Sie können Puppet Enterprise auch auf einem vorhandenen Server installieren. Mit dem Abbild allerdings ist der Start am leichtesten. Sie benötigen Informationen über den Server, um den Agent einrichten zu können.
- Puppet Enterprise Agent ist als Erweiterung für virtuelle Computer erhältlich, die Sie beim Erstellen eines virtuellen Computers oder auf einem schon existierenden installieren können.

Wenn Sie eine Anleitung benötigen, laden Sie "Erste Schritte" von der Seite [Microsoft Windows und Azure](http://puppetlabs.com/solutions/microsoft) herunter.


##Zusätzliche Ressourcen
[New Integrations with Microsoft Azure and Visual Studio, in englischer Sprache]

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Anmelden bei einem mit Linux betriebenen virtuellen Computer]

[Verwalten von Erweiterungen]

<!--Link references-->
[New Integrations with Microsoft Azure and Visual Studio, in englischer Sprache]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: virtual-machines-log-on-windows-server.md
[Anmelden bei einem mit Linux betriebenen virtuellen Computer]: virtual-machines-linux-how-to-log-on.md
[Azure VM Extensions and Features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=July15_HO4-->