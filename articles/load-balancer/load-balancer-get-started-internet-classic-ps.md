<properties 
   pageTitle="Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff im klassischen Modus mithilfe von PowerShell | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen Load Balancer mit Internetzugriff im klassischen Modus mithilfe von PowerShell erstellen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers (klassisch) mit Internetzugriff in PowerShell

[AZURE.INCLUDE [Load-Balancer-Get-Started-Internet-Classic-Selectors-include.MD](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können auch [erfahren, wie Sie mit dem Azure-Ressourcen-Manager einen Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Einrichten des Load Balancers mit PowerShell

Führen Sie zum Einrichten eines Load Balancers mithilfe von PowerShell die folgenden Schritte aus:

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.


2. Nach dem Erstellen eines virtuellen Computers können Sie PowerShell-Cmdlets verwenden, um einem virtuellen Computer in demselben Clouddienst einen Load Balancer hinzuzufügen.

Im folgenden Beispiel fügen Sie eine Load Balancer-Gruppe namens „webfarm“ zum Clouddienst „mytestcloud“ (oder „myctestcloud.cloudapp.net“) hinzu. Dabei werden die Endpunkte für den Load Balancer den virtuellen Computern „web1“ und „web2“ hinzugefügt. Der Load Balancer empfängt Netzwerkdatenverkehr an Port 80 und führt für die durch den lokalen Endpunkt (in diesem Fall Port 80) festgelegten virtuellen Computer per TCP einen Lastenausgleich durch.


### Schritt 1
Erstellen eines Endpunkts mit Lastenausgleich für den ersten virtuellen Computer „web1“

	Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### Schritt 2 

Erstellen eines weiteren Endpunkts für den zweiten virtuellen Computer „web2“ unter Verwendung desselben Namens für die Load Balancer-Gruppe

	Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## Entfernen eines virtuellen Computers aus einem Load Balancer

Sie können mithilfe von „Remove-AzureEndpoint“ den Endpunkt eines virtuellen Computers aus dem Load Balancer entfernen.

	Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## Nächste Schritte

Sie können auch einen [internen Lastenausgleich erstellen](load-balancer-get-started-ilb-classic-ps.md) und die Art des [Verteilungsmodus](load-balancer-distribution-mode.md) des Lastenausgleichs für ein bestimmtes Datenverkehrsverhalten im Netzwerk konfigurieren.

Wenn es für Ihre Anwendung erforderlich ist, dass Verbindungen mit Servern hinter einem Lastenausgleich aufrechterhalten werden, informieren Sie sich über [TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md). Hier erfahren Sie mehr über das Verhalten von Leerlaufverbindungen bei der Verwendung des Azure-Lastenausgleichs.

<!---HONumber=AcomDC_0128_2016-->