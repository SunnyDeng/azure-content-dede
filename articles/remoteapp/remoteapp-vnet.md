
<properties
    pageTitle="Überprüfen des Azure-VNET für die Verwendung mit Azure RemoteApp | Microsoft Azure"
    description="Hier erfahren Sie, wie Sie sich vergewissern, dass Ihr Azure-VNET mit Azure RemoteApp verwendet werden kann."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/05/2015"
    ms.author="elizapo" />



# Überprüfen des Azure-VNET für die Verwendung mit Azure RemoteApp

Bevor Sie ein Azure-VNET mit Azure RemoteApp verwenden, empfiehlt es sich, das VNET zu überprüfen. Dadurch werden Konnektivitätsprobleme vermieden.

Führen Sie die folgenden Schritte aus, um Ihr Azure-VNET zu überprüfen:

1. Erstellen Sie einen virtuellen Azure-Computer innerhalb des Subnetzes des Azure-VNET, das Sie mit Azure RemoteApp verwenden möchten.

2. Stellen Sie im Verwaltungsportal mithilfe der Option **Verbinden** eine Verbindung mit diesem virtuellen Computer her.
3. Fügen Sie den virtuellen Computer der Domäne hinzu, die Sie mit Azure RemoteApp verwenden möchten. Wenn Sie eine Hybrid-Sammlung erstellen, die mit Ihrem lokalen Netzwerk eine lokale Verbindung herstellt, verknüpfen Sie den virtuellen Computer mit Ihrer lokalen Domäne.

Anschließend kann das Azure-VNET mit RemoteApp verwendet werden.

Weitere Informationen zum End-to-End-Workflow für Hybridsammlungen finden Sie in den folgenden Artikeln:

- [How to plan your virtual network for Azure RemoteApp (auf Englisch)](remoteapp-planvpn.md)
- [Erstellen einer Hybridsammlung](remoteapp-create-hybrid-deployment.md)
- [Bereitstellen einer Azure RemoteApp-Sammlung für Azure Virtual Network (mit ExpressRoute-Unterstützung)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)

<!---HONumber=AcomDC_1210_2015-->