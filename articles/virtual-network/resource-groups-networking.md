<properties 
   pageTitle="Anbieter von Netzwerkressourcen"
	description="Anbieter von Netzwerkressourcen"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="adinah"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/23/2015"
	ms.author="telmos"/>

# Anbieter von Netzwerkressourcen
Ein zentrales Bedürfnis für den heutigen Geschäftserfolg ist die Fähigkeit, großmaßstäbliche netzwerksensible Anwendungen auf agile, flexible und wiederholbare Weise aufzubauen und zu verwalten. Mit Azure Resource Manager (ARM) können Sie solche Anwendungen als eine einzelne Ressourcensammlung in Ressourcengruppen erstellen. Solche Ressourcen werden durch mehrere Ressourcenanbieter unter ARM verwaltet.

Mit dem Azure Resource Manager (ARM) können Sie solche Anwendungen und die damit verbundene Sammlung von Netzwerkressourcen als eine einzelne Ressourcensammlung in einer Ressourcengruppe erstellen. Die Anwendung und die Netzwerkressourcen werden als eine Einheit in einer ARM-Ressourcengruppe ausgeführt.

Sie können Netzwerkressourcen mithilfe der folgenden Verwaltungsschnittstellen verwalten:

- REST basierte API
- PowerShell
- .NET SDK
- Node.JS SDK
- Java-SDK
- Azure-Befehlszeilenschnittstelle
- Azure-Portal
- ARM-Vorlagensprache

Mit Einführung der Anbieter von Netzwerkressourcen können Sie die folgenden Vorteile nutzen:

- **Metadaten** – Sie können mithilfe von Tags Informationen hinzufügen. Diese Tags können verwendet werden, um die Ressourcenverwendung über Ressourcengruppen und Abonnements hinweg nachzuverfolgen.
- **Bessere Kontrolle Ihres Netzwerks** - Netzwerkressourcen sind lose gekoppelt und Sie können sie granularer kontrollieren. Das bedeutet, dass Sie beim Verwalten der Netzwerkressourcen mehr Flexibilität haben.
- **Schnellere Konfiguration** - Da die Netzwerkressourcen lose gekoppelt sind, können Sie Netzwerkressourcen parallel erstellen und orchestrieren. Das führt zu einer drastisch reduzierten Konfigurationszeit.
- **Rollenbasierte Zugriffssteuerung** - Die rollenbasierte Zugriffssteuerung bietet neben der Möglichkeit, benutzerdefinierte Rollen für eine sichere Verwaltung zu erstellen, auch Standardrollen mit spezifischen Sicherheitsbereichen. 
- **Leichteres Verwalten und Bereitstellen** - Anwendungen können leichter bereitgestellt und verwaltet werden, da Sie einen ganzen Anwendungsstapel als eine einzelne Ressourcensammlung in einer Ressourcengruppe erstellen können. Das Bereitstellen erfolgt schneller, da Sie hierfür lediglich eine Vorlage einer JSON-Nutzlast zur Verfügung stellen müssen.
- **Schnelle Anpassung** - Sie können Vorlagen im deklarativen Stil verwenden, um eine wiederholbare und schnelle Anpassung von Bereitstellungen zu aktivieren. 
- **Wiederholbare Anpassung** - Sie können Vorlagen im deklarativen Stil verwenden, um eine wiederholbare und schnelle Anpassung von Bereitstellungen zu aktivieren.

## Netzwerkressourcen 
Die Verwaltung von Netzwerkressourcen kann nun unabhängig voneinander erfolgen, anstatt sie alle durch eine einzelne Computerressource (virtueller Computer) zu verwalten. Das stellt ein höheres Maß an Flexibilität und Agilität beim Verfassen einer komplexen und großmaßstäblichen Infrastruktur in einer Ressourcengruppe sicher.
 
Das untenstehende Diagramm stellt eine umfassende Sicht des Netzwerkressourcen-Modells und seinen Zuordnungen aus höherer Ebene dar. Die Ressourcen der höchsten Ebene sind farbig und blau konturiert. Zusätzlich zu den Ressourcen der höchsten Ebene sind untergeordnete Ressourcen farbig und grau konturiert dargestellt. Sie können jede Ressource individuell verwalten.

![Netzwerkressourcen-Modell](./media/resource-groups-networking/Figure1.png)

Eine konzeptionelle Ansicht einer Beispielbereitstellung mit einer Multi-Tier-Anwendung ist nachstehend dargestellt. Alle Netzwerkressourcen sind farbig unterlegt und blau konturiert.

![Netzwerkressourcen-Modell](./media/resource-groups-networking/Figure2.png)

## REST-API 
Wie zuvor erwähnt können Netzwerkressourcen über eine Vielzahl an Schnittstellen verwaltet werden, einschließlich REST API, .NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Azure Portal und Vorlagen.

Die Rest APIs entsprechen der HTTP 1.1 Protokollspezifikation. Die allgemeine URI-Struktur der API ist nachstehend dargestellt:

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Die Parameter in Klammern stellen die folgenden Elemente dar:

- **subscription-id** - Ihre Azure-Abonnement-ID.
- **resource-provider-namespace** - Namespace für den genutzten Anbieter. Der Wert für den Anbieter der Netzwerkressourcen ist *Microsoft.Network*.
- **region-name** - Der Name der Azure-Region.

Die folgenden HTTP-Methoden werden bei Aufrufen der REST API unterstützt:

- **PUT** - wird verwendet, um eine Ressource eines bestimmten Typs zu erstellen, eine Ressourceneigenschaft zu ändern oder eine Zuordnung zwischen Ressourcen zu ändern. 
- **GET** - Wird verwendet, um Informationen für eine bereitgestellte Ressource zu ermitteln.
- **DELETE** - Wird verwendet, um eine bestehende Ressource zu löschen.

Sowohl die Anforderung als auch die Antwort entsprechen einem JSON-Nutzlast-Format. Weitere Informationen finden Sie unter [Azure Resource Management APIs](https://msdn.microsoft.com/library/azure/dn948464.aspx).

## ARM-Vorlagensprache
Neben dem unerlässlichen Verwalten von Ressourcen (über APIs oder SDK) können Sie auch einen deklarativen Programmierstil verwenden, um Netzwerkressourcen mithilfe der ARM-Vorlagensprache zu erstellen und zu verwalten.

Nachstehend finden Sie eine Beispieldarstellung einer Vorlage –

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

Die Vorlage ist überwiegend eine JSON-Beschreibung der Ressourcen und die Instanzwerte sind über Parameter eingefügt. Das untenstehende Beispiel kann zum Erstellen eines virtuellen Netzwerks mit zwei Subnetzen verwendet werden.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Bei Verwendung einer Vorlage besteht die Option, die Parameterwerte manuell bereitzustellen. Alternativ können Sie eine Parameterdatei nutzen. Das untenstehende Beispiel zeigt einen möglichen Satz von Parameterwerten, die mit der oben abgebildeten Vorlage zu verwenden sind:

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


Die Hauptvorteile des Verwenden von Vorlagen sind:

- Sie können in einem deklarativen Stil eine komplexe Infrastruktur in einer Ressourcengruppe erstellen. Die Orchestrierung der Ressourcenerstellung, einschließlich der Verwaltung von Abhängigkeiten, wird von ARM bearbeitet. 
- Die Infrastruktur kann auf wiederholbare Weise regionsübergreifend oder innerhalb einer Region erstellt werden, indem einfach die Parameter geändert werden. 
- Der deklarative Stil führt zu einer kürzeren Durchlaufzeit beim Erstellen der Vorlagen und Einführen der Infrastruktur. 

Beispielvorlagen finden Sie unter [Azure Schnellstart-Vorlagen](https://github.com/Azure/azure-quickstart-templates).

Weitere Informationen zur ARM-Vorlagensprache finden Sie unter [Azure Resource Manager-Vorlagensprache](../resource-group-authoring-templates.md).

Die oben abgebildete Vorlage nutzt das virtuelle Netzwerk und Subnetzressourcen. Wie unten aufgeführt, gibt es weitere Netzwerkressourcen, die Sie nutzen können:

## NIC
Die Netzwerkschnittstellenkarte (Network Interface Card, NIC) stellt eine Netzwerkschnittstelle dar, die einem virtuellen Computer (VM) zugeordnet werden kann. Ein virtueller Computer hat ein oder mehrere NICs.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/Figure3.png)

Zu den Schlüsseleigenschaften einer NIC-Ressource zählen:

- IP-Einstellungen

Eine NIC kann auch den folgenden Netzwerkressourcen zugeordnet werden:

- Netzwerksicherheitsgruppen (NSG) 
- Lastenausgleichsmodul

## Virtuelles Netzwerk und Subnetz
Virtuelle Netzwerke (VNET) und Subnetze helfen, eine Sicherheitsbegrenzung für in Azure ausgeführte Arbeitsauslastungen zu definieren. Ein VNET verfügt über einen Adressraum, der auch als CIDR-Block bezeichnet wird.

Ein Subnetz ist eine untergeordnete Ressource eines VNET und hilft, die Segmente von Adressräumen innerhalb eines CIDR-Blocks mithilfe von IP-Adressenpräfixen zu definieren. Virtuelle Computer, die mehrere Arbeitsauslastungen ausführen, arbeiten grundsätzlich in einer Subnetzgrenze.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/Figure4.png)

Zu den Schlüsseleigenschaften einer VNET-Ressource zählen:

- IP-Adressenraum (CIDR-Block) 
- VNET-Name
- Subnetze

Ein VNET kann auch den folgenden Netzwerkressourcen zugeordnet werden:

- VPN-Gateway

Zu den Schlüsseleigenschaften eines Subnetzes zählen:

- IP-Adressenpräfix
- Subnetzname:

Ein Subnetz kann auch den folgenden Netzwerkressourcen zugeordnet werden:

- NSG

## Lastenausgleichsmodul
Ein Lastenausgleichsmodul wird verwendet, wenn Sie Ihre Anwendungen skalieren möchten. Zu den typischen Bereitstellungsszenarios zählen Anwendungen, die auf mehreren VM-Instanzen ausgeführt werden. Den VM-Instanzen ist ein Lastenausgleichsmodul vorgelagert, das hilft den Netzwerkdatenverkehr an die verschiedenen Instanzen zu verteilen.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/Figure5.png)

Lastenausgleichsmodule enthalten die folgenden untergeordneten Ressourcen:

- **Front-End-IP-Konfiguration** – ein Lastenausgleichsmodul kann ein oder mehrere Front-End-IP-Adressen umschließen, auch als virtuelle IPs (VIPs) bekannt. Diese IP-Adressen dienen als Eingang für den Datenverkehr. 
- **Back-End-Adresspool** – IP-Adressen, die den VM NICs zugeordnet sind, an die Last verteilt wird.
- **Lastenausgleichsregeln** – eine Regeleigenschaft ordnet eine bestimmte Front-End-IP-/Port-Kombination einer Back-End-IP-Adressen-/Port-Kombination zu. Mit einer einzelnen Definition eines Lastenausgleichsmodul können Sie mehrere Lastenausgleichsregeln definieren, von denen jede eine Kombination aus Front-End-IP und Port sowie Back-End-IP und Port ist, die dem virtuellen Computer zugeordnet ist. 
- **Überprüfungen** – Mit Überprüfungen können Sie die Integrität der VM-Instanzen nachverfolgen. Schlägt eine Integritätsüberprüfung fehl, wird die VM-Instanz automatisch aus der Rotation entfernt.
- **Eingehende NAT-Regeln** – NAT-Regeln definieren den Eingangsdatenverkehr, der durch die Front-End-IP fließt und an die Back-End-IP verteilt wird.

## Application Gateway

Application Gateway bietet eine von Azure verwaltete HTTP-Lastenausgleichslösung, die auf Lastenausgleich der HTTP-Ebene 7 basiert. Anwendungslastenausgleich ermöglicht die Verwendung von Weiterleitungsregeln für Netzwerkverkehr auf Basis von HTTP.

Application Gateways enthalten die folgenden untergeordneten Ressourcen:

- **Back-End-Serverpool**: Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder dem Subnetz des virtuellen Netzwerks angehören oder eine öffentliche IP-Adresse/VIP sein. 
- **Einstellungen für den Back-End-Serverpool**: Jeder Pool weist Einstellungen wie Port, Protokoll und cookiebasierte Affinität auf. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
- **Front-End-Port**: Dieser Port ist der öffentliche Port, der im Application Gateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
- **Listener**: Der Listener hat einen Front-End-Port, ein Protokoll (Http oder Https, bei beiden muss die Groß-/Kleinschreibung beachtet werden) und den Namen des SSL-Zertifikats (falls SSL-Auslagerung konfiguriert wird). 
- **Regel**: Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und mit ihr wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden sollen, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel „basic“ unterstützt. Die Regel „basic“ ist eine Round-Robin-Lastverteilung.


## Öffentliche IP
Eine öffentliche IP-Adresse bietet entweder eine reservierte oder dynamische öffentliche IP-Adresse. Eine öffentliche IP-Adresse kann einem Lastausgleichsmodul, NAT, zugewiesen oder einer privaten IP-Adresse auf einer NIC eines virtuellen Computers zugeordnet sein.

Zu den Schlüsseleigenschaften einer öffentlichen IP-Ressource zählen:

- **IP-Zuordnungsmethode** – reserviert oder dynamisch 

## Netzwerksicherheitsgruppen (NSG)
Eine NSG-Ressource ermöglicht das Erstellen einer Sicherheitsbegrenzung für Arbeitsauslastungen durch Implementieren von Zulassungs- und Ablehnungsregeln. Solche Regeln können auf NIC-Ebene (VM-Instanzebene) oder auf Subnetzebene (VM-Gruppe) angewendet werden.

Zu den Schlüsseleigenschaften einer NSG-Ressource zählen:

- **Sicherheitsregeln** - Eine NSG kann mehrere Sicherheitsregeln definieren. Jede Regel kann verschiedene Datenverkehrstypen zulassen oder ablehnen.

## Sicherheitsregel
Eine Sicherheitsregel ist eine der NSG untergeordnete Ressource.

Zu den Schlüsseleigenschaften einer Sicherheitsregel zählen:

- **Protokoll** – Das Netzwerkprotokoll, für das diese Regel gilt.
- **Quellportbereich** - Quellport, oder Bereich, von 0 bis 65535. Zur Übereinstimmung aller Ports kann ein Platzhalter verwendet werden. 
- **Zielportbereich** - Zielport, oder Bereich, von 0 bis 65535. Zur Übereinstimmung aller Ports kann ein Platzhalter verwendet werden.
- **Quelladresspräfix** - Quell-IP-Adressbereich. 
- **Zieladresspräfix** - Ziel-IP-Adressbereich.
- **Zugriff** – *Zulassen* oder *Ablehnen* von Datenverkehr.
- **Priorität** - ein Wert zwischen 100 und 4096. Die Prioritätsnummer muss für jede Regel in der Sammlung von Sicherheitsregeln eindeutig sein. Je niedrigere die Prioritätsnummer ist, desto höher ist die Priorität der Regel.
- **Richtung** – gibt an, ob die Regel für Datenverkehr in *eingehende* oder *ausgehende* Richtung angewendet wird. 

## VPN-Gateway 
Eine VPN-Gateway-Ressource ermöglicht Ihnen, eine sichere Verbindung zwischen ihrem lokalen Rechenzentrum und Azure zu erstellen. Eine VPN-Gateway-Ressource kann auf drei unterschiedliche Arten konfiguriert werden:
 
- **Punkt-zu-Standort** – Mit einem VPN-Client von einem beliebigen Computer können Sie sicher auf Ihre in einem VNET gehosteten Azure-Ressourcen zugreifen. 
- **Multisite-Verbindung** – Sie können von Ihren lokalen Rechenzentren eine sichere Verbindung zu Ressourcen aufbauen, die in einem VNET ausgeführt werden. 
- **VNET-zu-VNET** – Sie können über Azure-VNETs hinweg (in der gleichen Region oder regionsübergreifend) eine sichere Verbindung aufbauen, um Arbeitsauslastungen mit Georedundanz zu erstellen.

Zu den Schlüsseleigenschaften eines VPN-Gateway zählen:
 
- **Gateway-Typ** - dynamisch oder statisch weitergeleitetes Gateway. 
- **Adresspool-Präfix des VPN-Client** – IP-Adressen, die den Clients mit Punkt-zu-Standort-Verbindung zuzuweisen sind.



## Traffic Manager-Profil
Der Traffic Manager und seine untergeordnete Endpunkt-Ressource ermöglichen die Verteilung Ihres Datenverkehrs zu Endpunkten in und außerhalb von Azure. Eine solche Datenverkehrsverteilung wird durch Richtlinien bestimmt. Der Traffic Manager lässt auch eine Überwachung der Endpunkt-Integrität zu, sowie eine angemessene Umleitung des Datenverkehrs basierend auf der Integrität eines Endpunktes.

Zu den Schlüsseleigenschaften eines Traffic Manager zählen:

- **Datenverkehr-Routingmethode** - mögliche Werte sind *Leistung*, *Gewichtung* und *Priorität*.
- **DNS-Konfiguration** - FQDN für das Profil.
- **Protokoll** - Überwachungsprotokoll, mögliche Werte sind *HTTP* und *HTTPS*.
- **Port** - Überwachungsport. 
- **Pfad** - Überwachungspfad.
- **Endpunkte** - Container für Endpunkt-Ressourcen.

## Endpunkt 
Ein Endpunkt ist eine dem Traffic Manager-Profil untergeordnete Ressource. Er stellt einen Dienst- oder Webendpunkt dar, an den der Benutzerdatenverkehr anhand der in der Traffic Manager-Ressource konfigurierten Richtlinie verteilt wird.

Zu den Schlüsseleigenschaften eines Endpunktes zählen:
 
- **Typ** - der Typ des Endpunkts, mögliche Werte sind *Azure-Endpunkt*, *externer Endpunkt* und *geschachtelter Endpunkt*. 
- **Zielressourcen-ID** – öffentliche IP-Adresse eines Dienst- oder Webendpunktes. Dabei kann es sich um einen Azure- oder externen Endpunkt handeln.
- **Gewichtung** - Endpunkt-Gewichtung, die für die Datenverkehrsverwaltung verwendet wird. 
- **Priorität** - Priorität des Endpunktes, die zum Definieren einer Failover-Aktion verwendet wird. 

## Azure DNS

Azure DNS ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Microsoft Azure-Infrastruktur durchführt.

Zu den Schlüsseleigenschaften von Azure DNS zählen:

- ** DNS-Zonen ** – Domänenzoneninformationen zum Hosten von DNS-Einträgen einer bestimmten Domäne.
- ** DNS-Eintragssätze ** – eine Auflistung der Einträge eines bestimmten Typs. Unterstützte Typen sind A, AAAA, CNAME, MX, NS, SOA, SRV und TXT.


## Verwenden einer Vorlage

Sie können Dienste in Azure über eine Vorlage bereitstellen, indem Sie PowerShell oder AzureCLI verwenden oder indem Sie die Bereitstellung von GitHub per Klick ausführen. Führen Sie zum Bereitstellen von Diensten über eine Vorlage in GitHub die folgenden Schritte aus:

1. Öffnen Sie die Datei "template3" von GitHub. Öffnen Sie beispielsweise [Virtual network with two subnets](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Klicken Sie auf **Deploy to Azure**, und melden Sie sich dann mit Ihren Anmeldeinformationen im Azure-Portal an.
3. Überprüfen Sie die Vorlage, und klicken Sie dann auf **Speichern**.
4. Klicken Sie auf **Parameter bearbeiten**, und wählen Sie einen Speicherort aus, z. B. *USA, Westen* für VNet und Subnetze.
5. Ändern Sie ggf. die Parameter **ADDRESSPREFIX** und **SUBNETPREFIX**, und klicken Sie dann auf **OK**.
6. Klicken Sie auf **Ressourcengruppe auswählen**, und klicken Sie dann auf die Ressourcengruppe, die VNet und den Subnetzen hinzugefügt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf **Oder neu erstellen** klicken.
3. Klicken Sie auf **Erstellen**. Beachten Sie die Kachel mit dem Text **Bereitstellung der Vorlagenbereitstellung**. Wenn die Bereitstellung abgeschlossen ist, wird ein Bildschirm wie der folgende angezeigt.

![Bereitstellung von Beispielvorlagen](./media/resource-groups-networking/Figure6.png)


## Weitere Informationen

[Azure Netzwerk-API-Verweise](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Azure PowerShell-Verweis für Netzwerke](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure Resource Manager-Vorlagensprache](../resource-group-authoring-templates.md)

[Azure-Netzwerk – Häufig verwendete Vorlagen](https://github.com/Azure/azure-quickstart-templates)

[Anbieter von Computerressourcen](../virtual-machines-azurerm-versus-azuresm)

[Azure Resource Manager-Übersicht](../resource-group-overview)

[Rollenbasierte Zugriffssteuerung im Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Verwenden von Tags im Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Bereitstellungen von Vorlagen](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=August15_HO9-->