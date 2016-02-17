<properties 
	pageTitle="Verbinden mit virtuellen SQL Server-Maschinen (Ressourcen-Manager) | Microsoft Azure"
	description="In diesem Thema werden mit dem klassischen Bereitstellungsmodell erstellte Ressourcen verwendet, und es beschreibt, wie Sie eine Verbindung mit SQL Server auf einem virtuellen Computer in Azure herstellen. Die Szenarien unterscheiden sich abhängig von der Netzwerkkonfiguration und dem Clientstandort."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/18/2015"
	ms.author="jroth" />

# Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Ressourcen-Manager)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-sql-server-connectivity-resource-manager.md)
- [Classic](virtual-machines-sql-server-connectivity.md)

## Übersicht

Das Konfigurieren von Verbindungen mit SQL Server bei der Ausführung auf einer virtuellen Azure-Maschine im Ressourcen-Manager unterscheidet sich nicht erheblich von den erforderlichen Schritten für eine lokale SQL Server-Instanz. Sie müssen nach wie die Konfigurationsschritte für Firewall, Authentifizierung und Datenbankanmeldungen abarbeiten.

Es gibt jedoch einige Aspekte bei SQL Server-Verbindungen, die insbesondere Azure VMs betreffen. Dieser Artikel behandelt eine Reihe [allgemeiner Konnektivitätsszenarien](#connection-scenarios) und beschreibt dann [ausführliche Schritte für das Konfigurieren von SQL Server-Konnektivität auf einer Azure-VM](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

Der Schwerpunkt dieses Artikels liegt auf Konnektivität. Eine vollständige Anleitung, die Bereitstellung und Konnektivität behandelt, finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

## Konnektivitätsszenarien

Die Weise, wie ein Client Verbindungen mit SQL Server auf einem virtuellen Computer herstellt, unterscheidet sich je nach dem Standort des Clients und der Konfiguration des Computers und/oder Netzwerks. Zu diesen Szenarien gehören:

- [Verbinden mit SQL Server über das Internet](#connect-to-sql-server-over-the-internet)
- [Verbinden mit SQL Server innerhalb des gleichen virtuellen Netzwerks](#connect-to-sql-server-in-the-same-virtual-network)

### Verbinden mit SQL Server über das Internet

Wenn Sie über das Internet eine Verbindung mit Ihrem SQL Server-Datenbankmodul herstellen möchten, sind mehrere Schritte erforderlich: Konfigurieren der Firewall, Aktivieren der SQL-Authentifizierung und Konfigurieren der Netzwerksicherheitsgruppe. Sie benötigen eine Regel für die Netzwerksicherheitsgruppe, die TCP-Datenverkehr über Port 1433 zulässt.

Wenn Sie das Portal verwenden, um mit dem Ressourcen-Manager ein Image für virtuelle Maschinen mit SQL Server bereitzustellen, werden diese Schritte für Sie ausgeführt, wenn Sie für die SQL-Verbindungsoption **Öffentlich** auswählen:

![](./media/virtual-machines-sql-server-connectivity-resource-manager/sql-vm-portal-connectivity.png)

Wurde diese Option während der Bereitstellung nicht ausgewählt, können Sie SQL Server und Ihre virtuellen Maschinen manuell konfigurieren. Führen Sie dazu die [Schritte in diesem Artikel zum manuellen Konfigurieren der Verbindung](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm) aus.

Nach der manuellen Konfiguration kann jeder Client mit Internetzugriff eine Verbindung mit der SQL Server-Instanz herstellen. Dazu muss entweder die öffentliche IP-Adresse der virtuellen Maschine oder die dieser IP-Adresse zugewiesene DNS-Bezeichnung angegeben werden. Wird der SQL Server-Port 1433 verwendet, müssen Sie ihn nicht in der Verbindungszeichenfolge angeben.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Zwar wird so die Konnektivität für Clients über das Internet ermöglicht, dies bedeutet jedoch nicht, dass jeder Verbindungen mit ihrem SQL Server herstellen kann. Clients von außerhalb benötigen den richtigen Benutzernamen und das entsprechende Kennwort. Um die Sicherheit weiter zu erhöhen, können Sie den bekannten Port 1433 vermeiden. Wenn Sie beispielsweise SQL Server zum Lauschen an Port 1500 konfiguriert und entsprechende Regeln für die Firewall und die Netzwerksicherheitsgruppe eingerichtet haben, können Sie eine Verbindung herstellen, indem Sie die Portnummer an den Servernamen anfügen. Siehe folgendes Beispiel:

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Beachten Sie beim Einsatz dieses Verfahrens für die Kommunikation mit SQL Server, dass alle vom Datencenter zurückgegebenen Daten als ausgehender Verkehr gewertet werden. Sie unterliegen den normalen [Preisen für die Übertragung ausgehender Daten](https://azure.microsoft.com/pricing/details/data-transfers/). Dies trifft auch dann zu, wenn Sie dieses Verfahren von einem anderen Computer oder einem anderen Clouddienst im gleichen Azure-Datencenter aus einsetzen, da der Datenverkehr trotzdem den öffentlichen Lastenausgleich von Azure durchläuft.

### Verbinden mit SQL Server innerhalb des gleichen virtuellen Netzwerks

[Virtuelles Netzwerk](..\virtual-network\virtual-networks-overview.md) gibt die Möglichkeit zu weiteren Szenarien. Sie können Verbindungen mit VMs innerhalb des gleichen virtuellen Netzwerks herstellen, selbst wenn diese VMs in verschiedenen Ressourcengruppen ausgeführt werden. Mithilfe eines [Site-to-Site-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) können Sie eine hybride Architektur erstellen, die VMs mit lokalen Netzwerken und Computern verbindet.

Virtuelle Netzwerke ermöglichen auch das Einbinden Ihrer Azure-VMs in eine Domäne. Dies ist die einzige Möglichkeit, wie Sie Windows-Authentifizierung mit SQL Server verwenden können. Für alle anderen Verbindungsszenarien muss SQL-Authentifizierung mit Benutzernamen und Kennwörtern verwendet werden.

Wenn Sie das Portal verwenden, um mit dem Ressourcen-Manager ein Image für virtuelle Maschinen mit SQL Server bereitzustellen, werden die richtigen Firewallregeln für die Kommunikation im virtuellen Netzwerk eingerichtet, wenn Sie die SQL-Verbindungsoption **Privat** auswählen. Wurde diese Option während der Bereitstellung nicht ausgewählt, können Sie SQL Server und Ihre virtuellen Maschinen manuell konfigurieren. Führen Sie dazu die [Schritte in diesem Artikel zum manuellen Konfigurieren der Verbindung](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm) aus. Wenn Sie jedoch eine Domänenumgebung mit Windows-Authentifizierung konfigurieren möchten, brauchen Sie die Schritte in diesem Artikel zum Konfigurieren von SQL-Authentifizierung und der entsprechenden Anmeldeinformationen nicht auszuführen. Netzwerksicherheitsgruppen-Regeln für den Zugriff über das Internet müssen ebenfalls nicht konfiguriert werden.

Vorausgesetzt, Sie haben DNS in Ihrem virtuellen Netzwerk konfiguriert, können Sie eine Verbindung mit Ihrer SQL Server-Instanz herstellen, indem Sie den VM-Computernamen für SQL Server in der Verbindungszeichenfolge angeben. Im folgenden Beispiel wird zudem angenommen, dass darüber hinaus Windows-Authentifizierung konfiguriert und dem Benutzer Zugriff auf die SQL Server-Instanz erteilt wurde.

	"Server=mysqlvm;Integrated Security=true" 

Beachten Sie, dass Sie in diesem Szenario auch die IP-Adresse der VM angeben könnten.

## Schritte zum manuellen Konfigurieren von SQL Server-Konnektivität auf einer Azure-VM

Die folgenden Schritte veranschaulichen, wie Sie mithilfe von SQL Server Management Studio (SSMS) manuell die Konnektivität mit der SQL Server-Instanz einrichten und anschließend optional über das Internet eine Verbindung herstellen. Beachten Sie, dass viele dieser Schritte für Sie ausgeführt werden, wenn Sie die entsprechenden SQL Server-Konnektivitätsoptionen im Portal auswählen.

Bevor Sie eine Verbindung mit der Instanz von SQL Server über einen anderen virtuellen Computer oder über das Internet herstellen können, müssen Sie folgende Aufgaben abschließen, die in den nächsten Abschnitten beschrieben werden:

- [Öffnen der TCP-Ports in der Windows-Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Konfigurieren von SQL Server für das Abhören des TCP-Protokolls](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus](#configure-sql-server-for-mixed-mode-authentication)
- [Erstellen von Anmeldenamen für die SQL Server-Authentifizierung](#create-sql-server-authentication-logins)
- [Konfigurieren einer DNS-Bezeichnung für die öffentliche IP-Adresse](#configure-a-dns-label-for-the-public-ip-address)
- [Verbinden mit dem Datenbankmodul von einem anderen Computer aus](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server auf einem virtuellen Computer](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server im VM-Ressourcen-Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server im VM-Ressourcen-Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Nächste Schritte

Anweisungen zur Bereitstellung für diese Konnektivitätsschritte finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

Es ist wichtig, alle bewährten Sicherheitsmethoden für SQL Server bei der Ausführung auf virtuellen Azure-Computern durchzuarbeiten. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-security-considerations.md).

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->