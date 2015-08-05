<properties
	pageTitle="Anzeigen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen mit dem DocumentDB-Skript-Explorer | Microsoft Azure"
	description="Erfahren Sie mehr über den DocumentDB-Skript-Explorer, ein Azure-Vorschauportaltool, das Ihnen die Anzeige von serverseitigen DocumentDB-Programmierartefakten ermöglicht, z. B. gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen."
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
	ms.topic="get-started-article" 
	ms.date="06/10/2015"
	ms.author="stbaro"/>

# Anzeigen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen mit dem DocumentDB-Skript-Explorer

Dieser Artikel bietet eine Übersicht über den [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)-Skript-Explorer, ein Azure-Vorschauportaltool, das Ihnen die Anzeige von serverseitigen DocumentDB-Programmierartefakten ermöglicht, z. B. gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen. Weitere Informationen über die serverseitige DocumentDB-Programmierung finden Sie [hier](documentdb-programming.md).

Nach Abschluss dieses Lernprogramms können Sie die folgenden Fragen beantworten:

-	Wie kann ich einfach in DocumentDB gespeicherte Prozeduren über einen Webbrowser anzeigen?
-	Wie kann ich einfach DocumentDB-Trigger über einen Webbrowser anzeigen?
-	Wie kann ich einfach benutzerdefinierte DocumentDB-Funktionen über einen Webbrowser anzeigen?

## Starten des Skript-Explorers und Navigieren im Skript-Explorer

Der Skript-Explorer kann über jedes DocumentDB-Konto, jede -Datenbank und jedes -Sammlungsblatt gestartet werden.

1. Klicken Sie einfach oben auf dem Blatt des DocumentDB-Kontos oder der Datenbank auf den Befehl **Skript-Explorer**.

	![Screenshot des Skript-Explorer-Befehls](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. Alternativ befindet sich am unteren Rand von jedem Blatt ein Bereich **Entwicklertools**, der den Bereich **Skript-Explorer** enthält.

	![Screenshot des Skript-Explorer-Teils](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. Klicken Sie einfach auf den Befehl oder den Teil, um den Skript-Explorer zu starten.

	<p>Die Dropdown-Listen **Datenbank** und **Sammlung** werden basierend auf dem Kontext, in dem der Skript-Explorer gestartet wird, automatisch ausgefüllt. Wenn Sie diesen z. B. aus einem Datenbankblatt starten, sind die Felder der aktuellen Datenbank bereits ausgefüllt. Wenn Sie diesen aus einem Auflistungsblatt starten, sind die Felder der aktuellen Auflistung ausgefüllt.

	![Screenshot des Skript-Explorers](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. Mit den Dropdownlistenfeldern **Datenbank** und **Sammlung** können Sie die Sammlung, aus der aktuell Skripte angezeigt werden, auf einfache Weise ändern, ohne den Skript-Explorer beenden oder erneut starten zu müssen.

4. Der Skript-Explorer unterstützt auch das Filtern des aktuell geladenen Skriptsets nach der id-Eigenschaft. Geben Sie diese einfach im Filterfeld ein.

	![Screenshot der Skript-Explorers mit hervorgehobenem Filter](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	Die Ergebnisse in der Skript-Explorer-Liste sind nun basierend auf den angegebenen Kriterien gefiltert.

	![Screenshot der Skript-Explorers mit gefilterten Ergebnissen](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]Die Filterfunktion vom Skript-Explorer filtert nur aus dem ***aktuell*** geladenen Satz an Skripten und aktualisiert die aktuell ausgewählte Sammlung nicht automatisch.

5. Zum Aktualisieren der vom Skript-Explorer geladenen Skriptliste klicken Sie einfach auf den Befehl **Aktualisieren** im oberen Bereich des Blatts.

	![Screenshot des Befehls Aktualisieren in Skript-Explorer](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Anzeigen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen mit dem Skript-Explorer

Mit dem Skript-Explorer können Sie problemlos serverseitige Programmierartefakte in DocumentDB anzeigen.

- Klicken Sie einfach das Skript, von dem Sie der Inhalt interessiert.

	![Screenshot der Ansicht Skript-Erfahrung in Skript-Explorer](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Mit dem Skript-Explorer können außerdem problemlos die Systemeigenschaften des aktuell geladenen Skripts durch Klicken auf den Befehl **Eigenschaften** angezeigt werden.

	![Screenshot der Ansicht Skript-Eigenschaften in Skript-Explorer](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]Die Zeitstempeleigenschaft (_ts) wird intern als Epochenzeit dargestellt, im Skript-Explorer wird der Wert jedoch in einem vom Menschen lesbaren GMT-Format angezeigt.

## Nächste Schritte

Um weitere Informationen zu DocumentDB zu erhalten, klicken Sie [hier](http://azure.com/docdb).
 

<!---HONumber=July15_HO4-->