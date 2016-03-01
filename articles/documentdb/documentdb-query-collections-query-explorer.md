<properties
	pageTitle="DocumentDB-Abfrage-Explorer: SQL-Abfrage-Editor | Microsoft Azure"
	description="Sie erhalten Informationen zum DocumentDB-Abfrage-Explorer, einem SQL-Abfrage-Editor im Azure-Portal zum Schreiben von SQL-Abfragen und Ausführen dieser Abfragen für eine NoSQL-DocumentDB-Sammlung."
	keywords="SQL-Abfragen schreiben, SQL-Abfrage-Editor"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/23/2016"
	ms.author="anhoh"/>

# Schreiben, Bearbeiten und Ausführen von SQL-Abfragen für DocumentDB mit dem Abfrage-Explorer 

Dieser Artikel enthält eine Übersicht über den Abfrage-Explorer von [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Hierbei handelt es sich um ein Tool im Azure-Portal, mit dem Sie SQL-Abfragen in einer [DocumentDB-Sammlung](documentdb-create-collection) schreiben, bearbeiten und ausführen können.

1. Klicken Sie in der Navigationsleiste im Azure-Portal auf **DocumentDB-Konten**. Wenn **DocumentDB-Konten** nicht sichtbar ist, klicken Sie auf **Durchsuchen** und dann auf **DocumentDB-Konten**.

2. Klicken Sie oben im Blatt **DocumentDB-Konto** auf **Abfrage-Explorer**.

	![Screenshot: Azure-Portal mit hervorgehobenem Abfrage-Explorer](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

    >[AZURE.NOTE] Der Abfrage-Explorer wird auch auf den Blättern für Datenbanken und Sammlungen angezeigt.

3. Wählen Sie auf dem Blatt **Abfrage-Explorer** in den Dropdownlisten die **Datenbanken** und **Sammlungen** aus, die abgefragt werden sollen, und geben Sie die auszuführende Abfrage ein.

    Die Dropdownlisten **Datenbanken** und **Sammlungen** werden basierend auf dem Kontext, in dem Sie den Abfrage-Explorer starten, automatisch ausgefüllt.

    Die Standardabfrage `SELECT TOP 100 * FROM c` ist bereits vorhanden. Sie können die Standardabfrage akzeptieren oder eine eigene Abfrage erstellen. Verwenden Sie hierfür die SQL-Abfragesprache, die unter [Spickzettel für SQL-Abfragen](documentdb-sql-query-cheat-sheet.md) bzw. im Artikel [SQL-Abfrage und SQL-Syntax](documentdb-sql-query.md) beschrieben wird.

    Klicken Sie auf **Abfrage ausführen**, um die Ergebnisse anzuzeigen.

	![Screenshot: Schreiben von SQL-Abfragen im Abfrage-Explorer, SQL-Abfrage-Editor](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

4. Auf dem Blatt **Ergebnisse** wird die Ausgabe der Abfrage angezeigt.

	![Screenshot: Ergebnisse des Schreibens von SQL-Abfragen im Abfrage-Explorer](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## Arbeiten mit Ergebnissen

Standardmäßig zeigt der Abfrage-Explorer die Ergebnisse in Gruppen von jeweils 100 zurück. Wenn Ihre Abfrage mehr als 100 Ergebnisse erzeugt, verwenden Sie einfach die Befehle **Nächste Seite** und **Vorherige Seite**, um durch die Ergebnisse zu navigieren.

![Screenshot des Abfrage-Explorers – Seitennummerierung](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

Um erfolgreiche Abfragen zu erhalten, enthält der Bereich **Informationen** Kennzahlen, z. B. die Anforderungskosten, die Anzahl von Roundtrips der Abfrage und den aktuell angezeigten Ergebnissatz. Sie erfahren auch, ob weitere Ergebnisse vorhanden sind, die – wie bereits erwähnt – über den Befehl **Nächste Seite** angezeigt werden können.

![Screenshot der Abfrage-Explorers – Abfrageinformationen](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## Verwenden mehrerer Abfragen

Wenn Sie mehrere Abfragen verwenden und schnell zwischen den Abfragen wechseln möchten, können Sie alle Abfragen in das Abfragetextfeld im Blatt **Abfrage-Explorer** eingeben und dann die Abfrage markieren, die Sie ausführen möchten. Klicken Sie anschließend auf **Abfrage ausführen**, um die Ergebnisse anzuzeigen.

![Screenshot: Schreiben mehrerer SQL-Abfragen im Abfrage-Explorer (SQL-Abfrage-Editor) und Markieren und Ausführen einzelner Abfragen](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## Hinzufügen von Abfragen aus einer Datei in den SQL-Abfrage-Editor

Mithilfe des Befehls **Datei laden** können Sie die Inhalte einer vorhandenen Datei laden.

![Screenshot: Laden von SQL-Abfragen aus einer Datei in den Abfrage-Explorer per „Datei laden“](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## Problembehandlung

Wenn eine Abfrage mit Fehlern abgeschlossen wird, zeigt der Abfrage-Explorer eine Liste der Fehler an, die Sie bei der Fehlerbehebung unterstützen.

![Screenshot der Abfrage-Explorers – Abfragefehler](./media/documentdb-query-collections-query-explorer/queryerror.png)

## Ausführen von DocumentDB-SQL-Abfragen außerhalb des Portals

Der Abfrage-Explorer im Azure-Portal ist nur eine Möglichkeit, um SQL-Abfragen für DocumentDB auszuführen. Sie können SQL-Abfragen auch mit der [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder den [Client-SDKs](documentdb-sdk-dotnet.md) ausführen. Weitere Informationen zur Verwendung dieser oder anderer Methoden finden Sie unter [Ausführen von SQL-Abfragen](documentdb-sql-query.md#executing-sql-queries).

## Nächste Schritte

Weitere Informationen zur DocumentDB-SQL-Grammatik, die im Abfrage-Explorer unterstützt wird, finden Sie im Artikel [SQL-Abfrage und SQL-Syntax](documentdb-sql-query.md), oder drucken Sie sich den [Spickzettel für SQL-Abfragen](documentdb-sql-query-cheat-sheet.md) aus. Sie können auch experimentieren, indem Sie auf den [Query Playground](https://www.documentdb.com/sql/demo) zugreifen, um Abfragen online mit einem Beispieldataset zu testen.

<!---HONumber=AcomDC_0224_2016-->