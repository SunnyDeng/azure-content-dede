<properties
	pageTitle="Microsoft Azure-Abonnements und Diensteinschränkungen, Kontingente und Einschränkungen"
	description="Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschränkungen, Kontingenten und Einschränkungen bereit. Dies umfasst Informationen zum Erhöhen von Einschränkungen und Höchstwerten."
	services=""
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettem"
	editor="cgronlun"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="jroth"/>

# Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

## Übersicht

Dieses Dokument beschreibt einige der wichtigsten Einschränkungen in Microsoft Azure. Beachten Sie, dass dieses Dokument nicht alle Azure-Dienste behandelt. Mit der Zeit wird diese Liste der Einschränkungen erweitert, um größere Teile der Plattform abzudecken.

> [AZURE.NOTE]Falls Sie einen Grenzwert über den **Standardgrenzwert** anheben möchten, können Sie [eine gebührenfreie Onlinekundensupport-Anforderung öffnen](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Die Limits können nicht über die Werte unter **Maximales Limit** in der folgenden Tabelle angehoben werden. Falls keine Spalte **Maximales Limit** existiert, bedeutet dies, dass für die entsprechende Ressource keine änderbaren Limits existieren.


### Grenzwerte und der Azure-Ressourcen-Manager

Es ist jetzt möglich, mehrere Azure-Ressourcen in einer einzigen Azure-Ressourcengruppe zu kombinieren. Bei der Verwendung von Ressourcengruppen werden Grenzwerte, die bisher global waren, auf einer regionalen Ebene mit dem Azure-Ressourcen-Manager verwaltet. Weitere Informationen zu Azure- Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-portal.md).

In den folgenden Grenzwerten wurde eine neue Tabelle hinzugefügt, um alle abweichenden Grenzwerte bei Verwendung des Azure-Ressourcen-Managers aufzuzeigen. Es gibt beispielsweise eine Tabelle **Einschränkungen für Abonnements** und eine Tabelle **Abonnementgrenzwerte - Azure-Ressourcen-Manager**. Wenn ein Grenzwert für beide Szenarien gilt, wird er nur in der ersten Tabelle angezeigt. Sofern nicht anders angegeben, gelten Grenzwerte global für alle Regionen.

> [AZURE.NOTE]Wichtig ist, dass Kontingente für Ressourcen in Azure-Ressourcengruppen pro Region über Ihr Abonnement zugänglich sind, und nicht wie die Dienstverwaltungskontingente pro Abonnement. Verwenden wir Kernspeicherkontingente als Beispiel. Wenn Sie eine Erhöhung des Kontingents mit Unterstützung für Kernspeicher anfordern müssen, müssen Sie entscheiden, wie viel Kernspeicher Sie in den einzelnen Regionen verwenden möchten, und anschließend eine spezifische Anforderung für Azure-Ressourcengruppen-Kernspeicherkontingente für die gewünschten Beträge und Regionen vornehmen. Wenn Sie für die Ausführung Ihrer Anwendung 30 Kerne in Westeuropa benötigen, sollten Sie daher 30 Kerne in Westeuropa anfordern. In anderen Regionen erfolgt jedoch keine Erhöhung des Kernspeicherkontingents. Das Kontingent von 30 Kernen gilt nur für Westeuropa. <!-- --> Daher sollten Sie ggf. überlegen, wie hoch Ihre Azure-Ressourcengruppenkontingente für Ihre Workload in jeder Region sein müssen, und diesen Betrag in jeder Region anfordern, in der Sie eine Bereitstellung in Betracht ziehen. Weitere Informationen zum Ermitteln Ihrer aktuellen Kontingente für bestimmte Regionen finden Sie unter [Problembehandlung bei der Bereitstellung](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues).

## Einschränkungen für Abonnements

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

### Abonnementgrenzwerte - Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Grenzwerte für Ressourcengruppen

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## Grenzwerte für virtuelle Computer

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


### Grenzwerte für virtuelle Computer - Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Netzwerkeinschränkungen

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


### Netzwerkgrenzwerte - Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## Speichergrenzwerte

### Standardmäßige Speichergrenzwerte 

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Weitere Informationen zu Grenzwerten für Speicherkonten finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../articles/storage/storage-scalability-targets.md).


### Storage Premium-Grenzwerte

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../includes/azure-storage-limits-premium-storage.md)]


### Speichergrenzwerte - Azure-Ressourcen-Manager

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Grenzwerte für Clouddienste

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## App Service-Grenzwerte – Web-Apps, mobile Apps, API-Apps, Logik-Apps

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## Batchgrenzwerte

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## DocumentDB-Grenzwerte

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Mobile Engagement-Grenzwerte

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## Search-Grenzwerte

[AZURE.INCLUDE [azure-search-limits](../includes/azure-search-limits.md)]

Weitere Informationen zu den Grenzwerten für Azure Search finden Sie unter[Grenzwerte und Einschränkungen](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Einschränkungen für SQL-Datenbanken

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]

Weitere Informationen zu Grenzwerten für SQL-Datenbanken finden Sie in den folgenden Themen:

 - [Dienstebenen (Editionen) der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [Dienst- und Leistungsebenen für Azure SQL-Datenbanken](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [Kontingente der Datenbankdurchsatzeinheit (DTU = Database Throughput Unit)](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [Ressourceneinschränkungen für SQL-Datenbanken](sql-database/sql-database-resource-limits.md)

## Media Services-Einschränkungen

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## Mobile Services-Grenzwerte

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

## Servicebus-Einschränkungen

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

## Stream Analytics-Grenzwerte

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

## Active Directory-Einschränkungen

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


## Azure RemoteApp-Grenzwerte

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## StorSimple-Systemgrenzwerte

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


## Operational Insights-Grenzwerte

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

## Backup-Grenzwerte

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

## Site Recovery-Grenzwerte

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

## API Management-Grenzwerte

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

## Azure Redis Cache-Grenzwerte

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

## Key Vault-Grenzwerte

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

## Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

## Siehe auch

[Grundlegendes zu Azure-Einschränkungen und -Steigerungen](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=July15_HO4-->