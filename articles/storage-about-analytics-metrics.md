<properties 
	pageTitle="Informationen zu Metriken der Speicheranalyse" 
	description="Erfahren Sie, wie die Speicheranalyse verwenden, einschließlich Transaktions- und Kapazitätsmetriken, wie Metriken gespeichert werden und wie auf Metrikdaten zugegriffen wird." 
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

# Informationen zu Metriken der Speicheranalyse

## Übersicht

Von der Speicheranalyse können Metriken gespeichert werden, zu denen aggregierte Transaktionsstatistiken und Kapazitätsdaten für die an einen Speicherdienst gesendeten Anforderungen zählen. Berichte zu Transaktionen werden sowohl auf API-Vorgangsebene als auch auf Speicherdienstebene erstellt, während Berichte zur Kapazität nur auf Speicherdienstebene erstellt werden. Mit den Metrikdaten können die Speicherdienstnutzung analysiert, Probleme mit Anforderungen für den Speicherdienst diagnostiziert und die Leistung von Anwendungen verbessert werden, die einen Dienst verwenden.

Zum Verwenden der Speicheranalyse müssen Sie sie einzeln für jeden zu überwachenden Dienst aktivieren. Die Aktivierung ist im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) möglich. Weitere Informationen finden Sie unter [Vorgehensweise: Überwachen eines Speicherkontos](how-to-monitor-a-storage-account.md). Sie können die Speicheranalyse auch programmgesteuert über die REST-API oder die Clientbibliothek aktivieren. [Über die Vorgänge "BLOB-Diensteigenschaften abrufen", "Warteschlangendiensteigenschaften abrufen"](https://msdn.microsoft.com/library/hh452239.aspx) und [Tabellendiensteigenschaften abrufen](https://msdn.microsoft.com/library/hh452238.aspx) können Sie die Speicheranalyse für jeden Dienst aktivieren.

##Transaktionsmetriken

Ein umfassender Datensatz wird stündlich oder minütlich für jeden Speicherdienst und jeden angeforderten API-Vorgang aufgezeichnet, einschließlich Eingang/Ausgang, Verfügbarkeit, Fehlern und nach Prozentanteil kategorisierten Anforderungen.  Eine vollständige Liste der Transaktionsdetails finden Sie im Thema [Schema der Tabellen für Speicheranalysemetriken](https://msdn.microsoft.com/library/hh343264.aspx).

Transaktionsdaten werden auf zwei Ebenen aufgezeichnet, auf Dienstebene und API-Vorgangsebene. Auf Dienstebene werden stündlich zusammenfassende Statistiken für alle angeforderten API-Vorgänge in eine Tabellenentität geschrieben, wenn keine Anforderungen für den Dienst gesendet wurden. Auf API-Vorgangsebene werden Statistiken nur dann in eine Entität geschrieben, wenn der Vorgang während der betreffenden Stunde angefordert wurde.

Wenn Sie beispielsweise einen **GetBlob**-Vorgang für Ihren BLOB-Dienst ausführen, protokollieren Speicheranalysemetriken die Anforderung und fügen diese in die aggregierten Daten für den BLOB-Dienst und den **GetBlob**-Vorgang ein. Wenn jedoch im Verlauf der Stunde kein **GetBlob**-Vorgang angefordert wurde, wird für den betreffenden Vorgang keine Entität in "$MetricsTransactionsBlob" geschrieben.

Transaktionsmetriken werden sowohl für Anforderungen von Benutzern als auch für Anforderungen aufgezeichnet, die von der Speicheranalyse selbst generiert wurden. So werden z. B. Anforderungen der Speicheranalyse zum Schreiben von Protokollen und Tabellenentitäten aufgezeichnet. Weitere Informationen zur Abrechnung für diese Anforderungen finden Sie unter [Speicheranalyse und Speicheranalysekosten](https://msdn.microsoft.com/library/hh360997.aspx).

##Kapazitätsmetriken

>[AZURE.NOTE] Kapazitätsmetriken sind derzeit nur für den BLOB-Dienst verfügbar. Kapazitätsmetriken für den Tabellendienst und Warteschlangendienst werden in künftigen Versionen der Speicheranalyse verfügbar sein.

Kapazitätsdaten werden für den BLOB-Dienst eines Speicherkontos täglich aufgezeichnet, und es werden zwei Tabellenentitäten geschrieben. Eine Entität stellt Statistiken für Benutzerdaten bereit, während die andere Statistiken zum BLOB-Container "$logs" bereitstellt, der von der Speicheranalyse verwendet wird.  Die Tabelle "$MetricsCapacityBlob" enthält die folgenden Statistiken:

- **Capacity**: Die Größe des vom BLOB-Dienst des Speicherkontos genutzten Speichers in Bytes.

- **ContainerCount**: Die Anzahl der BLOB-Container im BLOB-Dienst des Speicherkontos.

- **ObjectCount**: Die Anzahl der Block- oder Seiten-BLOBs mit und ohne ausgeführtem Commit im BLOB-Dienst des Speicherkontos.

Weitere Informationen zu den Kapazitätsmetriken finden Sie unter "Schema der Tabellen für Speicheranalysemetriken".

##Speichern von Metriken

Alle Metrikdaten für jeden der Speicherdienste werden in drei Tabellen gespeichert, die für diesen Dienst reserviert sind: eine Tabelle für Transaktionsinformationen, eine Tabelle für minutenbezogene Transaktionsinformationen und eine weitere Tabelle für Kapazitätsinformationen. Informationen zur Transaktion und deren Dauer umfassen Anforderungs- und Antwortdaten, während Kapazitätsinformationen Daten zur Speicherverwendung enthalten.  Stunden-, Minutenmetriken und Kapazitätsinformationen zum BLOB-Dienst eines Speicherkontos können aus Tabellen abgerufen werden, die wie in der folgenden Tabelle beschrieben benannt sind.

| Metrikebene                      	| Tabellennamen                                                                                                                 	| Unterstützte Versionen                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Stundenmetriken, primärer Standort   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| Nur Versionen vor dem 15.08.2013. Obwohl diese Namen weiterhin unterstützt werden, wird empfohlen, die unten aufgeführten Tabellen zu verwenden. 	|
| Stundenmetriken, primärer Standort   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| Alle Versionen einschließlich 15.08.2013.                                                                                                           	|
| Minutenmetriken, primärer Standort   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| Alle Versionen einschließlich 15.08.2013.                                                                                                           	|
| Stundenmetriken, sekundärer Standort 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| Alle Versionen einschließlich 2013-08-15. Georedundante Replikation mit Lesezugriff muss aktiviert sein.                                                   	|
| Minutenmetriken, sekundärer Standort 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| Alle Versionen einschließlich 2013-08-15. Georedundante Replikation mit Lesezugriff muss aktiviert sein.                                                   	|
| Kapazität (nur BLOB-Dienst)       	| $MetricsCapacityBlob                                                                                                        	| Alle Versionen einschließlich 15.08.2013.                                                                                                           	|



Diese Tabellen werden automatisch erstellt, wenn die Speicheranalyse für ein Speicherkonto aktiviert wird. Der Zugriff auf diese Tabellen erfolgt über den Namespace des Speicherkontos. Beispiel:  `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`


##Zugreifen auf Metrikdaten

Auf alle Daten in den Metriktabellen kann mithilfe der Tabellendienst-APIs zugegriffen werden, einschließlich der .NET-APIs, die von der verwalteten Azure-Bibliothek bereitgestellt werden. Der Speicherkontoadministrator kann Tabellenentitäten lesen und löschen, jedoch nicht erstellen oder aktualisieren.

##Nächste Schritte
###Konzepte
[Aktivieren und Konfigurieren der Speicheranalyse](https://msdn.microsoft.com/library/hh360996.aspx)

[Schema der Tabellen für Speicheranalysemetriken ](https://msdn.microsoft.com/library/hh343264.aspx) 

[Protokollierte Speicheranalysevorgänge und Statusmeldungen](https://msdn.microsoft.com/library/hh343260.aspx) 

[Informationen zur Protokollierung durch die Speicheranalyse ](https://msdn.microsoft.com/library/hh343262.aspx) 

###Weitere Ressourcen

[Überwachen eines Speicherkontos](how-to-monitor-a-storage-account.md) 
<!--HONumber=47-->
