<properties 
	pageTitle="Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ ";Premium";" 
	description="Erfahren Sie, wie Sie die Unterstützung für virtuelle Netzwerke für Azure Redis Cache-Instanzen im Tarif ";Premium"; erstellen und verwalten." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="sdanie"/>

# Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"
Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -features bieten, einschließlich des neuen Premium-Tarifs.

Der Premium-Tarif für Azure Redis Cache umfasst Clustering, Persistenz und Unterstützung des virtuellen Netzwerks (VNET). Ein VNET ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Wenn eine Azure Redis Cache-Instanz mit einem VNET konfiguriert wird, ist dieses nicht öffentlich adressierbar, und auf das VNET kann nur über Clients innerhalb des VNET zugegriffen werden. In diesem Artikel wird erläutert, wie die Unterstützung eines virtuellen Netzwerks für eine Azure Redis Cache-Instanz vom Typ "Premium" konfiguriert wird.

Informationen zu anderen Funktionen des Premium-Caches finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md) und [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md).

## Warum VNET?
Die Bereitstellung von [Azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/) bietet verbesserte Sicherheit und Isolation für den Azure Redis Cache sowie Subnetze, Zugriffssteuerungsrichtlinien und andere Funktionen, mit denen der Zugriff auf Azure Redis Cache weiter eingeschränkt wird.

## Unterstützung für virtuelle Netzwerke
Die Unterstützung für ein virtuelles Netzwerk (VNET) wird während der Erstellung des Caches auf dem Blatt **Neuer Redis-Cache** konfiguriert. Um einen Cache zu erstellen, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Neu** > **Daten und Speicher** > **Redis Cache**.

![Erstellen eines Redis-Cache][redis-cache-new-cache-menu]

Zum Konfigurieren der VNET-Unterstützung wählen Sie zunächst auf dem Blatt **Wählen Sie Ihren Tarif** eine der **Premium**-Optionen für den Cache aus.

![Tarif auswählen][redis-cache-premium-pricing-tier]

Die VNET-Integration für Azure Redis Cache wird auf dem Blatt **Virtuelles Netzwerk** konfiguriert. Hier können Sie ein vorhandenes klassisches VNET auswählen. Um ein neues VNET zu verwenden, führen Sie die unter [Erstellen eines virtuellen Netzwerks (klassisch) über das Azure-Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) beschriebenen Schritte aus und kehren dann zum Blatt **Redis Cache Virtuelles Netzwerk** zurück, um das erstellte Netzwerk auszuwählen.

>[AZURE.NOTE]Azure Redis Cache kann mit klassischen VNETs ausgeführt werden. Informationen zum Erstellen eines klassischen VNET finden Sie unter [Erstellen eines virtuellen Netzwerks (klassisch) über das Azure-Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Informationen zum Herstellen einer Verbindung von klassischen VNETs mit ARM-VNETS finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md).

![Virtuelles Netzwerk][redis-cache-vnet]

Klicken Sie auf dem Blatt **Virtuelles Netzwerk** auf **Virtuelles Netzwerk**, um Ihr VNET auszuwählen und zu konfigurieren.

![Virtuelles Netzwerk][redis-cache-vnet-select]

Klicken Sie auf das gewünschte VNET, um es auszuwählen.

![Virtuelles Netzwerk][redis-cache-vnet-subnet]

Klicken Sie auf "Subnetz", um das gewünschte Subnetz auszuwählen.

![Virtuelles Netzwerk][redis-cache-vnet-ip]

Das Feld **Statische IP-Adresse** ist optional. Wenn hier keine Adresse angegeben wird, wird eine aus dem ausgewählten Subnetz ausgewählt. Wenn eine bestimmte **Statische IP-Adresse** gewünscht ist, geben Sie sie ein, und klicken Sie dann auf **OK**, um die VNET-Konfiguration zu speichern. Wenn die ausgewählte statische IP-Adresse bereits verwendet wird, wird eine Fehlermeldung angezeigt.

Auf den erstellten Cache kann nur über Clients innerhalb des gleichen VNET zugegriffen werden.

>[AZURE.IMPORTANT]Um bei Verwendung eines VNET auf Ihre Azure Redis Cache-Instanz zuzugreifen, übergeben Sie die statische IP-Adresse des Caches im VNET als ersten Parameter, und übergeben Sie einen `sslhost`-Parameter mit dem Endpunkt Ihres Caches. Im folgenden Beispiel ist die statische IP-Adresse `10.10.1.5` und der Cacheendpunkt `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("10.10.1.5,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Azure Redis Cache – häufig gestellte Fragen zum VNET

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Skalierung von Azure Redis Cache-Instanzen.

## Welche Probleme treten häufig bei einer fehlerhaften Konfiguration von Azure Redis Cache und VNETs auf?

Im Folgenden sind einige häufige Konfigurationsfehler aufgeführt, die dazu führen, dass Azure Redis Cache nicht ordnungsgemäß ausgeführt wird.

-	Kein Zugriff auf DNS. Azure Redis Cache-Instanzen in einem VNET benötigen Zugriff auf DNS für Teile des Überwachungs- und Laufzeitsystems des Caches. Wenn die Cacheinstanz keinen DNS-Zugriff hat, funktioniert die Überwachung nicht, und der Cache funktioniert nicht richtig.
-	Blockierte TCP-Ports, über die Clients eine Verbindung mit Redis herstellen, z. B. Port 6379 oder 6380.
-	Blockierter oder abgefangener ausgehender HTTPS-Datenverkehr aus dem virtuellen Netzwerk. Azure Redis Cache verwendet den ausgehenden HTTPS-Datenverkehr in Azure-Diensten, insbesondere in Azure Storage.
-	Virtuelle Computer mit blockierten Redis-Rolleninstanzen, sodass die Kommunikation zwischen den Rolleninstanzen im Subnetz nicht möglich ist. Redis-Rolleninstanzen müssen über TCP auf allen verwendeten Ports miteinander kommunizieren können. Dies kann zwar geändert werden, muss jedoch mindestens für alle in der Redis-Datei "CSDEF" verwendeten Ports zulässig sein.
-	Azure Load Balancer kann keine Verbindung mit den virtuellen Redis-Computern über den TCP/HTTP-Port 16001 herstellen. Azure Redis Cache hängt vom standardmäßigen Azure Load Balancer-Test ab, um zu ermitteln ob Rolleninstanzen ausgeführt werden. Beim standardmäßigen Load Balancer-Test wird der Azure-Gast-Agent über Port 16001 gepingt. Nur die Rolleninstanzen, die auf den Ping reagieren, werden in die Rotation für den Empfang des von der ILB weitergeleiteten Datenverkehrs platziert. Wenn sich keine Instanzen in der Rotation befinden, da Pings aufgrund blockierter Ports fehlschlagen, akzeptiert die ILB keine eingehenden TCP-Verbindungen.
-	Der für die Überprüfung des öffentlichen SSL-Schlüssels verwendete Webdatenverkehr der Clientanwendung ist blockiert. Clients von Redis (innerhalb des virtuellen Netzwerks) müssen HTTP-Datenverkehr an das öffentliche Internet weiterleiten können, um Zertifizierungsstellenzertifikate und Zertifikatsperrlisten herunterzuladen und die Überprüfung von SSL-Zertifikaten durchzuführen, wenn sie über Port 6380 eine Verbindung mit Redis herstellen und die Authentifizierung des SSL-Servers durchführen.
-	Azure Load Balancer kann keine Verbindung zu virtuellen Redis-Computern in einem Cluster über Port 1300x (13000, 13001 usw.) oder 1500x (15000, 15001 usw.) herstellen. VNETs werden in der CSDEF-Datei mit einem Load Balancer-Test zum Öffnen dieser Ports konfiguriert. Der Azure Load Balancer muss von NSGs zugelassen werden. Bei Standard-NSGs erfolgt dies über das Tag "AZURE\_LOADBALANCER". Dem Azure Load Balancer ist die statische IP-Adresse 168.63.126.16 zugewiesen. Weitere Informationen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](../virtual-network/virtual-networks-nsg.md).

## Kann ich VNETs mit einem Standard-Cache oder Basic-Cache verwenden?

VNETs können nur mit Premium-Caches verwendet werden.

## Nächste Schritte
Informationen zur Verwendung weiterer Funktionen des Premium-Caches finden Sie in den folgenden Artikeln.

-	[Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md)
-	[Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=AcomDC_1217_2015-->