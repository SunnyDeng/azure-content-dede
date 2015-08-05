<properties 
   pageTitle="Mehrere VIPs pro Clouddienst"
   description="Übersicht über MultiVIP und das Festlegen von mehreren virtuellen IP-Adressen für einen Clouddienst"
   services="load-balancer"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/01/2015"
   ms.author="telmos" />

# Mehrere VIPs pro Clouddienst
Sie können über das öffentliche Internet auf Azure-Clouddienste zugreifen, indem Sie eine von Azure bereitgestellte IP-Adresse verwenden. Diese öffentliche IP-Adresse wird als VIP (virtuelle IP) bezeichnet, da sie mit dem Azure-Lastenausgleich verknüpft ist und nicht wirklich mit den VM-Instanzen im Clouddienst. Sie können auf jede VM-Instanz innerhalb eines Clouddiensts über eine einzelne VIP zugreifen.

Es gibt jedoch Szenarios, in denen Sie möglicherweise mehr als eine VIP-Adresse als Einstiegspunkt für den gleichen Clouddienst benötigen. Beispielsweise kann der Clouddienst mehrere Websites hosten, die SSL-Verbindungen über den SSL-Standardport 443 erfordern, wobei jede Website für einen anderen Kunden oder Mandanten gehostet wird. In diesem Fall benötigen Sie eine andere öffentlich zugängliche IP-Adresse für jede Website. Das folgende Diagramm zeigt eine typische Webhosting-Konfiguration mit mehreren Mandanten, die mehrere SSL-Zertifikate auf dem gleichen öffentlichen Port erfordern.

![Multi-VIP-SSL-Szenario](./media/load-balancer-multivip/Figure1.png)

Im Szenario oben verwenden alle VIPs den gleichen öffentlichen Port (443), und der Datenverkehr wird zu einem oder mehreren virtuellen Computern mit Lastenausgleich auf einem eindeutigen privaten Port für die interne IP-Adresse des Clouddiensts umgeleitet, auf dem alle Websites gehostet werden.

>[AZURE.NOTE]Ein weiteres Szenario für die Verwendung mehrerer VIPs besteht im Hosten mehrerer Listener für SQL-AlwaysOn-Verfügbarkeitsgruppen in der gleichen Gruppe von virtuellen Computern.

VIPs sind in der Standardeinstellung dynamisch, das bedeutet, dass die dem Clouddienst tatsächlich zugewiesene IP-Adresse sich mit der Zeit ändern kann. Um dies zu verhindern, können Sie eine VIP-Adresse für Ihren Dienst reservieren. Weitere Informationen zu reservierten virtuellen IP-Adressen finden Sie unter [Reservierte öffentliche IP-Adresse](../virtual-networks-reserved-public-ip).

>[AZURE.NOTE]Informationen zu Preisen für VIPs und reservierte IP-Adressen finden Sie unter [Preise für IP-Adressen](http://azure.microsoft.com/pricing/details/ip-addresses/) .

Sie können PowerShell verwenden, um die vom Clouddienst verwendeten virtuellen IP-Adressen zu überprüfen, um virtuelle IP-Adressen hinzuzufügen und zu entfernen, eine virtuelle IP-Adresse einem Endpunkt zuzuordnen sowie den Lastenausgleich für eine bestimmte virtuelle IP-Adresse zu konfigurieren.

## Einschränkungen

Zum derzeitigen Zeitpunkt ist die Multi-VIP-Funktionalität auf die folgenden Szenarios beschränkt:

- **Nur IaaS**. Sie können Multi-VIP nur für Clouddienste aktivieren, die virtuelle Computer enthalten. Sie können Multi-VIP nicht in PaaS-Szenarios mit Rolleninstanzen verwenden.
- **Nur PowerShell**. Sie können Multi-VIP nur mithilfe von PowerShell verwalten.
- **Keine ARM-Unterstützung**. Der Azure-Ressourcen-Manager unterstützt Multi-VIP nicht.

>[AZURE.IMPORTANT]Diese Einschränkungen sind nicht dauerhaft und können sich jederzeit ändern. Besuchen Sie diese Seite regelmäßig, um über zukünftige Änderungen informiert zu sein.


## So fügen Sie einem Clouddienst eine virtuelle IP-Adresse hinzu
Um Ihrem Dienst eine VIP-Adresse hinzuzufügen, führen Sie den folgenden PowerShell-Befehl aus:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Durch den oben aufgeführten Befehl wird ein Ergebnis angezeigt, das dem folgenden Beispiel ähnelt:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## Entfernen einer VIP-Adresse aus einem Clouddienst
Führen Sie den folgenden PowerShell-Befehl aus, um die VIP zu entfernen, die dem Dienst im obigen Skript hinzugefügt wurde:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT]Sie können eine VIP-Adresse nur entfernen, wenn ihr keine Endpunkte zugeordnet sind.

## Abrufen von VIP-Informationen aus einem Clouddienst
Um die einem Clouddienst zugeordneten virtuellen IP-Adressen abzurufen, führen Sie das folgende PowerShell-Skript aus:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Durch das oben aufgeführte Skript wird ein Ergebnis angezeigt, das dem folgenden Beispiel ähnelt:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

In diesem Beispiel hat der Clouddienst drei virtuelle IP-Adressen:

- **Vip1** ist die Standard-VIP, da der Wert für "IsDnsProgrammedName" auf "true" festgelegt ist.
- **Vip2** und **Vip3** werden nicht verwendet, da sie über keine IP-Adressen verfügen. Sie werden nur verwendet, wenn Sie der VIP-Adresse einen Endpunkt zuordnen.

>[AZURE.NOTE]Ihrem Abonnement werden zusätzliche VIPs nur in Rechnung gestellt, wenn sie einem Endpunkt zugeordnet sind. Weitere Informationen zu Preisen finden Sie unter [Preise für IP-Adressen](http://azure.microsoft.com/pricing/details/ip-addresses/).

## So ordnen Sie eine VIP-Adresse einem Endpunkt zu
Um eine VIP in einem Clouddienst einem Endpunkt zuzuordnen, führen Sie den folgenden PowerShell-Befehl aus:

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
    | Update-AzureVM

Durch den oben aufgeführten Befehl wird ein mit der VIP verknüpfter Endpunkt namens *Vip2* auf Port *80* erstellt und mit dem virtuellen Computer namens *myVM1* in einem Clouddienst mit dem Namen *myService* über *TCP* auf Port *8080* verknüpft.

Führen Sie den folgenden PowerShell-Befehl aus, um die Konfiguration zu überprüfen:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Die Ausgabe ähnelt den folgenden Ergebnissen:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## So aktivieren Sie den Lastenausgleich für eine bestimmten VIP-Adresse
Sie können eine einzelne VIP für den Lastenausgleich mehreren virtuellen Computern zuordnen. Gehen wir beispielsweise davon aus, dass Sie einen Clouddienst mit dem Namen *myService* sowie zwei virtuelle Computer namens *myVM1* und *myVM2* haben. Der Clouddienst verfügt über mehrere VIPs, eine davon heißt *Vip2*. Wenn Sie sicherstellen möchten, dass für jeden Datenverkehr an Port *81* auf *Vip2* Lastenausgleich zwischen *myVM1* und *myVM2* auf Port *8181* ausgeführt wird, führen Sie das folgende PowerShell-Skript aus:

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

Sie können den Lastenausgleich auch zur Verwendung einer anderen VIP aktualisieren. Wenn Sie den folgenden PowerShell-Befehl ausführen, wird z. B. festgelegt, dass eine VIP-Adresse mit dem Namen "Vip1" für den Lastenausgleich verwendet wird:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## Weitere Informationen

[Lastenausgleich für Internetzugriff (Übersicht)](load-balancer-internet-overview.md)

[Erste Schritte mit Lastenausgleich für Internetzugriff](load-balancer-internet-getstarted.md)

[Virtuelle Netzwerke im Überblick](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[Reservierte IP-REST-APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=July15_HO4-->