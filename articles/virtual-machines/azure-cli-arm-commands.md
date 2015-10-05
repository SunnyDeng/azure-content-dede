<properties
	pageTitle="Verwenden der Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows Azure-Ressourcen mithilfe des Ressourcen-Manager-Bereitstellungsmodus verwalten."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-mangaer"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen einer Ressource mit dem Ressourcen-Manager-Bereitstellungsmodell. Sie können eine Ressource auch mit dem [klassischen Bereitstellungsmodell](virtual-machines-command-line-tools.md) erstellen.


In diesem Thema wird beschrieben, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure-CLI) im **ARM-Modus** Dienste in der Befehlszeile von Windows-, Mac- und Linux-Computern erstellen, verwalten und löschen können. Sie können die gleichen Aufgaben mithilfe der verschiedenen Bibliotheken der Azure-SDKs mit PowerShell und dem Azure-Portal ausführen.

Die Azure-Ressourcenverwaltung ermöglicht es Ihnen, eine Gruppe von Ressourcen – virtuelle Computer, Websites, Datenbanken usw. – als einzelne bereitstellbare Einheit zu erstellen. Sie können dann alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Für die Bereitstellung beschreiben Sie die Gruppenressourcen in einer JSON-Vorlage, die Sie dann für unterschiedliche Umgebungen, wie z. B. Tests, Staging und Produktion, verwenden können.

## Imperative und deklarative Ansätze

Wie beim [Dienstverwaltungsmodus (**asm**)](../virtual-machines-command-line-tools.md) bietet der **arm**-Modus der Azure-Befehlszeilenschnittstelle Ihnen Befehle, mit denen Sie imperativ Ressourcen in der Befehlszeile erstellen können. Wenn Sie beispielsweise `azure group create <groupname> <location>` eingeben, fordern Sie Azure auf, eine Ressourcengruppe zu erstellen, und mit `azure group deployment create <resourcegroup> <deploymentname>` weisen Sie Azure an, eine Bereitstellung einer beliebigen Anzahl von Elementen zu erstellen, die in einer Gruppe abgelegt werden sollen. Da jede Art von Ressource über imperative Befehle verfügt, können Sie sie miteinander verbinden, um recht komplexe Bereitstellungen zu erstellen.

Der Einsatz von Ressourcengruppen-_Vorlagen_, die eine Ressource beschreiben, ist jedoch ein deklarativer Ansatz, der sehr viel wirksamer ist und der es Ihnen ermöglicht, komplexe Bereitstellungen einer (fast) beliebigen Anzahl von Ressourcen für (nahezu) jeden Zweck zu automatisieren. Wenn Sie Vorlagen verwenden, ist der einzige imperative Befehl die Bereitstellung. Eine allgemeine Übersicht über Vorlagen, Ressourcen und Ressourcengruppen finden Sie unter [Übersicht über Azure-Ressourcengruppe](resource-groups-overview).

> [AZURE.NOTE]Neben den hier dokumentierten befehlsspezifischen Optionen gibt es drei weitere Optionen für die Anzeige detaillierter Ausgaben wie z. B. Anforderungsoptionen und Statuscodes. Der Parameter -v bietet ausführliche Ausgaben, und der Parameter -vv bietet noch detailliertere ausführliche Ausgaben. Mit der Option "--json" werden die Ergebnisse im reinen JSON-Format ausgegeben. Diese Option ist sehr nützlich für Skripterstellungsszenarien.
>
> Die Nutzung mit dem "--json"-Switch ist sehr häufig und ein wichtiger Teil für das Abrufen und Verstehen von Ergebnissen von Azure-CLI-Vorgängen, die Ressourceninformationen, Status und Protokolle zurückgeben, und auch für das Verwenden von Vorlagen. Sie können JSON-Parser-Tools installieren, z. B. **jq** oder **jsawk** oder Ihre bevorzugte Sprachbibliothek verwenden.

##Nutzungsanforderungen

Die Setupanforderungen für die Verwendung des **arm**-Modus mit der Azure-Befehlszeilenschnittstellen sind folgende:

- ein Azure-Konto ([hier erhalten Sie eine kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/))
- [Installation der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md)
- [Konfiguration der Azure-Befehlszeilenschnittstelle](../xplat-cli-connect.md) zum Verwenden einer Azure Active Directory-Identität oder eines Dienstprinzipals

Sobald Sie über ein Konto verfügen, und die Azure-Befehlszeilenschnittstelle installiert haben, müssen Sie

- in den **arm**-Modus wechseln, indem Sie `azure config mode arm` eingeben.
- Melden Sie sich bei Ihrem Azure-Konto durch Eingabe von `azure login` an, und geben Sie an den Eingabeaufforderungen Ihre Geschäfts- oder Schulidentität ein.

Geben Sie nun `azure` ein, um eine Liste der Befehle auf oberster Ebene anzuzeigen, die in den folgenden Abschnitten beschrieben sind.

## azure account: Verwalten Ihrer Kontoinformationen und Veröffentlichen von Einstellungen
Das Tool verwendet Ihre Azure-Kontoinformationen, um sich mit Ihrem Benutzerkonto zu verbinden. Sie finden diese Informationen im Azure-Portal in einer Einstellungsveröffentlichungsdatei, wie hier beschrieben. Sie können die Einstellungsveröffentlichungsdatei als persistente lokale Konfigurationseinstellung importieren, die das Tool dann in späteren Operationen verwendet. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

**Auflisten der importierten Abonnements**

	account list [options]

**Anzeigen von Details zu einem Abonnement**

	account show [options] [subscriptionNameOrId]

**Festlegen des aktuellen Abonnements**

	account set [options] <subscriptionNameOrId>

**Entfernen eines Abonnements oder einer Umgebung oder Löschen aller gespeicherten Konto- und Umgebungsinformationen**

	account clear [options]

**Befehle zum Verwalten Ihrer Kontoumgebung**

	account env list [options]
	account env show [options] [environment]
	account env add [options] [environment]
	account env set [options] [environment]
	account env delete [options] [environment]

## azure ad: Befehle zum Anzeigen von Active Directory-Objekten

**Befehle zum Anzeigen von Active Directory-Anwendungen**

	ad app create [options]
	ad app delete [options] <object-id>

**Befehle zum Anzeigen von Active Directory-Gruppen**

	ad group list [options]
	ad group show [options]

**Befehle zum Bereitstellen einer Active Directory-Untergruppe oder der Mitgliederinformationen**

	ad group member list [options] [objectId]

**Befehle zum Anzeigen von Active Directory-Dienstprinzipalen**

	ad sp list [options]
	ad sp show [options]
	ad sp create [options] <application-id>
	ad sp delete [options] <object-id>

**Befehle zum Anzeigen von Active Directory-Benutzern**

	ad user list [options]
	ad user show [options]

## azure availset: Befehle zum Verwalten der Verfügbarkeitsgruppen

**Erstellen einer Verfügbarkeitsgruppe innerhalb einer Ressourcengruppe**

	availset create [options] <resource-group> <name> <location> [tags]

**Auflisten der Verfügbarkeitsgruppen innerhalb einer Ressourcengruppe**

	availset list [options] <resource-group>

**Abrufen einer Verfügbarkeitsgruppe innerhalb einer Ressourcengruppe**

	availset show [options] <resource-group> <name>

**Löschen einer Verfügbarkeitsgruppe innerhalb einer Ressourcengruppe**

	availset delete [options] <resource-group> <name>

## azure config: Befehle zum Verwalten lokaler Einstellungen

**Auflisten von der Konfigurationseinstellungen der Azure-Befehlszeilenschnittstelle**

	config list [options]

**Löschen einer Konfigurationseinstellung**

	config delete [options] <name>

**Aktualisieren einer Konfigurationseinstellung**

	config set <name> <value>

**Festlegen des Arbeitsmodus der Azure-Befehlszeilenschnittstelle auf `arm` oder `asm`**

	config mode [options] <modename>


## azure feature: Befehle zum Verwalten von Kontofunktionen

**Auflisten aller für Ihr Abonnement verfügbaren Funktionen**

	feature list [options]

**Anzeigen einer Funktion**

	feature show [options] <providerName> <featureName>

**Registrieren einer in der Vorschau angezeigten Funktion eines Ressourcenanbieters**

	feature register [options] <providerName> <featureName>

## azure group: Befehle zum Verwalten der Ressourcengruppen

**Erstellen einer neuen Ressourcengruppe**

	group create [options] <name> <location>

**Festlegen von Tags für eine Ressourcengruppe**

	group set [options] <name> <tags>

**Löschen einer Ressourcengruppe**

	group delete [options] <name>

**Auflisten der Ressourcengruppen für Ihr Abonnement**

	group list [options]

**Anzeigen einer Ressourcengruppen für Ihr Abonnement**

	group show [options] <name>

**Befehle zum Verwalten von Protokollen der Ressourcengruppe**

	group log show [options] [name]

**Befehle zum Verwalten der Bereitstellung in einer Ressourcengruppe**

	group deployment create [options] [resource-group] [name]
	group deployment list [options] <resource-group> [state]
	group deployment show [options] <resource-group> [deployment-name]
	group deployment stop [options] <resource-group> [deployment-name]

**Befehle zum Verwalten Ihrer lokalen oder Katalogressourcengruppenvorlage**

	group template list [options]
	group template show [options] <name>
	group template download [options] [name] [file]
	group template validate [options] <resource-group>

## azure insights: Befehle im Zusammenhang mit der Überwachung von Insights (Ereignisse, Warnungsregeln, Einstellungen für die automatische Skalierung, Metriken)

**Abrufen von Vorgangsprotokolle für ein Abonnement, eine correlationId, eine Ressourcengruppe, Ressource oder einen Ressourcenanbieter**

	insights logs list [options]

## azure location: Befehle zum Abrufen verfügbarer Speicherorte für alle Ressourcentypen

**Auflisten der verfügbaren Speicherorte**

	location list [options]

## azure network: Befehle zum Verwalten von Netzwerkressourcen

**Befehle zum Verwalten virtueller Netzwerke**

	network vnet create [options] <resource-group> <name> <location>
Ermöglicht die Erstellung eines neuen virtuellen Networks. Im folgenden Beispiel erstellen wir ein virtuelles Netzwerk mit dem Namen "newvnet" für die Ressourcengruppe "myresourcegroup" in der Region "Westliche USA".


	azure network vnet create myresourcegroup newvnet "west us"
	info:    Executing command network vnet create
	+ Looking up virtual network "newvnet"
	+ Creating virtual network "newvnet"
	 Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet create command OK


Parameteroptionen:

 	-h, --help                                 output usage information
 	-v, --verbose                              use verbose output
	--json                                     use json output
 	-g, --resource-group <resource-group>      the name of the resource group
 	-n, --name <name>                          the name of the virtual network
 	-l, --location <location>                  the location
 	-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
      For example -a 10.0.0.0/24,10.0.1.0/24.
      Default value is 10.0.0.0/8

	-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
 	-t, --tags <tags>                          the tags set on this virtual network.
      Can be multiple. In the format of "name=value".
      Name is required and value is optional.
      For example, -t tag1=value1;tag2
	 -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet set [options] <resource-group> <name>

Aktualisiert eine virtuelle Netzwerkkonfiguration innerhalb einer Ressourcengruppe.

	azure network vnet set myresourcegroup newvnet

	info:    Executing command network vnet set
	+ Looking up virtual network "newvnet"
	+ Updating virtual network "newvnet"
	+ Loading virtual network state
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet set command OK

Parameteroptionen:

	   -h, --help                                 output usage information
	   -v, --verbose                              use verbose output
	   --json                                     use json output
	   -g, --resource-group <resource-group>      the name of the resource group
	   -n, --name <name>                          the name of the virtual network
	   -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
        For example -a 10.0.0.0/24,10.0.1.0/24.
        This list will be appended to the current list of address prefixes.
        The address prefixes in this list should not overlap between them.
        The address prefixes in this list should not overlap with existing address prefixes in the vnet.

	   -d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
        This list will be appended to the current list of DNS server IP addresses.

	   -t, --tags <tags>                          the tags set on this virtual network.
        Can be multiple. In the format of "name=value".
        Name is required and value is optional. For example, -t tag1=value1;tag2.
        This list will be appended to the current list of tags

	   --no-tags                                  remove all existing tags
	   -s, --subscription <subscription>          the subscription identifier
<BR>

	network vnet list [options] <resource-group>

Der Befehl ermöglicht es Ihnen, alle virtuellen Netzwerke in einer Ressourcengruppe aufzulisten.


	C:\>azure network vnet list myresourcegroup

	info:    Executing command network vnet list
	+ Listing virtual networks
		data:    ID
       Name      Location  Address prefixes  DNS servers
	data:    -------------------------------------------------------------------
	------  --------  --------  ----------------  -----------
	data:    /subscriptions/###############################/resourceGroups/
	wvnet   newvnet   westus    10.0.0.0/8
	info:    network vnet list command OK

Parameteroptionen:


      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

	network vnet show [options] <resource-group> <name>
Der Befehl zeigt die Eigenschaften des virtuellen Netzwerks in einer Ressourcengruppe.

	azure network vnet show -g myresourcegroup -n newvnet

	info:    Executing command network vnet show
	+ Looking up virtual network "newvnet"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
	data:    Name:                 newvnet
	data:    Type:                 Microsoft.Network/virtualNetworks
	data:    Location:             westus
	data:    Tags:
	data:    Provisioning state:   Succeeded
	data:    Address prefixes:
	data:     10.0.0.0/8
	data:    DNS servers:
	data:    Subnets:
	data:
	info:    network vnet show command OK
<BR>

	network vnet delete [options] <resource-group> <name>
Der Befehl entfernt ein virtuelles Netzwerk.

	azure network vnet delete myresourcegroup newvnetX

	info:    Executing command network vnet delete
	+ Looking up virtual network "newvnetX"
	Delete virtual network newvnetX? [y/n] y
	+ Deleting virtual network "newvnetX"
	info:    network vnet delete command OK

Parameteroptionen:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**Befehle zum Verwalten von Subnetzen virtueller Netzwerke**

	network vnet subnet create [options] <resource-group> <vnet-name> <name>
Dieser Befehl ermöglicht es Ihnen, einem vorhandenen virtuellen Netzwerk ein anderes Subnetz hinzufügen.

	azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

	info:    Executing command network vnet subnet create
	+ Looking up the subnet "subnet"
	+ Creating subnet "subnet"
	+ Looking up the subnet "subnet"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Name:                      subnet
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet create command OK

Parameteroptionen:

     -h, --help                                                       output usage information
     -v, --verbose                                                    use verbose output
		 --json                                                           use json output
	 -g, --resource-group <resource-group>                            the name of the resource group
	 -e, --vnet-name <vnet-name>                                      the name of the virtual network
     -n, --name <name>                                                the name of the subnet
     -a, --address-prefix <address-prefix>                            the address prefix
     -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
           e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
     -o, --network-security-group-name <network-security-group-name>  the network security group name
     -s, --subscription <subscription>                                the subscription identifier

<BR>

	network vnet subnet set [options] <resource-group> <vnet-name> <name>

Legt ein bestimmtes Subnetz eines virtuellen Netzwerks innerhalb einer Ressourcengruppe fest.


	C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet list [options] <resource-group> <vnet-name>

Listet alle Subnetze eines virtuellen Netzwerks für ein bestimmtes virtuelles Netzwerk innerhalb einer Ressourcengruppe auf.

	azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "subnet1"
	+ Setting subnet "subnet1"
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet set command OK
<BR>

	network vnet subnet show [options] <resource-group> <vnet-name> <name>
Zeigt die Eigenschaften für das Subnetz eines virtuellen Netzwerks an.

	azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet show
	+ Looking up the subnet "subnet1"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
	.Network/virtualNetworks/newvnet/subnets/subnet1
	data:    Name:                      subnet1
	data:    Type:                      Microsoft.Network/virtualNetworks/subnets
	data:    Provisioning state:        Succeeded
	data:    Address prefix:            10.0.1.0/24
	info:    network vnet subnet show command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-e, --vnet-name <vnet-name>            the name of the virtual network
	-n, --name <name>                      the name of the subnet
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
Entfernt ein Subnetz aus einem vorhandenen virtuellen Netzwerk.

	azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

	info:    Executing command network vnet subnet delete
	+ Looking up the subnet "subnet1"
	Delete subnet "subnet1"? [y/n] y
	+ Deleting subnet "subnet1"
	info:    network vnet subnet delete command OK

Parameteroptionen:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-e, --vnet-name <vnet-name>            the name of the virtual network
 	-n, --name <name>                      the subnet name
 	-s, --subscription <subscription>      the subscription identifier
 	-q, --quiet                            quiet mode, do not ask for delete confirmation

**Befehle zum Verwalten des Lastenausgleich**

	network lb create [options] <resource-group> <name> <location>
Erstellt eine Lastenausgleichsgruppe.

	azure network lb create -g myresourcegroup -n mylb -l westus

	info:    Executing command network lb create
	+ Looking up the load balancer "mylb"
	+ Creating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb create command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-l, --location <location>              the location
	-t, --tags <tags>                      the list of tags.
     Can be multiple. In the format of "name=value".
     Name is required and value is optional. For example, -t tag1=value1;tag2
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb list [options] <resource-group>
Listet Lastenausgleichsressourcen innerhalb einer Ressourcengruppe auf.

	azure network lb list myresourcegroup

	info:    Executing command network lb list
	+ Getting the load balancers
	data:    Name  Location
	data:    ----  --------
	data:    mylb  westus
	info:    network lb list command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb show [options] <resource-group> <name>

Zeigt die Lastenausgleichsinformationen eines bestimmten Lastenausgleichs innerhalb einer Ressourcengruppe an.

	azure network lb show myresourcegroup mylb -v

	info:    Executing command network lb show
	verbose: Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	data:    Name:                         mylb
	data:    Type:                         Microsoft.Network/loadBalancers
	data:    Location:                     westus
	data:    Provisioning state:           Succeeded
	info:    network lb show command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb delete [options] <resource-group> <name>

Löscht die Lastenausgleichsressourcen.

	azure network lb delete  myresourcegroup mylb

	info:    Executing command network lb delete
	+ Looking up the load balancer "mylb"
	Delete load balancer "mylb"? [y/n] y
	+ Deleting load balancer "mylb"
	info:    network lb delete command OK

Parameteroptionen:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-n, --name <name>                      the name of the load balancer
 	-q, --quiet                            quiet mode, do not ask for delete confirmation
 	-s, --subscription <subscription>      the subscription identifier

**Befehle zum Verwalten von Prüfpunkten eines Lastenausgleichs**

	network lb probe create [options] <resource-group> <lb-name> <name>

Erstellt die Überprüfungskonfiguration für den Integritätsstatus im Lastenausgleich. Denken Sie daran, dass zum Ausführen dieses Befehls der Lastenausgleich eine "frontend-ip"-Ressource benötigt. (Überprüfen Sie den Befehl "azure network frontend-ip", um dem Lastenausgleich eine IP-Adresse zuzuweisen.)

	azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

	info:    Executing command network lb probe create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe create command OK

Parameteroptionen:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-p, --protocol <protocol>              the probe protocol
	-o, --port <port>                      the probe port
	-f, --path <path>                      the probe path
	-i, --interval <interval>              the probe interval in seconds
	-c, --count <count>                    the number of probes
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb probe set [options] <resource-group> <lb-name> <name>

Aktualisiert eine vorhandene Lastenausgleichsüberprüfung mit den entsprechenden neuen Werten.

	azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

	info:    Executing command network lb probe set
		+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	info:    network lb probe set command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the probe
	-e, --new-probe-name <new-probe-name>  the new name of the probe
	-p, --protocol <protocol>              the new value for probe protocol
	-o, --port <port>                      the new value for probe port
	-f, --path <path>                      the new value for probe path
	-i, --interval <interval>              the new value for probe interval in seconds
	-c, --count <count>                    the new value for number of probes
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb probe list [options] <resource-group> <lb-name>

Listet die Überprüfungseigenschaften für eine Lastenausgleichsgruppe auf.

	C:\>azure network lb probe list -g myresourcegroup -l mylb

	info:    Executing command network lb probe list
	+ Looking up the load balancer "mylb"
	data:    Name       Protocol  Port  Path  Interval  Count
	data:    ---------  --------  ----  ----  --------  -----
	data:    mylbprobe  Tcp       443         300       2
	info:    network lb probe list command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier


	network lb probe delete [options] <resource-group> <lb-name> <name>
Entfernt den Prüfpunkt, der für den Lastenausgleich erstellt wurde.

	azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

	info:    Executing command network lb probe delete
	+ Looking up the load balancer "mylb"
	Delete a probe "mylbprobe?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb probe delete command OK

**Befehle zum Verwalten von Front-End-IP-Konfigurationen eines Lastenausgleichs**

	network lb frontend-ip create [options] <resource-group> <lb-name> <name>
Erstellt eine Front-End-IP-Konfiguration für eine vorhandenen Lastenausgleichsgruppe.

	azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

	info:    Executing command network lb frontend-ip create
	+ Looking up the load balancer "mylb"
	+ Looking up the subnet "subnet"
	+ Creating frontend IP configuration "myfrontendip"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
	/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:           10.0.1.4
	data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
	data:    Public IP address:
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip create command OK

<BR>

	network lb frontend-ip set [options] <resource-group> <lb-name> <name>

Ermöglicht die Aktualisierung einer vorhandenen Konfiguration einer Front-End-IP-Adresse. Mit dem nachfolgenden Befehl wird der bestehenden Lastenausgleichs-Front-End-IP namens "myfrontendip" eine öffentliche IP namens "mypubip5" hinzugefügt.

	azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

	info:    Executing command network lb frontend-ip set
	+ Looking up the load balancer "mylb"
	+ Looking up the public ip "mypubip5"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Name:                         myfrontendip
	data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
	data:    Provisioning state:           Succeeded
	data:    Private IP allocation method: Dynamic
	data:    Private IP address:
	data:    Subnet:
	data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
	data:    Inbound NAT rules
	data:    Outbound NAT rules
	data:    Load balancing rules
	data:
	info:    network lb frontend-ip set command OK

Parameteroptionen:

	-h, --help                                                         output usage information
	-v, --verbose                                                      use verbose output
	--json                                                             use json output
	-g, --resource-group <resource-group>                              the name of the resource group
	-l, --lb-name <lb-name>                                            the name of the load balancer
	-n, --name <name>                                                  the name of the frontend ip configuration
	-a, --private-ip-address <private-ip-address>                      the private ip address
	-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
	-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-i, --public-ip-name <public-ip-name>                              the public ip name.
	This public ip must exist in the same resource group as the lb.
	Please use public-ip-id if that is not the case.
	-b, --subnet-id <subnet-id>                                        the subnet id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
	-e, --subnet-name <subnet-name>                                    the subnet name
	-m, --vnet-name <vnet-name>                                        the virtual network name.
	This virtual network must exist in the same resource group as the lb.
	Please use subnet-id if that is not the case.
	-s, --subscription <subscription>                                  the subscription identifier

<BR>

	network lb frontend-ip list [options] <resource-group> <lb-name>

Listet alle Front-End-IP-Ressourcen auf, die für den Lastenausgleich konfiguriert wurden.

	azure network lb frontend-ip list -g myresourcegroup -l mylb

	info:    Executing command network lb frontend-ip list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Private IP allocation method  Subnet
	data:    -----------  ------------------  ----------------------------  ------
	data:    myprivateip  Succeeded           Dynamic
	info:    network lb frontend-ip list command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
Löscht das Front-End-IP-Objekt, das dem Lastenausgleich zugeordnet ist.

	network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
	info:    Executing command network lb frontend-ip delete
	+ Looking up the load balancer "mylb"
	Delete frontend ip configuration "myfrontendip"? [y/n] y
	+ Updating load balancer "mylb"

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the frontend ip configuration
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Befehle zum Verwalten von Back-End-Adresspools eines Lastenausgleichs**

	network lb address-pool create [options] <resource-group> <lb-name> <name>

Erstellt einen Back-End-Adresspool für einen Lastenausgleich.

	azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

	info:    Executing command network lb address-pool create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
	data:    Name:                      myaddresspool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool create command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool add [options] <resource-group> <lb-name> <name>

Dank eines Back-End-Adresspoolbereichs weiß ein Lastenausgleich, welche Ressourcen zum Weiterleiten von eingehendem Netzwerkdatenverkehr von seinem Endpunkt mithilfe des Azure-Ressourcen-Manager verwendet werden. Nachdem Sie den Back-End-Adresspoolbereich (siehe Befehl "azure network lb address-pool create") erstellt und ihm einen Namen gegeben haben, müssen Sie die Endpunkte hinzufügen, die jetzt von einer Ressource mit dem Namen "Netzwerkschnittstellen" definiert werden.

Um den Back-End-Adressbereich konfigurieren zu können, benötigen Sie mindestens eine "Netzwerkschnittstelle" (weitere Informationen finden Sie unter der Befehlszeile "azure network lb nic").

Im folgenden Beispiel wurde eine zuvor erstellte Netzwerkschnittstelle "nic1" verwendet, um den Back-End-Adresspoolbereich zu erstellen.

	azure network lb address-pool add -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool add
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:     id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/networkInterfaces/nic1/ipConfigurations/NIC-config
	data:    Load balancing rules:
	data:
	info:    network lb address-pool add command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier

<BR>

	network lb address-pool remove [options] <resource-group> <lb-name> <name>

Entfernt eine Netzwerkschnittstelle aus dem Back-End-IP-Adresspoolbereich.

	azure network lb address-pool remove -g myresourcegroup -l mylb -n mybackendpool -a nic1

	info:    Executing command network lb address-pool remove
	+ Looking up the load balancer "mylb"
	+ Getting network interfaces
	+ Updating network interface "nic1"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Name:                      mybackendpool
	data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
	data:    Provisioning state:        Succeeded
	data:    Backend IP configurations:
	data:    Load balancing rules:
	data:
	info:    network lb address-pool remove command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-i, --vm-id <vm-id>                    the virtual machine identifier.
	e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
	-m, --vm-name <vm-name>                the name of the virtual machine
	-d, --nic-id <nic-id>                  the network interface identifier.
	e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
	-a, --nic-name <nic-name>              the name of the network interface
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb address-pool list [options] <resource-group> <lb-name>

Listet den Back-End-IP-Adresspoolbereich für eine bestimmte Ressourcengruppe auf.

	azure network lb address-pool list -g myresourcegroup -l mylb

	info:    Executing command network lb address-pool list
	+ Looking up the load balancer "mylb"
	data:    Name           Provisioning state
	data:    -------------  ------------------
	data:    mybackendpool  Succeeded
	info:    network lb address-pool list command OK

Parameteroptionen:

 	-h, --help                             output usage information
 	-v, --verbose                          use verbose output
 	--json                                 use json output
 	-g, --resource-group <resource-group>  the name of the resource group
 	-l, --lb-name <lb-name>                the name of the load balancer
 	-s, --subscription <subscription>      the subscription identifier

<BR> network lb address-pool delete [Optionen] <resource-group> <lb-name> <name>

Entfernt die Back-End-IP-Pool-Bereichsressource aus dem Lastenausgleichsmodul.

	azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

	info:    Executing command network lb address-pool delete
	+ Looking up the load balancer "mylb"
	Delete backend address pool "mybackendpool"? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb address-pool delete command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the backend address pool
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Befehle zum Verwalten der Lastenausgleichsregeln**

	network lb rule create [options] <resource-group> <lb-name> <name>
Erstellen Sie Lastenausgleichsregeln.

Sie können eine Lastenausgleichsregel erstellen, indem Sie den Front-End-Endpunkt für den Lastenausgleich konfigurieren, sowie den Back-End-Adresspoolbereich, der den eingehenden Netzwerkverkehr empfängt. Einstellungen umfassen auch die Ports für den Front-End-IP-Endpunkt und Ports für den Back-End-Adresspoolbereich.

Im folgenden Beispiel wird veranschaulicht, wie Sie eine Lastenausgleichsregel erstellen, in der ein Front-End-Endpunkt zur Überwachung von Port 80 TCP festgelegt wird und die den Netzwerkverkehr zum Lastenausgleich an Port 8080 für den Back-End-Adresspoolbereich sendet.

	azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


	info:    Executing command network lb rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
	data:    Name:                      mylbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule create command OK

<BR>

	network lb rule set [options] <resource-group> <lb-name> <name>

Aktualisiert eine bestehende Lastenausgleichsregel, die in einer bestimmten Ressourcengruppe festgelegt ist. Im folgenden Beispiel wurde der Regelname von "mylbrule" in "mynewlbrule" geändert.

	azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

	info:    Executing command network lb rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Loading rule state
	data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
	data:    Name:                      mynewlbrule
	data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	data:    Protocol:                  Tcp
	data:    Frontend port:             80
	data:    Backend port:              8080
	data:    Enable floating IP:        false
	data:    Idle timeout in minutes:   10
	data:    Probes
	data:
	info:    network lb rule set command OK

Parameteroptionen:

	-h, --help                                         output usage information
	-v, --verbose                                      use verbose output
	--json                                             use json output
	-g, --resource-group <resource-group>              the name of the resource group
	-l, --lb-name <lb-name>                            the name of the load balancer
	-n, --name <name>                                  the name of the rule
	-r, --new-rule-name <new-rule-name>                new rule name
	-p, --protocol <protocol>                          the rule protocol
	-f, --frontend-port <frontend-port>                the frontend port
	-b, --backend-port <backend-port>                  the backend port
	-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
	-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
	-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
	-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
	-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
	-s, --subscription <subscription>                  the subscription identifier


	network lb rule list [options] <resource-group> <lb-name>

Listet alle Lastenausgleichsregeln auf, die für ein Lastenausgleichsmodul in einer bestimmten Ressourcengruppe konfiguriert wurden.

	azure network lb rule list -g myresourcegroup -l mylb

	info:    Executing command network lb rule list
	+ Looking up the load balancer "mylb"
	data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

	data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
	info:    network lb rule list command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier

	network lb rule delete [options] <resource-group> <lb-name> <name>

Löscht eine Lastenausgleichsregel.

	azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

	info:    Executing command network lb rule delete
	+ Looking up the load balancer "mylb"
	Delete load balancing rule mynewlbrule? [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb rule delete command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Befehle zum Verwalten von eingehenden Lastenausgleichs-NAT-Regeln**

	network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
Erstellt eine eingehende NAT-Regel für den Lastenausgleich.

Im folgenden Beispiel haben wir eine NAT-Regel von der Front-End-IP-Adresse, die zuvor definiert wurde, (weitere Informationen finden Sie unter "Befehl "azure network frontend-ip") mit einem eingehenden Überwachungsport und dem ausgehenden Port erstellt, an den das Lastenausgleichsmodul den Netzwerkdatenverkehr sendet.


	azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule create
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              80
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule create command OK

Parameteroptionen:

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id <vm-id>                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
	Please use vm-id if that is not the case.
	this parameter will be ignored if --vm-id is specified
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
Aktualisiert eine vorhandene eingehende NAT-Regel. Im folgenden Beispiel wurde der eingehenden Überwachungsport von 80 in 81 geändert.

	azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

	info:    Executing command network lb inbound-nat-rule set
	+ Looking up the load balancer "mylb"
	+ Updating load balancer "mylb"
	+ Looking up the load balancer "mylb"
	data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
	data:    Name:                      myinboundnat
	data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
	data:    Provisioning state:        Succeeded
	data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
	data:    Backend IP configuration
	data:    Protocol                   Tcp
	data:    Frontend port              81
	data:    Backend port               8080
	data:    Enable floating IP         false
	info:    network lb inbound-nat-rule set command OK

Parameteroptionen:

	-h, --help                                     output usage information
	-v, --verbose                                  use verbose output
	--json                                         use json output
	-g, --resource-group <resource-group>          the name of the resource group
	-l, --lb-name <lb-name>                        the name of the load balancer
	-n, --name <name>                              the name of the inbound NAT rule
	-p, --protocol <protocol>                      the rule protocol [tcp,udp]
	-f, --frontend-port <frontend-port>            the frontend port [0-65535]
	-b, --backend-port <backend-port>              the backend port [0-65535]
	-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
	-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
	-m, --vm-id [vm-id]                            the VM id.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
	-a, --vm-name <vm-name>                        the VM name.
	This virtual machine must exist in the same resource group as the lb.
	Please use vm-id if that is not the case
	-s, --subscription <subscription>              the subscription identifier
<BR>

	network lb inbound-nat-rule list [options] <resource-group> <lb-name>

Listet alle eingehenden NAT-Regel für den Lastenausgleich auf.

	azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

	info:    Executing command network lb inbound-nat-rule list
	+ Looking up the load balancer "mylb"
	data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
	data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
	---------------------
	data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

	info:    network lb inbound-nat-rule list command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-s, --subscription <subscription>      the subscription identifier
<BR>

	network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

Löscht die NAT-Regel für den Lastenausgleich in einer bestimmten Ressourcengruppe.

	azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

	info:    Executing command network lb inbound-nat-rule delete
	+ Looking up the load balancer "mylb"
	Delete inbound NAT rule "myinboundnat?" [y/n] y
	+ Updating load balancer "mylb"
	info:    network lb inbound-nat-rule delete command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-l, --lb-name <lb-name>                the name of the load balancer
	-n, --name <name>                      the name of the inbound NAT rule
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier

**Befehle zum Verwalten öffentlicher IP-Adressen**

	network public-ip create [options] <resource-group> <name> <location>
Erstellt eine öffentliche IP-Ressource. Sie erstellen die öffentliche IP-Ressource und ordnen sie einem Domänennamen zu.

	azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
	info:    Executing command network public-ip create
	+ Looking up the public ip "mytestpublicip1"
	+ Creating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Dynamic
	data:    Idle timeout:         4
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip create command OK


Parameteroptionen: -h, --help output usage information -v, --verbose use verbose output --json use json output -g, --resource-group <resource-group> the name of the resource group -n, --name <name> the name of the public ip -l, --location <location> the location -d, --domain-name-label <domain-name-label> the domain name label. Dies legt DNS auf <domain-name-label>.<location>.cloudapp.azure.com fest. -a, --allocation-method <allocation-method> die Zuordnungsmethode[Static][Dynamic] -i, --idletimeout <idletimeout> das Leerlauftimeout in Minuten -f, --reverse-fqdn <reverse-fqdn> der Reverse-FQDN -t, --tags <tags> die Tagliste. Can be multiple. In the format of "name=value". Name is required and value is optional. For example, -t tag1=value1;tag2 -s, --subscription <subscription> the subscription identifier <br>

	network public-ip set [options] <resource-group> <name>
Aktualisiert die Eigenschaften einer vorhandenen öffentlichen IP-Ressource. Im folgenden Beispiel wurde die öffentliche IP-Adresse von "Dynamisch" in "Statisch" geändert.

	azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
	info:    Executing command network public-ip set
	+ Looking up the public ip "mytestpublicip1"
	+ Updating public ip address "mytestpublicip1"
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
	data:    Name:                 mytestpublicip1
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip set command OK

Parameteroptionen:

	-h, --help                                   output usage information
	-v, --verbose                                use verbose output
	--json                                       use json output
	-g, --resource-group <resource-group>        the name of the resource group
	-n, --name <name>                            the name of the public ip
	-d, --domain-name-label [domain-name-label]  the domain name label.
	This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
	-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
	-i, --idletimeout <idletimeout>              the idle timeout in minutes
	-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
	-t, --tags <tags>                            the list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	--no-tags                                    remove all existing tags
	-s, --subscription <subscription>            the subscription identifier

<br> network public-ip list [Optionen] <resource-group> Listet alle öffentlichen IP-Ressourcen innerhalb einer Ressourcengruppe auf.

	azure network public-ip list -g myresourcegroup

	info:    Executing command network public-ip list
	+ Getting the public ip addresses
	data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
	data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
	data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
	data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
	data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-s, --subscription <subscription>      the subscription identifier
<BR> network public-ip show [Optionen] <resource-group> <name> Zeigt die Eigenschaften der öffentlichen IP für eine öffentliche IP-Ressource innerhalb einer Ressourcengruppe an.

	azure network public-ip show -g myresourcegroup -n mytestpublicip

	info:    Executing command network public-ip show
	+ Looking up the public ip "mytestpublicip1"
	data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
	data:    Name:                 mytestpublicip
	data:    Type:                 Microsoft.Network/publicIPAddresses
	data:    Location:             eastus
	data:    Provisioning state:   Succeeded
	data:    Allocation method:    Static
	data:    Idle timeout:         4
	data:    IP Address:           (static IP address)
	data:    Domain name label:    azureclitest
	data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
	info:    network public-ip show command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-s, --subscription <subscription>      the subscription identifier


	network public-ip delete [options] <resource-group> <name>

Löscht eine öffentliche IP-Ressource.

	azure network public-ip delete -g group-1 -n mypublicipname
	info:    Executing command network public-ip delete
	+ Looking up the public ip "mypublicipname"
	Delete public ip address "mypublicipname"? [y/n] y
	+ Deleting public ip address "mypublicipname"
	info:    network public-ip delete command OK

Parameteroptionen:

	-h, --help                             output usage information
	-v, --verbose                          use verbose output
	--json                                 use json output
	-g, --resource-group <resource-group>  the name of the resource group
	-n, --name <name>                      the name of the public IP
	-q, --quiet                            quiet mode, do not ask for delete confirmation
	-s, --subscription <subscription>      the subscription identifier


**Befehle zum Verwalten von Netzwerkschnittstellen**

	network nic create [options] <resource-group> <name> <location>
Erstellt eine Ressource mit dem Namen Netzwerkschnittstelle (NIC), die für den Lastenausgleich verwendet oder einer virtuellen Maschine zugeordnet werden kann.

	azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

	info:    Executing command network nic create
	+ Looking up the network interface "testnic1"
	+ Looking up the subnet "subnet-1"
	+ Creating network interface "testnic1"
	+ Looking up the network interface "testnic1"
	data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
	data:    Name:                   testnic1
	data:    Type:                   Microsoft.Network/networkInterfaces
	data:    Location:               eastus
	data:    Provisioning state:     Succeeded
	data:    IP configurations:
	data:       Name:                         NIC-config
	data:       Provisioning state:           Succeeded
	data:       Private IP address:           10.0.0.5
	data:       Private IP Allocation Method: Dynamic
	data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1

Parameteroptionen:

	-h, --help                                                       output usage information
	-v, --verbose                                                    use verbose output
	--json                                                           use json output
	-g, --resource-group <resource-group>                            the name of the resource group
	-n, --name <name>                                                the name of the network interface
	-l, --location <location>                                        the location
	-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
	-o, --network-security-group-name <network-security-group-name>  the network security group name.
	This network security group must exist in the same resource group as the nic.
	Please use network-security-group-id if that is not the case.
	-i, --public-ip-id <public-ip-id>                                the public IP identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
	-p, --public-ip-name <public-ip-name>                            the public IP name.
	This public ip must exist in the same resource group as the nic.
	Please use public-ip-id if that is not the case.
	-a, --private-ip-address <private-ip-address>                    the private IP address
	-u, --subnet-id <subnet-id>                                      the subnet identifier.
	e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
	--subnet-name <subnet-name>                                  the subnet name
	-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
	-t, --tags <tags>                                                the comma seperated list of tags.
	Can be multiple. In the format of "name=value".
	Name is required and value is optional.
	For example, -t tag1=value1;tag2
	-s, --subscription <subscription>                                the subscription identifier
	data:
	info:    network nic create command OK

<BR>

	network nic set [options] <resource-group> <name>

	network nic list [options] <resource-group>
	network nic show [options] <resource-group> <name>
	network nic delete [options] <resource-group> <name>

**Befehle zum Verwalten von Netzwerksicherheitsgruppen**

	network nsg create [options] <resource-group> <name> <location>
	network nsg set [options] <resource-group> <name>
	network nsg list [options] <resource-group>
	network nsg show [options] <resource-group> <name>
	network nsg delete [options] <resource-group> <name>

**Befehle zum Verwalten von Regeln von Netzwerksicherheitsgruppen**

	network nsg rule create [options] <resource-group> <nsg-name> <name>
	network nsg rule set [options] <resource-group> <nsg-name> <name>
	network nsg rule list [options] <resource-group> <nsg-name>
	network nsg rule show [options] <resource-group> <nsg-name> <name>
	network nsg rule delete [options] <resource-group> <nsg-name> <name>

**Befehle zum Verwalten des Traffic Manager-Profils**

	network traffic-manager profile create [options] <resource-group> <name>
	network traffic-manager profile set [options] <resource-group> <name>
	network traffic-manager profile list [options] <resource-group>
	network traffic-manager profile show [options] <resource-group> <name>
	network traffic-manager profile delete [options] <resource-group> <name>
	network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**Befehle zum Verwalten der Traffic Manager-Endpunkte**

	network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
	network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
	network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**Befehle zum Verwalten virtueller Netzwerkgateways**

	network gateway list [options] <resource-group>

## azure provider: Befehle zum Verwalten von Ressourcenanbieter-Registrierungen

**Auflisten der zuletzt registrierten Anbieter in ARM**

	provider list [options]

**Anzeigen von Details zum angeforderten Anbieternamespace**

	provider show [options] <namespace>

**Registrieren der Anbieter für das Abonnement**

	provider register [options] <namespace>

**Aufheben der Anbieterregistrierung für das Abonnement**

	provider unregister [options] <namespace>

## azure resource: Befehle zum Verwalten der Ressourcen

**Erstellen einer Ressource in einer Ressourcengruppe**

	resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**Aktualisieren einer Ressource in einer Ressourcengruppe ohne Vorlagen oder Parameter**

	resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**Auflisten der Ressourcen**

	resource list [options] [resource-group]

**Abrufen einer Ressource in einer Ressourcengruppe oder in einem Abonnement**

	resource show [options] <resource-group> <name> <resource-type> <api-version>

**Löschen einer Ressource in einer Ressourcengruppe**

	resource delete [options] <resource-group> <name> <resource-type> <api-version>

## azure role: Befehle zum Verwalten Ihrer Azure-Rollen

**Abrufen aller verfügbaren Rollendefinitionen**

	role list [options]

**Abrufen einer verfügbaren Rollendefinition**

	role show [options] [name]

**Befehle zum Verwalten Ihrer Rollenzuweisung**

	role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
	role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## azure storage: Befehle zum Verwalten Ihrer Speicherobjekte

**Befehle zum Verwalten Ihrer Speicherkonten**

	storage account list [options]
	storage account show [options] <name>
	storage account create [options] <name>
	storage account set [options] <name>
	storage account delete [options] <name>

**Befehle zum Verwalten Ihrer Speicherkontoschlüssel**

	storage account keys list [options] <name>
	storage account keys renew [options] <name>

**Befehle zum Anzeigen der Speicherverbindungszeichenfolge**

	storage account connectionstring show [options] <name>

**Befehle zum Verwalten Ihrer Speichercontainer**

	storage container list [options] [prefix]
	storage container show [options] [container]
	storage container create [options] [container]
	storage container delete [options] [container]
	storage container set [options] [container]

**Befehle zum Verwalten von Shared Access Signatures Ihres Speichercontainers**

	storage container sas create [options] [container] [permissions] [expiry]

**Befehle zum Verwalten von gespeicherten Zugriffsrichtlinien für Ihren Speichercontainer**

	storage container policy create [options] [container] [name]
	storage container policy show [options] [container] [name]
	storage container policy list [options] [container]
	storage container policy set [options] [container] [name]
	storage container policy delete [options] [container] [name]

**Befehle zum Verwalten Ihrer Speicher-Blobs**

	storage blob list [options] [container] [prefix]
	storage blob show [options] [container] [blob]
	storage blob delete [options] [container] [blob]
	storage blob upload [options] [file] [container] [blob]
	storage blob download [options] [container] [blob] [destination]

**Befehle zum Verwalten Ihrer Blob-Kopiervorgänge**

	storage blob copy start [options] [sourceUri] [destContainer]
	storage blob copy show [options] [container] [blob]
	storage blob copy stop [options] [container] [blob] [copyid]

**Befehle zum Verwalten von Shared Access Signatures Ihres Speicher-Blob**

	storage blob sas create [options] [container] [blob] [permissions] [expiry]

**Befehle zum Verwalten Ihrer Speicherdateifreigaben**

	storage share create [options] [share]
	storage share show [options] [share]
	storage share delete [options] [share]
	storage share list [options] [prefix]

**Befehle zum Verwalten Ihrer Speicherdateien**

	storage file list [options] [share] [path]
	storage file delete [options] [share] [path]
	storage file upload [options] [source] [share] [path]
	storage file download [options] [share] [path] [destination]

**Befehle zum Verwalten Ihres Speicherdateiverzeichnisses**

	storage directory create [options] [share] [path]
	storage directory delete [options] [share] [path]

**Befehle zum Verwalten Ihrer Speicherwarteschlangen**

	storage queue create [options] [queue]
	storage queue list [options] [prefix]
	storage queue show [options] [queue]
	storage queue delete [options] [queue]

**Befehle zum Verwalten von Shared Access Signatures Ihrer Speicherwarteschlange**

	storage queue sas create [options] [queue] [permissions] [expiry]

**Befehle zum Verwalten von gespeicherten Zugriffsrichtlinien für Ihre Speicherwarteschlange**

	storage queue policy create [options] [queue] [name]
	storage queue policy show [options] [queue] [name]
	storage queue policy list [options] [queue]
	storage queue policy set [options] [queue] [name]
	storage queue policy delete [options] [queue] [name]

**Befehle zum Verwalten Ihrer Speicherprotokolleigenschaften**

	storage logging show [options]
	storage logging set [options]

**Befehle zum Verwalten Ihrer Speichermetrikeneigenschaften**

	storage metrics show [options]
	storage metrics set [options]

**Befehle zum Verwalten Ihrer Speichertabellen**

	storage table create [options] [table]
	storage table list [options] [prefix]
	storage table show [options] [table]
	storage table delete [options] [table]

**Befehle zum Verwalten von Shared Access Signatures Ihrer Speichertabellen**

	storage table sas create [options] [table] [permissions] [expiry]

**Befehle zum Verwalten von gespeicherten Zugriffsrichtlinien für Ihre Speichertabelle**

	storage table policy create [options] [table] [name]
	storage table policy show [options] [table] [name]
	storage table policy list [options] [table]
	storage table policy set [options] [table] [name]
	storage table policy delete [options] [table] [name]

## azure tag: Befehle zum Verwalten des Ressourcen-Manager-Tags

**Hinzufügen eines Tags**

	tag create [options] <name> <value>

**Entfernen eines ganzen Tags oder eines Tagwerts**

	tag delete [options] <name> <value>

**Auflisten der Taginformationen**

	tag list [options]

**Abrufen eines Tags**

	tag show [options] [name]

## azure vm: Befehle zum Verwalten Ihrer virtuellen Azure-Computer

**Erstellen eines virtuellen Computers**

	vm create [options] <resource-group> <name> <location> <os-type>

**Erstellen eines virtuellen Computers mit Standardressourcen**

	vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>

**Auflisten der virtuellen Computer innerhalb einer Ressourcengruppe**

	vm list [options] <resource-group>

**Abrufen eines virtuellen Computers innerhalb einer Ressourcengruppe**

	vm show [options] <resource-group> <name>

**Löschen eines virtuellen Computers innerhalb einer Ressourcengruppe**

	vm delete [options] <resource-group> <name>

**Herunterfahren eines virtuellen Computers innerhalb einer Ressourcengruppe**

	vm stop [options] <resource-group> <name>

**Neustarten eines virtuellen Computers innerhalb einer Ressourcengruppe**

	vm restart [options] <resource-group> <name>

**Starten eines virtuellen Computers innerhalb einer Ressourcengruppe**

	vm start [options] <resource-group> <name>

**Herunterfahren eines virtuellen Computers innerhalb einer Ressourcengruppe und Freigeben der Serverressourcen**

	vm deallocate [options] <resource-group> <name>

**Auflisten der verfügbaren Größen virtueller Computer**

	vm sizes [options]

**Erfassen des virtuellen Computers als Betriebssystem-Image oder VM-Image**

	vm capture [options] <resource-group> <name> <vhd-name-prefix>

**Festlegen des Zustands des virtuellen Computers auf "Generalisiert"**

	vm generalize [options] <resource-group> <name>

**Abrufen der Instanzenansicht des virtuellen Computers**

	vm get-instance-view [options] <resource-group> <name>

**Ermöglicht es Ihnen, den Remotedesktopzugriff oder die SSH-Einstellungen auf einem virtuellen Computer zurückzusetzen und das Kennwort für das Konto mit Administrator- oder Sudo-Berechtigung zurückzusetzen**

	vm reset-access [options] <resource-group> <name>

**Aktualisieren des virtuellen Computers mit neuen Daten**

	vm set [options] <resource-group> <name>

**Befehle zum Verwalten der Datenträger Ihres virtuellen Computers**

	vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
	vm disk detach [options] <resource-group> <vm-name> <lun>
	vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**Befehle zum Verwalten von Ressourcenerweiterungen für virtuelle Computer**

	vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
	vm extension get [options] <resource-group> <vm-name>

**Befehle zum Verwalten des virtuellen Docker-Computers**

	vm docker create [options] <resource-group> <name> <location> <os-type>

**Befehle zum Verwalten von VM-Images**

	vm image list-publishers [options] <location>
	vm image list-offers [options] <location> <publisher>
	vm image list-skus [options] <location> <publisher> <offer>
	vm image list [options] <location> <publisher> [offer] [sku]

<!---HONumber=Sept15_HO4-->