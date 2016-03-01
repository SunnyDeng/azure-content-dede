<properties 
	pageTitle="Gewusst wie: Erstellen einer Datenbank in DocumentDB | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Datenbank mit dem Onlinedienstportal für Azure DocumentDB, einer NoSQL-Dokumentendatenbank für JSON, erstellen. Erhalten Sie noch heute ein kostenloses Konto." 
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
	ms.topic="get-started-article" 
	ms.date="02/19/2016" 
	ms.author="mimig"/>

# So erstellen Sie über das Azure-Portal eine Datenbank für DocumentDB

Um Microsoft Azure DocumentDB zu verwenden, müssen Sie über ein [DocumentDB-Konto](documentdb-create-account.md), eine Datenbank, eine Sammlung und Dokumente verfügen. Dieses Thema beschreibt die Erstellung einer Datenbank für DocumentDB im Microsoft Azure-Portal. Informationen zum Erstellen einer Datenbank mit einem der SDKs finden Sie unter [Weitere Methoden zum Erstellen einer DocumentDB-Datenbank](#other-ways-to-create-a-documentdb-database).

![Screenshot zur Erstellung einer Datenbank mit DocumentDB-Konten auf dem Blatt „Durchsuchen“ und einem DocumentDB-Konto auf dem Blatt „DocumentDB-Konten“](./media/documentdb-create-database/docdb-database-creation-1-2.png)

1.  Klicken Sie im [Azure-Portal](https://portal.azure.com/) in der Navigationsleiste auf **DocumentDB-Konten**. Wenn **DocumentDB-Konten** nicht sichtbar ist, klicken Sie auf **Durchsuchen** und dann auf **DocumentDB-Konten**.

2.  Wählen Sie auf dem Blatt**DocumentDB-Konten** das Konto aus, in dem Sie die NoSQL-Datenbank hinzufügen möchten. Wenn keine Konten aufgeführt sind, müssen Sie [ein DocumentDB-Konto erstellen](documentdb-create-account.md).

    ![Screenshot zur Erstellung einer Datenbank mit der Schaltfläche „Datenbank hinzufügen“, dem Feld „ID“ und der Schaltfläche „OK“](./media/documentdb-create-database/docdb-database-creation-3-5.png)

3. Klicken Sie auf dem Blatt **DocumentDB-Konto** auf **Datenbank hinzufügen**.

4. Geben Sie auf dem Blatt **Datenbank hinzufügen** die ID für Ihre neue Datenbank ein. Wenn der Name überprüft wurde, wird im **ID**-Feld ein grünes Häkchen angezeigt.

5. Klicken Sie am unteren Bildschirmrand auf **OK**, um die neue Datenbank zu erstellen.

6. Die neue Datenbank wird im Fokus **Datenbanken** des Blatts **DocumentDB-Konto** angezeigt.
 
	![Screenshot der neuen Datenbank auf dem Blatt "DocumentDB-Konto"](./media/documentdb-create-database/docdb-database-creation-6.png)

## Weitere Methoden zum Erstellen einer DocumentDB-Datenbank

Datenbanken müssen nicht über das Portal erstellt werden. Sie können diese auch mithilfe der [DocumentDB-SDKs](documentdb-sdk-dotnet.md) oder der [REST-API](https://msdn.microsoft.com/library/mt489072.aspx) erstellen. Informationen zum Arbeiten mit Datenbanken mithilfe des .NET SDK finden Sie unter [.NET-Datenbankbeispiele](documentdb-dotnet-samples.md#database-examples). Informationen zum Arbeiten mit Datenbanken mithilfe des Node.js SDK finden Sie unter [Node.js-Datenbankbeispiele](documentdb-nodejs-samples.md#database-examples).

## Nächste Schritte

Nachdem Sie nun gelernt haben, wie eine DocumentDB-Datenbank erstellt wird, besteht der nächste Schritt darin, [eine Sammlung zu erstellen](documentdb-create-collection.md).

Sobald die Sammlung erstellt ist, können Sie mithilfe des Dokument-Explorers im Portal [JSON-Dokumente hinzufügen](documentdb-view-json-document-explorer.md), das DocumentDB-Datenmigrationstool zum [Importieren von Dokumenten](documentdb-import-data.md) in der Sammlung verwenden oder mit einem der [DocumentDB SDKs](documentdb-sdk-dotnet.md) CRUD-Vorgänge ausführen. DocumentDB verfügt über SDKs für .NET, Java, Python, Node.js und JavaScript-API. .NET-Codebeispiele, die das Erstellen, Entfernen, Aktualisieren und Löschen von Dokumenten veranschaulichen, finden Sie unter [.NET-Dokumentbeispiele](documentdb-dotnet-samples.md#document-examples). Informationen zum Arbeiten mit Dokumenten mithilfe des Node.js SDK finden Sie unter [Node.js-Dokumentbeispiele](documentdb-nodejs-samples.md#document-examples).

Wenn eine Sammlung Dokumente enthält, können Sie in [DocumentDB SQL](documentdb-sql-query.md) an den Dokumenten [Abfragen ausführen](documentdb-sql-query.md#executing-sql-queries), indem Sie den [Abfrage-Explorer](documentdb-query-collections-query-explorer.md) im Portal, die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [SDKs](documentdb-sdk-dotnet.md) verwenden.

<!---HONumber=AcomDC_0224_2016-->