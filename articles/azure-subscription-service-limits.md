<properties
	pageTitle="Microsoft Azure-Abonnements und Diensteinschränkungen, Kontingente und Einschränkungen"
	description="Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschränkungen, Kontingenten und Einschränkungen bereit. Dies umfasst Informationen zum Erhöhen von Einschränkungen und Höchstwerten."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="mollybos"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2015"
	ms.author="jroth"/>

# Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

## Übersicht

Dieses Dokument beschreibt einige der wichtigsten Einschränkungen in Microsoft Azure. Beachten Sie, dass dieses Dokument nicht alle Azure-Dienste behandelt. Mit der Zeit wird diese Liste der Einschränkungen erweitert, um größere Teile der Plattform abzudecken.

> [AZURE.NOTE]Falls Sie einen Grenzwert über den **Standardgrenzwert** anheben möchten, können Sie [eine gebührenfreie Onlinekundensupport-Anforderung öffnen](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Die Limits können nicht über die Werte unter **Maximales Limit** in der folgenden Tabelle angehoben werden. Falls keine Spalte **Maximales Limit** existiert, bedeutet dies, dass für die entsprechende Ressource keine änderbaren Limits existieren.

## Grenzwerte und der Azure-Ressourcen-Manager

Es ist jetzt möglich, mehrere Azure-Ressourcen in einer einzigen Azure-Ressourcengruppe zu kombinieren. Bei der Verwendung von Ressourcengruppen werden Grenzwerte, die bisher global waren, auf einer regionalen Ebene mit dem Azure-Ressourcen-Manager verwaltet. Weitere Informationen zu Azure- Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-portal.md).

In den folgenden Grenzwerten wurde eine neue Tabelle hinzugefügt, um alle abweichenden Grenzwerte bei Verwendung des Azure-Ressourcen-Managers aufzuzeigen. Es gibt beispielsweise eine Tabelle **Einschränkungen für Abonnements** und eine Tabelle **Abonnementgrenzwerte - Azure-Ressourcen-Manager**. Wenn ein Grenzwert für beide Szenarien gilt, wird er nur in der ersten Tabelle angezeigt. Sofern nicht anders angegeben, gelten Grenzwerte global für alle Regionen.

> [AZURE.NOTE]Wichtig ist, dass Kontingente für Ressourcen in Azure-Ressourcengruppen pro Region über Ihr Abonnement zugänglich sind, und nicht wie die Dienstverwaltungskontingente pro Abonnement. Verwenden wir Kernspeicherkontingente als Beispiel. Wenn Sie eine Erhöhung des Kontingents mit Unterstützung für Kernspeicher anfordern müssen, müssen Sie entscheiden, wie viel Kernspeicher Sie in den einzelnen Regionen verwenden möchten, und anschließend eine spezifische Anforderung für Azure-Ressourcengruppen-Kernspeicherkontingente für die gewünschten Beträge und Regionen vornehmen. Wenn Sie für die Ausführung Ihrer Anwendung 30 Kerne in Westeuropa benötigen, sollten Sie daher 30 Kerne in Westeuropa anfordern. In anderen Regionen erfolgt jedoch keine Erhöhung des Kernspeicherkontingents. Das Kontingent von 30 Kernen gilt nur für Westeuropa. <!-- --> Daher sollten Sie ggf. überlegen, wie hoch Ihre Azure-Ressourcengruppenkontingente für Ihre Workload in jeder Region sein müssen, und diesen Betrag in jeder Region anfordern, in der Sie eine Bereitstellung in Betracht ziehen. Weitere Informationen zum Ermitteln Ihrer aktuellen Kontingente für bestimmte Regionen finden Sie unter [Problembehandlung bei der Bereitstellung](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues).

## Einschränkungen für Abonnements

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]


## Abonnementgrenzwerte - Azure-Ressourcen-Manager 

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch die Einführung des Azure-Ressourcen-Managers nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Grenzwerte für Ressourcengruppen

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## Grenzwerte für virtuelle Computer

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


## Grenzwerte für virtuelle Computer - Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Netzwerkeinschränkungen

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


## Netzwerkgrenzwerte - Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## Standardmäßige Speichergrenzwerte

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

## Storage Premium-Grenzwerte

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
</tr>
<tr>
   <td valign="middle"><p>Datenträgerkapazität insgesamt pro Konto</p></td>
   <td valign="middle"><p>35&#160;TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Kapazität für Momentaufnahmen insgesamt pro Konto</p></td>
   <td valign="middle"><p>10&#160;TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Bandbreite pro Konto (ein- und ausgehend)</p></td>
   <td valign="middle"><p>50&#160;GBit/s</p></td>
</tr>
</table>


## Speichergrenzwerte - Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure-Ressourcen-Managers und der Azure-Ressourcengruppen. Grenzwerte, die durch den Azure-Ressourcen-Manager nicht geändert wurden, sind im Folgenden nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Grenzwerte für Clouddienste

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Grenzwerte für Web-Apps (Websites)

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]


## Einschränkungen für die Batch-Vorschau

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## DocumentDB-Grenzwerte

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Mobile Engagement-Grenzwerte

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## Einschränkungen für SQL-Datenbanken

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]


## Media Services-Einschränkungen

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]


## Servicebus-Einschränkungen

[AZURE.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]


## Active Directory-Einschränkungen

[AZURE.INCLUDE [azure-active-directory-limits](../includes/azure-active-directory-limits.md)]


## RemoteApp-Grenzwerte

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## StorSimple-Systemgrenzwerte

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]
 
## Weitere Informationen

[Grundlegendes zu Azure-Einschränkungen und -Steigerungen](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=62-->