<properties 
	pageTitle="Erstellen einer DocumentDB-Datenbanksammlung | Microsoft Azure" 
	description="Erfahren Sie, wie Sie JSON-Dokumentsammlungen mit dem Onlinedienstportal für die cloudbasierte NoSQL-Dokumentendatenbank Azure DocumentDB erstellen. Holen Sie sich noch heute eine kostenlose Testversion." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/18/2015" 
	ms.author="mimig"/>

# Erstellen einer DocumentDB-Sammlung

Für die Verwendung von Microsoft Azure DocumentDB benötigen Sie ein [DocumentDB-Konto](documentdb-create-account.md), eine [Datenbank](documentdb-create-database.md), eine Sammlung und Dokumente. Dieses Thema beschreibt die Erstellung einer DocumentDB-Sammlung im Azure-Portal.

Sie wissen nicht, was eine Sammlung ist? Dann lesen Sie den Abschnitt [Was ist eine DocumentDB-Sammlung?](#what-is-a-documentdb-collection).

![Screenshot mit „DocumentDB-Konten“ in der Navigationsleiste, mit dem Konto im Blatt „DocumentDB-Konten“ und mit der Datenbank im Blatt „DocumentDB-Konten“ im Fokus „Datenbanken“](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  Klicken Sie im [Azure-Portal](https://portal.azure.com/) in der Navigationsleiste auf **DocumentDB-Konten**. 

2.  Wählen Sie auf dem Blatt **DocumentDB-Konten** das Konto aus, in dem Sie eine Sammlung hinzufügen möchten. Wenn keine Konten aufgeführt sind, müssen Sie [ein DocumentDB-Konto erstellen](documentdb-create-account.md).

3. Scrollen Sie im Blatt **DocumentDB-Konto** für das ausgewählte Konto nach unten zum Fokus **Datenbanken**, und wählen Sie dann die Datenbank aus, in der eine Sammlung hinzugefügt werden soll.
    
4. Klicken Sie auf dem Blatt **Datenbank** auf **Sammlungen hinzufügen**.

	![Screenshot mit Schaltfläche „Sammlung hinzufügen“ auf dem Blatt „Datenbank“, mit den Einstellungen im Blatt „Sammlung hinzufügen“ und Schaltfläche „OK“ – Azure-Portal für DocumentDB – Cloudbasierte Datenbankerstellung für NoSQL-JSON-Datenbanken](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. Geben Sie auf dem Blatt **Sammlung hinzufügen** die ID für Ihre neue Sammlung ein. Der Namen einer Sammlung muss zwischen 1 und 255 Zeichen lang sein und darf weder `/ \ # ?` noch nachstehende Leerzeichen enthalten. Wenn der Name überprüft wurde, wird im ID-Feld ein grünes Häkchen angezeigt.

6. Wählen Sie einen Tarif für die neue Sammlung aus. Bei jeder erstellten Sammlung handelt es sich um eine fakturierbare Entität. Weitere Informationen zu den verfügbaren Leistungsebenen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels.md).

7. Wählen Sie eine der folgenden **Indizierungsrichtlinien** aus:

	- **Standard** Bei dieser Richtlinie wird eine Hashindizierung für Zeichenfolgen und eine Bereichsindizierung für Zahlen verwendet. Sie eignet sich am besten für Gleichheitsabfragen an Zeichenfolgen, für ORDER BY und für Bereichs- und Gleichheitsabfragen an Zahlen. Bei dieser Richtlinie ist der Indexspeicheraufwand relativ gering, und sie schließt eine Geoindizierung ein.
	- **Bereich** Wählen Sie diese Richtlinie, wenn Sie ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen und Zeichenfolgen durchführen. Bei dieser Richtlinie ist der Indexspeicheraufwand höher als bei der Richtlinie **Standard**, und sie schließt eine Geoindizierung ein.

	Weitere Informationen zu den Indizierungsrichtlinien finden Sie unter [Indizierungsrichtlinien für DocumentDB](documentdb-indexing-policies.md).

8. Klicken Sie am unteren Bildschirmrand auf **OK**, um die neue Sammlung zu erstellen.


9. Die neue Sammlung wird jetzt im Fokus **Auflistungen** auf dem Blatt **Datenbank** angezeigt.
 
	![Screenshot mit der neuen Sammlung in Blatt „Datenbank“ – Azure-Portal für DocumentDB – Cloudbasierte Datenbankerstellung für NoSQL-JSON-Datenbanken](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Was ist eine DocumentDB-Sammlung? 

Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Eine Sammlung ist eine fakturierbare Entität, bei der die [Kosten](documentdb-performance-levels.md) durch die Leistungsebene bestimmt werden, die mit der Sammlung verknüpft ist.

Sammlungen stellen die Transaktionsgrenze für gespeicherte Prozeduren und Trigger dar, und sie bilden den Einstiegspunkt für Abfragen und CRUD-Vorgänge. Jede Sammlung hat eine reservierte Durchsatzmenge, die für diese Sammlung spezifisch ist und die nicht mit anderen Sammlungen im gleichen Konto gemeinsam genutzt wird. Aus diesem Grund können Sie Ihre Anwendung im Hinblick auf Speicher und Durchsatz horizontal hochskalieren, indem Sie weitere Sammlungen hinzufügen und dann Ihre Dokumente auf diese verteilen.

Sammlungen sind nicht identisch mit Tabellen in relationalen Datenbanken. Sammlungen erzwingen kein Schema. Als Datenbank erzwingt DocumentDB grundsätzlich keine Schemas, es handelt sich um eine schemafreie Datenbank. Daher können Sie unterschiedliche Arten von Dokumenten mit unterschiedlichen Schemas in derselben Sammlung speichern. Falls gewünscht, können Sie Sammlungen auch verwenden, um wie bei Tabellen Objekte eines einzelnen Typs zu speichern. Das geeignetste Modell ist nur davon abhängig, wie die Daten zusammen in Abfragen und Transaktionen angezeigt werden.

## Weitere Methoden zum Erstellen einer DocumentDB-Sammlung

Sammlungen müssen nicht über das Portal erstellt werden. Sie können diese auch mithilfe der [DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellen. Ein C#-Codebeispiel, das veranschaulicht, wie eine Sammlung mit dem DocumentDB .NET SDK erstellt wird, finden Sie in der Datei [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) im CollectionManagement-Projekt. Dieses steht im Repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) auf [GitHub.com](https://github.com) zur Verfügung.

## Problembehandlung

Wenn **Sammlung hinzufügen** im Azure-Portal deaktiviert ist, bedeutet das, dass Ihr Konto derzeit deaktiviert ist. Das passiert normalerweise, wenn alle Leistungsguthaben für den Monat aufgebraucht sind.

## Nächste Schritte

Nachdem Sie nun über eine Sammlung verfügen, besteht der nächste Schritt darin, Dokumente zur Sammlung hinzufügen oder in die Sammlung zu importieren. Zum Hinzufügen von Dokumenten zu einer Sammlung stehen Ihnen verschiedene Möglichkeiten zur Verfügung:

- Sie können [Dokumente hinzufügen](documentdb-view-json-document-explorer.md), indem Sie den Dokument-Explorer im Portal verwenden.
- Sie können [Dokumente und Daten importieren](documentdb-import-data.md), indem Sie das Datenmigrationstool für DocumentDB verwenden, mit dem Sie JSON- und CSV-Dateien importieren können. Sie können auch Daten aus SQL Server, MongoDB, dem Azure-Tabellenspeicher und anderen DocumentDB-Sammlungen importieren. 
- Sie können Dokumente auch mithilfe eines der [DocumentDB-SDKs](documentdb-sdk-dotnet.md) hinzufügen. DocumentDB verfügt über SDKs für .NET, Java, Python, Node.js und JavaScript-API. Die Datei [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) im Projekt „DatabaseManagement“ (steht im Repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) auf [GitHub.com](https://github.com) zur Verfügung) veranschaulicht CRUD-Vorgänge für Dokumente mit dem DocumentDB .NET SDK.

Wenn eine Sammlung Dokumente enthält, können Sie in [DocumentDB SQL](documentdb-sql-query.md) an den Dokumenten [Abfragen ausführen](documentdb-sql-query.md#executing-queries), indem Sie den [Abfrage-Explorer](documentdb-query-collections-query-explorer.md) im Portal, die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [SDKs](documentdb-sdk-dotnet.md) verwenden.

<!---HONumber=AcomDC_1223_2015-->