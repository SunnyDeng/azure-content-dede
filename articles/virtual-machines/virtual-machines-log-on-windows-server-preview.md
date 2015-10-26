<properties
	pageTitle="Anmelden bei einem virtuellen Windows Server-Computer | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie sich unter Verwendung des Azure-Vorschauportals und des Ressourcen-Manager-Bereitstellungsmodells bei einem virtuellen Windows Server-Computer anmelden."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).

Hierfür verwenden Sie die Schaltfläche **Verbinden** im Azure-Vorschauportal zum Starten einer Remotedesktopsitzung. Zuerst stellen Sie eine Verbindung mit dem virtuellen Computer her, und dann melden Sie sich an.

## Herstellen einer Verbindung mit dem virtuellen Computer

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an, falls noch nicht geschehen.

2.	Klicken Sie im Menü "Hub" auf **Durchsuchen**.

3.	Klicken Sie auf das Blatt "Suchen", führen Sie einen Bildlauf nach unten durch, und klicken Sie auf **Virtuelle Computer**.

	![Suchen nach virtuellen Computern](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.

5. Klicken Sie auf dem Blatt für den virtuellen Computer auf **Verbinden**.

	![Herstellen einer Verbindung mit dem virtuellen Computer](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Anmelden beim virtuellen Computer

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Problembehandlung

Wenn die Tipps zum Anmelden nicht hilfreich oder nicht das Gesuchte sind, finden Sie entsprechende Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md). Dieser Artikel führt Sie durch die Diagnose und Behebung von häufigen Problemen.

<!---HONumber=Oct15_HO3-->