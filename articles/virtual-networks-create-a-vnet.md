<properties 
   pageTitle="Erstellen eines virtuellen Netzwerks" 
   description="Führen Sie die genannten Schritte aus, um auf einfache Weise ein virtuelles Netzwerk zu erstellen." 
   services="virtual-network" 
   documentationCenter="" 
   authors="cherylmc" 
   manager="adinah" 
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/20/2015"
   ms.author="cherylmc"/>

# Erstellen eines virtuellen Netzwerks 



Wenn Sie ein virtuelles Netzwerk erstellen, können Ihre Dienste und VMS sicher miteinander kommunizieren, ohne über das Internet gehen zu müssen. Das Erstellen eines dedizierten, ausschließlich cloudbasierten virtuellen Netzwerks ist ein Vorgang, der relativ schnell und einfach ausgeführt werden kann. Da ein virtuelles Nur-Cloud-Netzwerk nicht für standortübergreifende Verbindungen gedacht ist, müssen Sie weder ein VPN-Gerät noch Authentifizierungszertifikate erwerben und konfigurieren. 

Nachdem Sie Ihr virtuelles Netzwerk erstellt haben, können Sie ihm neue VMs und PaaS-Instanzen hinzufügen. Wenn Sie das Verwaltungsportal zum erstellen Ihrer VMs verwenden, müssen Sie **Aus Galerie** wählen, um das virtuelle Netzwerk angeben zu können. Dies ist wichtig, da Sie dem Netzwerk nach dem Erstellen der VM nicht nachträglich eine VM hinzufügen können.

[Azure Note] **Verwenden Sie dieses Verfahren zum Erstellen eines dedizierten virtuellen Nur-Cloud-Netzwerks.** Aufgrund der höheren Komplexität einer standortübergreifenden Konfiguration sollte dieses Verfahren nicht zum Erstellen eines virtuellen Netzwerks verwendet werden, das später mit Ihrem lokalen Netzwerk verbunden wird. Wenn Sie eine sichere standortübergreifende Verbindung zwischen Azure und Ihrem lokalen Netzwerk erstellen möchten, finden Sie weitere Informationen unter [Sichere standortübergreifende Verbindungen](https://msdn.microsoft.com/library/azure/dn133798.aspx).

## <a name="CreateyourVNet">Erstellen des virtuellen Netzwerks</a>

1. Melden Sie sich beim **Verwaltungsportal** an.
2. Klicken Sie in der unteren linken Ecke des Bildschirms auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und anschließend auf **Virtuelles Netzwerk**. Klicken Sie auf **Benutzerdefiniert erstellen**, um den Konfigurations-Assistenten zu starten.
3. Geben Sie auf der Seite **Details des virtuellen Netzwerks** die folgenden Informationen ein, und klicken Sie in der unteren rechten Ecke auf den Weiter-Pfeil. Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie im Abschnitt **Details des virtuellen Netzwerks** unter [Informationen zu virtuellen Netzwerkeinstellungen im Verwaltungsportal](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  **Name:** Benennen Sie Ihr virtuelles Netzwerk. Sie verwenden den hier festgelegten Namen bei der Bereitstellung Ihrer VMs und Dienste, deshalb sollte der Name nicht zu kompliziert sein.

	-  **Speicherort:** Wählen Sie in der Dropdownliste die gewünschte Region aus. Das virtuelle Netzwerk wird in dem Azure-Datencenter erstellt, das sich in der angegebenen Region befindet.



4. Nehmen Sie auf der Seite **DNS-Server und VPN-Konnektivität** keine Änderungen vor. Wechseln Sie einfach durch Klicken auf den Pfeil zur nächsten Seite. Standardmäßig verwendet Azure eine grundlegende Namensauflösung für Ihr virtuelles Netzwerk. Möglicherweise sind Ihre Anforderungen für die Namensauflösung komplexer, sodass die grundlegende Namensauflösung von Azure nicht eingesetzt werden kann. In diesem Fall müssen Sie später möglicherweise einen virtuellen Computer mit DNS zu Ihrem virtuellen Netzwerk hinzufügen. Weitere Informationen zur Azure-Namensauflösung und DNS finden Sie unter [Namensauflösung](https://msdn.microsoft.com/library/azure/jj156088.aspx). 
5. Auf der Seite **Virtuelle Netzwerkadressräume** geben Sie den Adressraum ein, den Sie für dieses virtuelle Netzwerk (VNet) verwenden möchten. Sofern Sie keine bestimmte interne IP-Adresse für Ihre VMs benötigen oder ein spezifisches Subnetz für VMs erstellen möchten, die eine statische DIP erhalten, müssen Sie auf dieser Seite keine Änderungen vornehmen. Wenn Sie mehrere Subnetze erstellen möchten, klicken Sie auf dieser Seite auf **Subnetz hinzufügen**. Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie im Abschnitt **Details des virtuellen Netzwerks** unter [Informationen zu virtuellen Netzwerkeinstellungen im Verwaltungsportal](https://msdn.microsoft.com/library/azure/jj156074.aspx).

	-  Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie im Abschnitt **Details des virtuellen Netzwerks** unter [Informationen zu virtuellen Netzwerkeinstellungen im Verwaltungsportal](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  Da Sie dieses private virtuelle Netzwerk nicht über eine standortübergreifende VPN-Konfiguration mit Ihrem lokalen Netzwerk verbinden, müssen Sie diese Einstellungen nicht mit den IP-Adressbereichen für Ihr vorhandenes Netzwerk koordinieren. Wenn Sie erwägen, später eine standortübergreifende Konfiguration zu erstellen, müssen Sie die Adressräume jetzt mit den Bereichen koordinieren, die bereits am lokalen Standort vorhanden sind, um spätere Routingprobleme zu vermeiden. Das spätere Ändern der Bereiche kann sich kompliziert gestalten und führt häufig dazu, dass eine Neubereitstellung erforderlich wird.


6. Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite "Virtuelle Netzwerkadressräume", um mit der Erstellung des virtuellen Netzwerks zu beginnen. Nachdem das virtuelle Netzwerk erstellt wurde, wird im Verwaltungsportal auf der Netzwerkseite unterhalb von "Status" der Wert "Erstellt" angezeigt.
7. Nachdem Ihr virtuelles Netzwerk erstellt wurde, können Sie es bereitstellen. Wenn Sie beispielsweise eine VM in Ihrem VNet bereitstellen möchten, finden Sie weitere Informationen unter [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/). Wählen Sie beim Erstellen einer VM die Option **Aus Galerie** aus, damit Sie Ihr virtuelles Netzwerk auswählen können. Wenn Sie bereits vorhandene VMs und PaaS-Instanzen bereitgestellt haben, können diese nicht einfach in Ihr neues VNet verschoben werden. Dies liegt daran, dass die Netzwerkkonfigurationseinstellungen für diese während der Bereitstellung festgelegt werden. Sie müssen eine Neubereitstellung im neuen VNet durchführen.



## Nächste Schritte
-  [Virtuelle Netzwerke - technische Übersicht](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

 
-  [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [FAQs zu virtuellen Netzwerken](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](http://azure.microsoft.com/documentation/articles/virtual-networks-using-network-configuration-file/)

-  [Überblick über die Azure-Namensauflösung](http://go.microsoft.com/fwlink/?LinkId=248097)
 



<!--HONumber=47-->
