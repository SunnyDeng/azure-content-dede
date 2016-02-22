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

# Verwalten von NSGs mithilfe des Vorschauportals

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [NSGs im klassischen Bereitstellungsmodell zu erstellen](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Bereitstellen der ARM-Vorlage per Klick

Zurzeit ist es nicht möglich, NSGs aus der Vorschau zu erstellen. Sie können aber vorhandene NSGs verwalten. Damit Sie NSGs verwalten können, erstellen Sie mit der im öffentlichen Repository bereitgestellten Beispielvorlage die Ressourcen, die im Szenario oben beschrieben wurden. Stellen Sie [diese Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG) bereit, klicken Sie auf **In Azure bereitstellen**, ersetzen Sie ggf. die Werte der Standardparameter, und führen Sie die Schritte im Portal aus.

## Erstellen von Regeln in einer vorhandenen NSG

Führen Sie die Schritte unten aus, um im Vorschauportal Regeln in einer vorhandenen NSG zu erstellen.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Durchsuchen** > **Netzwerksicherheitsgruppen**.

![Vorschauportal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. Klicken Sie in der Liste der NSGs auf **NSG-FrontEnd** > **Eingehende Sicherheitsregeln**

![Vorschauportal – NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Klicken Sie in der Liste **Eingehende Sicherheitsregeln** auf **Hinzufügen**.

![Vorschauportal – Hinzufügen einer Regel](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Erstellen Sie im Blatt **Eingehende Sicherheitsregel hinzufügen** eine Regel mit dem Namen *web-rule* mit der Priorität *200*, und lassen Sie den Zugriff über *TCP* und Port *80* auf alle virtuellen Computer von allen Quellen zu. Klicken Sie dann auf **OK**. Die meisten dieser Einstellungen sind bereits die Standardwerte.

![Vorschauportal – Regeleinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Nach einigen Sekunden wird die neue Regel in der NSG angezeigt.

![Vorschauportal – Neue Regel](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=AcomDC_0211_2016-->