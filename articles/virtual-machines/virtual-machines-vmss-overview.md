<properties
	pageTitle="Übersicht über VM-Skalierungsgruppen | Microsoft Azure"
	description="Erfahren Sie mehr über VM-Skalierungsgruppen"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Übersicht über VM-Skalierungsgruppen

VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Gruppe von identischen virtuellen Computern bereitstellen und verwalten können. Alle virtuellen Computer in einer VM-Skalierungsgruppe sind gleich konfiguriert. Dadurch ermöglichen VM-Skalierungsgruppen eine echte automatische Skalierung. Eine Vorabbereitstellung der virtuellen Computer ist dabei nicht erforderlich. Das erleichtert die Erstellung leistungsfähiger Dienste, die auf große Rechenleistung, Big Data und Workloads in Containern ausgerichtet sind.

[AZURE.INCLUDE [virtual-machines-vmss-preview](../../includes/virtual-machines-vmss-preview-ps-include.md)]

Bei Anwendungen, für die Computeressourcen horizontal herunter- und hochskaliert werden müssen, werden Skalierungsoperationen implizit über Fehler- und Updatedomänen ausgeglichen. Eine Einführung in das Thema „VM-Skalierungsgruppen“ finden Sie in der [Ankündigung im Azure-Blog](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) (in englischer Sprache).

Sehen Sie sich diese Videos an, um weitere Informationen zu VM-Skalierungsgruppen zu erhalten:

 - [Mark Russinovich spricht über Azure-Skalierungsgruppen](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [VM-Skalierungsgruppen mit Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Erstellen und Verwalten von VM-Skalierungsgruppen

VM-Skalierungsgruppen können mithilfe von JSON-Vorlagen und [REST-APIs](https://msdn.microsoft.com/library/mt589023.aspx) definiert und bereitgestellt werden, ganz genauso wie einzelne virtuelle Azure-Ressourcen-Manager-Computer. Daher können alle standardmäßigen Azure-Ressourcen-Manager-Bereitstellungsmethoden verwendet werden. Weitere Informationen zu Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Eine Reihe von Beispielvorlagen für VM-Skalierungsgruppen finden Sie im GitHub-Repository für Azure-Schnellstartvorlagen:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) – Suchen Sie nach Vorlagen mit _vmss_ im Titel.

Auf den Detailseiten für diese Vorlagen finden Sie eine Schaltfläche, die eine Verknüpfung mit dem Bereitstellungsfeature des Portals herstellt. Zum Bereitstellen der VM-Skalierungsgruppe klicken Sie auf diese Schaltfläche und geben dann alle Parameter an, die im Portal erforderlich sind. Wenn Sie sich nicht sicher sind, ob eine Ressource Großbuchstaben oder eine gemischte Groß- und Kleinbuchschreibung unterstützt, ist es sicherer, für die Parameterwerte nur Kleinbuchstaben zu verwenden. Es gibt auch ein anschauliches Video mit einer Analyse einer VM-Skalierungsgruppenvorlage:

[Analyse einer VM-Skalierungsgruppenvorlage](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## Hochskalieren und Herunterskalieren einer VM-Skalierungsgruppe

Wenn Sie die Anzahl der virtuellen Computer in einer VM-Skalierungsgruppe erhöhen oder verringern möchten, ändern Sie einfach die Eigenschaft _capacity_ und stellen die Vorlage erneut bereit. Diese Einfachheit erleichtert es, eine benutzerdefinierte Skalierungsschicht zu schreiben, falls Sie benutzerdefinierte Skalierungsereignisse definieren möchten, die von der automatischen Skalierung in Azure nicht unterstützt werden.

Wenn Sie eine Vorlage erneut bereitstellen, um die Kapazität zu erhöhen, können Sie dafür eine viel kleinere Vorlage definieren, die nur die SKU und die geänderte Kapazität enthält. Ein entsprechendes Beispiel finden Sie hier: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Die Schritte, mit denen eine Skalierungsgruppe erstellt wird, die automatisch skaliert wird, werden unter [Automatisches Skalieren von Computern in einer VM-Skalierungsgruppe](virtual-machines-vmss-walkthrough.md) erläutert.

## Überwachen der VM-Skalierungsgruppe

Zurzeit sollten Sie den [Azure-Ressourcen-Explorer](https://resources.azure.com) verwenden, um VM-Skalierungsgruppen anzuzeigen. Bei VM-Skalierungsgruppen handelt es sich um Ressourcen unter „Microsoft.Compute“. Sie können sie daher anzeigen, indem Sie auf dieser Website die folgenden Links öffnen:

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## Szenarien für VM-Skalierungsgruppen

Dieser Abschnitt enthält einige typische Szenarien für VM-Skalierungsgruppen. Einige auf höherer Ebene angesiedelte Azure-Dienste (wie Batch, Service Fabric und Azure Container Service) verwenden diese Szenarien.

 - **Zugriff mit RDP oder SSH auf VM-Skalierungsgruppeninstanzen** – Eine VM-Skalierungsgruppe wird in einem VNet erstellt, und den einzelnen virtuellen Computern werden keine öffentlichen IP-Adressen zugeordnet. Das ist gut so, weil Sie im Allgemeinen den Aufwand und die Verwaltungsarbeiten vermeiden möchten, die mit dem Zuordnen von separaten IP-Adressen zu allen zustandslosen Ressourcen in Ihrem Computenetz verbunden sind. Außerdem können Sie über andere Ressourcen in Ihrem VNET, die über öffentliche IP-Adressen verfügen, leicht eine Verbindung mit diesen virtuellen Computern herstellen, z. B. über Load Balancer oder eigenständige virtuelle Computer.

 - **Herstellen einer Verbindung mit virtuellen Computern mithilfe von NAT-Regeln**: Sie können eine öffentliche IP-Adresse erstellen, sie einem Load Balancer zuweisen und NAT-Regeln (Network Address Translation) für eingehenden Datenverkehr definieren, die einen Port der IP-Adresse einem Port auf dem virtuellen Computer in der VM-Skalierungsgruppe zuordnen. Beispiel:

	Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

	Hier ist ein Beispiel für das Erstellen einer VM-Skalierungsgruppe, bei der NAT-Regeln verwendet werden, um eine SSH-Verbindung (Secure Shell) mit jedem virtuellen Computer in der Skalierungsgruppe über eine einzige öffentliche IP-Adresse zu ermöglichen: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).

	Hier ist ein Beispiel, bei dem dasselbe mit RDP (Remote Desktop Protocol) und Windows erreicht wird: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat).

 - **Herstellen einer Verbindung mit VMs mithilfe einer „Jumpbox“** – Wenn Sie eine VM-Skalierungsgruppe und einen eigenständigen virtuellen Computer in demselben VNET erstellen, können der eigenständige virtuelle Computer und die virtuellen Computer in der VM-Skalierungsgruppe eine Verbindung miteinander herstellen, indem sie ihre internen IP-Adressen verwenden, die durch das VNet/Subnetz definiert sind. Wenn Sie eine öffentliche IP-Adresse erstellen und dem eigenständigen virtuellen Computer zuweisen, können Sie mit RDP oder SSH eine Verbindung mit dem eigenständigen virtuellen Computer herstellen. Anschließend können Sie von diesem Computer aus eine Verbindung mit den VM-Skalierungsgruppeninstanzen herstellen. Vielleicht erkennen Sie an dieser Stelle, dass eine einfache VM-Skalierungsgruppe inhärent sicherer ist als ein einfacher eigenständiger virtueller Computer in Standardkonfiguration mit einer öffentlichen IP-Adresse.

	Als Beispiel für diese Herangehensweise erstellt die folgende Vorlage einen einfachen Mesos-Cluster mit einem eigenständigen virtuellen Mastercomputer, der einen auf einer VM-Skalierungsgruppe basierenden Cluster von virtuellen Computern verwaltet: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Roundrobin-Lastenausgleich für VM-Skalierungsgruppeninstanzen**: Wenn Sie eine Workload per Roundrobin an einen Computecluster aus virtuellen Computern übergeben möchten, können Sie einen Azure Load Balancer mit entsprechenden Lastenausgleichsregeln konfigurieren. Außerdem können Sie Tests definieren, um zu überprüfen, ob Ihre Anwendung ordnungsgemäß ausgeführt wird. Diese Tests pingen die Ports mit einem festgelegten Protokoll in vorgegebenen Zeitabständen und mit einem angegebenen Anforderungspfad.

	Im folgenden Beispiel wird eine VM-Skalierungsgruppe mit virtuellen Computern erstellt, auf denen ein IIS-Webserver ausgeführt wird. Dabei wird die Last für die einzelnen virtuellen Computer mit einem Load Balancer ausgeglichen. In dem Beispiel wird HTTP verwendet, um auf jedem virtuellen Computer eine spezifische URL zu pingen: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json). – Sehen Sie sich hier besonders den Ressourcentyp „Microsoft.Network/loadBalancers“ sowie „networkProfile“ und „extensionProfile“ in „virtualMachineScaleSet“ an.

 - **Bereitstellen einer VM-Skalierungsgruppe als Computecluster in einem PaaS-Cluster-Manager**: VM-Skalierungsgruppen werden mitunter als Workerrolle der nächsten Generation beschrieben. Diese Beschreibung ist zwar zutreffend, aber sie lädt zu Verwechslungen zwischen den Features von Skalierungsgruppen und PaaS v1-Workerrollen ein. In einem bestimmten Sinn stellen VM-Skalierungsgruppen tatsächlich eine „Workerrolle“ oder eine Workerressource bereit: Sie bieten eine generalisierte Computeressource, die von Plattformen und Laufzeitumgebungen unabhängig ist, sich anpassen lässt und in die Azure-Ressourcen-Manager-IaaS integriert ist.

	Eine PaaS v1-Workerrolle ist zwar im Hinblick auf die Unterstützung für Plattformen/Laufzeitumgebungen eingeschränkt (nur für Images der Windows-Plattform), aber sie bietet auch Dienste wie VIP-Austausch, konfigurierbare Upgradeeinstellungen und Einstellungen für laufzeitumgebungs- und anwendungsspezifische Bereitstellungen, die entweder _noch_ nicht in VM-Skalierungsgruppen zur Verfügung stehen oder die durch auf höherer Ebene angesiedelte PaaS-Dienste wie Service Fabric umgesetzt werden. Vor diesem Hintergrund können Sie VM-Skalierungsgruppen als eine Infrastruktur betrachten, die PaaS unterstützt. Das heißt: PaaS-Lösungen wie Service Fabric oder Cluster-Manager wie Mesos können auf VM-Skalierungsgruppen aufsetzen, um eine skalierbare Compute-Ebene zu bilden.

	Hier ist ein Beispiel für einen Mesos-Cluster, der eine VM-Skalierungsgruppe und einen eigenständigen virtuellen Computer in demselben VNET bereitstellt. Der eigenständige virtuelle Computer ist ein Mesos-Master, und die VM-Skalierungsgruppe stellt eine Gruppe von untergeordneten Knoten dar: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Bei künftigen Versionen des [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) werden komplexere und besser geschützte Versionen dieses auf VM-Skalierungsgruppen beruhenden Szenarios bereitgestellt.

## Richtlinien zur Leistungsfähigkeit von VM-Skalierungsgruppen und zum Skalieren

- Erstellen Sie während der öffentlichen Vorschau höchstens 500 virtuelle Computer in mehreren VM-Skalierungsgruppen gleichzeitig.
- Planen Sie mit höchstens 40 virtuellen Computern pro Speicherkonto.
- Nutzen Sie für die Namen der Speicherkonten verschiedene und im Alphabet möglichst weit auseinanderliegende Buchstaben. Die Beispielvorlagen für VM-Skalierungsgruppen (VMSS, Virtual Machine Scale Sets) in den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/) veranschaulichen die empfohlene Vorgehensweise.
- Wenn Sie benutzerdefinierte virtuelle Computer verwenden, sollten Sie mit höchstens 40 virtuellen Computern pro VM-Skalierungsgruppe in einem einzelnen Speicherkonto planen. Sie müssen das Image vorab in das Speicherkonto kopieren, bevor Sie mit der Bereitstellung der VM-Skalierungsgruppe beginnen können. Weitere Informationen finden Sie in den FAQ.
- Planen Sie mit höchstens 2048 virtuellen Computern pro VNET. Dieses Limit wird in Zukunft erhöht.
- Die Anzahl der virtuellen Computer, die Sie erstellen können, wird durch Ihr Computekernkontigent in einer beliebigen Region begrenzt. Unter Umständen müssen Sie sich an den Kundensupport wenden und das Limit im Computekontingent auch dann erhöhen lassen, wenn Sie heute über ein hohes Limit an Kernen verfügen, die für Clouddienste oder IaaS v1 genutzt werden können. Sie können Ihr Kontingent mit dem folgenden Befehl der Azure-Befehlszeilenschnittstelle abfragen: _azure vm list-usage_. Oder Sie verwenden dazu den folgenden PowerShell-Befehl: _Get-AzureRmVMUsage_ (falls Sie eine Version von PowerShell vor 1.0 nutzen, verwenden Sie _Get-AzureVMUsage_).

## VM-Skalierungsgruppen – häufig gestellte Fragen

**F.** Wie viele virtuelle Computer können in eine VM-Skalierungsgruppe aufgenommen werden?

**A.** 100, sofern Sie Plattformimages verwenden, die über mehrere Speicherkonten verteilt werden können. Wenn Sie benutzerdefinierte Images verwenden, bis zu 40, weil benutzerdefinierte Images während der Vorschau nur in einem einzelnen Speicherkonto platziert werden können.

**F.** Welche anderen Ressourceneinschränkungen bestehen für VM-Skalierungsgruppen?

**A.** Während der Vorschauperiode dürfen höchstens 500 virtuelle Computer in mehreren Skalierungsgruppen pro Region erstellt werden. Die vorhandenen [Diensteinschränkungen des Azure-Abonnements](../azure-subscription-service-limits.md) gelten.

**F.** Werden Datenträger innerhalb von VM-Skalierungsgruppen unterstützt?

**A.** Nicht in der ersten Version. Zum Speichern von Daten haben Sie folgende Möglichkeiten:

- Azure-Dateien (SMB-freigegebene Laufwerke)

- Betriebssystemlaufwerk

- Temporäres Laufwerk (lokal, nicht durch Azure Storage gesichert)

- Externer Datendienst (z. B. Remote-DB, Azure-Tabellen, Azure-Blobs)

**F.** In welchen Azure-Regionen werden VM-Skalierungsgruppen unterstützt?

**A.** VM-Skalierungsgruppen werden in allen Regionen unterstützt, die den Azure-Ressourcen-Manager unterstützen.

**F.** Wie erstellen Sie eine VM-Skalierungsgruppe mit einem benutzerdefinierten Image?

**A.** Lassen Sie die vhdContainers-Eigenschaft leer, wie z. B hier:

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
			},
			"osType": "Windows"
		}
	},


**F.** Welche virtuellen Computer werden entfernt, wenn ich meine Kapazität für VM-Skalierungsgruppen von 20 auf 15 verringere?

**A.** Die Entfernung der virtuellen Computer aus den Skalierungsgruppen erfolgt gleichmäßig über Upgrade- und Fehlerdomänen hinweg, um die Verfügbarkeit zu maximieren.

**F.** Was passiert, wenn ich dann die Kapazität von 15 auf 18 erhöhe?

**A.** Wenn Sie auf 18 erhöhen, werden virtuelle Computer mit den Indizes 15, 16 und 17 erstellt. In beiden Fällen werden die virtuellen Computer über Fehlerdomänen (FDs) und Updatedomänen (UDs) ausgeglichen.

**F.** Kann ich beim Verwenden mehrerer Erweiterungen in einer VM-Skalierungsgruppe eine Ausführungsreihenfolge erzwingen?

**A.** Nicht direkt. Bei der customScript-Erweiterung kann das Skript jedoch warten, bis eine andere Erweiterung abgeschlossen ist (z. B. durch Überwachung des Erweiterungsprotokolls – siehe [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**F.** Funktionieren VM-Skalierungsgruppen mit Azure-Verfügbarkeitsgruppen?

**A.** Ja. Eine VM-Skalierungsgruppe ist eine implizite Verfügbarkeitsgruppe mit 3 Fehlerdomänen (FDs) und 5 Updatedomänen (UDs). Unter virtualMachineProfile brauchen Sie nichts zu konfigurieren. In künftigen Versionen werden VM-Skalierungsgruppen wahrscheinlich mehrere Mandanten umspannen, aber derzeit entspricht eine Skalierungsgruppe einer einzigen Verfügbarkeitsgruppe.

<!---HONumber=AcomDC_0128_2016-->