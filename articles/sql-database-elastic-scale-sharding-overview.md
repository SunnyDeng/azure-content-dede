<properties title="Sharding Overview" pageTitle="Sharding - Übersicht" description="Reasons for sharding: scale database resources to increase availability or performance." metaKeywords="sharding, scaling, elastic scale, Azure SQL Database" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />
#Sharding - Übersicht 

##Prinzipien des Sharding 

**Sharding** ist eine Technik zur Verteilung großer Mengen identisch strukturierter Daten über eine Reihe voneinander unabhängiger Datenbanken. Sie ist besonders bei Cloudentwicklern beliebt, die SaaS-Angebote (Software as a Service) für Endbenutzer oder Unternehmen erstellen. Diese Endbenutzer werden häufig als "Mandanten‟ bezeichnet. Sharding kann aus verschiedenen Gründen erforderlich sein: 

* Die Gesamtdatenmenge ist so groß, dass sie aufgrund einschlägiger Beschränkungen nicht in einer einzelnen Datenbank gespeichert werden kann. 
* Der Transaktionsdurchsatz der Arbeitsauslastung überschreitet die Fähigkeiten einer einzelnen Datenbank. 
* Mandanten erfordern u. U. eine physische Trennung von anderen Mandaten, sodass für jeden Mandanten eine eigene Datenbank erforderlich ist. 
* Verschiedene Abschnitte einer Datenbank müssen sich aufgrund von Compliance- oder Leistungsanforderungen oder aus geopolitischen Gründen an verschiedenen geografischen Standorten befinden. 
 
Das Sharding funktioniert am besten, wenn jede Transaktion in einer Anwendung auf einen einzelnen Wert eines Sharding-Schlüssels begrenzt werden kann. Damit wird sichergestellt, dass alle Transaktionen einer bestimmten Datenbank eigen sind. 

Einige Anwendungen verwenden den einfachsten Ansatz und erstellen für jeden Mandanten eine eigene Datenbank. Dies ist das **Sharding-Muster mit einzelnen Mandanten**, das Isolation, Sicherungs-/Wiederherstellungsfunktion und Ressourcenskalierung auf der Ebene einzelner Mandanten bietet. Beim Sharding mit einzelnen Mandanten ist jede Datenbank mit einem bestimmten Mandanten-ID-Wert (oder Kundenschlüsselwert) verknüpft, wobei der Schlüssel jedoch nicht immer in den Daten vorhanden sein muss. Es ist Aufgabe der Anwendung, die einzelnen Anforderungen an die entsprechende Datenbank weiterzuleiten. 

![][1]

In anderen Szenarien werden mehrere Mandanten in Datenbanken zusammengefasst, statt sie in getrennten Datenbanken zu isolieren. Dabei handelt es sich um ein typisches **Sharding-Szenario mit mehreren Mandanten**, dem Überlegungen hinsichtlich der Kosten oder der Effizienz oder die Tatsache zugrunde liegen können, dass eine Anwendung eine große Anzahl von sehr kleinen Mandanten verwalten muss. Beim Sharding mit mehreren Mandanten sollen die Zeilen in den Datenbanktabellen einen Schlüssel enthalten, der die Mandanten-ID oder den Sharding-Schlüssel eindeutig identifiziert. Auch hier ist es Aufgabe der Anwendungsschicht, die Anforderung eines Mandanten an die entsprechende Datenbank weiterzuleiten. 

In anderen Szenarien, z. B. beim Erfassen von Daten von verteilten Geräten, kann das Sharding eingesetzt werden, um eine Gruppe von Datenbanken über einen Zeitraum verteilt zu füllen. Beispielsweise kann jedem Wochentag oder jeder Woche eine eigene Datenbank zugeordnet werden. In diesem Fall kann der Sharding-Schlüssel eine Ganzzahl sein, die das Datum darstellt (und in allen Zeilen der partitionierten Tabellen vorhanden ist). Abfragen zum Abruf von Informationen für einen Datumsbereich müssen von der Anwendung an die Teilmenge von Datenbanken, welche den fraglichen Bereich abdecken, weitergeleitet werden.

Unabhängig vom verwendeten Sharding-Modell, dient eine spezielle Datenstruktur, die als **Shard-Zuordnung** bezeichnet wird, als Nachschlagetabelle für die Zuordnung von Sharding-Schlüsselwerten zu Datenbanken und ermöglicht der Anwendung die Weiterleitung der Datenbankanforderungen. Dies wird auch **datenabhängiges Routing** genannt und ist Kernfunktion, die erforderlich ist, damit Anwendungen eine partitionierte Datenebene nutzen können. Die [Elastic Scale-Client-APIs](http://go.microsoft.com/?linkid=9862592) stellen eine Fülle von Funktionen zum [Verwalten von Shard-Zuordnungen](http://go.microsoft.com/?linkid=9862595) bereit und ermöglichen effiziente und einfach zu handhabende [Data-depenendent routing capabilities](http://go.microsoft.com/?linkid=9862596) in einer Anwendung. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png
