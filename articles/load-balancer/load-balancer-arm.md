<properties
   pageTitle="Azure-Ressourcen-Manager-Unterstützung für Lastenausgleich – Vorschau | Microsoft Azure"
   description="Verwenden von PowerShell für den Lastenausgleich mit Azure-Ressourcen-Manager (ARM) in der Vorschau Verwenden von Vorlagen für Lastenausgleich"
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
   ms.date="06/30/2015"
   ms.author="joaoma" />


# Unterstützung des Azure-Ressourcen-Managers für Load Balancer 

Der Azure-Ressourcen-Manager (ARM) ist das neue Verwaltungsframework für Dienste in Azure. Der Azure-Lastenausgleich kann jetzt mithilfe von APIs und Tools auf Basis von Azure-Ressourcen-Manager verwaltet werden. Weitere Informationen über den Azure-Ressourcen-Manager finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-preview-portal-using-resource-groups.md).

## Konzepte

Mit ARM enthält der Azure-Lastenausgleich die folgenden untergeordneten Ressourcen:

- Front-End-IP-Konfiguration – ein Lastenausgleichsmodul kann ein oder mehrere Front-End-IP-Adressen umschließen, auch als virtuelle IPs (VIPs) bekannt. Diese IP-Adressen dienen als Eingang für den Datenverkehr.

- Back-End-Adresspool – IP-Adressen, die den Netzwerkkarten des virtuellen Computers zugeordnet sind, an die ein Teil der Last verteilt wird.

- Lastenausgleichsregeln – eine Regeleigenschaft ordnet eine bestimmte IP-/Port-Kombination des Front-Ends einer IP-Adressen-/Port-Kombination des Back-Ends zu. Mit einer einzelnen Definition eines Lastenausgleichsmodul können Sie mehrere Lastenausgleichsregeln definieren, von denen jede eine Kombination aus Front-End-IP und Port sowie Back-End-IP und Port ist, die dem virtuellen Computer zugeordnet ist.

- Überprüfungen – Mit Überprüfungen können Sie die Integrität der VM-Instanzen nachverfolgen. Schlägt eine Integritätsüberprüfung fehl, wird die VM-Instanz automatisch aus der Rotation entfernt.

- Eingehende NAT-Regeln – NAT-Regeln definieren den Eingangsdatenverkehr, der durch die Front-End-IP fließt und an die Back-End-IP verteilt wird.


![](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/resource-groups-networking/20150429054039/figure5.png)



## Schnellstartvorlagen
Mit dem Azure-Ressourcen-Manager können Sie Ihre Anwendungen mit einer deklarativen Vorlage bereitstellen. In einer Vorlage können Sie mehrere Dienste zusammen mit ihren Abhängigkeiten bereitstellen. Sie verwenden die gleiche Vorlage, um Ihre Anwendung während jeder Phase des Anwendungslebenszyklus wiederholt bereitzustellen.

Vorlagen umfassen virtuelle Computer, virtuelle Netzwerke, Verfügbarkeitsgruppen, Netzwerkkarten (NICs), Speicherkonten, Lastenausgleichsmodule, Netzwerk-Sicherheitsgruppen und öffentliche IP-Adressen. Mit Vorlagen können Sie alle erforderlichen Elemente einer komplexen Anwendung mithilfe einer einfachen Datei erstellen, die Sie einchecken und gemeinsam bearbeiten können.

[Weitere Informationen zu Vorlagen](http://go.microsoft.com/fwlink/?LinkId=544798)

[Weitere Informationen zu Netzwerkressourcen](../resource-groups-networking)

Vorlagen, die Azure-Lastenausgleich verwenden, finden Sie in diesem [GitHub-Repository](https://github.com/Azure/azure-quickstart-templates) mit einer Reihe von Communityvorlagen.

Beispiele für Vorlagen:

- [2 VMs in a Load Balancer and load balancing rules](http://go.microsoft.com/fwlink/?LinkId=544799) (in englischer Sprache)

- [2 VMs in a VNET with an Internal Load Balancer and Load Balancer rules](http://go.microsoft.com/fwlink/?LinkId=544800) (in englischer Sprache)

- [2 VMs in a Load Balancer and configure NAT rules on the LB](http://go.microsoft.com/fwlink/?LinkId=544801) (in englischer Sprache)


## Einrichten von Azure-Lastenausgleich per PowerShell oder CLI

[Azure Networking Cmdlets](https://msdn.microsoft.com/library/azure/mt163510.aspx) können verwendet werden, um einen Lastenausgleich zu erstellen. Erste Schritte mit ARM-Cmdlets und REST-APIs

- [Erstellen eines Load Balancers mit dem Azure-Ressourcen-Manager](../load-balancer-arm-powershell)

- [Verwenden der Azure-CLI mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager)

- [Load Balancer REST APIs](https://msdn.microsoft.com/library/azure/mt163651.aspx) (in englischer Sprache)


## Siehe auch

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->