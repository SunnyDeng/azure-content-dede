<properties
   pageTitle="Empfohlene Benennungskonventionen für Azure-Ressourcen | Leitfaden | Microsoft Azure"
   description="Empfohlene Benennungskonventionen für Azure-Ressourcen Wie virtuelle Computer, Speicherkonten, Netzwerke, virtuelle Netzwerke, Subnetze und andere Azure-Entitäten benannt werden"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Empfohlene Benennungskonventionen für Azure-Ressourcen

Die Auswahl eines Namens für eine Ressource in Microsoft Azure ist wichtig, da:

- es schwierig ist, einen Namen zu einem späteren Zeitpunkt zu ändern.
- Namen die Anforderungen ihres bestimmten Ressourcentyps erfüllen müssen.

Durch konsistente Benennungskonventionen lassen sich Ressourcen außerdem einfacher finden. Zudem unterstützen sie das Verstehen der Rolle einer Ressource in einer Lösung.

Dieser Artikel enthält eine Zusammenfassung der Benennungsregeln und -einschränkungen für Azure-Ressourcen sowie eine Reihe von grundsätzlichen Empfehlungen für Benennungskonventionen. Sie können diese Empfehlungen als Ausgangspunkt für Ihre eigenen, Ihren Bedürfnissen angepassten Konventionen verwenden.

Bei Benennungskonventionen liegt der Schlüssel zum Erfolg darin, sie über Ihre Anwendungen und Organisationen hinweg zu erstellen, zu befolgen und sie anzupassen, während Sie weitere Anwendungen und Dienste über die Azure-Plattform bereitstellen.

## Benennen von Abonnements

Bei der Benennung von Azure-Abonnements verdeutlichen ausführliche Namen den Kontext und den Zweck jedes Abonnements. Wenn Sie in einer Umgebung arbeiten, die mehrere Abonnements enthalten kann, kann eine gemeinsam verwendete Benennungskonvention die Übersichtlichkeit deutlich verbessern.

Ein empfohlenes Muster für das Benennen von Abonnements:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- In den meisten Fällen wäre das Unternehmen bei jedem Abonnement dasselbe. Jedoch haben manche Unternehmen möglicherweise Tochterunternehmen innerhalb der Organisationsstruktur. Diese Unternehmen werden möglicherweise von einer zentralen IT-Abteilung verwaltet. In diesem Fall könnte zwischen ihnen unterschieden werden, indem sowohl der Name des Mutterunternehmens (*Contoso*) als auch der des Tochterunternehmens (*North Wind*) angegeben wird.

- Die Abteilung ist ein Name für einen Bereich in der Organisation, in der eine Gruppe von Personen arbeitet. Dieses Element ist innerhalb des Namespace optional. Dies liegt daran, dass manche Unternehmen aufgrund Ihrer Größe möglicherweise nicht so tief ins Detail gehen müssen.

- Die Produktlinie bezeichnet den spezifischen Namen eines Produkts oder einer Funktion, die von der Abteilung aus ausgeführt wird. Während dieses Element für interne Dienste und Anwendungen typischerweise optional ist, wird die Verwendung für extern gerichtete Dienste, die eine einfache Trennung und Identifizierung (wie zur klaren Trennung von Abrechnungsinformationen) erfordern, dringend empfohlen.

- Die Umgebung ist der Name für den Bereitstellungszyklus der Anwendungen oder Dienste wie Entwicklung, QA oder Bereitstellung.

| Unternehmen | Abteilung | Produktlinie oder Dienst | Environment | Vollständiger Name |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Bereitstellung | Contoso SocialGaming AwesomeService Bereitstellung |
| Contoso | SocialGaming | AwesomeService | Entwicklung | Contoso SocialGaming AwesomeService Entwicklung |
| Contoso | IT | InternalApps | Bereitstellung | Contoso IT InternalApps Bereitstellung |
| Contoso | IT | InternalApps | Entwicklung | Contoso IT InternalApps Entwicklung |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## Verwenden Sie Affixe, um Mehrdeutigkeit zu vermeiden

Beim Benennen von Ressourcen in Azure wird empfohlen, gängige Präfixe oder Suffixe zu verwenden, um den Typ und Kontext der Ressource zu identifizieren. Während alle Informationen zum Typ, zu Metadaten und Kontext programmgesteuert verfügbar sind, vereinfacht die Nutzung gemeinsamer Affixe die visuelle Identifizierung. Wenn Sie Affixe in Ihre Benennungskonvention integrieren, ist es wichtig, klar zu bestimmen, ob das Affix am Anfang (Präfix) oder am Ende (Suffix) des Namens steht.

Hier sind z. B. zwei mögliche Namen für einen Dienst, der ein Berechnungsmodul hostet:

- SvcCalculationEngine (Präfix)
- CalculationEngineSvc (Suffix)

Affixe können auf verschiedene Aspekte verweisen, die die entsprechenden Ressourcen beschreiben. Die folgende Tabelle zeigt einige üblicherweise verwendete Beispiele.

| Aspekt | Beispiel | Hinweise |
| ------ | ------- | ----- |
| Environment | dev, prod, qa | identifiziert die Umgebung für die Ressource |
| Standort | uw (USA, Westen), ue (USA, Osten) | identifiziert die Region, in welcher die Ressource bereitgestellt wird |
| Instanz | 01, 02 | für Ressourcen, die mehr als eine benannte Instanz besitzen (Webdienste etc.) |
| Produkt oder Dienst | service | identifiziert das Produkt, die Anwendung oder den Dienst, den die Ressource unterstützt |
| Rolle | sql, web, messaging | identifiziert die Rolle der zugeordneten Ressource |

Bei der Entwicklung einer bestimmten Benennungskonvention für Ihr Unternehmen oder Ihr(e) Projekt(e) ist es wichtig, gängige Affixe und deren Position (Suffix oder Präfix) auszuwählen.

## Benennungsregeln und -einschränkungen

Jeder Ressourcen- oder Diensttyp in Azure erzwingt Benennungseinschränkungen und einen Benennungsumfang. Jede Benennungskonvention oder jedes Benennungsmuster muss den erforderlichen Benennungsregeln und dem Benennungsumfang entsprechen. Während z. B. der Name einer VM einem DNS-Namen zugeordnet ist (und daher im gesamten Azure-Dienst einmalig sein muss), ist der Name eines VNET der Ressourcengruppe zugeordnet, in der es erstellt wurde.

Im Allgemeinen sollten Sie vermeiden, dass Sonderzeichen (`-` oder `_`) als erstes oder letztes Zeichen eines Namens verwendet werden, da dies den meisten Validierungsregeln widerspricht.

| Kategorie | Dienst oder Entität | Scope | Länge | Schreibweise | Gültige Zeichen | Vorgeschlagenes Muster | Beispiel |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Ressourcengruppe | Ressourcengruppe | Global | 1-64 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Unterstrich und Bindestrich | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Ressourcengruppe | Verfügbarkeitsgruppe | Ressourcengruppe | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Unterstrich und Bindestrich | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Allgemein | Tag | Zugeordnete Entität | 512 (Name), 256 (Wert) | Groß-/Kleinschreibung nicht beachten | Alphanumerisch | `"key" : "value"` | `"department" : "Central IT"` |
| Compute | Virtual Machine | Ressourcengruppe | 1-15 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Unterstrich und Bindestrich | `<name>-<role>-<instance>` | `profx-sql-001` |
| Speicher | Speicherkontoname (Daten) | Global | 3-24 | Kleinbuchstaben | Alphanumerisch | `<service short name><type><number>` | `profxdata001` |
| Speicher | Speicherkontoname (Datenträger) | Global | 3-24 | Kleinbuchstaben | Alphanumerisch | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Speicher | Containername | Speicherkonto | 3-63 |	Kleinbuchstaben | Alphanumerisch und Bindestrich | `<context>` | `logs` |
| Speicher | Blobname | Container | 1-1024 | Groß-/Kleinschreibung beachten | Jedes URL-Zeichen | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Speicher | Warteschlangenname | Speicherkonto | 3-63 | Kleinbuchstaben | Alphanumerisch und Bindestrich | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Speicher | Tabellenname | Speicherkonto | 3-63 |Groß-/Kleinschreibung nicht beachten | Alphanumerisch | `<service short name>-<context>` | `awesomeservice-logs` |
| Speicher | Dateiname | Speicherkonto | 3-63 | Kleinbuchstaben | Alphanumerisch | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Netzwerk | Virtual Network (VNet) | Ressourcengruppe | 2-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `<service short name>-[section]-vnet` | `profx-vnet` |
| Netzwerk | Subnetz | Übergeordnetes VNet | 2-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Unterstrich, Bindestrich und Punkt | `<role>-subnet` | `gateway-subnet` |
| Netzwerk | Netzwerkschnittstelle | Ressourcengruppe | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Netzwerk | Netzwerksicherheitsgruppen (NSG) | Ressourcengruppe | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Netzwerk | Netzwerksicherheitsgruppen-Regel | Ressourcengruppe | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `<descriptive context>` | `sql-allow` |
| Netzwerk | Öffentliche IP-Adresse | Ressourcengruppe | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `<vm or service name>-pip` | `profx-sql1-pip` |
| Netzwerk | Lastenausgleichsmodul | Ressourcengruppe | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `<service or role>-lb` | `profx-lb` |
| Netzwerk | Konfiguration der Regeln für die Lastenverteilung | Lastenausgleichsmodul | 1-80 | Groß-/Kleinschreibung nicht beachten | Alphanumerisch, Bindestrich, Unterstrich und Punkt | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## Organisieren von Ressourcen mit Tags

Der Azure Resource Manager unterstützt das Markieren von Entitäten mit beliebigen Textzeichenfolgen zur Identifizierung von Kontext und Optimierung der Automation. Ein Tag wie `"sqlVersion: "sql2014ee"` könnte z. B. jede VM in einer Bereitstellung, die SQL Server 2014 Enterprise Edition ausführt, zur Ausführung eines automatisierten Skripts identifizieren. Neben den ausgewählten Benennungskonventionen sollten Tags zur Erweiterung und Verbesserung des Kontexts verwendet werden.

> [AZURE.TIP] Ein weiterer Vorteil von Tags ist, dass sie sich über Ressourcengruppen erstrecken, wodurch Sie die Möglichkeit haben, Entitäten über verschiedene Bereitstellungen hinweg zu verknüpfen und zu korrelieren.

Jede Ressource oder Ressourcengruppe kann maximal **15** Tags haben. Der Tagname ist auf 512 Zeichen beschränkt und der Tagwert auf 256 Zeichen.

Weitere Informationen zur Verwendung von Tags für Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../resource-group-using-tags.md).

Einige der üblichen Anwendungsfälle für die Verwendung von Tags sind:

- **Abrechnung** – Gruppieren von Ressourcen und Zuordnen dieser zu Codes für Abrechnung oder verbrauchsbasierte Kostenzuteilung
- **ServicekontextIdentifikation** – Identifizieren von Gruppen von Ressourcen über Ressourcengruppen hinweg für allgemeine Operationen und Gruppierung
- **Zugriffssteuerung und Sicherheitskontext** – Identifizieren der Administratorrolle basierend auf Portfolio, System, Service, App, Instanz etc. 

> [AZURE.TIP] Markieren Sie frühzeitig – markieren Sie oft. Es ist besser, ein Basisschema zum Verwenden von Tags zu haben, das Sie mit der Zeit anpassen, als nachträglich eines erstellen zu müssen.

Ein Beispiel für einige übliche Ansätze zum Verwenden von Tags:

| Tag-Name | Schlüssel | Beispiel | Kommentar |
| -------- | --- | ------- | ------- |
| Rechnung an / ID für interne verbrauchsbasierte Kostenzuteilung | billTo | `IT-Chargeback-1234` | Ein interner E/A- oder Abrechnungscode |
| zuständige Person (DRI, Directly Responsible Individual) | managedBy | `joe@contoso.com` | Alias oder E-Mail-Adresse |
| Projektname | project-name | `myproject` | Name des Projekts oder der Produktlinie |
| Projektversion | project-version | `3.4` | Version des Projekts oder der Produktlinie |
| Environment | environment | `<Production, Staging, QA >` | Umgebungsbezeichner | 
| Ebene | tier | `Front End, Back End, Data` | Ebenen- oder Rollen-/Kontextidentifikation |
| Datenprofil | dataProfile | `Public, Confidential, Restricted, Internal` | Vertraulichkeit der in der Ressource gespeicherten Daten |
 
## Tipps und Tricks

Je nach Art der Anwendung erfordern bestimmte Ressourcentypen möglicherweise zusätzliche Sorgfalt beim Benennen und bei den Konventionen. Zusätzliche Informationen und Kontext hierzu sind im Folgenden aufgelistet.

### Virtual Machines

Vor allem in größeren Topologien optimieren sorgfältig benannte virtuelle Computer die Identifizierung der Rolle und des Zwecks jedes Computers deutlich und aktivieren besser vorhersagbare Skripterstellung.

> [AZURE.WARNING] Beachten Sie, dass jeder virtuelle Computer in Azure sowohl einen Azure-Ressourcennamen als auch einen Betriebssystem-Hostnamen hat. Wenn der Ressourcenname und der Hostname unterschiedlich sind, kann das Verwalten dieser VMs eine Herausforderung darstellen (wenn der virtuelle Computer z. B. von einer .vhd-Datei erstellt wird, die bereits ein konfiguriertes Betriebssystem mit einem Hostnamen enthält), und sollte daher vermieden werden.

- [Naming conventions for Windows Server VMs](https://support.microsoft.com/de-DE/kb/188997) (Benennungskonventionen für Windows Server-VMs)

<!-- TODO - recommendations on naming VMs. -->

###	Speicherkonten und -entitäten

Es gibt zwei primäre Anwendungsfälle für Speicherkonten – das Sichern von Datenträgern für VMs und das Speichern von Daten in Blobs, Warteschlangen und Tabellen. Speicherkonten, die für VM-Datenträger verwendet werden, sollten die Benennungskonvention befolgen, die sie dem übergeordneten VM-Namen zuordnet. (Außerdem sollten sie, aufgrund des potenziellen Bedarfs an mehreren Speicherkonten für leistungsfähige VM-SKUs, ein numerisches Suffix verwenden.)

> [AZURE.TIP] Speicherkonten – ob für Daten oder Datenträger – sollten eine Benennungskonvention befolgen, die es mehreren Speicherkonten erlaubt, verwendet zu werden (indem z. B. immer ein numerisches Suffix verwendet wird).

Es ist möglich, einen benutzerdefinierten Domänennamen zu konfigurieren, mit dem Sie auf Blobdaten in Ihrem Azure Storage-Konto zugreifen können. Der Standardendpunkt für den Blobdienst ist `https://mystorage.blob.core.windows.net`

Wenn Sie dem Blobendpunkt für Ihr Speicherkonto eine benutzerdefinierte Domäne (wie z. B. www.contoso.com) zuordnen, können Sie unter Verwendung dieser Domäne auch auf Blobdaten in Ihrem Speicherkonto zugreifen. Mit einem benutzerdefinierten Domänennamen könnten Sie z. B. mit `http://www.contoso.com/mycontainer/myblob` auf `http://mystorage.blob.core.windows.net/mycontainer/myblob` zugreifen.

Weitere Informationen zum Konfigurieren dieser Funktion finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob Storage-Endpunkt](../storage/storage-custom-domain-name.md).

Weitere Informationen zum Benennen von Blobs, Containern und Tabellen:

- [Benennen von Containern, BLOBs und Metadaten und Verweisen auf diese](https://msdn.microsoft.com/library/dd135715.aspx)
- [Benennen von Warteschlangen und Metadaten](https://msdn.microsoft.com/library/dd179349.aspx)
- [Benennen von Tabellen](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Ein Blobname kann jede Kombination von Zeichen enthalten, jedoch müssen reservierte URL-Zeichen ordnungsgemäß mit Escapezeichen versehen werden. Vermeiden Sie Blobnamen, die mit einem Punkt (.), einem Schrägstrich (/) oder einer Sequenz oder einer Kombination aus beidem endet. Gemäß der Konvention ist der Schrägstrich das Trennzeichen für das **virtuelle** Verzeichnis. Verwenden Sie keinen umgekehrten Schrägstrich (\\) in einem Blobnamen. Die Client-APIs lassen dies möglicherweise zu, ermitteln dann aber den Hash nicht richtig, und die Signaturen stimmen nicht überein.

Es ist nicht möglich, den Namen eines Speicherkontos oder -containers nach dem Erstellen zu ändern. Sie müssen es löschen und ein neues erstellen, wenn Sie einen neuen Namen verwenden möchten.

> [AZURE.TIP] Es wird empfohlen, dass Sie vor der Entwicklung eines neuen Diensts oder einer neuen Anwendung eine Benennungskonvention für alle Speicherkonten und -typen einrichten.

## Beispiel – Bereitstellen eines N-Tier-Diensts

In diesem Beispiel wird eine N-Tier-Dienstkonfiguration definiert. Sie besteht aus Front-End-IIS-Servern (in Windows Server-VMs gehostet) mit SQL Server (in zwei Windows Server-VMs gehostet), einem ElasticSearch-Cluster (in 6 Linux-VMs gehostet) und den damit verbundenen Speicherkonten, virtuellen Netzwerken, Ressourcengruppen und Load Balancer.

Wir beginnen mit dem Definieren der kontextbezogenen Konventionen für diese Anwendung:

| Entität | Konvention | Beschreibung |
| ------ | ---------- | ------------ |  
| Service Name | `profx` | Der Kurzname der bereitgestellten Anwendung oder des bereitgestellten Diensts. |
| Environment | `prod` | Dies dient der Produktionsbereitstellung (im Gegensatz zu qa, test etc.) |

Ausgehend von dieser Baseline können wir anschließend die Konventionen für jeden Ressourcentyp entwerfen:

| Ressourcentyp | Konventionsbasis | Beispiel |
| ------------- | --------------- | ------- |
| Abonnement | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Ressourcengruppe | `servicename-rg` | `profx-rg` |
| Virtuelles Netzwerk | `servicename-vnet` | `profx-vnet` |
| Subnetz | `role-subnet` | `sql-vnet` |
| Lastenausgleichsmodul | `servicename-lb` | `profx-lb` |
| Virtual Machine | `servicename-role[number]` | `profx-sql0` |
| Speicherkonto | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Wie im folgenden Diagramm dargestellt:

![Anwendungstopologiediagramm](media/guidance-naming-conventions/guidance-naming-convention-example.png "Beispielanwendungstopologie")

## Beispiel – Azure-CLI-Skript für die Bereitstellung des obigen Beispiels

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->