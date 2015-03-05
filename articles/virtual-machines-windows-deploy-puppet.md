<properties 
	pageTitle="Über Puppet und Azure Virtual Machines" 
	description="Beschreibt die Installation und Konfiguration von Puppet auf einem virtuellen Computer in Azure." 
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
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Über Puppet und Azure Virtual Machines

<p>Puppet Enterprise ist eine Automatisierungssoftware für das Erstellen, Bereitstellen und Verwalten Ihrer Infrastruktur. Sie können sie zum Verwalten Ihres IT-Infrastruktur-Lebenszyklus, einschließlich Ermittlung, Bereitstellung, Betriebssystem- und Anwendungskonfiguration, Orchestrierung und Berichterstellung verwenden.   

Puppet ist ein Clientserversystem. Puppet Master und der Puppet-Agent für Unternehmen stehen für die Installation über Azure zur Verfügung:

- Puppet Master ist als ein vorkonfiguriertes Image verfügbar, das auf einem Ubuntu-Server installiert ist. Sie können Puppet Enterprise auch auf einem vorhandenen Server installieren. Das Image ist jedoch die einfachste Möglichkeit für den Anfang. Sie benötigen Informationen über den Server, um den Agent einzurichten. 
- Der Puppet-Agent für Unternehmen steht als eine Erweiterung für einen virtuellen Computer zur Verfügung, den Sie installieren können, wenn Sie einen virtuellen Computer erstellen oder eine Installation auf einem vorhandenen virtuellen Computer vornehmen.

Laden Sie den "Getting Started Guide" (Leitfaden für die ersten Schritte) auf der Seite [Microsoft Windows and Azure](http://puppetlabs.com/solutions/microsoft) (Microsoft Windows und Azure, in englischer Sprache) herunter.  


##Zusätzliche Ressourcen
[New Integrations with Microsoft Azure and Visual Studio (Neue Integrationen in Microsoft Azure und Visual Studio, in englischer Sprache)]

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Anmelden bei virtuellen Computer unter Linux]

[Verwalten von Erweiterungen]

<!--Link references-->
[New Integrations with Microsoft Azure and Visual Studio (Neue Integrationen in Microsoft Azure und Visual Studio, in englischer Sprache)]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
[Anmelden bei virtuellen Computer unter Linux]: ../virtual-machines-linux-how-to-log-on
[Azure-VM-Erweiterungen und Features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=45--> 
