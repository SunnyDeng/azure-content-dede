<properties
   pageTitle="Zurücksetzen einer Azure VPN Gateway-Instanz | Microsoft Azure"
   description="In diesem Artikel sind die Schritte zum Zurücksetzen einer Azure VPN Gateway-Instanz beschrieben. Dieser Artikel bezieht sich auf VPN-Gateways, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="cherylmc"/>

# Zurücksetzen einer Azure VPN Gateway-Instanz mit PowerShell


In diesem Artikel werden die Schritte zum Zurücksetzen einer Azure VPN Gateway-Instanz mithilfe von PowerShell-Cmdlets beschrieben. Diese Anweisungen gelten für das klassische Bereitstellungsmodell. Zum Zurücksetzen von VPN-Gateways für virtuelle Netzwerke, die mithilfe des Ressourcen-Manager-Bereitstellungsmodells erstellt wurden, sind noch keine Cmdlets oder REST-APIs verfügbar. Diese Cmdlets/APIs werden gegenwärtig entwickelt. Zeigen Sie Ihr virtuelles Netzwerk im Vorschauportal an, um zu ermitteln, ob Ihr VPN-Gateway mithilfe des klassischen Bereitstellungsmodells erstellt wurde. Virtuelle Netzwerke, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden, werden im Vorschauportal im Bereich "Virtuelles Netzwerk (klassisch)" angezeigt.

Das Zurücksetzen von Azure VPN Gateway-Instanzen ist nützlich, wenn die standortübergreifende VPN-Verbindung bei mindestens einem S2S-VPN-Tunnel unterbrochen ist. In diesem Fall funktionieren Ihre lokalen VPN-Geräte ordnungsgemäß, können jedoch keine IPsec-Tunnelverbindungen mit Azure VPN Gateway-Instanzen herstellen. Bei Verwendung des Cmdlets *Reset-AzureVNetGateway* wird das Gateway neu gestartet, und die standortübergreifenden Konfigurationen werden erneut auf das Gateway angewendet. Die öffentliche IP-Adresse des Gateways bleibt unverändert. Die VPN-Routerkonfiguration muss also nicht mit einer neuen öffentlichen IP-Adresse für Azure VPN Gateway aktualisiert werden.


Überprüfen Sie vor dem Zurücksetzen des Gateways die unten aufgeführten Elemente für die einzelnen IPsec-Site-to-Site-VPN-Tunnel (S2S). Treten bei diesen Elementen Konflikte auf, werden S2S-VPN-Tunnelverbindungen unterbrochen. Indem Sie die Konfigurationen für Ihre lokalen VPN-Gateways und Azure VPN Gateway-Instanzen überprüfen und korrigieren, werden nicht erforderliche Neustarts und Unterbrechungen anderer funktionierender Verbindungen der Gateways verhindert.

Überprüfen Sie die folgenden Elemente, bevor Sie Ihr Gateway zurücksetzen.

- Die Internet-IP-Adressen (VIPs) der Azure VPN Gateway-Instanz und des lokalen VPN-Gateways sind sowohl in Azure als auch in den lokalen VPN-Richtlinien ordnungsgemäß konfiguriert.
- Der vorinstallierte Schlüssel muss sowohl bei der Azure VPN Gateway-Instanz als auch auf dem lokalen VPN-Gateway identisch sein.
- Wenn Sie eine bestimmte IPsec/IKE-Konfiguration – z. B. Verschlüsselung, Hashalgorithmus oder PFS (Perfect Forward Secrecy) – anwenden, stellen Sie sicher, dass die Azure VPN Gateway-Instanz und das lokale VPN-Gateway dieselbe Konfiguration aufweisen.


## Zurücksetzen eines VPN-Gateways mit PowerShell

Zum Zurücksetzen von Azure VPN Gateway-Instanzen wird das PowerShell-Cmdlet *Reset-AzureVNetGateway* verwendet. Jede Azure VPN Gateway-Instanz umfasst zwei VM-Instanzen, die in einer Konfiguration mit aktivem Standbymodus ausgeführt werden. Wenn der Befehl ausgeführt wird, wird die gegenwärtig aktive Instanz der Azure VPN Gateway-Instanz umgehend neu gestartet. Während des Failovers von der aktiven Instanz (die Instanz, die neu gestartet wird) auf die Standbyinstanz kommt es zu einer kurzen Unterbrechung. Diese Unterbrechung sollte weniger als 1 Minute dauern.

Im folgenden Beispiel wird die Azure VPN Gateway-Instanz für das virtuelle Netzwerk "ContosoVNet" zurückgesetzt.
 
			D:\PS> Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 		Error          :
	 		HttpStatusCode : OK
	 		Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 		Status         : Successful
			RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
			StatusCode     : OK


Wenn die Verbindung nach dem ersten Neustart nicht wiederhergestellt wird, führen Sie denselben Befehl erneut aus, um die zweite VM-Instanz (das neue aktive Gateway) neu zu starten. Wenn die beiden Neustarts nacheinander angefordert werden, dauert der Neustart der beiden VM-Instanzen (aktive Instanz und Standbyinstanz) etwas länger. Der Neustart der beiden VMs kann 2 bis 4 Minuten dauern, sodass die VPN-Konnektivität etwas länger unterbrochen ist.

Wenn nach den beiden Neustarts weiterhin standortübergreifende Konnektivitätsprobleme auftreten, erstellen Sie über das Azure-Portal ein Supportticket, um den Microsoft Azure-Support zu kontaktieren.


## Nächste Schritte
	
Weitere Informationen zu diesem Cmdlet finden Sie in der [PowerShell-Referenz](https://msdn.microsoft.com/library/azure/mt270366.aspx).

<!---HONumber=Oct15_HO3-->