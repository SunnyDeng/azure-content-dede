<properties 
	pageTitle="Speicheranalyse" 
	description="Verwalten von Nebenläufigkeit für die Blob-, Warteschlangen-, Tabellen- und Dateidienste" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>

# Speicheranalyse

## Übersicht

Die Azure-Speicheranalyse führt die Protokollierung aus und stellt Metrikdaten für ein Speicherkonto bereit. Mit diesen Daten können Sie Anforderungen verfolgen, Verwendungstrends analysieren und Probleme mit dem Speicherkonto diagnostizieren.

Zum Verwenden der Speicheranalyse müssen Sie sie einzeln für jeden zu überwachenden Dienst aktivieren. Die Aktivierung ist im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) möglich. Weitere Informationen finden Sie unter [Vorgehensweise: Überwachen eines Speicherkontos](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Über die Vorgänge [Blob-Diensteigenschaften abrufen](https://msdn.microsoft.com/library/hh452239.aspx), [Warteschlangendiensteigenschaften abrufen](https://msdn.microsoft.com/library/hh452243.aspx) und [Tabellendiensteigenschaften abrufen](https://msdn.microsoft.com/library/hh452238.aspx) können Sie die Speicheranalyse für jeden Dienst aktivieren.

Die aggregierten Daten werden in einem bekannten BLOB (zur Protokollierung) und in bekannten Tabellen (als Metrik) gespeichert. Der Zugriff erfolgt über APIs für den BLOB-Dienst und Tabellendienst.

Bei der Speicheranalyse ist die Menge der gespeicherten Daten auf 20 TB beschränkt. Diese Beschränkung gilt unabhängig vom Gesamtlimit für Ihr Speicherkonto. Weitere Informationen zu Abrechnungs- und Datenaufbewahrungsrichtlinien finden Sie unter [Speicheranalyse und Speicheranalysekosten](https://msdn.microsoft.com/library/hh360997.aspx). Weitere Informationen zu Begrenzungen für Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](https://msdn.microsoft.com/library/dn249410.aspx).

Eine ausführliche Anleitung zum Verwenden der Speicheranalyse sowie weiterer Tools, um Azure Storage-bezogene Probleme zu identifizieren, zu diagnostizieren und zu beheben, finden Sie unter [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).


## Informationen zur Protokollierung durch die Speicheranalyse

Die Speicheranalyse protokolliert ausführliche Informationen zu erfolgreichen und nicht erfolgreichen Anforderungen an einen Speicherdienst. Anhand dieser Informationen können einzelne Anforderungen überwacht und Probleme mit einem Speicherdienst untersucht werden. Anforderungen werden auf Grundlage der besten Leistung protokolliert.

Protokolleinträge werden nur bei einer Speicherdienstaktivität erstellt. Wenn beispielsweise ein Speicherkonto Aktivität im BLOB-Dienst, jedoch nicht im Tabellen- oder Warteschlangendienst aufweist, werden nur Protokolle für den BLOB-Dienst erstellt.

### Protokollierung authentifizierter Anforderungen

Die folgenden Typen authentifizierter Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen

- Fehlerhafte Anforderungen, einschließlich Timeout-, Drosselungs-, Netzwerk-, Autorisierungs- und anderer Fehler

- Anforderungen mithilfe einer SAS (Shared Access Signature), einschließlich fehlerhafter und erfolgreicher Anforderungen

- Anforderungen von Analysedaten

Anforderungen, die durch die Speicheranalyse selbst erfolgen, z. B. Protokollerstellung oder -löschung, werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten ist in den Themen [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx) und [Protokollformat der Speicheranalyse](https://msdn.microsoft.com/library/hh343259.aspx) dokumentiert.

### Protokollieren anonymer Anforderungen
Die folgenden Typen anonymer Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen

- Serverfehler

- Timeoutfehler für Client und Server

- Mit Fehlercode 304 (Nicht geändert) misslungene GET-Anforderungen

Alle anderen misslungenen anonymen Anforderungen werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten ist in den Themen [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx) und [Protokollformat der Speicheranalyse](](https://msdn.microsoft.com/library/hh343259.aspx)) dokumentiert.

### Wie Protokolle gespeichert werden
Alle Protokolle werden in Block-BLOBs in einem Container namens "$logs" gespeichert, der automatisch erstellt wird, wenn die Speicheranalyse für ein Speicherkonto aktiviert ist. Der Container „$logs“ befindet sich im BLOB-Namespace des Speicherkontos. Beispiel: `http://<accountname>.blob.core.windows.net/$logs`. Dieser Container kann nicht gelöscht werden, nachdem die Speicheranalyse aktiviert wurde. Sein Inhalt kann hingegen gelöscht werden.

>[Azure.NOTE]Der Container „$logs“ wird nicht angezeigt, wenn ein Containerauflistungsvorgang erfolgt, wie z. B. mit der [ListContainers](https://msdn.microsoft.com/library/ee758348.aspx)-Methode. Der Zugriff auf ihn muss direkt erfolgen. Beispielsweise können Sie die [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx)-Methode verwenden, um auf die Blobs im Container `$logs` zugreifen. Sobald Anforderungen protokolliert werden, lädt die Speicheranalyse Zwischenergebnisse als Blöcke hoch. Die Speicheranalyse führt regelmäßig Commits dieser Blöcke aus und macht sie als BLOB verfügbar.

Für Protokolle, die in derselben Stunde erstellt werden, können doppelte Einträge vorhanden sein. Sie können feststellen, ob ein Datensatz ein Duplikat ist, indem Sie **RequestId** und **Vorgangsnummer** prüfen.

### Benennungskonventionen für Protokolle
Jedes Protokoll wird im folgenden Format geschrieben:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

In der folgenden Tabelle werden alle Attribute im Protokollnamen beschrieben:

| Attribut | Beschreibung |
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> | Der Name des Speicherdiensts. Beispiel: "blob", "table" oder "queue" |
| YYYY | Die vierstellige Jahresangabe für das Protokoll. Beispiel: 2011 |
| MM | Die zweistellige Tagesangabe für das Protokoll. Beispiel: 07 |
| DD | Die zweistellige Tagesangabe für das Protokoll. Beispiel: 07 |
| hh | Die zweistellige Angabe der Stunde des Beginns der Protokolle im 24-Stunden-Format (UTC). Beispiel: 18 |
| mm | Die zweistellige Zahl, mit der die Anfangsminute der Protokolle angegeben wird. >[AZURE.NOTE]Dieser Wert wird in der aktuellen Version der Speicheranalyse nicht unterstützt und ist immer 00. |
| <counter> | Ein nullbasierter Zähler mit sechs Stellen, der die Anzahl der im Zeitraum von einer Stunde für den Speicherdienst generierten Protokoll-BLOBs angibt. Dieser Zähler beginnt bei 000000. Beispiel: 000001 |

In dem folgenden vollständigen Beispielprotokollnamen sind alle oben aufgeführten Beispiele enthalten:

    blob/2011/07/31/1800/000001.log

Der folgende Beispiel-URI kann für den Zugriff auf das obige Protokoll verwendet werden:

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

Wenn eine Speicheranforderung protokolliert wird, gibt der resultierende Protokollname die Stunde wieder, zu der der angeforderte Vorgang abgeschlossen wurde. Wenn beispielsweise eine GetBlob-Anforderung am 31.07.2011 um 18:30 Uhr abgeschlossen wurde, wird das Protokoll mit dem folgenden Präfix geschrieben: `blob/2011/07/31/1800/`.

### Protokollmetadaten
Alle Protokoll-BLOBs werden mit Metadaten gespeichert, mit deren Hilfe die im BLOB enthaltenen Protokollierungsdaten ermittelt werden können. In der folgenden Tabelle werden die einzelnen Metadatenattribute beschrieben:

| Attribut | Beschreibung |
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType | Gibt an, ob das Protokoll Informationen über Lese-, Schreib- oder Löschvorgänge enthält. Dieser Wert kann einen Typ oder eine durch Kommas getrennte Kombination aller drei Typen enthalten. Beispiel 1: write Beispiel 2: read,write Beispiel 3: read,write,delete |
| StartTime | Der erste Zeitpunkt eines Eintrags im Protokoll, im Format YYYY-MM-DDThh:mm:ssZ. Beispiel: 2011-07-31T18:21:46Z |
| EndTime | Der letzte Zeitpunkt eines Eintrags im Protokoll, im Format YYYY-MM-DDThh:mm:ssZ. Beispiel: 2011-07-31T18:22:09Z |
| LogVersion | Die Version des Protokollformats. Einziger derzeit unterstützter Wert: 1.0. |

In der folgenden Liste werden alle Beispielmetadaten unter Verwendung der obigen Beispiele dargestellt:

- LogType=write 

- StartTime=2011-07-31T18:21:46Z

- EndTime=2011-07-31T18:22:09Z

- LogVersion=1.0

### Zugreifen auf Protokollierungsdaten

Auf alle Daten im Container `$logs` kann mithilfe der Blob-Dienst-APIs zugegriffen werden, einschließlich der .NET-APIs, die von der verwalteten Azure-Bibliothek bereitgestellt werden. Der Speicherkontoadministrator kann Protokolle lesen und löschen, jedoch keine Protokolle erstellen oder aktualisieren. Beim Abfragen eines Protokolls können die Metadaten und der Name des Protokolls verwendet werden. Die Protokolle für eine bestimmte Stunde können außerhalb der Reihenfolge angezeigt werden, die Metadaten geben jedoch immer den Zeitraum der Einträge in einem Protokoll an. Daher können Sie beim Suchen eines bestimmten Protokolls eine Kombination aus Protokollnamen und Metadaten verwenden.

## Informationen zu Metriken der Speicheranalyse

Von der Speicheranalyse können Metriken gespeichert werden, zu denen aggregierte Transaktionsstatistiken und Kapazitätsdaten für die an einen Speicherdienst gesendeten Anforderungen zählen. Berichte zu Transaktionen werden sowohl auf API-Vorgangsebene als auch auf Speicherdienstebene erstellt, während Berichte zur Kapazität nur auf Speicherdienstebene erstellt werden. Mit den Metrikdaten können die Speicherdienstnutzung analysiert, Probleme mit Anforderungen für den Speicherdienst diagnostiziert und die Leistung von Anwendungen verbessert werden, die einen Dienst verwenden.

Zum Verwenden der Speicheranalyse müssen Sie sie einzeln für jeden zu überwachenden Dienst aktivieren. Die Aktivierung ist im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) möglich. Weitere Informationen finden Sie unter [Vorgehensweise: Überwachen eines Speicherkontos](../how-to-monitor-a-storage-account.md). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Über die Vorgänge [Blob-Diensteigenschaften abrufen, Warteschlangendiensteigenschaften abrufen](https://msdn.microsoft.com/library/hh452239.aspx) und [Tabellendiensteigenschaften abrufen](https://msdn.microsoft.com/library/hh452238.aspx) können Sie die Speicheranalyse für jeden Dienst aktivieren.

### Transaktionsmetriken

Ein umfassender Datensatz wird stündlich oder minütlich für jeden Speicherdienst und jeden angeforderten API-Vorgang aufgezeichnet, einschließlich Eingang/Ausgang, Verfügbarkeit, Fehlern und nach Prozentanteil kategorisierten Anforderungen. Eine vollständige Liste der Transaktionsdetails finden Sie im Thema [Schema der Tabellen für Speicheranalysemetriken](https://msdn.microsoft.com/library/hh343264.aspx).

Transaktionsdaten werden auf zwei Ebenen aufgezeichnet, auf Dienstebene und API-Vorgangsebene. Auf Dienstebene werden stündlich zusammenfassende Statistiken für alle angeforderten API-Vorgänge in eine Tabellenentität geschrieben, wenn keine Anforderungen für den Dienst gesendet wurden. Auf API-Vorgangsebene werden Statistiken nur dann in eine Entität geschrieben, wenn der Vorgang während der betreffenden Stunde angefordert wurde.

Wenn Sie beispielsweise einen **GetBlob**-Vorgang für Ihren Blob-Dienst ausführen, protokollieren Speicheranalysemetriken die Anforderung und fügen diese in die aggregierten Daten für den Blob-Dienst und den **GetBlob**-Vorgang ein. Wenn jedoch im Verlauf der Stunde kein **GetBlob**-Vorgang angefordert wurde, wird für den betreffenden Vorgang keine Entität in `$MetricsTransactionsBlob` geschrieben.

Transaktionsmetriken werden sowohl für Anforderungen von Benutzern als auch für Anforderungen aufgezeichnet, die von der Speicheranalyse selbst generiert wurden. So werden z. B. Anforderungen der Speicheranalyse zum Schreiben von Protokollen und Tabellenentitäten aufgezeichnet. Weitere Informationen zur Abrechnung für diese Anforderungen finden Sie unter [Speicheranalyse und Speicheranalysekosten](https://msdn.microsoft.com/library/hh360997.aspx).

### Kapazitätsmetriken

>[AZURE.NOTE]Kapazitätsmetriken sind derzeit nur für den BLOB-Dienst verfügbar. Kapazitätsmetriken für den Tabellendienst und Warteschlangendienst werden in künftigen Versionen der Speicheranalyse verfügbar sein.

Kapazitätsdaten werden für den BLOB-Dienst eines Speicherkontos täglich aufgezeichnet, und es werden zwei Tabellenentitäten geschrieben. Eine Entität stellt Statistiken für Benutzerdaten bereit, während die andere Statistiken zum Blob-Container `$logs` bereitstellt, der von der Speicheranalyse verwendet wird. Die Tabelle `$MetricsCapacityBlob` enthält die folgenden Statistiken:

- **Capacity**: Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Bytes.

- **ContainerCount**Die Anzahl der Blob-Container im Blob-Dienst des Speicherkontos.

- **ObjectCount**: Die Anzahl der Block- oder Seitenblobs mit und ohne ausgeführtem Commit im Blob-Dienst des Speicherkontos.

Weitere Informationen zu den Kapazitätsmetriken finden Sie unter "Schema der Tabellen für Speicheranalysemetriken".

### Speichern von Metriken

Alle Metrikdaten für jeden der Speicherdienste werden in drei Tabellen gespeichert, die für diesen Dienst reserviert sind: eine Tabelle für Transaktionsinformationen, eine Tabelle für minutenbezogene Transaktionsinformationen und eine weitere Tabelle für Kapazitätsinformationen. Informationen zur Transaktion und deren Dauer umfassen Anforderungs- und Antwortdaten, während Kapazitätsinformationen Daten zur Speicherverwendung enthalten. Stunden-, Minutenmetriken und Kapazitätsinformationen zum BLOB-Dienst eines Speicherkontos können aus Tabellen abgerufen werden, die wie in der folgenden Tabelle beschrieben benannt sind.

| Metrikebene | Tabellennamen | Unterstützte Versionen |
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Stundenmetriken, primärer Standort | $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue | Nur Versionen vor dem 15.08.2013. Obwohl diese Namen weiterhin unterstützt werden, wird empfohlen, die unten aufgeführten Tabellen zu verwenden. |
| Stundenmetriken, primärer Standort | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue | Alle Versionen einschließlich 15.08.2013. |
| Minutenmetriken, primärer Standort | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue | Alle Versionen einschließlich 15.08.2013. |
| Stundenmetriken, sekundärer Standort | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue | Alle Versionen einschließlich 2013-08-15. Georedundante Replikation mit Lesezugriff muss aktiviert sein. |
| Minutenmetriken, sekundärer Standort | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue | Alle Versionen einschließlich 2013-08-15. Georedundante Replikation mit Lesezugriff muss aktiviert sein. |
| Kapazität (nur BLOB-Dienst) | $MetricsCapacityBlob | Alle Versionen einschließlich 15.08.2013. |


Diese Tabellen werden automatisch erstellt, wenn die Speicheranalyse für ein Speicherkonto aktiviert wird. Der Zugriff auf diese Tabellen erfolgt über den Namespace des Speicherkontos. Beispiel: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`.

### Zugreifen auf Metrikdaten

Auf alle Daten in den Metriktabellen kann mithilfe der Tabellendienst-APIs zugegriffen werden, einschließlich der .NET-APIs, die von der verwalteten Azure-Bibliothek bereitgestellt werden. Der Speicherkontoadministrator kann Tabellenentitäten lesen und löschen, jedoch nicht erstellen oder aktualisieren.

## Abrechnung von Speicheranalysen

Die Speicheranalyse wird vom Speicherkontobesitzer aktiviert. Standardmäßig ist sie nicht aktiviert. Alle Metrikdaten werden von den Diensten eines Speicherkontos geschrieben. Deshalb ist jeder Schreibvorgang, der von der Speicheranalyse ausgeführt wird, gebührenpflichtig. Darüber hinaus ist die Menge des von Metrikdaten genutzten Speichers ebenfalls gebührenpflichtig.

Die folgenden Aktionen der Speicheranalyse sind gebührenpflichtig:

- Anforderungen zum Erstellen von BLOBs für die Protokollierung

- Anforderungen zum Erstellen von Tabellenentitäten für Metriken

Wenn Sie eine Datenbeibehaltungsrichtlinie konfiguriert haben und die Speicheranalyse ältere Protokollierungs- und Metrikdaten löscht, werden Ihnen keine Löschtransaktionen in Rechnung gestellt. Löschtransaktionen von einem Client sind jedoch gebührenpflichtig. Weitere Informationen zu Aufbewahrungsrichtlinien finden Sie unter [Festlegen einer Beibehaltungsrichtlinie für Speicheranalysedaten](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### Grundlegendes zu gebührenpflichtigen Anforderungen

Jede Anforderung, die an den Speicherdienst eines Kontos erfolgt, ist entweder gebührenpflichtig oder nicht. Die Speicheranalyse protokolliert jede einzelne an einen Dienst gerichtete Anforderung, einschließlich einer Statusmeldung, die angibt, wie die Anforderung behandelt wurde. Entsprechend speichert die Speicheranalyse Metriken sowohl für einen Dienst als auch für die API-Vorgänge dieses Diensts, einschließlich der Prozentsätze und Anzahl bestimmter Statusmeldungen. Diese gesamten Funktionen können Ihnen helfen, die gebührenpflichtigen Anforderungen zu analysieren, Verbesserungen an der Anwendung vorzunehmen und Probleme bei den Anforderungen an Dienste zu diagnostizieren. Weitere Informationen zur Abrechnung finden Sie unter [Understanding Azure Storage Billing - Bandwidth, Transactions, and Capacity](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx) (Grundlagen zur Abrechnung von Azure-Speicher – Bandbreite, Transaktionen und Kapazität, in englischer Sprache).

Wenn Sie Speicheranalysedaten überprüfen, können Sie anhand der Tabellen im Thema [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/azure/hh343260.aspx) bestimmen, welche Anforderungen gebührenpflichtig sind. Anschließend können Sie die Protokolle und Metrikdaten mit den Statusmeldungen vergleichen, um zu ermitteln, ob Ihnen eine bestimmte Anforderung in Rechnung gestellt wurde. Sie können auch die Tabellen im obigen Thema verwenden, um die Verfügbarkeit für einen Speicherdienst oder einen einzelnen API-Vorgang zu bestimmen.

## Nächste Schritte

### Einrichten der Speicheranalyse
- [Überwachen eines Speicherkontos](../how-to-monitor-a-storage-account.md) 
- [Aktivieren und Konfigurieren der Speicheranalyse](https://msdn.microsoft.com/library/hh360996.aspx)

### Protokollierung durch die Speicheranalyse  
- [Informationen zur Protokollierung durch die Speicheranalyse](https://msdn.microsoft.com/library/hh343262.aspx) 
- [Protokollformat der Speicheranalyse](https://msdn.microsoft.com/library/hh343259.aspx) 
- [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoftcom/library/hh343260.aspx) 

### Metriken der Speicheranalyse
- [Informationen zu Metriken der Speicheranalyse](https://msdn.microsoft.com/library/hh343258.aspx)
- [Schema der Tabellen für Speicheranalysemetriken](https://msdn.microsoft.com/library/hh343264.aspx) 
- [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx)  

<!---HONumber=July15_HO1-->