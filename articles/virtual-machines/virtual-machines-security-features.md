<properties 
	pageTitle="Sicherheitsangebote für Azure Virtual Machines" 
	description="Schneller Überblick über die wichtigsten Sicherheitsfeatures für virtuelle Azure-Computer zu Links zu Details" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Sicherheitsangebote für Azure Virtual Machines

<p>Die Sicherheitsaufgaben und die Konfiguration für die virtuellen Azure-Computer entsprechen weitestgehend denen, die für andere virtuelle oder physische Computer verwendet werden. Dazu zählen derartige Grundlagen wie die Verwendung sicherer Kennwörter, die Nichtverwendung bekannter Benutzernamen und das Herstellen einer Richtlinie und eines Plans für die Aktualisierung des Betriebssystems und von Anwendungen. Zusätzlich empfehlen wir die folgenden Verfahren, und wir stellen Features bereit, die Sie zum Implementieren jener verwenden können:

- Ausführen von Virenschutzsoftware/Antischadsoftware

- Verwenden von Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) auf Endpunkten
 
##Ausführen von Virenschutzsoftware/Antischadsoftware

Azure bietet verschiedene Optionen für Virenschutz-/Antischadsoftwarelösungen. Ihre Verwaltung ist jedoch Ihnen überlassen. Beispielsweise müssen Sie sich entscheiden, wann Sie Überprüfungen vornehmen und Updates installieren. Sie können beim Erstellen des virtuellen Computers oder später eine Virenschutzsoftware-Unterstützung hinzufügen. Zurzeit werden drei Lösungen als Sicherheitserweiterungen angeboten, die sowohl auf neuen als auch auf vorhandenen virtuellen Computern installiert werden können:

- [Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Azure-Computer](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Deploying Antimalware Solutions on Azure Virtual Machines](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/) (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern, in englischer Sprache)
 

##Verwenden von Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) auf Endpunkten virtueller Computer

Mit Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) können Sie den eingehenden Datenverkehr zu einem Endpunkt eines virtuellen Computers wahlweise zulassen oder verweigern. Diese Paketfilterungsfunktion bietet eine zusätzliche Schutzebene. Details in Bezug auf die Funktionsweise sowie Links zu Anweisungen finden Sie unter [Informationen zu Netzwerk-Zugriffssteuerungslisten (ACLs)](http://go.microsoft.com/fwlink/?LinkId=506655).

##Zusätzliche Ressourcen
[Ressourcen](http://azure.microsoft.com/support/trust-center/resources/) im Microsoft Azure-Vertrauenscenter


 

<!---HONumber=August15_HO6-->