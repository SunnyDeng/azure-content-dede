<properties 
	pageTitle="Gewusst wie: Erstellen einer Datenbank in DocumentDB | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Datenbank mit dem Onlinedienstportal für Azure DocumentDB, einer NoSQL-Dokumentendatenbank für JSON, erstellen. Holen Sie sich noch heute eine kostenlose Testversion." 
	keywords="So erstellen Sie eine Datenbank" 
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
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# So erstellen Sie eine DocumentDB-Datenbank

Um Microsoft Azure DocumentDB zu verwenden, müssen Sie über ein [DocumentDB-Konto](documentdb-create-account.md), eine Datenbank, eine Sammlung und Dokumente verfügen. Dieses Thema beschreibt die Erstellung einer Datenbank für DocumentDB im Microsoft Azure-Portal.

![Screenshot zur Erstellung einer Datenbank mit Schaltfläche "Durchsuchen", DocumentDB-Konten auf dem Blatt "Durchsuchen" und einem DocumentDB-Konto auf dem Blatt "DocumentDB-Konten"](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  Klicken Sie in der Navigationsleiste im [Azure-Portal](https://portal.azure.com/) auf **DocumentDB-Konten**. 

2.  Wählen Sie auf dem Blatt**DocumentDB-Konten** das Konto aus, in dem Sie die NoSQL-Datenbank hinzufügen möchten. Wenn keine Konten aufgeführt sind, müssen Sie [ein DocumentDB-Konto erstellen](documentdb-create-account.md).

3. Klicken Sie auf dem Blatt **DocumentDB-Konto** auf **Datenbank hinzufügen**.

4. Geben Sie auf dem Blatt **Datenbank hinzufügen** die ID für Ihre neue Datenbank ein. Wenn der Name überprüft wurde, wird im **ID**-Feld ein grünes Häkchen angezeigt.

5. Klicken Sie am unteren Bildschirmrand auf **OK**, um die neue Datenbank zu erstellen.

7. Die neue Datenbank wird im Fokus **Datenbanken** des Blatts **DocumentDB-Konto** angezeigt.
 
	![Screenshot der neuen Datenbank auf dem Blatt "DocumentDB-Konto"](./media/documentdb-create-database/docdb-database-creation-7.png)

## Weitere Methoden zum Erstellen einer DocumentDB-Datenbank

Datenbanken müssen nicht über das Portal erstellt werden. Sie können diese auch mithilfe der [DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) erstellen. Ein C#-Codebeispiel, das veranschaulicht, wie eine Datenbank mit dem DocumentDB .NET SDK erstellt wird, finden Sie in der Datei [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) im DatabaseManagement-Projekt. Dieses steht im Repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) auf [GitHub.com](https://github.com) zur Verfügung.

## Nächste Schritte

Nachdem Sie nun gelernt haben, wie eine DocumentDB-Datenbank erstellt wird, besteht der nächste Schritt darin, [eine Sammlung zu erstellen](documentdb-create-collection.md).

Sobald die Sammlung erstellt ist, können Sie mithilfe des Dokument-Explorers im Portal [JSON-Dokumente hinzufügen](documentdb-view-json-document-explorer.md), das DocumentDB-Datenmigrationstool zum [Importieren von Dokumenten](documentdb-import-data.md) in der Sammlung verwenden oder mit einem der [DocumentDB SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) CRUD-Vorgänge ausführen. DocumentDB verfügt über SDKs für .NET, Java, Python, Node.js und JavaScript-API. .NET-Codebeispiele, die veranschaulichen, wie Sie Dokumente erstellen, entfernen, aktualisieren und löschen, finden Sie in [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) im DocumentManagement-Projekt im Repository „azure-documentdb-net“ auf „GitHub.com“.

Nachdem eine Sammlung Dokumente enthält, können Sie in [DocumentDB SQL](documentdb-sql-query.md) an den Dokumenten [Abfragen ausführen](documentdb-sql-query.md#executing-queries), indem Sie den [Abfrage-Explorer](documentdb-query-collections-query-explorer.md) im Portal, die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) verwenden.

<!---HONumber=AcomDC_0107_2016-->