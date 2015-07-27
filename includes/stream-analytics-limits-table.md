<properties 
   pageTitle="Tabelle zu Beschränkungen für Stream Analytics"
   description="Beschreibung der Systembegrenzungen und empfohlenen Größen für Stream Analytics-Komponenten und -Verbindungen."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/13/2015"
   ms.author="jeffstok" />

| Bezeichnung | Begrenzung | Kommentare |
|----------------- | ------------|--------- |
| Maximale Anzahl der Streamingeinheiten pro Abonnement und Region | 50 | Beim [Microsoft Support](https://support.microsoft.com/de-de) können Sie die Erhöhung der Streamingeinheiten auf über 50 für Ihr Abonnement anfordern. |
| Maximaler Durchsatz einer Streamingeinheit | 1 MB/s* | Der maximale Durchsatz pro Streamingeinheit hängt vom jeweiligen Szenario ab. Der tatsächliche Durchsatz kann geringer ausfallen und hängt von der Komplexität der Abfragen und von der Partitionierung ab. Weitere Informationen finden Sie im Artikel [Skalieren von Azure Stream Analytics-Aufträgen](../articles/stream-analytics/stream-analytics-scale-jobs.md). |
| Beschränkung der Abfrage von SELECT-Anweisungen | 5 Ausgaben pro Abfrage | Diese Beschränkung wird in Zukunft möglicherweise erhöht. |
| Beschränkung für Unterabfragen von SELECT-Anweisungen | 14 Aggregate pro Unterabfrage | Diese Beschränkung wird in Zukunft möglicherweise erhöht. |

<!---HONumber=July15_HO3-->