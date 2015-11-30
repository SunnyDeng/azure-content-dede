<properties 
	pageTitle="Erstellen einer DocumentDB-Datenbanksammlung | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Sammlungen mit dem Onlinedienstportal für Azure DocumentDB, einer verwalteten NoSQL-Dokumentendatenbank für JSON, erstellen. Holen Sie sich noch heute eine kostenlose Testversion." 
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
	ms.date="09/28/2015" 
	ms.author="mimig"/>

# Erstellen einer DocumentDB-Sammlung

Für die Verwendung von Microsoft Azure DocumentDB benötigen Sie ein [DocumentDB-Konto](documentdb-create-account.md), eine [Datenbank](documentdb-create-database.md), eine Sammlung und Dokumente. Dieses Thema beschreibt die Erstellung einer DocumentDB-Sammlung im Azure-Vorschauportal.

![Screenshot mit „DocumentDB-Konten“ in der Navigationsleiste, mit dem Konto im Blatt „DocumentDB-Konten“ und mit der Datenbank im Blatt „DocumentDB-Konten“ im Fokus „Datenbanken“](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  Klicken Sie in der Navigationsleiste im [Azure-Vorschauportal](https://portal.azure.com/) auf **DocumentDB-Konten**. 

2.  Wählen Sie auf dem Blatt **DocumentDB-Konten** das Konto aus, in dem Sie eine Sammlung hinzufügen möchten. Wenn keine Konten aufgeführt sind, müssen Sie [ein DocumentDB-Konto erstellen](documentdb-create-account.md).

3. Scrollen Sie im Blatt **DocumentDB-Konto** für das ausgewählte Konto nach unten zum Fokus **Datenbanken**, und wählen Sie dann die Datenbank aus, in der eine Sammlung hinzugefügt werden soll.
    
4. Klicken Sie auf dem Blatt **Datenbank** auf **Sammlungen hinzufügen**.

	![Screenshot mit Schaltfläche "Sammlung hinzufügen" auf dem Blatt "Datenbank", Einstellungen im Blatt "Sammlung hinzufügen" und Schaltfläche "OK"](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. Geben Sie auf dem Blatt **Sammlung hinzufügen** die ID für Ihre neue Sammlung ein. Wenn der Name überprüft wurde, wird im ID-Feld ein grünes Häkchen angezeigt.

6. Wählen Sie einen Tarif für die neue Sammlung aus. Bei jeder erstellten Sammlung handelt es sich um eine fakturierbare Entität. Weitere Informationen zu den verfügbaren Leistungsebenen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels.md).

7. Wählen Sie eine der folgenden **Indizierungsrichtlinien** aus:

	- **Standard** Diese Richtlinie empfiehlt sich, wenn Sie Gleichheitsabfragen für Zeichenfolgen durchführen und ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen verwenden. Der Indexspeicheraufwand dieser Richtlinie ist geringer als bei **Bereich**.
	- **Hash**. Diese Richtlinie empfiehlt sich, wenn Sie Gleichheitsabfragen für Zahlen und Zeichenfolgen durchführen. Bei dieser Richtlinie ist der Indexspeicheraufwand am geringsten.
	- **Bereich** Wählen Sie diese Richtlinie, wenn Sie ORDER BY-, Bereichs- und Gleichheitsabfragen für Zahlen und Zeichenfolgen durchführen. Der Indexspeicheraufwand dieser Richtlinie ist höher als bei **Standard** oder **Hash**.

	Weitere Informationen zu den Indizierungsrichtlinien finden Sie unter [Indizierungsrichtlinien für DocumentDB](documentdb-indexing-policies.md).

8. Klicken Sie am unteren Bildschirmrand auf **OK**, um die neue Sammlung zu erstellen.


9. Die neue Sammlung wird jetzt im Fokus **Auflistungen** auf dem Blatt **Datenbank** angezeigt.
 
	![Screenshot der neuen Sammlung im Blatt "Datenbank"](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Weitere Methoden zum Erstellen einer DocumentDB-Sammlung

Sammlungen müssen nicht über das Vorschauportal erstellt werden. Sie können diese auch mithilfe der [DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellen. Ein C#-Codebeispiel, das veranschaulicht, wie eine Sammlung mit dem DocumentDB .NET SDK erstellt wird, finden Sie in der Datei [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) im CollectionManagement-Projekt. Dieses steht im Repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) auf [GitHub.com](https://github.com) zur Verfügung.

## Nächste Schritte

Nachdem Sie nun über eine Sammlung verfügen, besteht der nächste Schritt darin, Dokumente zur Sammlung hinzufügen oder in die Sammlung zu importieren. Zum Hinzufügen von Dokumenten zu einer Sammlung stehen Ihnen verschiedene Möglichkeiten zur Verfügung:

- Sie können [Dokumente hinzufügen](../documentdb-view-json-document-explorer.md), indem Sie den Dokument-Explorer im Vorschauportal verwenden.
- Sie können [Dokumente und Daten importieren](documentdb-import-data.md), indem Sie das Datenmigrationstool für DocumentDB verwenden, mit dem Sie JSON- und CSV-Dateien importieren können. Sie können auch Daten aus SQL Server, MongoDB, dem Azure-Tabellenspeicher und anderen DocumentDB-Sammlungen importieren. 
- Sie können Dokumente auch mithilfe eines der [DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) hinzufügen. DocumentDB verfügt über SDKs für .NET, Java, Python, Node.js und JavaScript-API. Die Datei [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) im Projekt „DatabaseManagement“ (steht im Repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) auf [GitHub.com](https://github.com) zur Verfügung) veranschaulicht CRUD-Vorgänge für Dokumente mit dem DocumentDB .NET SDK.

Nachdem eine Sammlung Dokumente enthält, können Sie in [DocumentDB SQL](documentdb-sql-query.md) an den Dokumenten [Abfragen ausführen](documentdb-sql-query.md#executing-queries), indem Sie den [Abfrage-Explorer](documentdb-query-collections-query-explorer.md) im Vorschauportal, die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) verwenden.

<!---HONumber=Nov15_HO4-->