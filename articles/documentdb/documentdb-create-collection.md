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
	ms.topic="get-started-article" 
	ms.date="03/30/2016" 
	ms.author="mimig"/>

# So erstellen Sie eine DocumentDB-Sammlung über das Azure-Portal

Für die Verwendung von Microsoft Azure DocumentDB benötigen Sie ein [DocumentDB-Konto](documentdb-create-account.md), eine [Datenbank](documentdb-create-database.md), eine Sammlung und Dokumente. Dieses Thema beschreibt die Erstellung einer DocumentDB-Sammlung im Azure-Portal.

Sie wissen nicht, was eine Sammlung ist? Dann lesen Sie den Abschnitt [Was ist eine DocumentDB-Sammlung?](#what-is-a-documentdb-collection).

1.  Klicken Sie im [Azure-Portal](https://portal.azure.com/) in der Navigationsleiste auf **DocumentDB-Konten**. Wenn **DocumentDB-Konten** nicht sichtbar ist, klicken Sie auf **Durchsuchen** und dann auf **DocumentDB-Konten**.

    ![Screenshot mit „DocumentDB-Konten“ in der Navigationsleiste, mit dem Konto im Blatt „DocumentDB-Konten“ und mit der Datenbank im Blatt „DocumentDB-Konten“ im Fokus „Datenbanken“](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

2.  Wählen Sie auf dem Blatt **DocumentDB-Konten** das Konto aus, in dem Sie eine Sammlung hinzufügen möchten. Wenn keine Konten aufgeführt sind, müssen Sie [ein DocumentDB-Konto erstellen](documentdb-create-account.md).

3. Scrollen Sie im Blatt **DocumentDB-Konto** für das ausgewählte Konto nach unten zum Fokus **Datenbanken**, und wählen Sie dann die Datenbank aus, in der eine Sammlung hinzugefügt werden soll.

    ![Screenshot mit „DocumentDB-Konten“ in der Navigationsleiste, mit dem Konto im Blatt „DocumentDB-Konten“ und mit der Datenbank im Blatt „DocumentDB-Konten“ im Fokus „Datenbanken“](./media/documentdb-create-collection/docdb-database-creation-3.png)

4. Klicken Sie auf dem Blatt **Datenbank** auf **Sammlungen hinzufügen**.

	![Screenshot mit Schaltfläche „Sammlung hinzufügen“ auf dem Blatt „Datenbank“, mit den Einstellungen im Blatt „Sammlung hinzufügen“ und Schaltfläche „OK“ – Azure-Portal für DocumentDB – Cloudbasierte Datenbankerstellung für NoSQL-JSON-Datenbanken](./media/documentdb-create-collection/docdb-collection-creation-4.png)

5. Geben Sie auf dem Blatt **Sammlung hinzufügen** im Feld **ID** die ID für Ihre neue Sammlung ein. Der Namen einer Sammlung muss zwischen 1 und 255 Zeichen lang sein und darf weder `/ \ # ?` noch nachstehende Leerzeichen enthalten. Wenn der Name überprüft wurde, wird im ID-Feld ein grünes Häkchen angezeigt.

	![Screenshot mit Schaltfläche „Sammlung hinzufügen“ auf dem Blatt „Datenbank“, mit den Einstellungen im Blatt „Sammlung hinzufügen“ und Schaltfläche „OK“ – Azure-Portal für DocumentDB – Cloudbasierte Datenbankerstellung für NoSQL-JSON-Datenbanken](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

6. Wenn Sie eine Sammlung mit einer Partition erstellen möchten, klicken Sie auf den Pfeil, um einen Tarif auszuwählen. Anschließend klicken Sie auf „Alle anzeigen“ und wählen eine Leistungsstufe für die Sammlung aus. Weitere Informationen zu den verfügbaren Leistungsebenen finden Sie unter [Leistungsebenen in DocumentDB](documentdb-performance-levels.md). Bei jeder erstellten Sammlung handelt es sich um eine fakturierbare Entität.

	Wenn Sie eine partitionierte Sammlung erstellen möchten, fahren Sie mit Schritt 7 fort, da Sie keinen Tarif auswählen müssen.

7. Wählen Sie einen **Partitionierungsmodus** für die Sammlung aus, entweder **Einzelne Partition** oder **Partitioniert**. Eine einzelne Partition verfügt über eine reservierte Speicherkapazität von 10 GB und kann Durchsatzebenen von 400 bis 10.000 Anforderungseinheiten pro Sekunde aufweisen. Eine partitionierte Auflistung kann skaliert werden, um 250 GB an Speicher über mehrere Partitionen verteilt zu verarbeiten, und kann Durchsatzebenen von 10.100 bis 250.000 Anforderungseinheiten pro Sekunde aufweisen.

8. Wählen Sie den **Durchsatz** für die partitionierte Sammlung. Eine Anforderungseinheit (Request Unit, RU) entspricht dem Durchsatz des Lesevorgangs eines Dokuments mit 1 KB. Weitere Informationen zu Anforderungseinheiten finden Sie unter [Anforderungseinheiten](documentdb-request-units.md).

9. Wenn Sie eine partitionierte Sammlung erstellen, wählen Sie den **Partitionsschlüssel** für die Sammlung. Die Auswahl des richtigen Partitionsschlüssels ist wichtig für die Erstellung einer leistungsfähigen-Auflistung. Weitere Informationen zum Auswählen eines Partitionsschlüssels finden Sie unter [Partitionieren von Daten in DocumentDB](documentdb-partition-data.md).

10. Klicken Sie am unteren Bildschirmrand auf **OK**, um die neue Sammlung zu erstellen.

11. Die neue Sammlung wird jetzt im Fokus **Auflistungen** auf dem Blatt **Datenbank** angezeigt.
 
	![Screenshot mit der neuen Sammlung in Blatt „Datenbank“ – Azure-Portal für DocumentDB – Cloudbasierte Datenbankerstellung für NoSQL-JSON-Datenbanken](./media/documentdb-create-collection/docdb-collection-creation-9.png)

## Was ist eine DocumentDB-Sammlung? 

Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Eine Sammlung ist eine fakturierbare Entität, bei der die [Kosten](documentdb-performance-levels.md) durch den bereitgestellten Durchsatz der Sammlung bestimmt werden. Sammlungen können eine/n oder mehrere Partitionen oder Server umfassen und können skaliert werden, um praktisch unbegrenzte Mengen an Speicher oder Durchsatz zu verarbeiten.

Sammlungen werden von DocumentDB automatisch in einen oder mehrere physische Server partitioniert. Wenn Sie eine Sammlung erstellen, können Sie den bereitgestellten Durchsatz in Form von Anforderungseinheiten pro Sekunde und einer Partitionsschlüsseleigenschaft angeben. Der Wert dieser Eigenschaft wird von DocumentDB zum Verteilen von Dokumenten zwischen Partitionen und zum Weiterleiten von Anforderungen wie Abfragen verwendet. Der Partitionsschlüssel fungiert außerdem als die Transaktionsgrenze für gespeicherte Prozeduren und Trigger. Jede Sammlung hat eine reservierte Durchsatzmenge, die für diese Sammlung spezifisch ist und die nicht mit anderen Sammlungen im gleichen Konto gemeinsam genutzt wird. Daher können Sie Ihre Anwendung sowohl im Hinblick auf Speicher als auch Durchsatz horizontal hochskalieren.

Sammlungen sind nicht identisch mit Tabellen in relationalen Datenbanken. Sammlungen erzwingen kein Schema. Als Datenbank erzwingt DocumentDB grundsätzlich keine Schemas, es handelt sich um eine schemafreie Datenbank. Daher können Sie unterschiedliche Arten von Dokumenten mit unterschiedlichen Schemas in derselben Sammlung speichern. Falls gewünscht, können Sie Sammlungen auch verwenden, um wie bei Tabellen Objekte eines einzelnen Typs zu speichern. Das geeignetste Modell ist nur davon abhängig, wie die Daten zusammen in Abfragen und Transaktionen angezeigt werden.

## Weitere Methoden zum Erstellen einer DocumentDB-Sammlung

Sammlungen müssen nicht über das Portal erstellt werden. Sie können diese auch mithilfe der [DocumentDB-SDKs](documentdb-sdk-dotnet.md) und der REST-API erstellen.

- Ein C#-Codebeispiel finden Sie unter [C#-Sammlungsbeispiele](documentdb-dotnet-samples.md#collection-examples). 
- Ein Node.js-Codebeispiel finden Sie unter [Node.js-Sammlungsbeispiele](documentdb-nodejs-samples.md#collection-examples).
- Ein Python-Codebeispiel finden Sie unter [Python-Sammlungsbeispiele](documentdb-python-samples.md#collection-examples).
- Ein REST-API-Beispiel finden Sie unter [Erstellen einer Sammlung](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## Problembehandlung

Wenn **Sammlung hinzufügen** im Azure-Portal deaktiviert ist, bedeutet das, dass Ihr Konto derzeit deaktiviert ist. Das passiert normalerweise, wenn alle Leistungsguthaben für den Monat aufgebraucht sind.

## Nächste Schritte

Nachdem Sie nun über eine Sammlung verfügen, besteht der nächste Schritt darin, Dokumente zur Sammlung hinzufügen oder in die Sammlung zu importieren. Zum Hinzufügen von Dokumenten zu einer Sammlung stehen Ihnen verschiedene Möglichkeiten zur Verfügung:

- Sie können [Dokumente hinzufügen](documentdb-view-json-document-explorer.md), indem Sie den Dokument-Explorer im Portal verwenden.
- Sie können [Dokumente und Daten importieren](documentdb-import-data.md), indem Sie das Datenmigrationstool für DocumentDB verwenden, mit dem Sie JSON- und CSV-Dateien importieren können. Sie können auch Daten aus SQL Server, MongoDB, dem Azure-Tabellenspeicher und anderen DocumentDB-Sammlungen importieren. 
- Sie können Dokumente auch mithilfe eines der [DocumentDB-SDKs](documentdb-sdk-dotnet.md) hinzufügen. DocumentDB verfügt über SDKs für .NET, Java, Python, Node.js und JavaScript-API. C#-Codebeispiele, die die Verwendung von Dokumenten mithilfe des DocumentDB .NET SDK veranschaulichen, finden Sie unter [C#-Dokumentbeispiele](documentdb-dotnet-samples.md#document-examples). Node.js-Codebeispiele, die die Verwendung von Dokumenten mithilfe des DocumentDB Node.js SDK veranschaulichen, finden Sie unter [Node.js-Dokumentbeispiele](documentdb-nodejs-samples.md#document-examples).

Wenn eine Sammlung Dokumente enthält, können Sie in [DocumentDB SQL](documentdb-sql-query.md) an den Dokumenten [Abfragen ausführen](documentdb-sql-query.md#executing-queries), indem Sie den [Abfrage-Explorer](documentdb-query-collections-query-explorer.md) im Portal, die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) oder eines der [SDKs](documentdb-sdk-dotnet.md) verwenden.

<!---HONumber=AcomDC_0330_2016-->