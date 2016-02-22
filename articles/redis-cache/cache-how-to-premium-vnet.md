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
	ms.date="02/04/2016" 
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

>[AZURE.NOTE] Azure Redis Cache kann mit klassischen VNETs ausgeführt werden. Informationen zum Erstellen eines klassischen VNET finden Sie unter [Erstellen eines virtuellen Netzwerks (klassisch) über das Azure-Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Informationen zum Herstellen einer Verbindung von klassischen VNETs mit ARM-VNETS finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md).

Klicken Sie auf dem Blatt **Neuer Redis Cache** auf **Virtuelles Netzwerk (klassisch)**, und wählen Sie das gewünschte VNET aus der Dropdownliste aus, um Ihr VNET zu konfigurieren.

![Virtuelles Netzwerk][redis-cache-vnet]

Wählen Sie das gewünschte Subnetz aus der Dropdownliste **Subnetz** aus.

![Virtuelles Netzwerk][redis-cache-vnet-ip]

Das Feld **Statische IP-Adresse** ist optional. Wenn hier keine Adresse angegeben wird, wird eine aus dem ausgewählten Subnetz ausgewählt. Wenn eine bestimmte **Statische IP-Adresse** gewünscht ist, geben Sie sie ein, und klicken Sie dann auf **OK**, um die VNET-Konfiguration zu speichern. Wenn die ausgewählte statische IP-Adresse bereits verwendet wird, wird eine Fehlermeldung angezeigt.

Nachdem der Cache erstellt wurde, können Sie die IP-Adresse und weitere Informationen über das VNET anzeigen, indem Sie auf dem Blatt **Einstellungen** auf **Virtuelles Netzwerk** klicken.

![Virtuelles Netzwerk][redis-cache-vnet-info]

>[AZURE.IMPORTANT] Um bei Verwendung eines VNET auf Ihre Azure Redis Cache-Instanz zuzugreifen, übergeben Sie die statische IP-Adresse des Caches im VNET als ersten Parameter, und übergeben Sie einen `sslhost`-Parameter mit dem Endpunkt Ihres Caches. Im folgenden Beispiel ist die statische IP-Adresse `172.160.0.99` und der Cacheendpunkt `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
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

Beim Hosten von Azure Redis Cache in einem VNET werden die in der folgenden Tabelle angegebenen Ports verwendet. Wenn diese Ports gesperrt sind, funktioniert der Cache möglicherweise nicht ordnungsgemäß. Eine bestehende Sperre für mindestens einen dieser Ports ist die häufigste Fehlkonfiguration, die beim Verwenden von Azure Redis Cache in einem VNET auftritt.

| Port(s) | Richtung | Transportprotokoll | Zweck | Remote-IP |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | Ausgehend | TCP | Redis-Abhängigkeiten von Azure Storage/PKI (Internet) | * |
| 53 | Ausgehend | TCP/UDP | Redis-Abhängigkeiten von DNS (Internet/VNet) | * |
| 6379, 6380 | Eingehend | TCP | Clientkommunikation mit Redis, Azure-Lastenausgleich | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | Eingehend/ausgehend | TCP | Implementierungsdetail für Redis | VIRTUAL\_NETWORK |
| 8500 | Eingehend | TCP/UDP | Azure-Lastenausgleich | AZURE\_LOADBALANCER |
| 10221-10231 | Eingehend/ausgehend | TCP | Implementierungsdetail für Redis (kann den Remoteendpunkt auf VIRTUAL\_NETWORK beschränken) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | Eingehend | TCP | Clientkommunikation mit Redis-Clustern, Azure-Lastenausgleich | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | Eingehend | TCP | Clientkommunikation mit Redis-Clustern, Azure-Lastenausgleich | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | Eingehend | TCP/UDP | Azure-Lastenausgleich | AZURE\_LOADBALANCER |
| 20226 | Eingehend und ausgehend | TCP | Implementierungsdetail für Redis-Cluster | VIRTUAL\_NETWORK |




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

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0211_2016-->