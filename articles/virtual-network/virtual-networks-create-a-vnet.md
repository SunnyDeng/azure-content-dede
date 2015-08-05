<properties
   pageTitle="Erstellen eines virtuellen Netzwerks"
   description="Führen Sie die genannten Schritte aus, um auf einfache Weise ein virtuelles Netzwerk zu erstellen."
   services="virtual-network"
   documentationCenter=""
   authors="telmos"
   manager="carolz"
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2015"
   ms.author="telmosampaio"/>

# Erstellen eines virtuellen Netzwerks

Wenn Sie ein virtuelles Netzwerk erstellen, können Ihre Dienste und VMS sicher miteinander kommunizieren, ohne über das Internet gehen zu müssen. Das Erstellen eines dedizierten, ausschließlich cloudbasierten virtuellen Netzwerks ist ein Vorgang, der relativ schnell und einfach ausgeführt werden kann. Da ein virtuelles Nur-Cloud-Netzwerk nicht für standortübergreifende Verbindungen gedacht ist, müssen Sie weder ein VPN-Gerät noch Authentifizierungszertifikate erwerben und konfigurieren.

Nachdem Sie Ihr virtuelles Netzwerk erstellt haben, können Sie ihm neue VMs und PaaS-Instanzen hinzufügen. Wenn Sie das Verwaltungsportal zum Erstellen Ihrer VMs verwenden, müssen Sie **Aus Katalog** wählen, um das virtuelle Netzwerk angeben zu können. Dies ist wichtig, da Sie dem Netzwerk nach dem Erstellen der VM nicht nachträglich eine VM hinzufügen können.

[Azure-Hinweis] Verwenden Sie **dieses Verfahren** zum Erstellen eines dedizierten, ausschließlich cloudbasierten virtuellen Netzwerks. Aufgrund der höheren Komplexität einer standortübergreifenden Konfiguration sollte dieses Verfahren nicht zum Erstellen eines virtuellen Netzwerks verwendet werden, das später mit Ihrem lokalen Netzwerk verbunden wird. Wenn Sie eine sichere standortübergreifende Verbindung zwischen Azure und Ihrem lokalen Netzwerk erstellen möchten, finden Sie weitere Informationen unter [Sichere standortübergreifende Verbindungen](https://msdn.microsoft.com/library/azure/dn133798.aspx).

## Erstellen des virtuellen Netzwerks

1. Melden Sie sich am **Verwaltungsportal** an.
2. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.
3. Geben Sie auf dem Bildschirm **Details des virtuellen Netzwerks** die folgenden Informationen ein, und klicken Sie dann auf den Vorwärtspfeil unten rechts. Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie unter [Gewusst wie: Verwalten der Eigenschaften eines Virtual Network (VNet)](../virtual-networks-settings) im Abschnitt mit den Details zum virtuellen Netzwerk.
	-  **Name**: Name des virtuellen Netzwerks. Der hier festgelegte Name wird bei der Bereitstellung Ihrer VMs und Dienste verwendet. Es empfiehlt sich daher, keinen allzu komplizierten Namen zu wählen.

	-  **Standort**: Wählen Sie in der Dropdownliste die gewünschte Region aus. Das virtuelle Netzwerk wird in dem Azure-Rechenzentrum erstellt, das sich in der angegebenen Region befindet.



4. Nehmen Sie auf der Seite **DNS-Server und VPN-Konnektivität** keine Änderungen vor. Wechseln Sie einfach durch Klicken auf den Pfeil zur nächsten Seite. Standardmäßig verwendet Azure eine grundlegende Namensauflösung für Ihr virtuelles Netzwerk. Möglicherweise sind Ihre Anforderungen für die Namensauflösung komplexer, sodass die grundlegende Namensauflösung von Azure nicht eingesetzt werden kann. In diesem Fall müssen Sie später möglicherweise einen virtuellen Computer mit DNS zu Ihrem virtuellen Netzwerk hinzufügen. Weitere Informationen zu Azure-Namensauflösung und DNS finden Sie unter [Namensauflösung](https://msdn.microsoft.com/library/azure/jj156088.aspx).
5. Auf der Seite **Virtuelle Netzwerkadressräume** geben Sie den Adressraum ein, den Sie für dieses VNet verwenden möchten. Sofern Sie keine bestimmte interne IP-Adresse für Ihre VMs benötigen oder ein spezifisches Subnetz für VMs erstellen möchten, die eine statische DIP erhalten, müssen Sie auf dieser Seite keine Änderungen vornehmen. Wenn Sie mehrere Subnetze erstellen möchten, klicken Sie auf dieser Seite auf **Subnetz hinzufügen**. Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie unter **Gewusst wie: Verwalten der Eigenschaften eines Virtual Network (VNet)** im Abschnitt mit den Details zum virtuellen Netzwerk[](../virtual-networks-settings).

	-  Weitere Informationen zu den Einstellungen auf der Detailseite finden Sie unter **Gewusst wie: Verwalten der Eigenschaften eines Virtual Network (VNet)** im Abschnitt mit den Details zum virtuellen Netzwerk[](../virtual-networks-settings).
	-  Da Sie dieses private virtuelle Netzwerk nicht über eine standortübergreifende VPN-Konfiguration mit Ihrem lokalen Netzwerk verbinden, müssen Sie diese Einstellungen nicht mit den IP-Adressbereichen für Ihr vorhandenes Netzwerk koordinieren. Wenn Sie erwägen, später eine standortübergreifende Konfiguration zu erstellen, müssen Sie die Adressräume jetzt mit den Bereichen koordinieren, die bereits am lokalen Standort vorhanden sind, um spätere Routingprobleme zu vermeiden. Das spätere Ändern der Bereiche kann sich kompliziert gestalten und führt häufig dazu, dass eine Neubereitstellung erforderlich wird.


6. Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite "Virtuelle Netzwerkadressräume", um mit der Erstellung des virtuellen Netzwerks zu beginnen. Wenn das virtuelle Netzwerk erstellt wurde, wird auf der Netzwerkseite im Verwaltungsportal unter Status der Eintrag Erstellt angezeigt.
7. Nachdem Ihr virtuelles Netzwerk erstellt wurde, können Sie es bereitstellen. Wenn Sie beispielsweise einen virtuellen Computer in Ihrem VNet bereitstellen möchten, lesen Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines/virtual-machines-create-custom.md) weiter. Wählen Sie beim Erstellen eines virtuellen Computers die Option **Aus Katalog** aus, damit Sie Ihr virtuelles Netzwerk auswählen können. Wenn Sie bereits vorhandene VMs und PaaS-Instanzen bereitgestellt haben, können diese nicht einfach in Ihr neues VNet verschoben werden. Dies liegt daran, dass die Netzwerkkonfigurationseinstellungen für diese während der Bereitstellung festgelegt werden. Sie müssen eine Neubereitstellung im neuen VNet durchführen.



## Nächste Schritte
-  Erfahren Sie mehr über [virtuelle Netzwerke](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx) in Azure. 

-  [Hinzufügen eines virtuellen Computers zu einem virtuellen Netzwerk](../virtual-machines/virtual-machines-create-custom.md)

<!---HONumber=July15_HO4-->