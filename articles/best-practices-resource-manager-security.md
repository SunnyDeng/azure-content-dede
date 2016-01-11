<properties
	pageTitle="Sicherheitsaspekte für Azure-Ressourcen-Manager"
	description="Veranschaulicht empfohlene Vorgehensweisen im Azure-Ressourcen-Manager für den Schutz von Ressourcen mit Schlüsseln und geheimen Schlüsseln, rollenbasierter Zugriffssteuerung und Sicherheitsgruppen."
	services="azure-resource-manager"
	documentationCenter=""
	authors="george-moore"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="georgem"/>


# Sicherheitsaspekte für Azure-Ressourcen-Manager

Bei den Sicherheitsaspekten für Ihre Azure-Ressourcen-Manager-Vorlagen müssen mehrere Bereiche berücksichtigt werden: Schlüssel und geheime Schlüssel, rollenbasierte Zugriffssteuerung und Netzwerksicherheitsgruppen.

Dieses Thema setzt voraus, dass Sie mit der rollenbasierten Zugriffssteuerung in Azure-Ressourcen-Manager vertraut sind. Weitere Informationen finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

Dieses Thema ist Teil eines umfangreicheren Whitepapers. Um den vollständigen Artikel zu lesen, laden Sie [World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf) herunter.

## Geheime Schlüssel und Zertifikate

Azure Virtual Machines, Azure-Ressourcen-Manager und Azure Key Vault sind vollständig integriert, um den sicheren Umgang mit Zertifikaten zu unterstützen, die in der VM bereitgestellt werden. Das Verwenden von Azure Key Vault mit Ressourcen-Manager zum Orchestrieren und Speichern von geheimen VM-Schlüsseln und Zertifikaten ist eine bewährte Methode, die folgende Vorteile bietet:

- Die Vorlagen enthalten nur URI-Verweise auf die geheimen Schlüssel, was bedeutet, dass die tatsächlichen geheimen Schlüssel nicht in Code-, Konfigurations- oder Quellcoderepositorys enthalten sind. Dies verhindert umfassende Phishing-Angriffe auf interne oder externe Repositorys, z. B. "harvest-bots" auf GitHub.
- Im Schlüsseltresor gespeicherte geheime Schlüssel unterliegen vollständig der rollenbasierten Zugriffssteuerung durch einen vertrauenswürdigen Mitarbeiter. Wenn der vertrauenswürdige Mitarbeiter das Unternehmen verlässt oder innerhalb des Unternehmens in eine neue Abteilung versetzt wird, hat er keinen Zugriff mehr auf die Schlüssel, die er im Tresor erstellt hat.
- Vollständige Trennung aller Ressourcen:
      - die Vorlagen zum Bereitstellen der Schlüssel 
      - die Vorlagen zum Bereitstellen eines virtuellen Computers mit Verweisen auf die Schlüssel 
      - die tatsächlichen Schlüsseldaten im Tresor Jede Vorlage (und Aktion) kann für eine vollständige Trennung von Aufgaben verschiedenen Rollen für die rollenbasierte Zugriffssteuerung unterliegen.
- Das Laden der geheimen Schlüssel in einen virtuellen Computer zum Zeitpunkt der Bereitstellung erfolgt über einen direkten Kanal zwischen Azure Fabric und dem Schlüsseltresor innerhalb der Grenzen des Microsoft-Datencenters. Sobald sich die Schlüssel im Schlüsseltresor befinden, sehen sie nie wieder das "Tageslicht" über einen nicht vertrauenswürdigen Kanal außerhalb des Datencenters.  
- Da Schlüsseltresore stets regional sind, sind die geheimen Schlüssel in den VMs stets ortsbezogen. Es sind keine globalen Schlüsseltresore.

### Trennung der Schlüssel von Bereitstellungen

Eine bewährte Methode ist das Vorhalten getrennter Vorlagen für die folgenden Aufgaben:

1.	Erstellung von Tresoren (die die Schlüsseldaten enthalten)
2.	Bereitstellung der VMs (mit URI-Verweisen auf die in den Tresoren enthaltenen Schlüssel)

Ein typisches Unternehmensszenario ist das Einrichten einer kleinen vertrauenswürdigen Gruppe mit Zugriff auf wichtige geheime Informationen in den bereitgestellten Workloads und einer größeren Gruppe von Dev/Ops-Mitarbeitern, die VM-Bereitstellungen erstellen oder aktualisieren können. Es folgt eine Beispiel einer ARM-Vorlage, die einen neuen Tresor im Kontext der Identität des derzeit authentifizierten Benutzers in Azure Active Directory erstellt und konfiguriert. Dieser Benutzer hat die Standardberechtigung zum Erstellen, Löschen, Auflisten, Aktualisieren, Sichern, Wiederherstellen und Abrufen der öffentliche Hälfte der Schlüssel in diesem neuen Schlüsseltresor.

Während die meisten Felder in dieser Vorlage selbsterklärend sind, sind für die Einstellung **enableVaultForDeployment** weitere Hintergrundinformationen erforderlich: Auf Tresore besteht kein ständiger Zugriff durch andere Komponenten der Azure-Infrastruktur. Durch das Festlegen dieses Werts wird den Azure Compute-Infrastrukturkomponenten ein schreibgeschützter Zugriff auf diesen bestimmten benannten Tresor gewährt. Daher ist es eine weitere bewährte Methode, vertrauliche Unternehmensdaten nicht im selben Tresor mit geheimen VM-Schlüsseln zu vermengen.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

Sobald der Tresor erstellt wurde, ist der nächste Schritt das Verweisen auf diesen Tresor in der Bereitstellungsvorlage einer neuen VM. Wie bereits erwähnt, ist es eine bewährte Methode, eine getrennte Dev/Ops-Gruppe für das Verwalten von VM-Bereitstellungen einzurichten, die keinen direkten Zugriff auf die Schlüssel hat, die im Tresor gespeichert sind.

Das nachstehende Vorlagenfragment besteht aus übergeordneten Bereitstellungskonstrukten, von denen jedes auf überaus vertrauliche geheime Schlüssel verweist, die nicht der direkten Kontrolle des Bedieners unterliegen.

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

## Dienstprinzipale für abonnementübergreifende Interaktionen

Dienstidentitäten werden in Active Directory als Dienstprinzipale dargestellt. Dienstprinzipale stehen im Mittelpunkt der Aktivierung von Schlüsselszenarien für IT-Abteilungen von Unternehmen, Systemintegratoren (SI) und Clouddienstanbietern (Cloud Service Vendor, CSV). Es gibt spezifische Anwendungsfälle, bei denen eine dieser Organisationen mit dem Abonnement eines ihrer Kunden interagieren muss.

Ihre Organisation kann ein Angebot bereitstellen, das zum Überwachen einer Lösung dient, die in der Umgebung und im Abonnement Ihrer Kunden bereitgestellt ist. In diesem Fall benötigen Sie Zugriff auf Protokolle und andere Daten innerhalb des Kontos eines Kunden, damit Sie es in Ihrer Überwachungslösung nutzen können. Als IT-Abteilung eines Unternehmens oder Systemintegrator können Sie einem Kunden ein Angebot machen, bei dem Sie eine Funktion für ihn bereitstellen und verwalten, wie z. B. eine Datenanalyseplattform, und sich das Angebot im kundeneigenen Abonnement befindet.

In diesen Fällen benötigt Ihr Unternehmen eine Identität, der Zugriff zum Ausführen dieser Aktionen innerhalb des Kontexts eines Kundenabonnements gewährt wird.

Diese Szenarien führen bei Ihren Kunden zu verschiedenen Überlegungen:

-	Aus Gründen der Sicherheit muss der Zugriff möglicherweise auf bestimmte Arten von Aktionen beschränkt werden, z. B. in Form eines schreibgeschützten Zugriffs.
-	Da Ressourcen mit anfallenden Kosten bereitgestellt werden, gelten aus wirtschaftlichen Gründen ggf. ähnliche Einschränkungen für den Zugriff.
-	Aus Sicherheitsgründen muss der Zugriff u. U. auf nur eine bestimmte Ressource (Speicherkonten) oder Ressourcen (Ressourcengruppe mit einer Umgebung oder Lösung) begrenzt werden.
-	Sollte sich die Beziehung zu einem Anbieter ändern, benötigt der Kunden die Möglichkeit, den Zugriff für den Systemintegrator oder Clouddienstanbieter zu aktivieren/deaktivieren.
-	Da Aktionen in diesem Konto Auswirkungen auf die Abrechnung haben, wünscht der Kunde Prüffähigkeit und Zuständigkeit für die Abrechnung.
-	Was die Einhaltung von Vorschriften angeht, möchte der Kunde Ihr Verhalten in seiner Umgebung überwachen können.

Eine Kombination aus einem Dienstprinzipal und rollenbasierter Zugriffssteuerung kann verwendet werden, um diese Anforderungen zu erfüllen.

## Netzwerksicherheitsgruppen

Viele Szenarien haben Anforderungen, die vorgeben, wie der Datenverkehr zu einer oder mehreren VM-Instanzen in Ihrem virtuellen Netzwerk gesteuert wird. Im Rahmen der Bereitstellung einer ARM- Vorlage können Sie dazu eine Netzwerksicherheitsgruppe (NSG) verwenden.

Bei einer NSG handelt es sich um ein Objekt oberster Ebene, das Ihrem Abonnement zugeordnet ist. Eine NSG enthält Zugriffssteuerungsregeln, die Datenverkehr zu VM-Instanzen zulassen oder verweigern. Die Regeln für eine NSG können jederzeit geändert werden, und die Änderungen werden auf alle zugeordneten Instanzen angewendet. Für die Verwendung einer NSG benötigen Sie ein virtuelles Netzwerk mit Regionszuordnung (Standort). NSGs sind nicht mit virtuellen Netzwerken kompatibel, die einer Affinitätsgruppe zugeordnet sind. Wenn Sie über kein regionales virtuelles Netzwerk verfügen und trotzdem den an Ihren Endpunkten eingehenden Datenverkehr steuern möchten, lesen Sie [Was ist eine Netzwerk-Zugriffssteuerungsliste (Access Control List, ACL)?](../virtual-network/virtual-networks-acl.md).

Sie können eine NSG einem virtuellen Computer oder einem Subnetz innerhalb eines virtuellen Netzwerks zuordnen. Bei der Zuordnung zu einem virtuellen Computer gilt die NSG für den gesamten ein- und ausgehenden Datenverkehr der VM-Instanz. Bei der Zuordnung zu einem Subnetz in Ihrem virtuellen Netzwerk gilt sie für den gesamten ein- und ausgehenden Datenverkehr aller VM-Instanzen im Subnetz. Ein virtueller Computer bzw. ein Subnetz kann jeweils nur einer einzelnen NSG zugeordnet werden, wobei jede NSG bis zu 200 Regeln enthalten kann. Pro Abonnement können 100 NSGs verwendet werden.

>[AZURE.NOTE]Endpunktbasierte ACLs und Netzwerksicherheitsgruppen können nicht für die gleiche VM-Instanz verwendet werden. Wenn Sie eine NSG verwenden möchten und bereits eine Endpunkt-ACL eingerichtet ist, entfernen Sie zuerst die Endpunkt-ACL. Informationen zur Vorgehensweise finden Sie unter [Verwalten von Zugriffssteuerungslisten (ACLs) für Endpunkte mithilfe von PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

### Funktionsweise von Netzwerksicherheitsgruppen

Netzwerksicherheitsgruppen unterscheiden sich von Endpunkt-basierten ACLs. Endpunkt-ACLs betreffen nur den öffentlichen Port, der über den Eingabeendpunkt verfügbar gemacht wird. Eine NSG kann für mehrere VM-Instanzen gelten und steuert den gesamten ein- und ausgehenden Datenverkehr des virtuellen Computers.

Eine Netzwerksicherheitsgruppe hat einen *Namen*, ist einer *Region* zugeordnet (einem der unterstützten Azure-Standorte) und verfügt über eine aussagekräftige Bezeichnung. Sie enthält zwei Arten von Regeln: eingehende Regeln und ausgehende Regeln. Eingehende Regeln werden auf die eingehenden Pakete eines virtuellen Computers angewendet, ausgehende Regeln gelten für ausgehende Pakete. Die Regeln werden auf dem Servercomputer angewendet, auf dem sich der virtuelle Computer befindet. Ein eingehendes oder ausgehendes Paket muss einer Zulassungsregel entsprechen. Andernfalls wird es gelöscht.

Regeln werden gemäß ihrer Priorität verarbeitet. So wird beispielsweise eine Regel mit einer niedrigeren Prioritätsnummer (z. B. 100) vor einer Regel mit höherer Prioritätsnummer (z. B. 200) verarbeitet. Sobald eine Übereinstimmung gefunden wurde, werden keine weiteren Regeln mehr verarbeitet.

Eine Regel umfasst Folgendes:

-	Name: Ein eindeutiger Bezeichner für die Regel.
-	Typ: Eingehend/ausgehend
-	Priorität: Eine ganze Zahl zwischen 100 und 4096 (Regeln werden von niedrig nach hoch verarbeitet)
-	Quell-IP-Adresse: CIDR des Quell-IP-Adressbereichs
-	Quellportbereich: Eine ganze Zahl oder ein Bereich von 0 und bis 65536
-	Ziel-IP-Adressbereich: CIDR des Ziel-IP-Adressbereichs
-	Ziel-Portbereich: Eine ganze Zahl oder ein Bereich von 0 bis 65536
-	Protokoll: TCP, UDP oder ' *'
-	Zugriff: Zulassen/Verweigern

### Standardregeln

Eine NSG enthält Standardregeln. Die Standardregeln können zwar nicht gelöscht werden, haben aber niedrigste Priorität und können somit durch selbst erstellte Regeln außer Kraft gesetzt werden. Die Standardregeln beschreiben die empfohlenen Standardeinstellungen der Plattform. Wie in den folgenden Standardregeln zu sehen, wird Datenverkehr aus einem bzw. in ein virtuelles Netzwerk in ein- und ausgehender Richtung zugelassen.

In ausgehender Richtung wird die Verbindung mit dem Internet zugelassen, in eingehender Richtung wird sie dagegen standardmäßig blockiert. Eine Standardregel ermöglicht dem Azure-Lastenausgleich die Überprüfung der Integrität einer VM. Sie können diese Regel außer Kraft setzen, wenn der virtuelle Computer oder die Gruppe von virtuellen Computern, für den bzw. für die die NSG gilt, nicht am Lastenausgleich beteiligt ist.

Die Standardregeln sind in den folgenden Tabellen aufgeführt.

**Eingehende Standardregeln**

Name |	Priorität |	Quell-IP |	Quellport |	Ziel-IP |	Zielport |	Protokoll |	Access
--- | --- | --- | --- | --- | --- | --- | ---
ALLOW VNET INBOUND | 65000 | VIRTUAL\_NETWORK |	* |	VIRTUAL\_NETWORK | * |	* | ZULASSEN
ALLOW AZURE LOAD BALANCER INBOUND | 65001 | AZURE\_LOADBALANCER | * | * | * | * | ZULASSEN
DENY ALL INBOUND | 65500 | * | * | * | * | * | VERWEIGERN

**Ausgehende Standardregeln**

Name |	Priorität |	Quell-IP |	Quellport |	Ziel-IP |	Zielport |	Protokoll |	Access
--- | --- | --- | --- | --- | --- | --- | ---
ALLOW VNET OUTBOUND | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | ZULASSEN
ALLOW INTERNET OUTBOUND | 65001 | * | * | INTERNET | * | * | ZULASSEN
DENY ALL OUTBOUND | 65500 | * | * | * | * | * | VERWEIGERN

### Besondere Infrastrukturregeln

NSG-Regeln sind explizit. Es wird nur der Datenverkehr zugelassen oder verweigert, der in der NSG angegeben ist. Es gibt jedoch zwei Arten von Datenverkehr, die unabhängig von der NSG-Angabe immer zulässig sind. Diese Vorkehrung wurde zur Unterstützung der Infrastruktur getroffen.

- **Virtuelle IP des Hostknotens**: Grundlegende Infrastrukturdienste wie DHCP, DNS und die Systemüberwachung werden über die virtualisierte Host-IP-Adresse 168.63.129.16 bereitgestellt. Diese öffentliche IP-Adresse gehört Microsoft und ist die einzige virtuelle IP-Adresse, die in allen Regionen zu diesem Zweck verwendet wird. Die IP-Adresse wird der physischen IP-Adresse des Servercomputers (Hostknotens) zugeordnet, der die VM hostet. Der Hostknoten fungiert als DHCP-Relay, als rekursiver DNS-Resolver und als Integritätstestquelle für den Load Balancer und den Computer. Eingehende Kommunikation für diese IP-Adresse darf nicht als Angriff betrachtet werden.
- **Lizenzierung (Schlüsselverwaltungsdienst)**: Die in den VMs verwendeten Windows-Images müssen lizenziert werden. Zu diesem Zweck wird eine Lizenzierungsanforderung an die Hostserver des Schlüsselverwaltungsdiensts gesendet, die solche Abfragen verarbeiten. Dafür wird immer der ausgehende Port 1688 verwendet.

### Standardtags

Standardtags sind vom System bereitgestellte Bezeichner für eine Kategorie von IP-Adressen. Standardtags können in benutzerdefinierten Regeln angegeben werden.

**Standardtags für NSGs**

Tag |	Beschreibung
--- | ---
VIRTUAL\_NETWORK |	Gibt Ihren gesamten Netzwerkadressbereich an. Dieser umfasst den Adressraum des virtuellen Netzwerks (IP-CIDR in Azure) sowie den gesamten verbundenen lokalen Adressraum (lokale Netzwerke). Dazu gehören auch die VNET-zu-VNET-Adressräume.
AZURE\_LOADBALANCER | Bezeichnet den Azure Infrastructure-Lastenausgleich und wird in eine IP-Adresse eines Azure-Datencenters umgewandelt, die als Ausgangspunkt für die Integritätstests von Azure fungiert. Nur erforderlich, wenn der virtuelle Computer oder die Gruppe virtueller Computer, der bzw. die der NSG zugeordnet ist, Teil eines Satzes mit Lastenausgleich ist.
INTERNET | Gibt den IP-Adressraum an, der außerhalb des virtuellen Netzwerks liegt und über das öffentliche Internet erreichbar ist. Dieser Bereich schließt auch den Azure-eigenen öffentlichen IP-Adressraum mit ein.

### Ports und Portbereiche

NSG-Regeln können für einen einzelnen Quell- oder Zielport oder einen Portbereich angegeben werden. Dieser Ansatz ist besonders dann hilfreich, wenn Sie eine Vielzahl von Ports für eine Anwendung (beispielsweise FTP) öffnen möchten. Der Bereich muss fortlaufend sein und kann nicht mit der Angabe einzelner Ports kombiniert werden. Um einen Bereich von Ports anzugeben, verwenden Sie das Bindestrichzeichen (-). Beispiel: **100-500**.

### ICMP-Datenverkehr

Mit den aktuellen NSG-Regeln können Sie TCP oder UDP, jedoch nicht ICMP als Protokoll angeben. ICMP-Datenverkehr ist in einem virtuellen Netzwerk jedoch standardmäßig über die eingehenden Regeln zulässig, die ein-/ausgehenden Datenverkehr für sämtliche Ports und Protokolle (*) innerhalb des virtuellen Netzwerks zulassen.

### Zuordnen einer NSG zu einer VM

Bei direkter Zuordnung einer NSG zu einer VM werden die Netzwerkzugriffsregeln in der NSG direkt auf den gesamten Datenverkehr angewendet, der für die VM bestimmt ist. Sobald die NSG mit Regeländerungen aktualisiert wird, gelten diese Änderungen innerhalb weniger Minuten für den Datenverkehr. Wenn die NSG von der VM getrennt wird, ändert sich deren Status in den vor der Zuordnung der NSG, d h. in die Systemstandardeinstellungen.

### Zuordnen einer NSG zu einem Subnetz

Bei Zuordnung einer NSG zu einem Subnetz werden die Netzwerkzugriffsregeln in der NSG auf alle VMs im Subnetz angewendet. Jede Aktualisierung der Zugriffsregeln in der NSG wird binnen weniger Minuten auf alle VMs im Subnetz angewendet.

### Zuordnen einer NSG zu einem Subnetz und einer VM

Sie können eine NSG einer VM und eine weitere NSG dem Subnetz zuordnen, in dem sich die VM befindet. Dieses Szenario wird unterstützt, um die VM mit zwei Schutzebenen zu versehen. Für eingehenden Datenverkehr befolgt das Paket die Zugriffsregeln, die im Subnetz angegeben sind, gefolgt von den Regeln in der VM. Falls ausgehend, befolgt das Paket zuerst die in der VM und dann die im Subnetz angegebenen Regeln (siehe unten).

![Zuordnen einer NSG zu einem Subnetz und einer VM](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

Die Zuordnung einer NSG zu einer VM oder einem Subnetz macht die Netzwerkzugriffsregeln äußerst explizit. Die Plattform fügt keine implizite Regel ein, um eingehenden Datenverkehr für einen bestimmten Port zuzulassen. In diesem Fall gilt: Wenn Sie einen Endpunkt für die VM erstellen, müssen Sie auch eine Regel erstellen, die Datenverkehr aus dem Internet zulässt. Andernfalls kann von außen nicht auf *VIP:{Port}* zugegriffen werden.

Angenommen, Sie erstellen eine neue VM und eine neue NSG. Anschließend ordnen Sie die NSG der VM zu. Dank der Regel ALLOW VNET INBOUND kann die VM mit anderen VMs im virtuellen Netzwerk kommunizieren. Dank der Regel „ALLOW INTERNET OUTBOUND“ kann der virtuelle Computer zudem ausgehende Verbindungen mit dem Internet herstellen. Später erstellen Sie einen Endpunkt an Port 80, um Datenverkehr für Ihre auf dem virtuellen Computer ausgeführte Website empfangen zu können. An die VIP (öffentliche virtuelle IP-Adresse) an Port 80 gerichtete Pakete aus dem Internet erreichen die VM erst, wenn Sie der NSG eine Regel wie in der folgenden Tabelle hinzufügen:

**Explizite Regel, die Datenverkehr an einem bestimmten Port zulässt**

Name |	Priorität |	Quell-IP |	Quellport |	Ziel-IP |	Zielport |	Protokoll |	Access
--- | --- | --- | --- | --- | --- | --- | ---
WEB | 100 | INTERNET | * | * | 80 | TCP | ZULASSEN

## Benutzerdefinierte Routen

Azure verwendet eine Routentabelle, um zu entscheiden, wie der IP-Datenverkehr basierend auf dem Ziel der einzelnen Pakete weitergeleitet wird. Obwohl Azure eine auf den Einstellungen des virtuellen Netzwerks beruhende Standardroutentabelle bereitstellt, müssen Sie dieser möglicherweise benutzerdefinierte Routen hinzufügen.

Der häufigste Grund für benutzerdefinierte Einträge in die Routentabelle ist die Verwendung eines virtuellen Geräts in der Azure-Umgebung. Berücksichtigen Sie das in der folgenden Abbildung dargestellte Szenario. Angenommen, Sie möchten sicherstellen, dass der gesamte Datenverkehr vom Front-End-Subnetz zur mittleren Ebene und den gesicherten Subnetzen ein virtuelles Firewallgerät durchläuft. Wenn Sie das Gerät einfach Ihrem virtuellen Netzwerk hinzufügen und mit den verschiedenen Subnetzen verbinden, erreichen Sie dieses Ziel nicht. Sie müssen zusätzlich die Routingtabelle für Ihr Subnetz ändern, um sicherzustellen, dass die Pakete an das virtuelle Firewallgerät weitergeleitet werden.

Dasselbe gilt, wenn Sie ein virtuelles NAT-Gerät zur Steuerung des Datenverkehrs zwischen dem virtuellen Azure-Netzwerk und dem Internet implementieren. Um sicherzustellen, dass das virtuelle Gerät verwendet wird, müssen Sie eine Route erstellen, die dafür sorgt, dass der gesamte Datenverkehr für das Internet zum virtuellen Gerät weitergeleitet werden muss.

### Routing

Pakete werden über ein TCP/IP-Netzwerk weitergeleitet, das auf einer Routentabelle beruht, die auf den einzelnen Knoten im physischen Netzwerk definiert wird. Eine Routentabelle ist eine Sammlung der einzelnen Routen, anhand derer entschieden wird, wohin die Pakete beruhend auf der Ziel-IP-Adresse weitergeleitet werden. Eine Route besteht aus den folgenden Elementen:

- Adresspräfix. Das Ziel-CIDR, das für die Route gilt, z. B. 10.1.0.0/16.
- Typ des nächsten Hops. Der Azure-Hop-Typ, an den das Paket gesendet werden soll. Mögliche Werte:
  - Lokal. Entspricht dem lokalen virtuellen Netzwerk. Wenn Sie z. B. im gleichen virtuellen Netzwerk über die beiden Subnetze 10.1.0.0/16 und 10.2.0.0/16 verfügen, weist die Route für die einzelnen Subnetze in der Routentabelle für den nächsten Hop den Wert "Lokal" auf.
  - VPN-Gateway. Entspricht einem Azure S2S-VPN-Gateway.
  - Internet. Entspricht dem Standard-Internet-Gateway der Azure-Infrastruktur.
  - Virtuelles Gerät. Entspricht einem virtuellen Gerät, das Sie Ihrem virtuellen Azure-Netzwerk hinzugefügt haben.
  - NULL. Entspricht einem schwarzen Loch. Pakete, die an ein schwarzes Loch weitergeleitet werden, werden überhaupt nicht weitergeleitet.
-	Wert für den nächsten Hop. Der Wert für den nächsten Hop enthält die IP-Adresse, an die die Pakete weitergeleitet werden sollen. Die Werte für den nächsten Hop dürfen nur für Routen verwendet werden, für die als Typ des nächsten Hops *Virtuelles Gerät* ausgewählt wurde. Der nächste Hop muss im Subnetz sein (die lokale Schnittstelle des virtuellen Geräts entsprechend der Netzwerk-ID), nicht in einem Remotesubnetz. 

![Routing](./media/best-practices-resource-manager-security/routing.png)

### Standardrouten

Jedes in einem virtuellen Netzwerk erstellte Subnetz wird automatisch einer Routentabelle zugeordnet, die folgende Standardroutenregeln enthält:

- Regel für das lokale VNet: Diese Regel wird automatisch für jedes Subnetz in einem virtuellen Netzwerk erstellt. Sie gibt an, dass eine direkte Verbindung zwischen den virtuellen Computern im VNet besteht und dazwischen kein nächster Hop vorhanden ist. Dadurch können die virtuellen Computer im gleichen Subnetz unabhängig von der Netzwerk-ID der virtuellen Computer miteinander kommunizieren, ohne eine Standardgatewayadresse anzufordern.
- Lokale Regel: Diese Regel gilt für den gesamten Datenverkehr an den lokalen Adressbereich. Hierbei wird das VPN-Gateway als der nächste Hop verwendet.
- Internetregel: Diese Regel behandelt den gesamten Datenverkehr in das öffentliche Internet und verwendet das Infrastruktur-Internetgateway als nächsten Hop für den gesamten Datenverkehr ins Internet.

### BGP-Routen

Zum Zeitpunkt der Erstellung dieses Artikels wird [ExpressRoute](expressroute/expressroute-introduction.md) noch nicht vom [Netzwerkressourcenanbieter](virtual-network/resource-groups-networking.md) für den Azure-Ressourcen-Manager unterstützt. Wenn Sie über eine ExpressRoute-Verbindung zwischen dem lokalen Netzwerk und Azure verfügen, können Sie BGP für das Weitergeben der Routen aus Ihrem lokalen Netzwerk an Azure aktivieren, sobald ExpressRoute vom Netzwerkressourcenanbieter unterstützt wird. Diese BGP-Routen werden auf die gleiche Weise wie Standard- und benutzerdefinierte Routen in den einzelnen Azure-Subnetzen verwendet. Weitere Informationen finden Sie unter [Einführung zu ExpressRoute](expressroute/expressroute-introduction.md).

>[AZURE.NOTE]Sobald ExpressRoute vom Netzwerkressourcenanbieter unterstützt wird, können Sie die Azure-Umgebung für die Verwendung der Tunnelerzwingung über das lokale Netzwerk konfigurieren, indem Sie für das Subnetz 0.0.0.0/0 eine benutzerdefinierte Route erstellen, die als nächsten Hop das VPN-Gateway verwendet. Dies funktioniert nur mit einem VPN-Gateway, nicht jedoch mit ExpressRoute. In ExpressRoute wird die Tunnelerzwingung über BGP konfiguriert.

### Benutzerdefinierte Routen

Die oben angegebenen Standardrouten können in der Azure-Umgebung nicht angezeigt werden. Zudem handelt es sich in den meisten Umgebungen um die einzigen erforderlichen Routen. Möglicherweise müssen Sie in bestimmten Fällen jedoch eine Routentabelle erstellen und dieser eine oder mehrere Routen hinzufügen. Die gilt z. B. für:

-	Tunnelerzwingung für das Internet über das lokale Netzwerk.
-	Verwendung von virtuellen Geräten in Ihrer Azure-Umgebung.

In den oben genannten Szenarios müssen Sie eine Routentabelle erstellen und dieser benutzerdefinierte Routen hinzufügen. Sie können über mehrere Routentabellen verfügen, wobei eine Routentabelle einem oder mehreren Subnetzen zugeordnet werden kann. Die einzelnen Subnetze können jeweils nur einer Routentabelle zugeordnet werden. Alle virtuellen Computer und Clouddienste in einem Subnetz verwenden die diesem Subnetz zugeordnete Routentabelle.

Für die Subnetze gelten solange Standardrouten, bis diesen eine Routentabelle zugeordnet wurde. Sobald eine Zuordnung vorhanden ist, erfolgt das Routing beruhend auf der [längsten Präfixübereinstimmung](https://en.wikipedia.org/wiki/Longest_prefix_match) (Longest Prefix Match, LPM) zwischen den benutzerdefinierten und den Standardrouten. Wenn mehrere Routen mit identischer längster Präfixübereinstimmung vorhanden sind, wird die Route in der folgenden Reihenfolge beruhend ihrem Ursprung ausgewählt:

1.	Benutzerdefinierte Route
2.	BGP-Route (bei Verwendung von ExpressRoute)
3.	Standardroute

>[AZURE.NOTE]Benutzerdefinierte Routen sind nur für virtuelle Azure-Computer und Clouddienste möglich. Wenn Sie beispielsweise zwischen dem lokalen Netzwerk und Azure ein virtuelles Firewallgerät hinzufügen möchten, müssen Sie eine benutzerdefinierte Route für die Azure-Routentabellen erstellen, die den gesamten Datenverkehr zum lokalen Adressraum an das virtuelle Gerät weiterleitet. Allerdings durchläuft der eingehende Datenverkehr vom lokalen Adressraum das VPN-Gateway oder den ExpressRoute-Kreis, um unter Umgehung des virtuellen Geräts direkt zur Azure-Umgebung zu gelangen.

### IP-Weiterleitung

Wie oben angeführt, ist einer der wichtigsten Gründe für das Erstellen einer benutzerdefinierten Route das Weiterleiten von Datenverkehr an virtuelle Geräte. Ein virtuelles Gerät ist letztlich nur ein virtueller Computer, der eine Anwendung zur Verarbeitung des Netzwerkverkehrs ausführt, z. B. eine Firewall oder ein NAT-Gerät.

Dieser virtuelle Computer muss eingehenden Datenverkehr empfangen können, der nicht an ihn selbst adressiert ist. Damit ein virtueller Computer an andere Ziele gerichteten Datenverkehr empfangen kann, müssen Sie auf dem virtuellen Computer die IP-Weiterleitung aktivieren.

## Nächste Schritte
- Um zu verstehen, wie Sicherheitsprinzipale mit dem richtigen Zugriff für das Arbeiten mit Ressourcen in Ihrer Organisation eingerichtet werden, lesen Sie [Authentifizieren eines Dienstprinzipals mit Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md).
- Wenn Sie den Zugriff auf eine Ressource sperren müssen, können Sie Verwaltungssperren verwenden. Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](resource-group-lock-resources.md).
- Informationen zum Konfigurieren von Routing und der IP-Weiterleitung finden Sie unter [Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure](virtual-network/virtual-networks-udr-how-to.md). 
- Eine Übersicht über die rollenbasierte Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung über das Microsoft Azure-Portal](role-based-access-control-configure.md).

<!---HONumber=AcomDC_1223_2015-->