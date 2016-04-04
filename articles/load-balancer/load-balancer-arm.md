<properties
   pageTitle="Azure-Ressourcen-Manager-Unterstützung für Lastenausgleich – Vorschau | Microsoft Azure"
   description="Verwenden von PowerShell für den Lastenausgleich mit Azure-Ressourcen-Manager (ARM) in der Vorschau Verwenden von Vorlagen für Lastenausgleich"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />


# Unterstützung des Azure-Ressourcen-Managers für Load Balancer 

Der Azure-Ressourcen-Manager (ARM) ist das neue Verwaltungsframework für Dienste in Azure. Der Azure-Lastenausgleich kann jetzt mithilfe von APIs und Tools auf Basis von Azure-Ressourcen-Manager verwaltet werden.

## Konzepte

Mit ARM enthält der Azure-Lastenausgleich die folgenden untergeordneten Ressourcen:

- Front-End-IP-Konfiguration – ein Lastenausgleichsmodul kann ein oder mehrere Front-End-IP-Adressen umschließen, auch als virtuelle IPs (VIPs) bekannt. Diese IP-Adressen dienen als Eingang für den Datenverkehr.

- Back-End-Adresspool – IP-Adressen, die den Netzwerkkarten des virtuellen Computers zugeordnet sind, an die ein Teil der Last verteilt wird.

- Lastenausgleichsregeln – eine Regeleigenschaft ordnet eine bestimmte IP-/Port-Kombination des Front-Ends einer IP-Adressen-/Port-Kombination des Back-Ends zu. Mit einer einzelnen Definition eines Lastenausgleichsmodul können Sie mehrere Lastenausgleichsregeln definieren, von denen jede eine Kombination aus Front-End-IP und Port sowie Back-End-IP und Port ist, die dem virtuellen Computer zugeordnet ist.

- Überprüfungen – Mit Überprüfungen können Sie die Integrität der VM-Instanzen nachverfolgen. Schlägt eine Integritätsüberprüfung fehl, wird die VM-Instanz automatisch aus der Rotation entfernt.

- Eingehende NAT-Regeln – NAT-Regeln definieren den Eingangsdatenverkehr, der durch die Front-End-IP fließt und an die Back-End-IP verteilt wird.


![](./media/load-balancer-arm/load-balancer-arm.png)



## Schnellstartvorlagen
Mit dem Azure-Ressourcen-Manager können Sie Ihre Anwendungen mit einer deklarativen Vorlage bereitstellen. In einer Vorlage können Sie mehrere Dienste zusammen mit ihren Abhängigkeiten bereitstellen. Sie verwenden die gleiche Vorlage, um Ihre Anwendung während jeder Phase des Anwendungslebenszyklus wiederholt bereitzustellen.

Vorlagen umfassen virtuelle Computer, virtuelle Netzwerke, Verfügbarkeitsgruppen, Netzwerkkarten (NICs), Speicherkonten, Lastenausgleichsmodule, Netzwerk-Sicherheitsgruppen und öffentliche IP-Adressen. Mit Vorlagen können Sie alle erforderlichen Elemente einer komplexen Anwendung mithilfe einer einfachen Datei erstellen, die Sie einchecken und gemeinsam bearbeiten können.

[Weitere Informationen zu Vorlagen](http://go.microsoft.com/fwlink/?LinkId=544798)

[Weitere Informationen zu Netzwerkressourcen](../virtual-network/resource-groups-networking)

Vorlagen, die Azure-Lastenausgleich verwenden, finden Sie in diesem [GitHub-Repository](https://github.com/Azure/azure-quickstart-templates) mit einer Reihe von Communityvorlagen.

Beispiele für Vorlagen:

- [2 VMs in a Load Balancer and load balancing rules (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=544799)

- [2 VMs in a VNET with an Internal Load Balancer and Load Balancer rules (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=544800)

- [2 VMs in a Load Balancer and configure NAT rules on the LB (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=544801)


## Einrichten von Azure-Lastenausgleich per PowerShell oder CLI

[Azure Networking Cmdlets](https://msdn.microsoft.com/library/azure/mt163510.aspx) können verwendet werden, um einen Lastenausgleich zu erstellen. Erste Schritte mit ARM-Cmdlets und REST-APIs

- [Erstellen eines Load Balancers mit dem Azure-Ressourcen-Manager](load-balancer-get-started-internet-arm-ps.md)

- [Verwenden der Azure-CLI mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md)

- [Load Balancer REST APIs (in englischer Sprache)](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## Nächste Schritte

Sie können auch einen [Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-arm-ps.md) und die Art des [Verteilungsmodus](load-balancer-distribution-mode.md) des Load Balancers für ein bestimmtes Datenverkehrsverhalten im Netzwerk konfigurieren.

Wenn es für Ihre Anwendung erforderlich ist, dass Verbindungen mit Servern hinter einem Load Balancer aufrechterhalten werden, informieren Sie sich über [TCP-Leerlauftimeout-Einstellungen für den Load Balancer](load-balancer-tcp-idle-timeout.md). Hier erfahren Sie mehr über das Verhalten von Leerlaufverbindungen bei der Verwendung des Azure-Lastenausgleichs.

<!---HONumber=AcomDC_0323_2016-->