<properties 
	pageTitle="Branchenanwendung in Azure | Microsoft Azure" 
	description="Entdecken Sie den Wert einer Branchenanwendung in Azure, richten Sie eine Testumgebung ein, und stellen Sie eine hochverfügbare Konfiguration bereit." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


Richten Sie Ihre webbasierte, nur im Intranet verfügbare Branchenanwendung in Microsoft Azure ein – so profitieren Sie von der einfachen Konfiguration sowie der Möglichkeit, die Anwendung schnell um neue Kapazitäten zu erweitern.
 
Mit den Funktionen für virtuelle Computer und virtuelle Netzwerke der Azure-Infrastrukturdienste können Sie schnell eine Branchenanwendung bereitstellen und ausführen, auf die die Benutzer Ihres Unternehmens zugreifen können. Sie können beispielsweise Folgendes einrichten:

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Da es sich bei Azure Virtual Network um eine Erweiterung Ihres lokalen Netzwerks mit den richtigen Benennungen und dem richtigen Datenverkehrsrouting handelt, greifen Ihre Benutzer so auf die Branchenanwendung zu, als ob diese sich im lokalen Rechenzentrum befände.

Dank dieser Konfiguration können Sie die Kapazität der Anwendung mühelos erweitern, indem Sie neue virtuelle Azure-Computer hinzufügen, deren laufende Kosten für Hardware und Wartung geringer sind als bei der Ausführung einer vergleichbaren Anwendung in Ihrem Rechenzentrum.

Der nächste Schritt besteht darin, eine in Azure gehostete Branchenanwendung zu Entwicklungs- und Testzwecken einzurichten.

## Erstellen einer in Azure gehosteten Branchenanwendung zu Entwicklungs- und Testzwecken

Ein standortübergreifendes virtuelles Netzwerk ist mit einem lokalen Netzwerk per Standort-zu-Standort-VPN oder ExpressRoute-Verbindung verbunden. Wenn Sie eine Entwicklungs- und Testumgebung erstellen möchten, die die endgültige Konfiguration imitiert, und mit dem Anwendungszugriff und der Remoteverwaltung über eine VPN-Verbindung experimentieren möchten, helfen Ihnen die Informationen unter [Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) weiter.

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
Sie können diese Entwicklungs- und Testumgebung im Rahmen Ihres [MSDN-Abonnements](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) oder eines [Azure-Testabonnements](http://azure.microsoft.com/pricing/free-trial/) kostenlos erstellen.

Der nächste Schritt besteht darin, eine Branchenanwendung mit hoher Verfügbarkeit in Azure zu erstellen.

## Bereitstellen einer in Azure gehosteten Branchenanwendung

Die grundlegende, repräsentative Konfiguration für eine Branchenanwendung mit hoher Verfügbarkeit in Azure sieht wie folgt aus:

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Sie besteht aus folgenden Elementen:

- Eine nur im Intranet verfügbare Branchenanwendung mit zwei Servern auf Web- und Datenbankebene.
- Eine SQL Server AlwaysOn-Konfiguration mit zwei virtuellen Computern, auf denen SQL Server ausgeführt wird, sowie einem Hauptknotencomputer in einem Cluster.
- Active Directory-Domänendienste im virtuellen Netzwerk mit zwei Replikatdomänencontrollern.

Eine Übersicht über die Branchenanwendungen finden Sie unter [Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664).

### Stückliste

Diese Basiskonfiguration erfordert den folgenden Satz von Azure-Diensten und -Komponenten:

- Sieben virtuelle Computer
- Vier zusätzliche Datenträger für die Domänencontroller und die virtuellen Computer, auf denen SQL Server ausgeführt wird
- Drei Verfügbarkeitsgruppen
- Ein standortübergreifendes virtuelles Netzwerk
- Zwei Speicherkonten

### Phasen der Bereitstellung

Verwenden Sie zum Bereitstellen dieser Konfiguration das folgende Verfahren:

- Phase 1: Konfigurieren von Azure 

	Verwenden Sie Azure PowerShell, um die Speicherkonten, Verfügbarkeitsgruppen und ein standortübergreifendes virtuelles Netzwerk zu erstellen. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

- Phase 2: Konfigurieren der Domänencontroller

	Konfigurieren Sie zwei Active Directory-Replikatdomänencontroller und DNS-Einstellungen für das virtuelle Netzwerk. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

- Phase 3: Konfigurieren der SQL Server-Infrastruktur

	Erstellen Sie die virtuellen Computer, auf denen SQL Server ausgeführt werden soll, sowie den Cluster. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 3](virtual-machines-workload-high-availability-LOB-application-phase3.md).

- Phase 4: Konfigurieren der Webserver

	Erstellen Sie die virtuellen Webservercomputer, und fügen Sie Ihre Branchenanwendung hinzu. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md).

- Phase 5: Konfigurieren einer SQL Server-AlwaysOn-Verfügbarkeitsgruppe

	Bereiten Sie die Anwendungsdatenbanken vor, erstellen Sie eine SQL Server-AlwaysOn-Verfügbarkeitsgruppe, und fügen Sie dann die Anwendungsdatenbanken hinzu. Die ausführlichen Konfigurationsschritte finden Sie unter [Phase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

Nach der Konfiguration können Sie diese Branchenanwendung problemlos erweitern, indem Sie dem Cluster weitere Webserver oder virtuelle Computer hinzufügen, auf denen SQL Server ausgeführt wird.

## Zusätzliche Ressourcen

[Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: SharePoint Server 2013-Farm](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO3-->