<properties 
   pageTitle="Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen | Microsoft Azure"
   description="In diesem Dokument erhalten Sie einen Überblick über das Verknüpfen von virtuellen Netzwerken (VNets) mit ExpressRoute-Verbindungen."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc" />

# Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)  
- [Template - Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

In diesem Artikel erhalten Sie eine Übersicht über das Verknüpfen von virtuellen Netzwerken (VNETs) mit ExpressRoute-Verbindungen. Virtuelle Netzwerke können Teil desselben Abonnements sein oder zu einem anderen Abonnement gehören. Dieser Artikel gilt für virtuelle Netzwerke, für die das klassische Bereitstellungsmodell verwendet wird. Wenn Sie ein virtuelles Netzwerk verknüpfen möchten, das mit der Azure-Ressourcen-Manager-Bereitstellungsmethode bereitgestellt wurde, können Sie dazu eine Vorlage verwenden. Über die Registerkarte oben können Sie zur Vorlage navigieren.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Konfigurationsvoraussetzungen

- Sie benötigen die neueste Version der Azure PowerShell-Module. Sie können das neueste PowerShell-Modul aus dem PowerShell-Abschnitt der [Azure-Downloadseite](https://azure.microsoft.com/downloads/) herunterladen. Befolgen Sie die Anleitung auf der Seite [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Sie erhalten dort eine Schritt-für-Schritt-Anleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module. 
- Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
- Sie benötigen eine aktive ExpressRoute-Verbindung. 
	- Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren. 
	- Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](expressroute-howto-routing-classic.md). 
	- Das private Azure-Peering muss konfiguriert sein, und das BGP-Peering zwischen Ihrem Netzwerk und Microsoft muss aktiv sein, damit End-to-End-Konnektivität bereitgestellt werden kann.

Sie können bis zu 10 VNets mit einer ExpressRoute-Verbindung verknüpfen. Alle ExpressRoute-Verbindungen müssen sich in derselben geopolitischen Region befinden. Wenn Sie das ExpressRoute Premium-Add-On aktiviert haben, können Sie eine größere Anzahl von virtuellen Netzwerken mit der ExpressRoute-Verbindung verknüpfen. Weitere Informationen zum Premium-Add-On finden Sie in den [häufig gestellten Fragen](expressroute-faqs.md).

## Verknüpfen eines VNets im gleichen Azure-Abonnement mit einer ExpressRoute-Verbindung

Sie können das folgende Cmdlet verwenden, um ein virtuelles Netzwerk mit einer ExpressRoute-Verbindung zu verknüpfen. Stellen Sie sicher, dass das Gateway für das virtuelle Netzwerk erstellt wurde und für das Erstellen von Verknüpfungen bereit ist, bevor Sie das Cmdlet ausführen.

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Verknüpfen eines VNets in einem anderen Azure-Abonnement mit einer ExpressRoute-Verbindung

Eine ExpressRoute-Verbindung kann für mehrere Abonnements freigegeben werden. Die folgende Abbildung zeigt eine einfache schematische Darstellung der Freigabe von ExpressRoute-Verbindungen für mehrere Abonnements. Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede der Abteilungen innerhalb der Organisation kann ihr eigenes Abonnement zum Bereitstellen von Diensten verwenden, für die Verbindung mit dem lokalen Netzwerk kann jedoch eine einzelne gemeinsam genutzte ExpressRoute-Verbindung verwendet werden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Verbindung nutzen.

>[AZURE.NOTE] Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.

![Abonnementübergreifende Konnektivität](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Verwaltung

Der *Verbindungsbesitzer* ist der Administrator/Co-Administrator des Abonnements, in dem die ExpressRoute-Verbindung erstellt wird. Der Besitzer der Verbindung kann Administratoren/Co-Administratoren anderer Abonnements (im Workflowdiagramm *Verbindungsbenutzer* genannt) für die Nutzung der dedizierten Verbindung in seinem Besitz autorisieren. Verbindungsbenutzer, die für die Nutzung der ExpressRoute-Verbindung einer Organisation autorisiert sind, können ein VNet in ihrem Abonnement mit der ExpressRoute-Verbindung verknüpfen, sobald sie autorisiert wurden.

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und aufzuheben. Das Aufheben einer Autorisierung führt dazu, dass alle Verknüpfungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### Aktionen als Verbindungsbesitzer 

#### Erstellen einer Autorisierung
	
Der Verbindungsbesitzer autorisiert die Administratoren anderer Abonnements für die Nutzung der angegebenen Verbindung. Im folgenden Beispiel ermöglicht der Administrator der Verbindung (Contoso IT) dem Administrator eines anderen Abonnements (Dev-Test) das Verknüpfen von 2 VNETs mit der Verbindung, indem dessen Microsoft-ID angegeben wird. Das Cmdlet sendet keine E-Mail an die angegebene Microsoft-ID. Der Verbindungsbesitzer muss den Besitzer des anderen Abonnements explizit darüber benachrichtigen, dass die Autorisierung erfolgt ist.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### Überprüfen von Autorisierungen

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung alle für eine bestimmte Verbindung ausgestellten Autorisierungen überprüfen.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### Aktualisieren von Autorisierungen

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung die Autorisierungen bearbeiten.

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Löschen von Autorisierungen

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung Autorisierungen für einen Benutzer widerrufen oder löschen.

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Aktionen als Verbindungsbenutzer

#### Überprüfen von Autorisierungen

Mit dem folgenden Cmdlet können Benutzer einer Verbindung Autorisierungen überprüfen.

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### Abrufen von Verknüpfungsautorisierungen

Mit dem folgenden Cmdlet können Benutzer einer Verbindung eine Verknüpfungsautorisierung abrufen.

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=AcomDC_0128_2016-->