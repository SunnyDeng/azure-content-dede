<properties 
	pageTitle="Informationen zur Protokollierung durch die Speicheranalyse" 
	description="Erfahren Sie, wie das Protokoll der Speicheranalyse, authentifizierte und nicht authentifizierte Anforderungen verwendet und Protokolle gespeichert werden. " 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Informationen zur Protokollierung durch die Speicheranalyse

## Übersicht
Die Speicheranalyse protokolliert ausführliche Informationen zu erfolgreichen und nicht erfolgreichen Anforderungen an einen Speicherdienst. Anhand dieser Informationen können einzelne Anforderungen überwacht und Probleme mit einem Speicherdienst untersucht werden. Anforderungen werden auf Grundlage der besten Leistung protokolliert.

Zum Verwenden der Speicheranalyse müssen Sie sie einzeln für jeden zu überwachenden Dienst aktivieren. Die Aktivierung ist im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) möglich. Weitere Informationen finden Sie unter [Vorgehensweise: Überwachen eines Speicherkontos](../how-to-monitor-a-storage-account). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. Über die Vorgänge "BLOB-Diensteigenschaften abrufen", "Warteschlangendiensteigenschaften abrufen" und "Tabellendiensteigenschaften abrufen" können Sie die Speicheranalyse für jeden Dienst aktivieren.

Protokolleinträge werden nur bei einer Speicherdienstaktivität erstellt. Wenn beispielsweise ein Speicherkonto Aktivität im BLOB-Dienst, jedoch nicht im Tabellen- oder Warteschlangendienst aufweist, werden nur Protokolle für den BLOB-Dienst erstellt.

##Protokollierung authentifizierter Anforderungen
Die folgenden Typen authentifizierter Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen

- Fehlerhafte Anforderungen, einschließlich Timeout-, Drosselungs-, Netzwerk-, Autorisierungs- und anderer Fehler

- Anforderungen mithilfe einer SAS (Shared Access Signature), einschließlich fehlerhafter und erfolgreicher Anforderungen

- Anforderungen von Analysedaten

Anforderungen, die durch die Speicheranalyse selbst erfolgen, z. B. Protokollerstellung oder -löschung, werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten ist in den Themen [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx) und [Protokollformat der Speicheranalyse](https://msdn.microsoft.com/library/hh343259.aspx) dokumentiert.

##Protokollieren anonymer Anforderungen
Die folgenden Typen anonymer Anforderungen werden protokolliert:

- Erfolgreiche Anforderungen

- Serverfehler

- Timeoutfehler für Client und Server

- Mit Fehlercode 304 (Nicht geändert) misslungene GET-Anforderungen

Alle anderen misslungenen anonymen Anforderungen werden nicht protokolliert. Eine vollständige Liste der protokollierten Daten ist in den Themen [Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx) und [Protokollformat der Speicheranalyse](](https://msdn.microsoft.com/library/hh343259.aspx)) dokumentiert.

##Wie Protokolle gespeichert werden
Alle Protokolle werden in Block-BLOBs in einem Container namens "$logs" gespeichert, der automatisch erstellt wird, wenn die Speicheranalyse für ein Speicherkonto aktiviert ist. Der Container "$logs" befindet sich im BLOB-Namespace des Speicherkontos. Beispiel:  `http://<accountname>.blob.core.windows.net/$logs`. Dieser Container kann nicht gelöscht werden, nachdem die Speicheranalyse aktiviert wurde. Sein Inhalt kann hingegen gelöscht werden.

>[Azure.NOTE] Der Container "$logs" wird nicht angezeigt, wenn ein Containerauflistungsvorgang erfolgt, wie z. B. die [ListContainers](https://msdn.microsoft.com/library/ee758348.aspx)-Methode. Der Zugriff auf ihn muss direkt erfolgen. Beispielsweise können Sie die [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx)-Methode verwenden, um auf die BLOBs im Container "$logs" zugreifen.
Sobald Anforderungen protokolliert werden, lädt die Speicheranalyse Zwischenergebnisse als Blöcke hoch. Die Speicheranalyse führt regelmäßig Commits dieser Blöcke aus und macht sie als BLOB verfügbar.

Für Protokolle, die in derselben Stunde erstellt werden, können doppelte Einträge vorhanden sein. Sie können feststellen, ob ein Datensatz ein Duplikat ist, indem Sie **RequestId** und **Vorgangsnummer** prüfen.

##Benennungskonventionen für Protokolle
Jedes Protokoll wird im folgenden Format geschrieben:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

In der folgenden Tabelle werden alle Attribute im Protokollnamen beschrieben:

| Attribut      	| Beschreibung                                                                                                                                                                                	|
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> 	| Der Name des Speicherdiensts. Beispiel: "blob", "table" oder "queue"                                                                                                                        	|
| YYYY           	| Die vierstellige Jahresangabe für das Protokoll. Beispiel: 2011                                                                                                                                         	|
| MM             	| Die zweistellige Monatsangabe für das Protokoll. Beispiel: 07                                                                                                                                           	|
| DD             	| Die zweistellige Tagesangabe für das Protokoll. Beispiel: 07                                                                                                                                           	|
| hh             	| Die zweistellige Angabe der Stunde des Beginns der Protokolle im 24-Stunden-Format (UTC). Beispiel: 18                                                                                   	|
| mm             	| Die zweistellige Zahl, mit der die Anfangsminute der Protokolle angegeben wird. >[AZURE.NOTE]Dieser Wert wird in der aktuellen Version der Speicheranalyse nicht unterstützt und ist immer 00. 	|
| <counter>      	| Ein nullbasierter Zähler mit sechs Stellen, der die Anzahl der im Zeitraum von einer Stunde für den Speicherdienst generierten Protokoll-BLOBs angibt. Dieser Zähler beginnt bei 000000. Beispiel: 000001   	|

In dem folgenden vollständigen Beispielprotokollnamen sind alle oben aufgeführten Beispiele enthalten:

    blob/2011/07/31/1800/000001.log

Der folgende Beispiel-URI kann für den Zugriff auf das obige Protokoll verwendet werden:

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

Wenn eine Speicheranforderung protokolliert wird, gibt der resultierende Protokollname die Stunde wieder, zu der der angeforderte Vorgang abgeschlossen wurde. Wenn beispielsweise eine "GetBlob"-Anforderung am 31.07.2011 um 18:30 Uhr abgeschlossen wurde, wird das Protokoll mit dem folgenden Präfix geschrieben: blob/2011/07/31/1800/

##Protokollmetadaten
Alle Protokoll-BLOBs werden mit Metadaten gespeichert, mit deren Hilfe die im BLOB enthaltenen Protokollierungsdaten ermittelt werden können. In der folgenden Tabelle werden die einzelnen Metadatenattribute beschrieben:

| Attribut  	| Beschreibung                                                                                                                                                                                                                                               	|
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType    	| Gibt an, ob das Protokoll Informationen über Lese-, Schreib- oder Löschvorgänge enthält. Dieser Wert kann einen Typ oder eine durch Kommas getrennte Kombination aller drei Typen enthalten.   Beispiel 1: write Beispiel 2: read,write Beispiel 3: read,write,delete 	|
| StartTime  	| Der erste Zeitpunkt eines Eintrags im Protokoll, im Format YYYY-MM-DDThh:mm:ssZ. Beispiel: 2011-07-31T18:21:46Z                                                                                                                                          	|
| EndTime    	| Der letzte Zeitpunkt eines Eintrags im Protokoll, im Format YYYY-MM-DDThh:mm:ssZ. Beispiel: 2011-07-31T18:22:09Z                                                                                                                                            	|
| LogVersion 	| Die Version des Protokollformats. Der einzige derzeit unterstützte Wert ist: 1.0                                                                                                                                                                                 	|

In der folgenden Liste werden alle Beispielmetadaten unter Verwendung der obigen Beispiele dargestellt:

- LogType=write 

- StartTime=2011-07-31T18:21:46Z 

- EndTime=2011-07-31T18:22:09Z 

- LogVersion=1.0 

##Zugreifen auf Protokollierungsdaten

Auf alle Daten im Container "$logs" kann mithilfe der BLOB-Dienst-APIs zugegriffen werden, einschließlich der .NET-APIs, die von der verwalteten Azure-Bibliothek bereitgestellt werden. Der Speicherkontoadministrator kann Protokolle lesen und löschen, jedoch keine Protokolle erstellen oder aktualisieren. Beim Abfragen eines Protokolls können die Metadaten und der Name des Protokolls verwendet werden. Die Protokolle für eine bestimmte Stunde können außerhalb der Reihenfolge angezeigt werden, die Metadaten geben jedoch immer den Zeitraum der Einträge in einem Protokoll an. Daher können Sie beim Suchen eines bestimmten Protokolls eine Kombination aus Protokollnamen und Metadaten verwenden.

##Nächste Schritte

[Protokollformat der Speicheranalyse](https://msdn.microsoft.com/library/hh343259.aspx) 

[Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx) 

[Informationen zu Metriken der Speicheranalyse](https://msdn.microsoft.com/library/hh343258.aspx) 
<!--HONumber=47-->
