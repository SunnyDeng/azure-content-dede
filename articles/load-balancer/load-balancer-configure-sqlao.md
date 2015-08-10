<properties 
   pageTitle="Konfigurieren des Lastenausgleichs für SQL-AlwaysOn | Microsoft Azure"
   description="Konfigurieren von Lastenausgleich für SQL-AlwaysOn und Verwenden von Powershell zum Erstellen des Lastenausgleichs für die SQL-Implementierung"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# Konfigurieren des Lastenausgleichs für SQL-AlwaysOn

SQL Server-AlwaysOn-Verfügbarkeitsgruppen können nun mit ILB ausgeführt werden. Verfügbarkeitsgruppen sind eine führende SQL Server-Lösung für hohe Verfügbarkeit und Notfallwiederherstellung. Der Verfügbarkeitsgruppenlistener ermöglicht Clientanwendungen eine nahtlose Verbindung mit dem primären Replikat unabhängig von der Anzahl der Replikate in der Konfiguration.

Der Listenername \(DNS\) wird einer IP-Adresse mit Lastenausgleich zugeordnet, und der Azure-Lastenausgleich leitet den eingehenden Datenverkehr nur an den primären Server in der Replikatgruppe.


Sie können ILB-Unterstützung für SQL Server-AlwaysOn-Endpunkte \(Listener\) verwenden. Sie steuern den Zugriff auf den Listener und können die IP-Adresse mit Lastenausgleich aus einem bestimmten Subnetz im virtuellen Netzwerk \(VNet\) auswählen.

Mithilfe von ILB auf dem Listener kann auf den SQL Server-Endpunkt \(z. B. Server=tcp:ListenerName,1433;Database=DatabaseName\) nur über Folgendes zugegriffen werden:

Dienste und virtuelle Computer im gleichen virtuellen Netzwerk, Dienste und virtuelle Computer aus dem lokalen Netzwerk, Dienste und virtuelle Computer aus verbundenen VNets

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


Interner Lastenausgleich kann nur über PowerShell konfiguriert werden.


## Hinzufügen von internem Lastenausgleich zu einem Dienst 

### Schritt 1:

Im folgenden Beispiel konfigurieren wir ein virtuelles Netzwerk, das ein Subnetz namens "Subnet-1" enthält:

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

Schritt 2.

## Hinzufügen von Endpunkten mit Lastenausgleich für ILB auf jedem virtuellen Computer

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM


## Siehe auch

[Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen](load-balancer-internet-getstarted.md)

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-internal-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO5-->