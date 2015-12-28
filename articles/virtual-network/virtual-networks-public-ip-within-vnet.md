<properties 
   pageTitle="Verwenden von öffentlichen IP-Adressen in einem virtuellen Netzwerk"
   description="Hier erfahren Sie, wie Sie ein virtuelles Netzwerk für die Verwendung öffentlicher IP-Adressen konfigurieren."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Öffentlicher IP-Adressraum in einem virtuellen Netzwerk (VNet)

Sie können jetzt VNets einen öffentlichen IP-Adressraum hinzufügen. Bislang konnten Sie Ihren VNets nur RFC 1918-Adressblöcke (privater Adressraum) hinzufügen. Wenn Sie einen öffentlichen IP-Adressbereich hinzufügen, wird dieser als Teil des privaten VNet-IP-Adressraums behandelt, der nur innerhalb des VNets, aus miteinander verbundenen VNets und von Ihrem lokalen Standort aus erreichbar ist.

Ein öffentlicher IP-Adressbereich wird folgendermaßen hinzugefügt:

![Öffentliche IP-Adresse – Konzepte](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Wie füge ich einen öffentlichen IP-Adressbereich hinzu?

Sie fügen einen öffentlichen IP-Adressbereich genau wie einen privaten IP-Adressbereich hinzu: entweder mithilfe einer *netcfg*-Datei oder durch die Konfiguration im Portal. Sie können einen öffentlichen IP-Adressbereich beim Erstellen des VNets oder zu einem späteren Zeitpunkt hinzufügen. Das folgende Beispiel zeigt den öffentlichen und den privaten IP-Adressraum im gleichen virtuellen Netzwerk.

![Öffentliche IP-Adresse im Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Gibt es Einschränkungen?

Es gibt einige IP-Adressbereiche, die nicht zulässig sind:

- 224\.0.0.0/4 (Multicast)

- 255\.255.255.255/32 (Übertragung)

- 127\.0.0.0/8 (Loopback)

- 169\.254.0.0/16 (Verbindungslokal)

- 68\.63.129.16/32 (Internes DNS)

## Nächste Schritte

[Verwalten der Eigenschaften eines virtuellen Netzwerks (VNet)](../virtual-networks-settings)

[Verwalten von DNS-Servern, die von einem virtuellen Netzwerk (VNet) verwendet werden](../virtual-networks-manage-dns-in-vnet)

[Löschen eines virtuellen Netzwerks (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=AcomDC_1217_2015-->