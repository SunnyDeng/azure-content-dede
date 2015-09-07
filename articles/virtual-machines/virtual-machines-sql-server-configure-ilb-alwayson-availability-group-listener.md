<properties 
	pageTitle="Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure"
	description="In diesem Tutorial erfahren Sie, wie Sie in Azure einen Listener für eine AlwaysOn-Verfügbarkeitsgruppe mit dem internen Load Balancer erstellen."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/11/2015"
	ms.author="jroth"/>

# Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Übersicht

In diesem Thema erfahren Sie, wie Sie mit dem **internen Load Balancer (ILB)** einen Listener für eine AlwaysOn-Verfügbarkeitsgruppe konfigurieren.

Ihre Verfügbarkeitsgruppe kann Replikate enthalten, die ausschließlich lokal, ausschließlich in Azure oder sowohl lokal als auch in Azure verfügbar sind (Hybridkonfigurationen). Azure-Replikate können sich innerhalb derselben Region oder in mehreren Regionen befinden, wobei mehrere virtuelle Netzwerke (VNets) verwendet werden. Bei den nachfolgenden Schritten wird davon ausgegangen, dass bereits eine [Verfügbarkeitsgruppe konfiguriert wurde](https://msdn.microsoft.com/library/azure/dn249504.aspx), Sie jedoch noch keinen Listener konfiguriert haben.

Beachten Sie die folgenden Einschränkungen, die für Verfügbarkeitsgruppenlistener in Azure bei Verwendung des internen Lastenausgleichs gelten:

- Der Verfügbarkeitsgruppenlistener wird unter Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2 unterstützt.

- Die Clientanwendung darf sich nicht in demselben Clouddienst befinden wie Ihre Verfügbarkeitsgruppen-VMs. Azure bietet keine Unterstützung für Direct Server Return, wenn sich Client und Server im selben Clouddienst befinden.

- Da der Listener entweder für die Verwendung der VIP-Adresse des Clouddiensts oder der VIP-Adresse des internen Load Balancer konfiguriert wird, wird pro Clouddienst maximal ein Verfügbarkeitsgruppenlistener unterstützt. Beachten Sie, dass diese Einschränkung weiterhin gilt, wenngleich Azure inzwischen die Erstellung mehrerer VIP-Adressen in einem Clouddienst unterstützt.

>[AZURE.NOTE]Der Schwerpunkt dieses Tutorials liegt auf der Verwendung von PowerShell zum Erstellen eines Listeners für eine Verfügbarkeitsgruppe, die Azure-Replikate umfasst. Weitere Informationen zum Konfigurieren von Listenern mithilfe von SSMS oder Transact-SQL finden Sie unter [Erstellen oder Konfigurieren eines Verfügbarkeitsgruppenlisteners](https://msdn.microsoft.com/library/hh213080.aspx).

## Festlegen des Zugriffs auf den Listener

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Der Schwerpunkt dieses Artikels liegt auf dem Erstellen eines Listeners, der den **internen Load Balancer** verwendet. Wenn Sie einen öffentlichen/externen Listener benötigen, finden Sie die entsprechenden Schritte im Artikel zum Einrichten eines [externen Listeners](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

## Erstellen von VM-Endpunkten mit Lastenausgleich und Direct Server Return

Sie müssen für ILB zunächst den internen Load Balancer einrichten. Verwenden Sie zu diesem Zweck das folgende Skript.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Für **ILB** sollten statische IP-Adressen zugewiesen werden. Untersuchen Sie zunächst die aktuelle VNET-Konfiguration, indem Sie den folgenden Befehl ausführen:

		(Get-AzureVNetConfig).XMLConfiguration

1. Notieren Sie sich den unter **Subnetz** angegebenen Namen für das Subnetz, in dem sich die zum Hosten der Replikate verwendeten VMs befinden. Dieser Name wird für den Parameter **$SubnetName** des Skripts verwendet.

1. Notieren Sie sich anschließend den für **VirtualNetworkSite** angegebenen Namen und den Wert von **AddressPrefix** für das Subnetz, in dem sich die zum Hosten der Replikate verwendeten VMs befinden. Suchen Sie nach einer verfügbaren IP-Adresse, indem Sie diese beiden Werte im Befehl **Test-AzureStaticVNetIP** verwenden und **AvailableAddresses** untersuchen. Beispiel: Wenn der Name des VNETs *MyVNet* lautet und der Adressbereich des Subnetzes bei *172.16.0.128* beginnt, werden mithilfe des folgenden Befehls die verfügbaren Adressen aufgelistet:

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Wählen Sie eine der verfügbaren Adressen, und verwenden Sie diese im folgenden Skript als Parameter **$ILBStaticIP**.

3. Kopieren Sie das unten stehende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablenwerte gemäß Ihrer Umgebung fest (beachten Sie, dass für einige Parameter die Standardwerte angegeben wurden). Hinweis: Bei vorhandenen Bereitstellungen, die Affinitätsgruppen verwenden, kann kein interner Lastenausgleich hinzugefügt werden. Weitere Informationen zu den Voraussetzungen für den internen Lastenausgleich finden Sie unter [Interner Load Balancer](../load-balancer/load-balancer-internal-overview.md). Wenn Ihre Verfügbarkeitsgruppe sich über mehrere Azure-Regionen erstreckt, müssen Sie das Skript zudem in jedem Datencenter für den Clouddienst und die Knoten ausführen, die sich in diesem Datencenter befinden.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value
		
		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
		
		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM 
		}

1. Nachdem Sie die Variablen festgelegt haben, kopieren Sie das Skript zur Ausführung aus dem Text-Editor in Ihre Azure PowerShell-Sitzung. Wenn die Aufforderung weiterhin >> anzeigt, geben Sie erneut ENTER ein, um die Skriptausführung zu starten. Hinweis:

>[AZURE.NOTE]Das Azure-Verwaltungsportal bietet gegenwärtig keine Unterstützung für den internen Load Balancer. Folglich werden im Portal weder der interne Load Balancer noch die Endpunkte angezeigt. Wenn der Load Balancer auf einem Endpunkt ausgeführt wird, gibt **Get-AzureEndpoint** jedoch eine interne IP-Adresse zurück. Andernfalls wird null zurückgegeben.

## Stellen Sie gegebenenfalls sicher, dass KB2854082 installiert ist.

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Öffnen der Firewallports in Verfügbarkeitsgruppenknoten

[AZURE.INCLUDE [Firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Erstellen des Verfügbarkeitsgruppenlisteners

[AZURE.INCLUDE [Firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Für ILB muss die IP-Adresse des internen Load Balancer verwendet werden, der zuvor erstellt wurde. Verwenden Sie das folgende Skript, um diese IP-Adresse in PowerShell abzurufen.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Kopieren Sie in einer der VMs das unten stehende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablen auf die zuvor notierten Werte fest.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. Nachdem Sie die Variablen festgelegt haben, öffnen Sie ein Windows PowerShell-Fenster mit erhöhten Rechten, und kopieren Sie das Skript aus dem Text-Editor zur Ausführung in Ihre Azure PowerShell-Sitzung. Wenn die Aufforderung weiterhin >> anzeigt, geben Sie erneut ENTER ein, um die Skriptausführung zu starten.

2. Wiederholen Sie diesen Schritt für jeden virtuellen Computer. Mit diesem Skript wird die IP-Adressressource mit der IP-Adresse des Clouddienst konfiguriert. Außerdem werden weitere Parameter wie z. B. der Testport festgelegt. Wenn die IP-Adressressource online geschaltet wird, kann sie auf Abrufvorgänge am Testport reagieren, die vom Endpunkt mit Lastenausgleich ausgehen, der zuvor in diesem Tutorial erstellt wurde.

## Onlineschalten des Listeners

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Aufgaben nach der Erstellung

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Testen des Verfügbarkeitsgruppenlisteners (innerhalb desselben VNets)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Nächste Schritte

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=August15_HO9-->