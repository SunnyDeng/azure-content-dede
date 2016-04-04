<properties 
   pageTitle="Verwalten der NSGs mithilfe des Vorschauportals im Resource Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie vorhandene NSGs mithilfe des Vorschauportals im Resource Manager verwalten"
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
   ms.date="03/14/2016"
   ms.author="telmos" />

# Verwalten von NSGs mithilfe des Vorschauportals

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## Abrufen von Informationen

Sie können Ihre vorhandenen NSGs sehen, Regeln für eine vorhandene NSG abrufen und herausfinden, welchen Ressourcen eine NSG zugeordnet ist.

### Anzeigen vorhandener NSGs
Um alle vorhandenen NSGs in einem Abonnement anzuzeigen, führen Sie die folgenden Schritte aus.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Durchsuchen** > **Netzwerksicherheitsgruppen**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Überprüfen Sie die Liste der Netzwerksicherheitsgruppen auf dem Blatt **Netzwerksicherheitsgruppen**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Zum Anzeigen der Liste der Netzwerksicherheitsgruppen in der Ressourcengruppe **RG-NSG** führen Sie die folgenden Schritte aus.

1. Klicken Sie auf **Ressourcengruppen >** > **RG-NSG** > **...**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Suchen Sie in der Liste der Ressourcen nach Elementen, die das NSG-Symbol anzeigen, wie auf dem Blatt **Ressourcen** unten gezeigt.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
		 
### Auflisten aller Regeln für eine NSG

Um die Regeln für eine NSG namens **NSG-FrontEnd** anzuzeigen, führen Sie die folgenden Schritte aus.

1. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** oder oben gezeigten Blatt **Ressourcen** auf **NSG-FrontEnd**.
2. Klicken Sie auf der Registerkarte **Einstellungen** auf **Eingangssicherheitsregeln**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. Das Blatt **Eingangssicherheitsregeln** wird wie unten dargestellt angezeigt.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Klicken Sie auf der Registerkarte **Einstellungen** auf **Ausgangssicherheitsregeln**, um diese anzuzeigen.

>[AZURE.NOTE] Um Standardregeln anzuzeigen, klicken Sie auf das Symbol **Standardregeln** am oberen Rand des Blatts, auf dem die Regeln angezeigt werden.

### Anzeigen von NSG-Zuordnungen

Um anzuzeigen, welchen Ressourcen die NSG **NSG-FrontEnd** zugeordnet ist, führen Sie die folgenden Schritte aus.

1. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** oder oben gezeigten Blatt **Ressourcen** auf **NSG-FrontEnd**.
2. Klicken Sie auf der Registerkarte **Einstellungen** auf **Subnetze**, um anzuzeigen, welche Subnetze der NSG zugeordnet sind.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Klicken Sie auf der Registerkarte **Einstellungen** auf **Netzwerkschnittstellen**, um anzuzeigen, welche NICs der NSG zugeordnet sind.

## Verwalten von Regeln

Sie können einer vorhandenen NSG Regeln hinzufügen, vorhandene Regeln bearbeiten und Regeln entfernen.

### Hinzufügen einer Regel

Um eine Regel hinzuzufügen, die **eingehenden** Verkehr an Port **443** von jedem Computer aus an die **NSG-FrontEnd**-NSG zulässt, führen Sie die folgenden Schritte aus.

1. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** oder oben gezeigten Blatt **Ressourcen** auf **NSG-FrontEnd**.
2. Klicken Sie auf der Registerkarte **Einstellungen** auf **Eingangssicherheitsregeln**.
3. Klicken Sie auf dem Blatt **Eingangssicherheitsregeln** auf **Hinzufügen**. Tragen Sie dann auf dem Blatt **Eingangssicherheitsregel hinzufügen** die Werte wie unten dargestellt ein, und klicken Sie dann auf **OK**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Nach wenigen Sekunden sehen Sie, dass die neue Regel auf dem Blatt **Eingangssicherheitsregel** angezeigt wird.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### Ändern einer Regel

Um die oben erstellte Regel zu ändern, sodass eingehender Verkehr nur aus dem **Internet** zugelassen wird, führen Sie die folgenden Schritte aus.

1. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** oder oben gezeigten Blatt **Ressourcen** auf **NSG-FrontEnd**.
2. Klicken Sie auf der Registerkarte **Einstellungen** auf die oben erstellte Regel.
3. Ändern Sie auf dem Blatt **allow-https** die Eigenschaft **Quelle** wie unten dargestellt, und klicken Sie dann auf **Speichern**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### Löschen einer Regel

Um die oben erstellte Regel zu löschen, führen Sie die folgenden Schritte aus.

1. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** oder oben gezeigten Blatt **Ressourcen** auf **NSG-FrontEnd**.
2. Klicken Sie auf der Registerkarte **Einstellungen** auf die oben erstellte Regel.
3. Klicken Sie auf dem Blatt **allow-https**, auf **Löschen**, und klicken Sie dann auf **Ja**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## Verwalten von Zuordnungen

Sie können eine NSG Subnetzen und NICs zuordnen. Sie können auch die Zuordnung einer NSG zu jeder beliebigen Ressource, der sie zugeordnet ist, aufheben.

### Zuordnen einer NSG zu einer NIC

Um die **NSG-FrontEnd**-NSG der **TestNICWeb1**-NIC zuzuordnen, führen Sie die folgenden Schritte aus.

1. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** oder oben gezeigten Blatt **Ressourcen** auf **NSG-FrontEnd**.
2. Klicken Sie auf der Registerkarte **Einstellungen** auf **Netzwerkschnittstellen** > **Zuordnen** > **TestNICWeb1**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### Aufheben der Zuordnung einer NSG zu einer NIC

Um die Zuordnung der **NSG-FrontEnd**-NSG zur **TestNICWeb1**-NIC aufzuheben, führen Sie die folgenden Schritte aus.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen** > **RG-NSG** > **...** > **TestNICWeb1**.
2. Klicken Sie auf dem Blatt **TestNICWeb1** auf **Sicherheit ändern...** > **Keine**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Auf diesem Blatt können Sie auch die NIC jeder beliebigen vorhandenen NSG zuordnen.

### Aufheben der Zuordnung einer NSG zu einem Subnetz

Um die Zuordnung der **NSG-FrontEnd**-NSG zum **FrontEnd**-Subnetz aufzuheben, führen Sie die folgenden Schritte aus.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen** > **RG-NSG** > **...** > **TestVNet**.
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Subnetze** > **FrontEnd** > **Netzwerksicherheitsgruppe** > **Keine**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Klicken Sie auf dem Blatt **FrontEnd** auf **Speichern**.

![Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### Zuordnen einer NSG zu einem Subnetz

Um die **NSG-FrontEnd**-NSG erneut dem **FrontEnd**-Subnetz zuzuordnen, führen Sie die folgenden Schritte aus.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen** > **RG-NSG** > **...** > **TestVNet**.
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Subnetze** > **FrontEnd** > **Netzwerksicherheitsgruppe** > **NSG-FrontEnd**.
3. Klicken Sie auf dem Blatt **FrontEnd** auf **Speichern**.

>[AZURE.NOTE] Sie können auch vom Blatt **Einstellungen** der NSG aus eine NSG einem Subnetz zuordnen.

## Löschen einer NSG

Sie können eine NSG nur löschen, wenn sie keiner Ressource zugeordnet ist. Um eine NSG zu löschen, gehen Sie folgendermaßen vor.

1. Klicken Sie im Azure-Portal auf **Ressourcengruppen** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Netzwerkschnittstellen**.
3. Wenn NICs aufgeführt sind, klicken Sie auf die NIC, und führen Sie Schritt 2 in [Aufheben der Zuordnung einer NSG zu einer NIC](#Dissociate-an-NSG-from-a-NIC) aus.
4. Wiederholen Sie Schritt 3 für jede NIC.
5. Klicken Sie auf dem Blatt **Einstellungen** auf **Subnetze**.
6. Wenn Subnetze aufgeführt sind, klicken Sie auf das Subnetz, und führen Sie die Schritte 2 und 3 in [Aufheben der Zuordnung einer NSG zu einem Subnetz](#Dissociate-an-NSG-from-a-subnet) aus.
7. Führen Sie einen Bildlauf nach links zum **NSG-FrontEnd**-Blatt aus, und klicken Sie dann auf **Löschen** > **Ja**.

[Azure-Portal – NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## Nächste Schritte

- [Aktivieren der Protokollierung](virtual-network-nsg-manage-log.md) für NSGs.

<!---HONumber=AcomDC_0323_2016-->