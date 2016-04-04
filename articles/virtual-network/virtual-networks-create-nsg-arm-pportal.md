<properties 
   pageTitle="Gewusst wie: Erstellen von NSGs im ARM-Modus über das Azure-Portal | Microsoft Azure"
   description="Erfahren Sie, wie Sie NSGs im ARM-Modus über das Azure-Portal erstellen und bereitstellen."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
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

# Verwalten von NSGs mithilfe des Azure-Portals

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [NSGs im klassischen Bereitstellungsmodell zu erstellen](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Die folgenden Beispielbefehle für PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die Befehle so ausführen möchten, wie sie in diesem Dokument angezeigt werden, erstellen Sie zunächst die Testumgebung durch Bereitstellen [dieser Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd). Klicken Sie auf **In Azure bereitstellen**, ersetzen Sie bei Bedarf die Standardparameterwerte, und befolgen Sie dann die Anweisungen im Portal. Die nachstehenden Schritte verwenden **RG-NSG** als Namen der Ressourcengruppe, für die die Vorlagen bereitgestellt wurden.

## Erstellen der NSG-FrontEnd-NSG

Führen Sie die nachstehenden Schritte aus, um die **NSG-FrontEnd**-NSG wie im obigen Szenario gezeigt zu erstellen.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Durchsuchen >** > **Netzwerksicherheitsgruppen**.

	![Azure-Portal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. Klicken Sie im Blatt **Netzwerksicherheitsgruppen** auf **Hinzufügen**.

	![Azure-Portal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. Erstellen Sie im Blatt **Netzwerksicherheitsgruppe erstellen** eine NSG namens *NSG-FrontEnd* in der Ressourcengruppe *RG-NSG*, und klicken Sie dann auf **Erstellen**.

	![Azure-Portal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## Erstellen von Regeln in einer vorhandenen NSG

Führen Sie die nachstehenden Schritte aus, um im Azure-Portal Regeln in einer vorhandenen NSG zu erstellen.

2. Klicken Sie auf **Durchsuchen >** > **Netzwerksicherheitsgruppen**.

3. Klicken Sie in der Liste der NSGs auf **NSG-FrontEnd** > **Eingehende Sicherheitsregeln**

	![Azure-Portal – NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Klicken Sie in der Liste **Eingehende Sicherheitsregeln** auf **Hinzufügen**.

	![Azure-Portal – Hinzufügen einer Regel](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Erstellen Sie im Blatt **Eingehende Sicherheitsregel hinzufügen** eine Regel mit dem Namen *web-rule* mit der Priorität *200*, und lassen Sie den Zugriff über *TCP* und Port *80* auf alle virtuellen Computer von allen Quellen zu. Klicken Sie dann auf **OK**. Beachten Sie, dass die meisten dieser Einstellungen bereits Standardwerte sind.

	![Azure-Portal – Regeleinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Nach einigen Sekunden wird die neue Regel in der NSG angezeigt.

	![Azure-Portal – Neue Regel](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Wiederholen Sie die Schritte bis 6 zum Erstellen einer eingehenden Regel namens *RDP-Regel* mit einer Priorität von *250*, und erlauben Sie damit den Zugriff über *TCP* auf Port *3389* auf einen beliebigen virtuellen Computer von einer beliebigen Quelle aus.

## Zuordnen der NSG zum FrontEnd-Subnetz

1. Klicken Sie auf **Durchsuchen >** > **Ressourcengruppen** > **RG-NSG**.
2. Klicken Sie im Blatt **RG NSG** auf **...** > **TestVNet**.

	![Azure-Portal – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. Klicken Sie im Blatt **Einstellungen** auf **Subnetze** > **FrontEnd** > **Netzwerksicherheitsgruppe** > **NSG-FrontEnd**.

	![Azure-Portal – Subnetzeinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. Klicken Sie im Blatt **FrontEnd** auf **Speichern**.

	![Azure-Portal – Subnetzeinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## Erstellen der NSG-BackEnd-NSG

Um die **NSG-BackEnd**-NSG zu erstellen, und sie dem **BackEnd**-Subnetz zuzuordnen, führen Sie die folgenden Schritte aus.

1. Wiederholen Sie die Schritte in [Erstellen der NSG-FrontEnd-NSG](#Create-the-NSG-FrontEnd-NSG) zum Erstellen einer NSG namens *NSG-BackEnd*.
2. Wiederholen Sie die Schritte in [Erstellen von Regeln in einer vorhandenen NSG](#Create-rules-in-an-existing-NSG) zum Erstellen der **eingehenden** Regeln in der folgenden Tabelle.

	|Eingehende Regel|Ausgehende Regel|
	|---|---|
	|![Azure-Portal – Eingehende Regel](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Azure-Portal – Ausgehende Regel](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Wiederholen Sie die Schritte in [Zuordnen der NSG zum FrontEnd-Subnetz](#Associate-the-NSG-to-the-FrontEnd-subnet), um die **NSG-BackEnd**-NSG dem **BackEnd**-Subnetz zuzuordnen.

## Nächste Schritte

- Lernen Sie, wie Sie [vorhandene NSGs verwalten](virtual-network-manage-nsg-arm-portal.md).
- [Aktivieren der Protokollierung](virtual-network-nsg-manage-log.md) für NSGs

<!---HONumber=AcomDC_0323_2016-->