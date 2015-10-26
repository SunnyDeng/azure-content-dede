<properties 
   pageTitle="Einrichten einer statischen privaten IP-Adresse im klassischen Modus mithilfe des Vorschauportals | Microsoft Azure"
   description="Grundlegendes zu statischen privaten IP-Adressen und zur Verwaltung dieser IP-Adressen im klassischen Modus mithilfe des Portals"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# Einrichten einer statischen privaten IP-Adresse (klassisch) im Vorschauportal

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im Ressourcen-Manager-Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Die folgenden Schritte setzen voraus, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Schritte ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](virtual-networks-create-vnet-classic-pportal.md) beschrieben.

## Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers
Erstellen Sie in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01* und der statischen privaten IP-Adresse *192.168.1.101*. Gehen Sie dabei wie folgt vor:

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **NEU** > **Compute** > **Windows Server 2012 R2 Datacenter**. Beachten Sie, dass in der Liste **Bereitstellungsmodell auswählen** bereits **Klassisch** angezeigt wird. Klicken Sie anschließend auf **Erstellen**.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Geben Sie auf dem Blatt **VM erstellen** den Namen des zu erstellenden virtuellen Computers (in diesem Szenario *DNS01*), das lokale Administratorkonto und das gewünschte Kennwort ein.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Klicken Sie auf **Optionale Konfiguration** > **Netzwerk** > **Virtual Network** und anschließend auf **TestVNet**. Wenn **TestVNet** nicht verfügbar ist, stellen Sie sicher, dass Sie den Speicherort *USA (Mitte)* verwenden und dass die am Anfang dieses Artikels beschriebene Testumgebung erstellt wurde.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. Stellen Sie sicher, dass auf dem Blatt **Netzwerk** das Subnetz *FrontEnd* ausgewählt ist, und klicken Sie anschließend auf **IP-Adressen**. Klicken Sie unter **IP-Adresszuweisung** auf **Statisch**, und geben Sie dann im Feld **IP-Adresse** den Wert *192.168.1.101* ein, wie nachfolgend dargestellt.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)

6. Klicken Sie auf dem Blatt **IP-Adressen** auf **OK**, klicken Sie auf dem Blatt **Netzwerk** auf **OK**, und klicken Sie dann auf dem Blatt **Optionale Konfiguration** auf **OK**.
7. Klicken Sie auf dem Blatt **VM erstellen** auf **Erstellen**. Beachten Sie die Kachel, die im unteren Bereich des Dashboards angezeigt wird.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer

Um die Informationen zur statischen privaten IP-Adresse für den virtuellen Computer anzuzeigen, der mit den obigen Schritten erstellt wurde, führen Sie die folgenden Schritte aus.

1. Klicken Sie im Azure-Vorschauportal auf **ALLE DURCHSUCHEN** > **Virtuelle Computer (Klassisch)** > **DNS01** > **Alle Einstellungen** > **IP-Adressen**, um die Informationen zur IP-Adresszuweisung und IP-Adresse anzuzeigen, wie nachfolgend dargestellt.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Um die statische private IP-Adresse für den zuvor erstellten virtuellen Computer zu entfernen, führen Sie die folgenden Schritte aus.
	
1. Klicken Sie auf dem oben gezeigten Blatt **IP-Adressen** rechts neben **IP-Adresszuweisung** auf **Dynamisch**, klicken Sie anschließend auf **Speichern** und klicken Sie dann auf **Ja**.

	![Erstellen eines virtuellen Computers im Vorschauportal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie die folgenden Schritte aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

1. Klicken Sie auf dem oben gezeigten Blatt **IP-Adressen** rechts neben **IP-Adresszuweisung** auf **Statisch**.
2. Geben Sie im Feld **IP-Adresse** den Wert *192.168.1.101* ein, klicken Sie anschließend auf **Speichern**, und klicken Sie dann auf **Ja**.

## Nächste Schritte

- Weitere Informationen zu [reservierten öffentlichen IP-Adressen](../virtual-networks-reserved-public-ip).
- Weitere Informationen zu [öffentlichen IP-Adressen auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip).
- Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO3-->