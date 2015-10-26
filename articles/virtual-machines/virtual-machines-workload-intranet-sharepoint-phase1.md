<properties
	pageTitle="SharePoint Server 2013-Farm, Phase 1 | Microsoft Azure"
	description="In Phase 1 der SharePoint Server 2013-Farm erstellen Sie in Azure das virtuelle Netzwerk und weitere Elemente der Azure-Infrastruktur."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# SharePoint-Intranetfarm-Workload Phase 1: Konfigurieren von Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


In dieser Phase der Intranet-Bereitstellung einer SharePoint 2013-Farm mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in den Azure-Infrastrukturdiensten erstellen Sie die Azure Netzwerk- und Speicherinfrastruktur in der Azure-Dienstverwaltung. Diese Phase muss vor Beginn von [Phase 2](virtual-machines-workload-intranet-sharepoint-phase2.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

Azure muss mit den folgenden grundlegenden Netzwerkkomponenten bereitgestellt werden:

- Einem standortübergreifenden virtuellen Netzwerk mit einem Subnetz.
- Drei Azure Clouddiensten.
- Einem Azure-Speicherkonto zum Speichern von VHD-Datenträgerimages und weiteren Datenträgern.

## Voraussetzungen

Füllen Sie vor der Konfiguration der Azure-Komponenten die folgenden Tabellen aus. Dieser Abschnitt soll Ihnen bei der Konfiguration von Azure helfen. Am besten drucken Sie die Seite aus und notieren Sie sich in den Lücken die erforderlichen Informationen oder kopieren Sie den Abschnitt in ein Dokument und füllen Sie es in der elektronischen Version aus.

Die Einstellungen für das virtuelle Netzwerk (VNet) tragen Sie in Tabelle V ein.

Element | Konfigurationselement | Beschreibung | Wert
--- | --- | --- | ---
1\. | VNet-Name | Der Name, den Sie dem virtuellen Azure-Netzwerk zuweisen (z. B. SPFarmNet) . | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | VNet-Standort | Das Azure-Rechenzentrum, in dem sich das virtuelle Netzwerk befindet. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Name des lokalen Netzwerks | Der Name, den Sie Ihrem Unternehmensnetzwerk zuweisen. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | IP-Adresse des VPN-Geräts | Die öffentliche IPv4-Adresse der Schnittstelle Ihres VPN-Geräts im Internet. Fragen Sie Ihre IT-Abteilung nach dieser Adresse. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | VNet-Adressraum | Der Adressraum des virtuellen Netzwerks (definiert in einem einzigen privaten Adresspräfix). Fragen Sie Ihre IT-Abteilung nach diesem Adressraum. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Erster finaler DNS-Server | Die vierte mögliche IP-Adresse für den Adressraum des Subnetzes des virtuellen Netzwerks (Tabelle S). Fragen Sie Ihre IT-Abteilung nach diesen Adressen. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Zweiter finaler DNS-Server | Die fünfte mögliche IP-Adresse für den Adressraum des Subnetzes des virtuellen Netzwerks (Tabelle S). Fragen Sie Ihre IT-Abteilung nach diesen Adressen. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle V: Konfiguration eines standortübergreifenden virtuellen Netzwerks**

Füllen Sie für das Subnetz dieser Lösung Tabelle S aus. Geben Sie für das Subnetz einen Anzeigenamen, einen auf dem Adressraum des virtuellen Netzwerks basierenden IP-Adressraum und einen beschreibenden Zweck an. Der Adressraum muss im Classless Interdomain Routing (CIDR)-Format eingegeben werden, das auch als Netzwerkpräfixformat bezeichnet wird. Beispiel: 10.24.64.0/20. Fragen Sie Ihre IT-Abteilung nach diesem Adressraum. Er wird aus dem Adressraum des virtuellen Netzwerks bestimmt.

Element | Subnetzname | Subnetzadressraum | Zweck
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle S: Subnetze im virtuellen Netzwerk**

> [AZURE.NOTE]Diese vordefinierte Architektur verwendet aus Gründen der Einfachheit nur ein Subnetz. Wenn Sie zur Emulierung einer Subnetzisolierung verschiedene Datenverkehrsfilter überlagern möchten, können Sie Azure-[Netzwerksicherheitsgruppen](virtual-networks-nsg.md) verwenden.

Füllen Sie für die zwei lokalen DNS-Server, die Sie bei der anfänglichen Einrichtung der Domänencontroller in Ihrem virtuellen Netzwerk verwenden möchten, Tabelle D aus. Geben Sie jedem DNS-Server einen Anzeigenamen und eine einzelne IP-Adresse. Der Anzeigename muss nicht mit dem Hostnamen oder dem Computernamen des DNS-Servers übereinstimmen. Auch wenn hierfür nur zwei Einträge vorgesehen sind, können Sie noch weitere hinzufügen. Erarbeiten Sie diese Liste gemeinsam mit Ihrer IT-Abteilung.

Element | Anzeigename des DNS-Servers | IP-Adresse des DNS-Servers
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle D: Lokale DNS-Server**

Zur Weiterleitung von Paketen aus dem standortübergreifenden Netzwerk über die Site-to-Site-VPN-Verbindung müssen Sie das virtuelle Netzwerk mit einem lokalen Netzwerk konfigurieren, das eine Liste der Adressräume (in CIDR-Notation) für alle erreichbaren Standorte im lokalen Netzwerk Ihres Unternehmens enthält. Die Liste der Adressräume, die Ihr lokales Netzwerk definieren, darf sich nicht mit den Adressräumen anderer virtueller oder lokaler Netzwerke überschneiden. Das bedeutet nichts anderes, als dass die Adressräume für die konfigurierten virtuellen Netzwerke und die lokalen Netzwerke eindeutig sein müssen.

Füllen Sie zur Angabe der Adressräume Ihres lokalen Netzwerks Tabelle L aus. Hierfür sind zwar nur drei Einträge vorgesehen, vermutlich werden Sie aber mehr benötigen. Erarbeiten Sie die Liste dieser Adressräume gemeinsam mit Ihrer IT-Abteilung.

Element | Adressraum des lokalen Netzwerks
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle L: Adresspräfixe für das lokale Netzwerk**

Zum Erstellen des virtuellen Netzwerks mit den Einstellungen der Tabellen V, S, D, und L gehen Sie nach den Anweisungen im Abschnitt [Erstellen eines standortübergreifenden virtuellen Netzwerks mithilfe von Konfigurationstabellen](virtual-machines-workload-deploy-vnet-config-tables.md) vor.

> [AZURE.NOTE]Dieses Verfahren führt Sie durch die Erstellung eines virtuellen Netzwerks, das eine Site-to-Site-VPN-Verbindung verwendet. Informationen zur Verwendung von ExpressRoute für Ihre Site-to-Site-Verbindung finden Sie unter [ExpressRoute – technische Übersicht](../expressroute/expressroute-introduction.md).

Nach der Erstellung des virtuellen Azure-Netzwerks ermittelt das Azure-Verwaltungsportal Folgendes:

- Die öffentliche IPv4-Adresse des Azure-VPN-Gateways Ihres virtuellen Netzwerks.
- Den vorinstallierten Internet Protocol Security (IPsec)-Schlüssel für die Site-to-Site-VPN-Verbindung.

Um diese Informationen nach der Erstellung des virtuellen Netzwerks im Azure-Verwaltungsportal anzuzeigen, klicken Sie auf **Netzwerke**, den Namen des virtuellen Netzwerks und dann auf die Menüoption **Dashboard**.

Als Nächstes konfigurieren Sie das Gateway für das virtuelle Netzwerk, um eine sichere Site-to-Site-VPN-Verbindung zu erstellen. Anweisungen hierzu finden Sie unter [Konfigurieren eines Gateways eines virtuellen Netzwerks im Verwaltungsportal](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

Erstellen Sie die Site-to-Site-VPN-Verbindung zwischen dem neuen virtuellen Netzwerk und einem lokalen VPN-Gerät. Anweisungen hierzu finden Sie unter [Konfigurieren eines Gateways eines virtuellen Netzwerks im Verwaltungsportal](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

Stellen Sie anschließend sicher, dass der Adressraum des virtuellen Netzwerks aus Ihrem lokalen Netzwerk erreichbar ist. Dies erfolgt in der Regel durch Hinzufügen einer Route zwischen dem Adressraum des virtuellen Netzwerks und Ihrem VPN-Gerät und der Bekanntgabe dieser Route für den Rest der Routinginfrastruktur Ihres Unternehmensnetzwerks. Erarbeiten Sie diese Lösung gemeinsam mit Ihrer IT-Abteilung.

Befolgen Sie anschließend die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md) zum Installieren von Azure PowerShell auf dem lokalen Computer. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung.

Wählen Sie zunächst das richtige Azure-Abonnement für diese Befehle aus. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Sie erhalten den korrekten Abonnementnamen aus der Eigenschaft **SubscriptionName** der Ausgabe des Befehls **Get-AzureSubscription**.

Erstellen Sie als Nächstes die drei für diese SharePoint-Farm erforderlichen Clouddienste. Füllen Sie Tabelle C aus.

Element | Zweck | Name des Clouddiensts
--- | --- | ---
1\. | Domänencontroller | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL-Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | SharePoint-Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle C: Namen der Clouddienste**

Wählen Sie für jeden Clouddienst einen eindeutigen Namen aus. *Der Name eines Clouddiensts darf nur Buchstaben, Zahlen und Bindestriche enthalten. Das erste und das letzte Zeichen im Feld müssen Buchstaben oder Zahlen sein.*

So können Sie den ersten Clouddienst beispielsweise „DCs-*EindeutigeZeichenfolge*“ nennen, wobei *EindeutigeZeichenfolge* eine Abkürzung Ihres Unternehmens ist. Heißt Ihr Unternehmen beispielsweise „Tailspin Toys“, so können Sie den Clouddienst „DCs-Tailspin“ nennen.

Mit dem folgenden Azure PowerShell-Befehl können Sie den Namen auf seine Eindeutigkeit auf dem lokalen Computer testen.

	Test-AzureName -Service <Proposed cloud service name>

Wenn dieser Befehl „False“ zurückgibt, ist der vorgeschlagene Name eindeutig. Erstellen Sie den Clouddienst dann mit dem folgenden Befehl:

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

Notieren Sie die Namen der neu erstellten Clouddienste in Tabelle C.

Erstellen Sie als Nächstes ein Speicherkonto für die SharePoint-Farm. *Geben Sie einen eindeutigen Namen nur mit Kleinbuchstaben und Zahlen ein.* Mit dem folgenden Azure PowerShell-Befehl können Sie überprüfen, ob der Name des Speicherkontos eindeutig ist:

	Test-AzureName -Storage <Proposed storage account name>

Wenn dieser Befehl „False“ zurückgibt, ist der vorgeschlagene Name eindeutig. Erstellen Sie das Speicherkonto anschließend und aktivieren Sie es für das Abonnement. Verwenden Sie dazu die folgenden Befehle:

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Definieren Sie als Nächstes die Namen von vier Verfügbarkeitsgruppen. Füllen Sie Tabelle A aus.

Element | Zweck | Name der Verfügbarkeitsgruppe
--- | --- | ---
1\. | Domänencontroller | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL-Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | SharePoint-Anwendungsserver | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | SharePoint-Front-End-Webserver | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle A: Namen der Verfügbarkeitsgruppen**

Sie benötigen diesen Namen beim Erstellen der virtuellen Computer in den Phasen 2, 3 und 4.

Hier sehen Sie die nach erfolgreichem Abschluss dieser Phase erstellte Konfiguration.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieses Workloads gehen Sie zu [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md).

## Zusätzliche Ressourcen

[Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO3-->