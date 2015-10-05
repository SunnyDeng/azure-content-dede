## Virtuelles Netzwerk
Virtuelle Netzwerke (VNET) und Subnetze helfen, eine Sicherheitsbegrenzung für in Azure ausgeführte Arbeitsauslastungen zu definieren. Ein VNET ist durch eine Sammlung von Adressräumen gekennzeichnet, die auch als CIDR-Blöcke bezeichnet werden.

Ein Subnetz ist eine untergeordnete Ressource eines VNET und hilft, die Segmente von Adressräumen innerhalb eines CIDR-Blocks mithilfe von IP-Adressenpräfixen zu definieren. NICs können zu Subnetzen hinzugefügt und mit virtuellen Computern verbunden werden, sodass sie Konnektivität für verschiedene Workloads bereitstellen.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/Figure4.png)

Zu den Schlüsseleigenschaften einer VNET-Ressource zählen:

- IP-Adressenraum (CIDR-Block) 
- VNET-Name
- Subnetze
- DNS-Server

Ein VNET kann auch den folgenden Netzwerkressourcen zugeordnet werden:

- VPN-Gateway

### Subnetz

Zu den Schlüsseleigenschaften eines Subnetzes zählen:

- IP-Adressenpräfix
- Subnetzname:

Ein Subnetz kann auch den folgenden Netzwerkressourcen zugeordnet werden:

- NSG

<!---HONumber=Sept15_HO4-->