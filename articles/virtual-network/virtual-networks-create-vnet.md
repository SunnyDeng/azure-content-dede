<properties 
   pageTitle="Erstellen eines virtuellen Netzwerks (VNet)"
   description="Hier erfahren Sie, wie Sie ein virtuelles Netzwerk (VNet) erstellen."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Erstellen eines virtuellen Netzwerks (VNet)

Wenn Sie ein virtuelles Netzwerk (VNet) erstellen, können Ihre Dienste und VMs im VNet sicher miteinander kommunizieren, ohne über das Internet gehen zu müssen. Das Erstellen eines Azure VNet ist ein relativ schneller und einfacher Prozess, wenn das VNet nicht mit anderen VNets oder Ihrem lokalen Netzwerk verbunden werden soll, da Sie in diesem Fall weder ein VPN-Gerät abrufen und konfigurieren noch die IP-Adressen für andere VNets oder das lokale Netzwerk koordinieren müssen.

>[AZURE.WARNING]Verwenden Sie dieses Verfahren nicht zum Erstellen eines VNet, das später mit anderen VNets oder Ihrem lokalen Netzwerk verbunden werden soll. Informationen zum Erstellen einer sicheren standortübergreifenden Verbindung oder Hybridverbindung finden Sie unter [Informationen zur sicheren standortübergreifenden Konnektivität für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/dn133798.aspx). Informationen Erstellen eines mit einem anderen VNet verbundenen VNet finden Sie unter [Konfigurieren einer Verbindung zwischen VNets](https://msdn.microsoft.com/library/azure/dn690122.aspx).

## Konfigurieren des VNet

1. Melden Sie sich am **Azure-Verwaltungsportal** an.

1. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

1. Geben Sie auf der Seite **Details zum virtuellen Netzwerk** die folgenden Informationen ein:

	![Details zum virtuellen Netzwerk](./media/virtual-networks-create-vnet/IC736054.png)

	- **Name**: Geben Sie einen Namen für Ihr VNet ein. Wir haben z. B. den Namen *EastUSVNet* erstellt. Sie können einen beliebigen Namen angeben. Sie verwenden den hier festgelegten VNet-Namen bei der Bereitstellung Ihrer VMs und Dienste. Er sollte daher nicht zu kompliziert sein.

	- **Ort **: Wählen Sie den Ort (die Region) in der Dropdownliste aus. Der Ort steht in direkter Beziehung zu dem physischen Standort (Region), an dem sich Ihre Ressourcen (VMs) befinden sollen, wenn Sie sie in diesem VNet bereitstellen. Wenn sich Ihre VMs z. B. physisch in *USA, Osten* befinden sollen, wählen Sie diese Region aus. Sie können die Ihrem VNet zugeordnete Region nach dem Erstellen nicht mehr ändern.

1. Nehmen Sie auf der Seite **DNS-Server und VPN-Konnektivität** keine Änderungen vor. Wechseln Sie einfach durch Klicken auf den Pfeil zur nächsten Seite. Standardmäßig verwendet Azure eine grundlegende Namensauflösung für Ihr VNet. Möglicherweise sind Ihre Anforderungen für die Namensauflösung komplexer, sodass die grundlegende Namensauflösung von Azure nicht eingesetzt werden kann. In diesem Fall können Sie Ihrem VNet später einen virtuellen Computer mit DNS hinzufügen. Weitere Informationen zur Azure-Namensauflösung und DNS finden Sie unter [Namensauflösung (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx).

1. Auf der Seite **Virtuelle Netzwerkadressräume** geben Sie den Adressraum ein, den Sie für dieses VNet verwenden möchten. Sofern Sie keine bestimmte interne IP-Adresse für Ihre VMs benötigen oder ein spezifisches Subnetz für VMs erstellen möchten, die eine statische DIP erhalten, müssen Sie auf dieser Seite keine Änderungen vornehmen. Wenn Sie mehrere Subnetze erstellen möchten, klicken Sie auf dieser Seite auf **Subnetz hinzufügen**.

	Weitere Informationen:

	- Die Bereiche auf dieser Seite enthalten die dynamischen internen IP-Adressen (DIPs), die Ihre VMs erhalten, wenn Sie sie in diesem VNet bereitstellen. Diese IP-Adressen dienen nur zur Kommunikation innerhalb des VNet. Es handelt sich dabei nicht um IP-Adressen für Internetendpunkte.

	- Da Sie dieses private VNet nicht über eine standortübergreifende VPN-Konfiguration mit Ihrem lokalen Netzwerk verbinden, müssen Sie diese Einstellungen nicht mit den IP-Adressbereichen für Ihr vorhandenes lokales Netzwerk koordinieren. Wenn Sie erwägen, später eine standortübergreifende Konfiguration zu erstellen, müssen Sie die Adressräume jetzt mit den Bereichen koordinieren, die bereits am lokalen Standort vorhanden sind, um spätere Routingprobleme zu vermeiden. Eine spätere Änderung der Bereiche kann kompliziert sein, insbesondere dann, wenn Sie überlappende Adressbereiche haben.

	![Adressraum](./media/virtual-networks-create-vnet/IC716778.png)

1. Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite **Virtuelle Netzwerkadressräume**, um das VNet zu erstellen. Wenn das VNet erstellt wurde, wird auf der Seite **Netzwerke** im Verwaltungsportal unter **Status** der Eintrag **Erstellt** angezeigt.

1. Sobald Ihr VNet erstellt wurde, können Sie VMs und PaaS-Instanzen im VNet erstellen. Wählen Sie beim Erstellen einer neuen VM unbedingt die Option **Aus Katalog** aus, damit Sie Ihr VNet auswählen können. Wenn Sie bereits vorhandene VMs und PaaS-Instanzen bereitgestellt haben, können diese nicht einfach in Ihr neues VNet verschoben werden. Dies liegt daran, dass die erforderlichen Netzwerkkonfigurationseinstellungen für diese VMs und PaaS-Instanzen während der Bereitstellung hinzugefügt werden.

## Hinzufügen von virtuellen Computern zu Ihrem VNet

Nachdem Sie Ihr VNet erstellt haben, können Sie ihm neue VMs hinzufügen. Es ist wichtig, dass Sie zuerst das VNet erstellen und anschließend die VM bereitstellen. Eine VM kann nach ihrer Bereitstellung nicht in ein VNet verschoben werden, ohne sie erneut bereitzustellen. Wenn Sie das Verwaltungsportal zum Erstellen der VMs verwenden, ist die Benutzeroberfläche zum Bereitstellen Ihrer VM in einem VNet nur über **Neu/Server/Virtueller Computer/Aus Katalog** verfügbar. Wenn Sie den Assistenten zum Erstellen Ihrer VM ausführen, sehen Sie auf der Seite **Konfiguration des virtuellen Computers** die Optionen **Region/Affinitätsgruppe/Virtuelles Netzwerk**. Wählen Sie in der Dropdownliste das bereits erstellte VNet aus. Weitere Informationen zum Erstellen von virtuellen Computern finden Sie unter [Azure Virtual Machines](../virtual-machines).

## Nächste Schritte

[Verwalten der Eigenschaften eines virtuellen Netzwerks (VNet)](../virtual-networks-settings)

[ Verwalten von DNS-Servern, die von einem virtuellen Netzwerk (VNet) verwendet werden](../virtual-networks-manage-dns-in-vnet)

[Verwenden von öffentlichen IP-Adressen in einem virtuellen Netzwerk](../virtual-networks-public-ip-within-vnet)

[Löschen eines virtuellen Netzwerks (VNet)](../virtual-networks-delete-vnet)
 

<!---HONumber=July15_HO4-->