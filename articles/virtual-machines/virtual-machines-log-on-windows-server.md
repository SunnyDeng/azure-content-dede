<properties 
	pageTitle="Anmelden an einem virtuellen Computer unter Windows Server" 
	description="Erfahren Sie, wie Sie das Azure-Verwaltungsportal verwenden, um sich an einem virtuellen Computer unter Windows Server anzumelden." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird#

Hierfür verwenden Sie die Schaltfläche **Verbinden** im Azure-Portal zum Starten einer Remotedesktopsitzung. (Weitere Informationen zu virtuellen Linux-Computern finden Sie unter [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-how-to-log-on.md).)

## Anmelden

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Tipps zur Problembehandlung

Folgende Dinge können Sie für eine schnelle Behebung Ihres Problems ausprobieren:

Wenn Sie Probleme mit der Remotedesktopverbindung haben, setzen Sie die Konfiguration über das Portal zurück. Klicken Sie im Dashboard des virtuellen Computers unter **Auf einen Blick** auf **Remotekonfiguration zurücksetzen**.

Wenn Sie Probleme mit Ihrem Kennwort haben, setzen Sie es über das Portal zurück. Klicken Sie im Dashboard des virtuellen Computers unter **Auf einen Blick** auf **Kennwort zurücksetzen**.

Wenn diese Tipps nicht helfen, müssen Sie eine ausführlichere Problembehandlung durchführen. Anweisungen finden Sie unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md).
 
 

<!---HONumber=58_postMigration-->