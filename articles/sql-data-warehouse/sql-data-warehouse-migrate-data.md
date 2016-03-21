<properties
   pageTitle="Migrieren Ihrer Daten nach SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Migration von Daten in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="barbkess;sonyama"/>

# Migrieren von Daten
Das Hauptziel bei der Migration von Daten besteht im Auffüllen der SQLDW-Datenbank. Dies kann auf verschiedene Weise erfolgen. Zum Erreichen dieses Ziels können die ADF-Kopieraktivität, SSIS und bcp verwendet werden. Mit zunehmender Menge der Daten empfiehlt es sich jedoch, den Vorgang der Datenmigration in verschiedene Schritte aufzugliedern. Dies bietet Ihnen die Möglichkeit, die einzelnen Schritte im Hinblick auf Leistung und Belastbarkeit zu optimieren und so eine reibungslose Datenmigration zu gewährleisten.

In diesem Artikel werden zunächst einfache Migrationsszenarios der ADF-Kopieraktivität sowie von SSIS und bcp erörtert. Dann wird detaillierter erläutert, wie die Migration optimiert werden kann.

## Azure Data Factory (ADF)-Kopieraktivität
Die [ADF-Kopieraktivität][] ist Teil von [Azure Data Factory][]. Mithilfe der ADF-Kopieraktivität können Sie Ihre Daten in Flatfiles im lokalen Speicher, in Remoteflatfiles im Azure-Blob-Speicher oder direkt in SQL Data Warehouse exportieren.

Wenn sich Ihre Daten in Flatfiles befinden, müssen Sie sie zuerst in den Azure-Blob-Speicher übertragen. Erst dann können Sie sie in SQL Data Warehouse laden. Nachdem die Daten in den Azure-Blob-Speicher übertragen wurden, können Sie sie dann wieder mithilfe der [ADF-Kopieraktivität][] in SQL Data Warehouse übertragen.

Auch PolyBase stellt eine leistungsstarke Möglichkeit zum Laden der Daten dar. Allerdings bedeutet dies, dass statt einem zwei Tools verwendet werden. Wenn Ihr Schwerpunkt auf der besten Leistung liegt, sollten Sie PolyBase verwenden. Wenn Sie nur ein Tool verwenden möchten (und die Daten nicht allzu umfangreich sind), ist ADF die beste Lösung für Sie.

> [AZURE.NOTE] Für PolyBase müssen die Datendateien mit UTF-8 codiert sein. Dies ist die Standardcodierung der ADF-Kopieraktivität, sodass keine Änderungen vorgenommen werden müssen. Es gilt lediglich zu beachten, dass das Standardverhalten der ADF-Kopieraktivität nicht geändert wird.

In diesem Artikel finden Sie [Beispiele zur Verwendung der ADF-Kopieraktivität].

## Integration Services ##
Integration Services (SSIS) ist ein leistungsfähiges und flexibles ETL-Tool (Extrahieren Transformieren und Laden), das komplexe Workflows, Datentransformation und verschiedene Optionen zum Laden von Daten unterstützt. Mit SSIS können Sie Daten in Azure oder als Teil einer größeren Migration übertragen.

> [AZURE.NOTE] Mit SSIS können Daten ohne die Bytereihenfolge-Marke in der Datei in das Format UTF-8 exportiert werden. Um dies zu konfigurieren, müssen Sie die Zeichendaten im Datenfluss mithilfe der abgeleiteten Spaltenkomponente zunächst so konvertieren, dass die UTF-8-Codepage 65001 verwendet wird. Nachdem die Spalten konvertiert wurden, schreiben Sie die Daten in den Flatfile-Zieladapter. Dabei müssen Sie sicherstellen, dass 65001 auch als Codepage für die Datei ausgewählt wurde.

SSIS stellt eine Verbindung mit SQL Data Warehouse her. Die Verbindungsherstellung entspricht der mit einer SQL Server-Bereitstellung. Für die Verbindungen muss jedoch ein ADO.NET-Verbindungs-Manager verwendet werden. Zudem sollten Sie darauf achten, dass die Einstellung "Sofern verfügbar, Masseneinfügung verwenden" konfiguriert ist, um den Durchsatz zu maximieren. Weitere Informationen zu dieser Eigenschaft finden Sie in dem Artikel zum [ADO.NET-Zieladapter][].

> [AZURE.NOTE] Die Verbindungsherstellung mit Azure SQL Data Warehouse mithilfe von OLEDB wird nicht unterstützt.

Darüber hinaus besteht immer die Möglichkeit, dass ein Paket aufgrund von Drosselung oder Netzwerkproblemen fehlschlagen kann. Entwerfen Sie die Pakete so, dass sie zum Zeitpunkt des Fehlers fortgesetzt werden können, ohne dass die vor dem Fehler abgeschlossenen Arbeitsschritte erneut ausgeführt werden müssen.

Weitere Informationen finden Sie in der [SSIS-Dokumentation][].

## bcp
bcp ist ein Befehlszeilenprogramm, das für den Import und Export von Flatfiledaten entwickelt wurde. Einige Transformationen können während des Datenexports durchgeführt werden. Zum Durchführen einfacher Transformationen können Sie die Daten mit einer Abfrage auswählen und transformieren. Nach dem Export können die Flatfiles dann direkt in das Ziel in der SQL Data Warehouse-Datenbank geladen werden.

> [AZURE.NOTE] Häufig empfiehlt es sich, die während des Datenexports verwendeten Transformationen in einer Ansicht im Quellsystem zu kapseln. Dadurch wird sichergestellt, dass die Logik beibehalten wird und der Prozess wiederholbar ist.

bcp bietet folgende Vorteile:

- Einfachheit. bcp-Befehle lassen sich einfach erstellen und ausführen.
- Neustartfähiger Ladevorgang. Nach dem Exportieren kann der Ladevorgang beliebig oft ausgeführt werden.

bcp weist folgende Einschränkungen auf:

- bcp funktioniert nur mit tabellarischen Flatfiles. Für Dateien im XML- oder JSON-Format kann es z. B. nicht verwendet werden.
- bcp unterstützt den Export in das Format UTF-8 nicht. Dies verhindert möglicherweise die Verwendung von PolyBase für mithilfe von bcp exportierte Daten.
- Die Funktionen für die Datentransformation beschränken sich auf den Export und sind relativ einfach.
- bcp bietet keine Stabilität beim Laden von Daten über das Internet. Jede Instabilität im Netzwerk kann daher zu Fehlern beim Laden führen.
- bcp stützt sich auf das in der Zieldatenbank vor dem Laden vorhandene Schema.

Weitere Informationen finden Sie unter [Load data with bcp][] (in englischer Sprache).

## Optimieren der Datenmigration
Ein SQLDW-Datenmigrationsprozess kann wirksam in drei separate Schritte unterteilt werden:

1. Exportieren der Quelldaten
2. Übertragen der Daten in Azure
3. Laden der Daten in die SQLDW-Zieldatenbank

Jeder Schritt kann einzeln optimiert werden, sodass ein stabiler, neustartfähiger und flexibler Migrationsprozess entsteht, durch den die Leistung in jedem Schritt maximiert wird.

## Optimieren des Ladens von Daten
Betrachten wir diese Schritte zunächst in der umgekehrten Reihenfolge: Daten können am schnellsten über PolyBase geladen werden. Für die Optimierung eines über PolyBase durchgeführten Ladevorgangs müssen in den vorhergehenden Schritten bestimmte Voraussetzungen gegeben sein. Daher ist es am besten, sich hiermit im Voraus vertraut zu machen. Sie lauten wie folgt:

1. Codierung der Datendateien
2. Format der Datendateien
3. Speicherort der Datendateien

### Codieren
Für PolyBase müssen Datendateien mit UTF-8 codiert sein. Dies bedeutet, dass Ihre Daten beim Export dieser Anforderung entsprechen müssen. Wenn die Daten nur einfache ASCII-Zeichen (keine erweiterten ASCII-Zeichen) enthalten, werden diese direkt dem UTF-8-Standard zugeordnet, und Sie müssen sich keine größeren Gedanken über die Codierung machen. Wenn die Daten jedoch Sonderzeichen wie z. B. Umlaute, Akzente oder Symbole enthalten oder nicht lateinische Sprachen unterstützen, müssen Sie sicherstellen, dass die Exportdateien ordnungsgemäß UTF-8-codiert sind.

> [AZURE.NOTE] bcp unterstützt den Export von Daten in das Format UTF-8 nicht. Daher ist es am besten, die Daten mithilfe von Integration Services oder der ADF-Kopieraktivität zu exportieren. Es soll auch darauf hingewiesen werden, dass die UTF-8 Bytereihenfolge-Marke (Byte Order Mark, BOM) in der Datendatei nicht erforderlich ist.

Alle Dateien, die mit UTF-16 codiert sind, müssen ***vor*** der Datenübertragung neu geschrieben werden.

### Format der Datendateien
PolyBase erfordert das feste Zeilenabschlusszeichen "\\n" oder einen Zeilenvorschub. Ihre Datendateien müssen diesem Standard entsprechen. Für Zeichenfolgen- oder Spaltenabschlusszeichen gibt es keine Einschränkungen.

Sie müssen jede Spalte in der Datei als Teil der externen Tabelle in PolyBase definieren. Stellen Sie sicher, dass alle exportierten Spalten erforderlich sind und die Datentypen den erforderlichen Standards entsprechen.

Ausführliche Informationen zu den unterstützten Datentypen finden Sie im Artikel [Migrieren Ihres Schemas].

### Speicherort der Datendateien
SQL Data Warehouse verwendet PolyBase ausschließlich zum Laden von Daten aus dem Azure-Blob-Speicher. Aus diesem Grund müssen die Daten zuerst in den Blob-Speicher übertragen werden.

## Optimieren der Datenübertragung
Einer der langsamsten Schritte bei der Datenmigration ist die Übertragung der Daten in Azure. Dabei kann nicht nur die Netzwerkbandbreite Probleme verursachen, sondern auch die Zuverlässigkeit des Netzwerks zu Beeinträchtigungen führen. Die Migration von Daten in Azure erfolgt standardmäßig über das Internet, sodass mit Übertragungsfehlern zu rechnen ist. Diese Fehler führen jedoch möglicherweise dazu, dass Daten vollständig oder teilweise erneut gesendet werden müssen.

Glücklicherweise stehen mehrere Optionen zur Verbesserung der Geschwindigkeit und Stabilität dieses Vorgangs zur Verfügung:

### [ExpressRoute][]
Beispielsweise können Sie [ExpressRoute][] verwenden, um die Übertragung von Daten zu beschleunigen. [ExpressRoute][] ermöglicht Ihnen die Erstellung einer privaten Verbindung mit Azure, d. h., die Verbindung erfolgt nicht über das öffentliche Internet. Dieser Schritt ist keineswegs unbedingt erforderlich. Dadurch verbessert sich jedoch der Durchsatz bei der Übertragung von Daten in Azure von einem Standort vor Ort oder in der Nähe.

Die Verwendung von [ExpressRoute][] bietet folgende Vorteile:

1. Erhöhte Zuverlässigkeit
2. Höhere Netzwerkgeschwindigkeit
3. Niedrigere Netzwerklatenz
4. Höhere Netzwerksicherheit

[ExpressRoute][] erweist sich nicht nur bei der Migration, sondern auch bei einer Reihe verschiedener Szenarios als vorteilhaft.

Interessiert? Weitere Informationen und Preise finden Sie in der [ExpressRoute-Dokumentation][].

### Azure Import/Export-Dienst
Der Azure Import/Export-Dienst ist ein Datenübertragungsprozess zur Übertragung umfangreicher (GB++) und sehr umfangreicher (TB++) Datenmengen in Azure. Dabei werden Ihre Daten auf Datenträger geschrieben und an ein Azure-Rechenzentrum versendet. Der Inhalt der Datenträger wird dann in Ihrem Auftrag in Azure-Blob-Speicher geladen.

Allgemeine Übersicht über den Import- und Exportprozess:

1. Konfigurieren eines Azure-Blob-Speichercontainers zum Empfangen der Daten
2. Exportieren der Daten in den lokalen Speicher
2. Kopieren der Daten auf SATA II/III-Festplatten (3,5 Zoll) mit dem +++[Azure Import/Export-Tool]
3. Erstellen eines Importauftrags mithilfe des Azure Import/Export-Diensts mit den vom [Azure Import/Export-Tool] generierten Journaldateien
4. Versenden der Datenträger an das von Ihnen benannte Azure-Rechenzentrum
5. Übertragen der Daten in Ihren Azure-Blob-Speichercontainer
6. Laden der Daten in SQLDW mithilfe von PolyBase

### Befehlszeilenprogramm [AZCopy][]
Das Befehlszeilenprogramm [AZCopy][] eignet sich hervorragend zum Übertragen Ihrer Daten in Azure Storage-Blobs. Es ist für die Übertragung kleiner (MB++) bis sehr umfangreicher (GB++) Datenmengen konzipiert. [AZCopy] bietet zudem einen hohen stabilen Durchsatz beim Übertragen von Daten in Azure und ist daher eine gute Wahl für die Datenübertragung. Nach dem Übertragen können Sie die Daten mithilfe von PolyBase in SQL Data Warehouse laden. Sie können AZCopy mit dem Task "Prozess ausführen" auch in Ihre SSIS-Pakete integrieren.

Zur Verwendung von AZCopy müssen Sie dieses Programm zunächst herunterladen und installieren. Es ist eine [Produktionsversion][] und eine [Vorschauversion ][] verfügbar.

Eine Datei können Sie mit einem Befehl wie dem folgenden aus Ihrem Dateisystem hochladen:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Allgemeine Übersicht über diesen Prozess:

1. Konfigurieren eines Azure-Blob-Speichercontainers zum Empfangen der Daten
2. Exportieren der Daten in den lokalen Speicher
3. Kopieren der Daten in den Azure-Blob-Speichercontainer mithilfe von AZCopy
4. Laden der Daten in SQL Data Warehouse mithilfe von PolyBase

Die vollständige Dokumentation finden Sie hier: [AZCopy][].

## Optimieren des Datenexports
Neben dem Sicherstellen, dass der Export den von PolyBase vorgegebenen Anforderungen entspricht, können Sie den gesamten Prozess durch die Optimierung des Datenexports weiter verbessern.

> [AZURE.NOTE] Da die Daten für PolyBase im Format UTF-8 codiert sein müssen, ist es unwahrscheinlich, dass Sie den Export der Daten mit bcp durchführen. bcp unterstützt die Ausgabe von Datendateien in UTF-8 nicht. SSIS oder die ADF-Kopieraktivität eignen sich weitaus besser für diesen Datenexport.

### Datenkomprimierung
PolyBase kann die mit GZip komprimierten Daten lesen. Wenn Sie Ihre Daten in GZip-Dateien komprimieren können, minimieren Sie die Menge der über das Netzwerk übertragenen Daten.

### Mehrere Dateien
Die Unterteilung großer Tabellen in mehrere Dateien verbessert nicht nur die Exportgeschwindigkeit, sondern auch die Neustartfähigkeit bei der Übertragung und die gesamte Verwaltbarkeit der Daten, wenn sie sich im Azure-Blob-Speicher befinden. Einer der vielen Vorteile von PolyBase ist, dass alle Dateien in einem Ordner gelesen und wie eine einzige Tabelle behandelt werden. Es empfiehlt sich daher, die Dateien für die einzelnen Tabellen jeweils in einem separaten Ordner zu speichern.

PolyBase unterstützt auch eine Funktion zum "rekursiven Ordnerdurchlauf". Mit dieser Funktion können Sie die Organisation der exportierten Daten weiter verbessern und so die Datenverwaltung optimieren.

Weitere Informationen zum Laden von Daten mit PolyBase finden Sie unter [Load data with bcp][] (in englischer Sprache).


## Nächste Schritte
Weitere Informationen zur Migration finden Sie unter [Migrieren Ihrer Lösung nach SQL Data Warehouse][]. Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[ADF-Kopieraktivität]: ../data-factory/data-factory-copy-activity.md
[Beispiele zur Verwendung der ADF-Kopieraktivität]: ../data-factory/data-factory-copy-activity-examples.md
[Entwicklungsübersicht]: sql-data-warehouse-develop-overview.md
[Migrieren Ihrer Lösung nach SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load data with bcp]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute-Dokumentation]: http://azure.microsoft.com/documentation/services/expressroute/

[Produktionsversion]: http://aka.ms/downloadazcopy/
[Vorschauversion ]: http://aka.ms/downloadazcopypr/
[ADO.NET-Zieladapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS-Dokumentation]: https://msdn.microsoft.com/library/ms141026.aspx

<!---HONumber=AcomDC_0309_2016-->