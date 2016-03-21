<properties 
	pageTitle="Einführung in den Premium-Tarif von Azure Redis Cache" 
	description="Hier erfahren Sie, wie Sie für Ihre Azure Redis Cache-Instanzen im Tarif ";Premium"; Redis-Persistenz Redis-Clustering und VNET-Unterstützung erstellen und verwalten." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="sdanie"/>

# Einführung in den Premium-Tarif von Azure Redis Cache
Azure Redis Cache ist ein verteilter, verwalteter Cache, mit der Sie hoch skalierbare und reaktionsschnelle Anwendungen erstellen können, indem Sie äußerst schnellen Zugriff auf Daten bieten.

Der neue Tarif "Premium" ist ein unternehmensfähiger Tarif, der alle Features des Tarifs "Standard" umfasst und weitere Features bietet, z. B. eine bessere Leistung, größere Arbeitslasten, Notfallwiederherstellung und verbesserte Sicherheit. Nachfolgend finden Sie weitere Informationen zu den zusätzlichen Features des Cachetarifs "Premium".

## Bessere Leistung als in den Tarifen "Standard" und "Basic"
**Bessere Leistung in den Tarifen "Standard" und "Basic".** Caches im Tarif "Premium" werden auf Hardware mit schnelleren Prozessoren bereitgestellt, die im Vergleich zu den Tarifen "Basic" oder "Standard" eine bessere Leistung bieten. Caches im Tarif "Premium" erreichen einen höheren Durchsatz und geringere Wartezeiten.

**Der Durchsatz für Cache derselben Größe ist im Tarif "Premium" im Vergleich zum Tarif "Standard" höher.** Für einen 53 GB-Cache beträgt der Durchsatz von P4 (Premium) z. B. 250 KB Anforderungen pro Sekunde im Vergleich zu 150 KB für C6 (Standard).

Ausführliche Informationen zu Größe, Durchsatz und Bandbreite von Premium-Caches finden Sie in den [häufig gestellten Fragen zu Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Redis-Datenpersistenz
Der Tarif "Premium" ermöglicht die Persistenz der Cachedaten in einem Azure Storage-Konto. In einem "Basic"-/"Standard"-Cache werden alle Daten nur im Arbeitsspeicher gespeichert. Bei Problemen mit der zugrunde liegenden Infrastruktur kann es zu Datenverlusten kommen. Es wird empfohlen, das Redis-Feature für Datenpersistenz im Tarif "Premium" zu verwenden, um den Schutz vor Datenverlusten zu erhöhen. Azure Redis Cache bietet RDB- und AOF-Optionen (demnächst verfügbar) bei der [Redis-Persistenz](http://redis.io/topics/persistence).

Informationen zum Konfigurieren von Persistenz finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md).

##Redis-Cluster
Wenn Sie Caches mit einer Größe über 53 GB erstellen oder Daten über mehrere Redis-Knoten horizontal partitionieren möchten, können Sie das im Tarif "Premium" verfügbare Redis-Clustering verwenden. Jeder Knoten besteht aus einem von Azure für hohe Verfügbarkeit verwalteten Paar aus primärem Cache und Replikatcache.

**Redis-Clustering bietet Ihnen maximale Skalierung und Durchsatz.** Der Durchsatz steigt linear, je mehr Shards (Knoten) Sie im Cluster verwenden. Wenn Sie z. B. einen P4-Cluster mit 10 Shards erstellen, beträgt der verfügbare Durchsatz 250 KB * 10 = 2,5 Millionen Anforderungen pro Sekunde. Ausführliche Informationen zu Größe, Durchsatz und Bandbreite von Premium-Caches finden Sie in den [häufig gestellten Fragen zu Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

Informationen zum Einstieg in die Arbeit mit Clustern finden Sie unter [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md).

##Erhöhte Sicherheit und Isolierung
Auf im Tarif "Basic" oder "Standard" erstellte Caches kann über das öffentliche Internet zugegriffen werden. Der Zugriff auf den Cache ist basierend auf dem Zugriffsschlüssel eingeschränkt. Im Tarif "Premium" können Sie darüber hinaus sicherstellen, dass nur Clients in einem bestimmten Netzwerk auf den Cache zugreifen können. Sie können Redis-Cache in einem [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) bereitstellen. Sie können alle Features von VNet, z. B. Subnetze, Richtlinien für die Zugriffssteuerung und andere Features, verwenden, um den Zugriff auf Redis weiter einzuschränken.

Weitere Informationen finden Sie unter [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).

## Nächste Schritte

Erstellen Sie einen Cache, und untersuchen Sie die neuen Features des "Premium"-Tarifs.

-	[Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md)
-	[Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md)
-	[Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md)
  

<!---HONumber=AcomDC_0309_2016-->