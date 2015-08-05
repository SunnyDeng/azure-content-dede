<properties 
   pageTitle="Freigeben einer ExpressRoute-Verbindung für mehrere Abonnements | Microsoft Azure"
   description="Dieser Artikel führt Sie durch die Freigabe Ihrer ExpressRoute-Verbindung für mehrere Azure-Abonnements."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2015"
   ms.author="cherylmc" />

# Freigeben einer ExpressRoute-Verbindung für mehrere Abonnements

Eine einzelne ExpressRoute-Verbindung kann für mehrere Abonnements freigegeben werden. **Abbildung 1** zeigt, wie die Freigabe von ExpressRoute-Verbindungen für mehrere Abonnements funktioniert.

Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jeder der Abteilungen innerhalb der Organisation kann ihr eigenes Abonnement zum Bereitstellen ihrer Dienste verwenden, kann aber eine einzelne dedizierte ExpressRoute-Verbindung für die Verbindung mit dem Unternehmensnetzwerk mit anderen Abteilungen gemeinsam nutzen. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die dedizierte ExpressRoute-Verbindung besitzen. Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der dedizierten Verbindung in Rechnung gestellt. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Verbindung nutzen.

**Abbildung 1**

![Abonnementfreigabe](./media/expressroute-share-circuit/IC766124.png)

## Verwaltung

Der *Verbindungsbesitzer* ist der Administrator/Co-Administrator des Abonnements, in dem die dedizierte ExpressRoute-Verbindung erstellt wird. Der Besitzer der Verbindung kann Administratoren/Co-Administratoren anderer Abonnements (im Workflowdiagramm *Verbindungsbenutzer* genannt) für die Nutzung der dedizierten Verbindung in seinem Besitz autorisieren. Verbindungsbenutzer, die für die Nutzung der dedizierten Verbindung einer Organisation autorisiert sind, können VNet in ihrem Abonnement mit der dedizierten Verbindung verknüpfen, sobald sie autorisiert sind.

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und aufzuheben. Das Aufheben einer Autorisierung führt dazu, dass alle Verknüpfungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

## Workflow

1. Der Verbindungsbesitzer autorisiert die Administratoren anderer Abonnements für die Nutzung der angegebenen Verbindung. Im folgenden Beispiel ermöglicht der Administrator der Verbindung (Contoso-IT) dem Administrator eines anderen Abonnements (Contoso Sales) das Verknüpfen von 2 VNETs mit der Verbindung, indem dessen Microsoft-ID angegeben wird. Das Cmdlet sendet keine E-Mail an die angegebene Microsoft-ID. Der Verbindungsbesitzer muss den Besitzer des anderen Abonnements explizit darüber benachrichtigen, dass die Autorisierung erfolgt ist.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. Nach Benachrichtigung durch den Verbindungsbesitzer kann der Administrator des autorisierten Abonnements zum Abrufen des Dienstschlüssels der Verbindung das folgende Cmdlet ausführen. In diesem Beispiel muss sich der Administrator von Contoso Sales zunächst mit der angegebenen Microsoft-ID (salesadmin@contoso.com) anmelden.

		PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. Der Administrator des autorisierten Abonnements führt das folgende Cmdlet aus, um den Verknüpfungsvorgang abzuschließen.

		PS C:\> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

Das ist schon alles. Das VNet von Contoso Sales in Azure ist nun mit einer Verbindung im Besitz von ContosoIT verknüpft.

## Verwalten der Autorisierung

Der Verbindungsbesitzer kann eine Verbindung für bis zu 10 Azure-Abonnements freigeben. Der Verbindungsbesitzer kann sehen, wer für die Verbindung autorisiert wurde. Der Besitzer kann die Autorisierung jederzeit aufheben. Wenn der Verbindungsbesitzer eine Autorisierung aufhebt, werden alle anhand von "LinkAuthorizationId" identifizierten Verknüpfungen, die durch diese Autorisierung zugelassen sind, sofort gelöscht. Die verknüpften VNETs haben dann über die ExpressRoute-Verbindung keine Konnektivität mehr mit dem lokalen Netzwerk.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


Das folgende Diagramm zeigt den Workflow der Autorisierung:

![Workflow des Freigebens für andere Abonnements](./media/expressroute-share-circuit/IC759525.png)

## Nächste Schritte

Weitere Informationen zu ExpressRoute finden Sie unter [ExpressRoute – Übersicht](expressroute-introduction.md).

<!---HONumber=July15_HO4-->