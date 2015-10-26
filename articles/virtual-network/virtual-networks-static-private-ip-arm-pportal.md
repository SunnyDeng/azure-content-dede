<properties 
   pageTitle="Einrichten einer statischen privaten IP-Adresse im ARM-Modus mithilfe des Vorschauportals | Microsoft Azure"
   description="Grundlegendes zu privaten IP-Adressen (DIPs) und zur Verwaltung dieser IP-Adressen im ARM-Modus mithilfe des Vorschauportals"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# Einrichten einer statischen privaten IP-Adresse im Vorschauportal

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im klassischen Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Die folgenden Schritte setzen voraus, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Schritte ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](virtual-networks-create-vnet-arm-pportal.md) beschrieben.

## Erstellen einer VM zum Testen statischer privater IP-Adressen

Im Vorschauportal ist es nicht möglich, beim Erstellen eines virtuellen Computers im Ressourcen-Manager-Bereitstellungsmodus eine statische private IP-Adresse festzulegen. Sie müssen den virtuellen Computer zuerst erstellen und dann eine statische private IP-Adresse festlegen.

Erstellen Sie in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01*, indem Sie die folgenden Schritte ausführen.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **NEU** > **Compute** > **Windows Server 2012 R2 Datacenter**. Beachten Sie, dass in der Liste **Bereitstellungsmodell auswählen** bereits **Ressourcen-Manager** angezeigt wird. Klicken Sie anschließend auf **Erstellen**, wie in der folgenden Abbildung gezeigt.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Geben Sie auf dem Blatt **Grundlagen** den Namen des zu erstellenden virtuellen Computers (in diesem Szenario *DNS01*), das lokale Administratorkonto und das Kennwort ein, wie in der folgenden Abbildung gezeigt.

	![Blatt "Grundlagen"](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Stellen Sie sicher, dass unter **Speicherort** die Region *USA (Mitte)* ausgewählt ist. Klicken Sie anschließend unter **Ressourcengruppe** auf **Vorhandene auswählen**, klicken Sie erneut auf **Ressourcengruppe**, klicken Sie auf *TestRG*, und klicken Sie dann auf **OK**.

	![Blatt "Grundlagen"](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Wählen Sie auf dem Blatt **Größe auswählen** die Option **A1 Standard**, und klicken Sie anschließend auf **Auswählen**.

	![Blatt "Größe auswählen"](./media/virtual-networks-static-ip-arm-pportal/figure04.png)

6. Überprüfen Sie auf dem Blatt **Einstellungen**, dass für die folgenden Eigenschaften die nachfolgenden Werte festgelegt sind, und klicken Sie anschließend auf **OK**.

	-**Speicherkonto**: *vnetstorage*
	- **Netzwerk**: *TestVNet*
	- **Subnetz**: *FrontEnd*

	![Blatt "Größe auswählen"](./media/virtual-networks-static-ip-arm-pportal/figure05.png)

7. Klicken Sie auf dem Blatt **Zusammenfassung** auf **OK**. Beachten Sie die Kachel, die im unteren Bereich des Dashboards angezeigt wird.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer

Um die Informationen zur statischen privaten IP-Adresse für den virtuellen Computer anzuzeigen, der mit den obigen Schritten erstellt wurde, führen Sie die folgenden Schritte aus.

1. Klicken Sie im Azure-Vorschauportal auf **ALLE DURCHSUCHEN** > **Virtuelle Computer** > **DNS01** > **Alle Einstellungen** > **Netzwerkschnittstellen**, und klicken Sie anschließend auf die einzige aufgeführte Netzwerkschnittstelle.

	![Bereitstellen der VM-Kachel](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Klicken Sie auf dem Blatt **Netzwerkschnittstelle** auf **Alle Einstellungen** > **IP-Adressen** und beachten Sie die Werte für **Zuweisung** und **IP-Adresse**.

	![Bereitstellen der VM-Kachel](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie die folgenden Schritte aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

1. Klicken Sie auf dem oben gezeigten Blatt **IP-Adressen** unter **Zuweisung** auf **Statisch**.
2. Geben Sie im Feld **IP-Adresse** den Wert *192.168.1.101* ein, und klicken Sie anschließend auf **Speichern**.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE]Wenn Sie nach dem Klicken auf **Speichern** feststellen, dass die Zuweisung immer noch den Wert **dynamisch** anzeigt, bedeutet dies, dass die eingegebene IP-Adresse bereits verwendet wird. Versuchen Sie, eine andere IP-Adresse zu verwenden.

## Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Um die statische private IP-Adresse für den zuvor erstellten virtuellen Computer zu entfernen, führen Sie den folgenden Schritt aus.
	
1. Klicken Sie auf dem oben gezeigten Blatt **IP-Adressen** unter **Zuweisung** auf **Dynamisch**, und klicken Sie anschließend auf **Save**.

## Nächste Schritte

- Weitere Informationen zu [reservierten öffentlichen IP-Adressen](../virtual-networks-reserved-public-ip).
- Weitere Informationen zu [öffentlichen IP-Adressen auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip).
- Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO3-->