<properties
	pageTitle="Microsoft Azure-Abonnements und Diensteinschränkungen, Kontingente und Einschränkungen"
	description="Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschränkungen, Kontingenten und Einschränkungen bereit. Dies umfasst Informationen zum Erhöhen von Einschränkungen und Höchstwerten."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="jroth"/>

# Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

## Übersicht

Dieses Dokument beschreibt einige der wichtigsten Einschränkungen in Microsoft Azure. Beachten Sie, dass dieses Dokument nicht alle Azure-Dienste behandelt. Mit der Zeit wird diese Liste der Einschränkungen erweitert, um größere Teile der Plattform abzudecken.

> [AZURE.NOTE] Falls Sie einen Grenzwert über den **Standardgrenzwert** anheben möchten, können Sie [eine gebührenfreie Onlinekundensupport-Anforderung öffnen](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Die Limits können nicht über die Werte unter **Maximales Limit** in der folgenden Tabelle angehoben werden. Falls keine Spalte **Maximales Limit** existiert, bedeutet dies, dass für die entsprechende Ressource keine änderbaren Limits existieren.

## Grenzwerte und der Azure-Ressourcen-Manager

Es ist jetzt möglich, mehrere Azure-Ressourcen in einer einzigen Azure-Ressourcengruppe zu kombinieren. Bei der Verwendung von Ressourcengruppen werden Grenzwerte, die bisher global waren, auf einer regionalen Ebene mit dem Azure-Ressourcen-Manager verwaltet. Weitere Informationen zu Azure- Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](azure-portal/resource-group-portal.md).

In den folgenden Grenzwerten wurde eine neue Tabelle hinzugefügt, um alle abweichenden Grenzwerte bei Verwendung des Azure-Ressourcen-Managers aufzuzeigen. Es gibt beispielsweise eine Tabelle **Einschränkungen für Abonnements** und eine Tabelle **Abonnementgrenzwerte - Azure-Ressourcen-Manager**. Wenn ein Grenzwert für beide Szenarien gilt, wird er nur in der ersten Tabelle angezeigt. Sofern nicht anders angegeben, gelten Grenzwerte global für alle Regionen.

> [AZURE.NOTE] Wichtig ist, dass Kontingente für Ressourcen in Azure-Ressourcengruppen pro Region über Ihr Abonnement zugänglich sind, und nicht wie die Dienstverwaltungskontingente pro Abonnement. Verwenden wir Kernspeicherkontingente als Beispiel. Wenn Sie eine Erhöhung des Kontingents mit Unterstützung für Kernspeicher anfordern müssen, müssen Sie entscheiden, wie viel Kernspeicher Sie in den einzelnen Regionen verwenden möchten, und anschließend eine spezifische Anforderung für Azure-Ressourcengruppen-Kernspeicherkontingente für die gewünschten Beträge und Regionen vornehmen. Wenn Sie für die Ausführung Ihrer Anwendung 30 Kerne in Westeuropa benötigen, sollten Sie daher 30 Kerne in Westeuropa anfordern. In anderen Regionen erfolgt jedoch keine Erhöhung des Kernspeicherkontingents. Das Kontingent von 30 Kernen gilt nur für Westeuropa.
<!-- -->
Daher sollten Sie gegebenenfalls überlegen, wie hoch Ihre Azure-Ressourcengruppenkontingente für Ihre Workload in jeder Region sein müssen, und diesen Betrag in jeder Region anfordern, in der Sie eine Bereitstellung in Betracht ziehen. Weitere Informationen zum Ermitteln Ihrer aktuellen Kontingente für bestimmte Regionen finden Sie unter [Problembehandlung bei der Bereitstellung](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues).


## Dienstspezifische Grenzwerte

- [Active Directory](#active-directory-limits)
- [API Management](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Azure Redis Cache](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Sicherung](#backup-limits)
- [Batch](#batch-limits)
- [BizTalk Services](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Cloud Services](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [Data Lake Analytics](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [IoT Hub](#iot-hub-limits)
- [Schlüsseltresor](#key-vault-limits)
- [Media Services](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [Mobile Services](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [Netzwerk](#networking-limits)
- [Notification Hubs-Dienst](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [Ressourcengruppe](#resource-group-limits)
- [Scheduler](#scheduler-limits)
- [Suchen,](#search-limits)
- [Service Bus](#service-bus-limits)
- [Site Recovery](#site-recovery-limits)
- [SQL-Datenbank](#sql-database-limits)
- [Speicher](#storage-limits)
- [StorSimple-System](#storsimple-system-limits)
- [Stream Analytics](#stream-analytics-limits)
- [Abonnement](#subscription-limits)
- [Traffic Manager](#traffic-manager-limits)
- [Virtual Machines](#virtual-machines-limits)


### Grenzwerte für Abonnements
#### Grenzwerte für Abonnements
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### Abonnementgrenzwerte – Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### Grenzwerte für Ressourcengruppen

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### Grenzwerte für virtuelle Computer
#### Grenzwerte für virtuelle Computer
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### Grenzwerte für virtuelle Computer – Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### Grenzwerte für Netzwerke

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### Grenzwerte für Netzwerke
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### Traffic Manager-Grenzwerte

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS-Grenzwerte

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### Speichergrenzwerte

Weitere Informationen zu Grenzwerten für Speicherkonten finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../articles/storage/storage-scalability-targets.md).

#### Grenzwerte für den Speicherdienst

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### Grenzwerte für Datenträger virtueller Computer

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Weitere Informationen finden Sie unter [Größen virtueller Computer](../articles/virtual-machines/virtual-machines-size-specs.md).

**Standardspeicherkonten**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Storage Premium-Konten**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### Grenzwerte für Speicherressourcenanbieter

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### Grenzwerte für Clouddienste

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### App Service-Grenzwerte
Die folgenden App Service-Grenzwerte umfassen Grenzwerte für Web-Apps, Mobile Apps, API-Apps und Logik-Apps.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### Scheduler-Grenzwerte

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Batch-Grenzwerte

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###BizTalk Services-Grenzwerte
In der folgende Tabelle werden die Grenzwerte für Azure Biztalk Services aufgeführt.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB-Grenzwerte

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Mobile Engagement-Grenzwerte

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### Search-Grenzwerte

Die Tarife bestimmen die Kapazität und die Beschränkungen des Suchdiensts. Folgende Tarife sind verfügbar:

- **Free:** Mehrmandantendienst, der mit anderen Azure-Abonnenten gemeinsam genutzt wird und für Bewertung und kleine Entwicklungsprojekte vorgesehen ist.
- **Basic (Vorschau)** bietet spezifische Computerressourcen für Produktions-Workloads mit geringerem Umfang. Dieser Tarif wird derzeit in der Vorschau und zu einem reduzierten Preis angeboten.
- **Standard (S1 und S2)** ist für Produktions-Workloads vorgesehen. Eine Version mit höherer Kapazität (**S2**) ist auf Anfrage verfügbar (senden Sie eine E-Mail an azuresearch_contact@microsoft.com).

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

Weitere Informationen zu anderen Einschränkungen, z. B. Dokumentgröße, Schlüssel, Anforderungen und Antworten, finden Sie unter [Grenzwerte für den Azure Search-Dienst](search/search-limits-quotas-capacity.md).

### Media Services-Grenzwerte

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN-Grenzwerte

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### Mobile Services-Grenzwerte

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### Grenzwerte für den Notification Hubs-Dienst

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### Service Bus-Grenzwerte

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT Hub-Grenzwerte

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Data Factory-Grenzwerte

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### Grenzwerte für Data Lake Analytics
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### Stream Analytics-Grenzwerte

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Active Directory-Grenzwerte

[AZURE.INCLUDE [AAD-Diensteinschränkungen](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp-Grenzwerte

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple-Systemgrenzwerte

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights-Grenzwerte

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### Backup-Grenzwerte

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Site Recovery-Grenzwerte

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights-Grenzwerte

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API Management-Grenzwerte

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis Cache-Grenzwerte

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### Schlüsseltresor-Grenzwerte

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### Grenzwerte für SQL-Datenbanken

Einschränkungen für SQL-Datenbanken finden Sie unter [Ressourceneinschränkungen für SQL-Datenbank](sql-database/sql-database-resource-limits.md).

## Weitere Informationen

[Grundlegendes zu Azure-Einschränkungen und -Steigerungen](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Größen virtueller Computer und Clouddienste für Azure](virtual-machines/virtual-machines-size-specs.md)

[Größen für Cloud Services](cloud-services/cloud-services-sizes-specs.md)

<!---HONumber=AcomDC_0302_2016-->