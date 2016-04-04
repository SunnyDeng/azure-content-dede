<properties
	pageTitle="Konfigurieren eines virtuellen Netzwerks für ein DevTest Lab | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein vorhandenes virtuelles Netzwerk und ein Subnetz konfigurieren und auf einem virtuellen Computer verwenden."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# Konfigurieren eines virtuellen Netzwerks für ein DevTest Lab

## Übersicht

Wie im Artikel [Hinzufügen einer VM mit Artefakten zu einem Azure DevTest Lab](devtest-lab-add-vm-with-artifacts.md) erläutert, können Sie beim Erstellen eines virtuellen Computers in einem Lab ein konfiguriertes virtuelles Netzwerk (und ein Subnetz) für diesen virtuellen Computer angeben. Ein Szenario hierfür wäre, wenn Sie von Ihren virtuellen Computern aus auf Ihre Unternehmensnetzwerkressourcen zugreifen möchten, und zwar über das virtuelle Netzwerk, das mit ExpressRoute oder Standort-zu-Standort-VPN eingerichtet wurde. In den folgenden Abschnitten wird veranschaulicht, wie Sie Ihr vorhandenes virtuelles Netzwerk den Virtual Network-Einstellungen hinzufügen, sodass es beim Erstellen Ihrer virtuellen Computer zur Auswahl steht.

## Konfigurieren eines virtuellen Netzwerks für ein DevTest Lab über das Azure-Portal
Die folgenden Schritte führen Sie durch das Hinzufügen eines vorhandenen virtuellen Netzwerks (und Subnetzes) zu einem DevTest Lab, sodass es beim Erstellen eines virtuellen Computers in dem gleichen Labor verwendet werden kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**, und tippen Sie dann in der Liste auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Das Blatt **Einstellungen** des ausgewählten Labs wird angezeigt.

1. Tippen Sie auf **Virtuelle Netzwerke**.

	![Virtuelle Netzwerke können auf dem Blatt „Einstellungen“ des Labs konfiguriert werden.](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. Auf dem Blatt **Virtuelle Netzwerke** sehen Sie eine Liste der virtuellen Netzwerke, die Sie für das aktuelle Lab konfiguriert haben, sowie das standardmäßige virtuelle Netzwerk, das für Ihr Lab erstellt wird.

1. Tippen Sie auf **+ Hinzufügen**.

	![Fügen Sie Ihrem Lab ein vorhandenes virtuelles Netzwerk hinzu.](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. Tippen Sie auf dem Blatt **Virtuelles Netzwerk** auf **[Virtuelles Netzwerk auswählen]**.

	![Wählen Sie ein vorhandenes virtuelles Netzwerk aus.](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. Wählen Sie auf dem Blatt **Virtuelles Netzwerk wählen** das gewünschte virtuelle Netzwerk aus. Das Blatt zeigt alle virtuellen Netzwerke, die sich im Abonnement in derselben Region wie das Lab befinden.

1. Nachdem Sie ein virtuelles Netzwerk ausgewählt haben und zum Blatt **Virtuelles Netzwerk** zurückgekehrt sind, sehen Sie, dass mehrere Felder aktiviert wurden.

	![Wählen Sie ein vorhandenes virtuelles Netzwerk aus.](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Geben Sie eine Beschreibung für Ihre Kombination aus virtuellem Netzwerk und Lab an.

1. Damit ein Subnetz bei der Lab-Erstellung verwendet werden kann, aktivieren Sie die Option **BEI VM-ERSTELLUNG VERWENDEN**.

1. Um die Verwendung öffentlicher IP-Adressen in einem Subnetz zu ermöglichen, aktivieren Sie die Option **ÖFFENTLICHE IP-ADRESSE ZULASSEN**.

1. Geben Sie im Feld **MAX. VMS PRO BENUTZER** die maximale Anzahl von VMs pro Benutzer für jedes Subnetz an. Wenn eine unbeschränkte Anzahl von VMs verwendet werden soll, lassen Sie dieses Feld leer.

1. Tippen Sie auf **Speichern**.

1. Jetzt ist das virtuelle Netzwerk konfiguriert und kann beim Erstellen eines neuen virtuellen Computers ausgewählt werden. Dies wird im Artikel [Hinzufügen einer VM mit Artefakten zu einem Azure DevTest Lab](devtest-lab-add-vm-with-artifacts.md) erläutert.

## Nächste Schritte

Nachdem Sie Ihrem Lab die gewünschten virtuellen Netzwerke hinzugefügt haben, besteht der nächste Schritt darin, [Ihrem DevTest Lab einen virtuellen Computer hinzuzufügen](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0323_2016-->