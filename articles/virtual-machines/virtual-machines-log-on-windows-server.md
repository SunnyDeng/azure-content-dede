<properties
	pageTitle="Anmelden an einem virtuellen Computer unter Windows Server"
	description="Erfahren Sie, wie Sie das Azure-Portal verwenden, um sich an einem virtuellen Computer unter Windows Server anzumelden."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird#

Hierfür verwenden Sie die Schaltfläche **Verbinden** im Azure-Vorschauportal zum Starten einer Remotedesktopsitzung. Zuerst stellen Sie eine Verbindung mit dem virtuellen Computer her, und dann melden Sie sich an.

Sie möchten eine Verbindung mit einem virtuellen Linux-Computer herstellen? Siehe [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-how-to-log-on.md).

## Herstellen einer Verbindung mit dem virtuellen Computer

Hier finden Sie eine exemplarische Vorgehensweise für die Schritte dieses Tutorials.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. Melden Sie sich am [Azure-Portal](http://manage.windowsazure.com) an, falls Sie dies noch nicht getan haben.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

3. Klicken Sie in der Befehlsleiste auf **Verbinden**.

	![Anmelden beim virtuellen Computer](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## Anmelden beim virtuellen Computer

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Tipps zur Problembehandlung

Folgende Dinge können Sie für eine schnelle Behebung Ihres Problems im Portal ausprobieren:

-	Wenn Sie Probleme mit der Remotedesktopverbindung haben, setzen Sie die Konfiguration zurück. Klicken Sie im Dashboard des virtuellen Computers unter **Auf einen Blick** auf **Remotekonfiguration zurücksetzen**.
-	Wenn Sie Probleme mit Ihrem Kennwort haben, setzen Sie es zurück. Klicken Sie im Dashboard des virtuellen Computers unter **Auf einen Blick** auf **Kennwort zurücksetzen**.

Wenn die Tipps nicht funktionieren oder nicht das Gesuchte sind, finden Sie entsprechende Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md). Dieser Artikel führt Sie durch die Diagnose und Behebung von häufigen Problemen.

<!---HONumber=Sept15_HO3-->