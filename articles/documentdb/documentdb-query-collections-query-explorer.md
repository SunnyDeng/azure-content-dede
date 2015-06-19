<properties 
	pageTitle="Erstellen, Bearbeiten und Ausführen von SQL-Abfragen in einer DocumentDB-Sammlung mithilfe des Abfrage-Explorers | Azure" 
	description="Erfahren Sie mehr über den DocumentDB-Abfrage-Explorer, ein Azure-Vorschauportaltool zum Erstellen, Bearbeiten und Ausführen von SQL-Abfragen in einer DocumentDB-Sammlung." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# Erstellen, Bearbeiten und Ausführen von SQL-Abfragen in einer DocumentDB-Sammlung mithilfe des Abfrage-Explorers #

Dieser Artikel bietet eine Übersicht über den Abfrage-Explorer von [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), ein Azure-Vorschauportaltool, mit dem Sie Abfragen in einer DocumentDB-Sammlung erstellen, bearbeiten und ausführen können. 

Nach Abschluss dieses Lernprogramms können Sie die folgenden Fragen beantworten:  

-	Wie kann ich über einen Webbrowser auf einfache Weise Abfragen in einer DocumentDB-Sammlung erstellen, bearbeiten und ausführen?
-	Wie kann ich über einen Webbrowser einfach durch Seiten der DocumentDB-Abfrageergebnisse navigieren?
-	Wie kann Syntaxfehler meiner DocumentDB-Abfrage beheben? 

##<a id="Launch"></a>Starten des Abfrage-Explorers und Navigieren im Abfrage-Explorer##

Der Abfrage-Explorer kann über jedes DocumentDB-Konto, jede -Datenbank und jedes -Sammlungsblatt gestartet werden.
  
1. Am unteren Rand jedes Blattes befindet sich der Bereich **Entwicklertools**, der die Kachel **Abfrage-Explorer** enthält.
	
	![Screenshot of Query Explorer part](./media/documentdb-query-collections-query-explorer/queryexplorerpart.png) 

2. Klicken Sie einfach auf die Kachel, um den Abfrage-Explorer zu starten.

	Die Dropdown-Listen **Datenbank** und **Sammlung** werden basierend auf dem Kontext, in dem der Abfrage-Explorer gestartet wird, automatisch ausgefüllt.  Wenn Sie diesen z. B. aus einem Datenbankblatt starten, sind die Felder der aktuellen Datenbank bereits ausgefüllt. Wenn Sie diesen aus einem Auflistungsblatt starten, sind die Felder der aktuellen Auflistung ausgefüllt.

	![Screenshot of Query Explorer](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

##<a id="Create"></a>Erstellen, Bearbeiten und Ausführen von Abfragen mit dem Abfrage-Explorer##

Mit dem Abfrage-Explorer können Sie problemlos Abfragen für eine DocumentDB-Sammlung erstellen, bearbeiten und ausführen. Darüber hinaus bietet er grundlegende Funktionen zum Hervorheben von Stichwörtern und Werten, um die Abfragebearbeitung zu verbessern.  

- Beim ersten Öffnen des Abfrage-Explorers wird die Standardabfrage "SELECT * FROM c" bereitgestellt.  Sie können die Standardabfrage akzeptieren oder Ihre eigene erstellen und dann auf die Schaltfläche **Abfrage ausführen** klicken, um die Ergebnisse anzuzeigen. Der Abfrage-Explorer unterstützt die SQL-Abfragesprache von DocumentDB, wie in [Abfragen von DocumentDB](documentdb-sql-query.md) beschrieben.

	![Screenshot of Query Explorer query results](./media/documentdb-query-collections-query-explorer/queryresults1.png) 

- Standardmäßig zeigt der Abfrage-Explorer die Ergebnisse in Gruppen von jeweils 100 zurück.  Wenn Ihre Abfrage mehr als 100 Ergebnisse erzeugt, verwenden Sie einfach die Befehle **Nächste Seite** und **Vorherige Seite**, um durch die Ergebnisse zu navigieren.

	![Screenshot of Query Explorer pagination support](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

- Erfolgreiche Abfragen bieten Informationen, wie z. B. die Anforderungskosten, die Liste der aktuell angezeigten Ergebnisse und ob mehr Ergebnisse vorhanden sind, die wie bereits erwähnt über den Befehl **Nächste Seite** angezeigt werden können.

	![Screenshot of Query Explorer query information](./media/documentdb-query-collections-query-explorer/queryinformation.png)

- Wenn eine Abfrage mit Fehlern abgeschlossen wird, zeigt der Abfrage-Explorer eine Liste der Fehler an, die Sie bei der Fehlerbehebung unterstützen.

	![Screenshot of Query Explorer query errors](./media/documentdb-query-collections-query-explorer/queryerror.png)

##<a name="NextSteps"></a>Nächste Schritte

- Weitere Informationen zu DocumentDB finden Sie [hier](http://azure.com/docdb).
- Weitere Informationen zur DocumentDB-SQL-Grammatik, die im Abfrage-Explorer unterstützt wird, finden Sie [hier](documentdb-sql-query.md).

<!--HONumber=49--> 