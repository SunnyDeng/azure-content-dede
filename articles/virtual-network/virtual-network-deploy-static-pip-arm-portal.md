<properties 
   pageTitle="Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse mit dem Azure-Portal im Ressourcen-Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie VMs mit einer statischen öffentlichen IP-Adresse über das Azure-Portal im Ressourcen-Manager bereitstellen."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse mithilfe des Azure-Portals

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Erstellen einer VM mit einer statischen öffentlichen IP-Adresse 

Führen Sie die unten angegebenen Schritte aus, um eine VM mit einer statischen öffentlichen IP-Adresse im Azure-Portal zu erstellen.

1. Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie oben links im Portal auf **Neu**>>**Compute**>**Windows Server 2012 R2 Datacenter**.
3. Wählen Sie in der Liste **Bereitstellungsmodell auswählen** die Option **Ressourcen-Manager**, und klicken Sie auf **Erstellen**.
4. Geben Sie auf dem Blatt **Grundlagen** die VM-Informationen wie unten gezeigt ein, und klicken Sie dann auf **OK**.

	![Azure-Portal – Grundlagen](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Klicken Sie auf dem Blatt **Größe auswählen** wie unten gezeigt auf **A1 Standard**, und klicken Sie dann auf **Auswählen**.

	![Azure-Portal – Größe auswählen](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Klicken Sie auf dem Blatt **Einstellungen** auf **Öffentliche IP-Adresse** und dann auf dem Blatt **Öffentliche IP-Adresse erstellen** unter **Zuweisung** wie unten gezeigt auf **Statisch**. Klicken Sie anschließend auf **OK**.

	![Azure-Portal – Öffentliche IP-Adresse erstellen](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Klicken Sie auf dem Blatt **Einstellungen** auf **OK**.
8. Prüfen Sie die Informationen auf dem Blatt **Zusammenfassung** (unten gezeigt), und klicken Sie anschließend auf **OK**.

	![Azure-Portal – Öffentliche IP-Adresse erstellen](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Beachten Sie die neue Kachel in Ihrem Dashboard.

	![Azure-Portal – Öffentliche IP-Adresse erstellen](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Nach der Erstellung der VM wird das Blatt **Einstellungen** wie unten dargestellt angezeigt.

	![Azure-Portal – Öffentliche IP-Adresse erstellen](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0211_2016-->