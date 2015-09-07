<properties 
   pageTitle="Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Computern"
	description="Dieses Thema beschreibt, wie eine Verbindung zu einer SQL Server-Instanz hergestellt wird, die auf einem virtuellen Computer in Azure ausgeführt wird. Die Szenarien unterscheiden sich abhängig von der Netzwerkkonfiguration und dem Clientstandort."
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
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Computern
 
## Übersicht

Das Konfigurieren von Verbindungen mit SQL Server bei der Ausführung auf einem virtuellen Azure-Computer unterscheidet sich nicht dramatisch von den erforderlichen Schritten für eine lokale SQL Server-Instanz. Sie müssen nach wie die Konfigurationsschritte für Firewall, Authentifizierung und Datenbankanmeldungen abarbeiten.

Es gibt jedoch einige Aspekte bei SQL Server-Verbindungen, die insbesondere Azure VMs betreffen. Dieser Artikel behandelt eine Reihe [allgemeiner Konnektivitätsszenarien](#connection-scenarios) und beschreibt dann [ausführliche Schritte für das Konfigurieren von SQL Server-Konnektivität in einer Azure VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE]Der Schwerpunkt dieses Artikels liegt auf Konnektivität. Eine vollständige Anleitung, die Bereitstellung und Konnektivität behandelt, finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

## Konnektivitätsszenarien

Die Weise, wie ein Client Verbindungen mit SQL Server auf einem virtuellen Computer herstellt, unterscheidet sich je nach dem Standort des Clients und der Konfiguration des Computers und/oder Netzwerks. Zu diesen Szenarien gehören:

- [Verbinden mit SQL Server im selben Cloud-Dienst](#connect-to-sql-server-in-the-same-cloud-service)
- [Verbinden mit SQL Server über das Internet](#connect-to-sql-server-over-the-internet)
- [Verbinden mit SQL Server innerhalb des gleichen virtuellen Netzwerks](#connect-to-sql-server-in-the-same-virtual-network)

### Verbinden mit SQL Server im selben Cloud-Dienst

Im gleichen Clouddienst können mehrere virtuelle Computer erstellt werden. Das Konzept dieses Szenarios mit virtuellen Computern ist unter [Verbinden virtueller Computer mit einem virtuellen Netzwerk oder Clouddienst](cloud-services-connect-virtual-machine.md) erläutert.

Führen Sie zunächst die [Schritte in diesem Artikel zum Konfigurieren der Konnektivität](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm) aus. Beachten Sie, dass Sie keinen öffentlichen Endpunkt einrichten müssen, wenn Sie Verbindungen zwischen Computern im gleichen Clouddienst einrichten.

Sie können den **Hostnamen** der VM in der Clientverbindungszeichenfolge verwenden. Der Hostname ist der Name, den Sie Ihrer VM bei der Erstellung gegeben haben. Wenn Ihre SQL-VM beispielsweise **mysqlvm** heißt und den Clouddienst-DNS-Namen **mycloudservice.cloudapp.net** trägt, kann eine Client-VM im gleichen Clouddienst die folgende Verbindungszeichenfolge zum Herstellen einer Verbindung verwenden:

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### Verbinden mit SQL Server über das Internet

Wenn Sie eine Verbindung mit Ihrem SQL Server-Datenbankmodul aus dem Internet herstellen möchten, müssen Sie einen VM-Endpunkt für eingehende TCP-Kommunikation erstellen. Dieser Azure-Konfigurationsschritt leitet den eingehenden Datenverkehr des TCP-Ports zu einem TCP-Port, auf den der virtuelle Computer zugreifen kann.

Führen Sie zunächst die [Schritte in diesem Artikel zum Konfigurieren der Konnektivität](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm) aus. Jeder Client mit Internetzugang kann dann durch Angeben des Clouddienst-DNS-Namens (wie etwa **mycloudservice.cloudapp.net**) und des VM-Endpunkts (wie etwa **57500**) eine Verbindung mit der SQL Server-Instanz herstellen.

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Zwar wird so die Konnektivität für Clients über das Internet ermöglicht, dies bedeutet jedoch nicht, dass jeder Verbindungen mit ihrem SQL Server herstellen kann. Clients von außerhalb benötigen den richtigen Benutzernamen und das entsprechende Kennwort. Zur weiteren Erhöhung der Sicherheit können Sie einen anderen als den bekannten Port 1433 für den öffentlichen Endpunkt des virtuellen Computers verwenden. Nach Möglichkeit sollten Sie dem Endpunkt auch eine ACL hinzufügen, um den Datenverkehr auf die von Ihnen zugelassenen Clients einzuschränken. Weitere Informationen zur Verwendung von ACLs für Endpunkte finden Sie unter [Verwalten der ACL für einen Endpunkt](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)

>[AZURE.NOTE]Beachten Sie beim Einsatz dieses Verfahrens für die Kommunikation mit SQL Server, dass alle vom Datencenter zurückgegebenen Daten als ausgehender Verkehr gewertet werden. Sie unterliegen den normalen [Preisen für die Übertragung ausgehender Daten](http://azure.microsoft.com/pricing/details/data-transfers). Dies trifft auch dann zu, wenn Sie dieses Verfahren von einem anderen Computer oder einem anderen Clouddienst im gleichen Azure-Datencenter aus einsetzen, da der Datenverkehr trotzdem den öffentlichen Lastenausgleich von Azure durchläuft.

### Verbinden mit SQL Server innerhalb des gleichen virtuellen Netzwerks

[Virtuelles Netzwerk](..\virtual-network\virtual-networks-overview.md) gibt die Möglichkeit zu weiteren Szenarien. Sie können Verbindungen mit VMs innerhalb des gleichen virtuellen Netzwerks herstellen, selbst wenn diese VMs in verschiedenen Clouddiensten ausgeführt werden. Mithilfe eines [websiteübergreifenden VPNs](../vpn-gateway/vpn-gateway-site-to-site-create.md) können Sie eine hybride Architektur erstellen, die VMs mit lokalen Netzwerken und Computern verbindet.

Virtuelle Netzwerke ermöglichen auch das Einbinden Ihrer Azure-VMs in eine Domäne. Dies ist die einzige Möglichkeit, wie Sie Windows-Authentifizierung mit SQL Server verwenden können. Für alle anderen Verbindungsszenarien muss SQL-Authentifizierung mit Benutzernamen und Kennwörtern verwendet werden.

Führen Sie zunächst die [Schritte in diesem Artikel zum Konfigurieren der Konnektivität](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm) aus. Wenn Sie eine Domänenumgebung mit Windows-Authentifizierung konfigurieren möchten, brauchen Sie die Schritte in diesem Artikel zum Konfigurieren von SQL-Authentifizierung und der entsprechenden Anmeldeinformationen nicht auszuführen. Außerdem ist für dieses Szenario kein öffentlicher Endpunkt erforderlich.

Vorausgesetzt, Sie haben DNS konfiguriert, können Sie eine Verbindung mit Ihrer SQL Server-Instanz herstellen, indem Sie den VM-Hostnamen für SQL Server in der Verbindungszeichenfolge angeben. Im folgenden Beispiel wird angenommen, dass darüber hinaus Windows-Authentifizierung konfiguriert und dem Benutzer Zugriff auf die SQL Server-Instanz erteilt wurde.

	"Server=mysqlvm;Integrated Security=true" 

Beachten Sie, dass Sie in diesem Szenario auch die IP-Adresse der VM angeben könnten.

## Schritte zum Konfigurieren von SQL Server-Konnektivität in einer Azure-VM

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server auf einem virtuellen Computer](../../includes/virtual-machines-sql-server-connection-steps.md)]

## Nächste Schritte

Anweisungen zur Bereitstellung für diese Konnektivitätsschritte finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

Wenn Sie außerdem AlwaysOn-Verfügbarkeitsgruppen zum Erzielen von hoher Verfügbarkeit und Notfallwiederherstellung einsetzen möchten, sollten Sie die Implementierung eines Listeners in Erwägung ziehen. Datenbankclients stellen dann Verbindungen mit dem Listener anstelle direkter Verbindungen mit den SQL Server-Instanzen her. Der Listener leitet Clients auf das primäre Replikat in der Verfügbarkeitsgruppe um. Weitere Informationen finden Sie unter [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Es ist wichtig, alle bewährten Sicherheitsmethoden für SQL Server bei der Ausführung auf virtuellen Azure-Computern durchzuarbeiten. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-security-considerations.md).

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->