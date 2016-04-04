<properties
   pageTitle="Umbenennen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Umbenennen von Tabellen in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Umbenennen in SQL Data Warehouse
SQL Server unterstützt das Umbenennen von Datenbanken über die gespeicherte Prozedur ```sp_renamedb```, SQL Data Warehouse hingegen verwendet die DDL-Syntax für die Umbenennung. Der DDL-Befehl lautet ```RENAME OBJECT```.

## Umbenennen von Tabellen

Derzeit können nur Tabellen umbenannt werden. Zum Umbenennen einer Tabelle wird die folgende Syntax verwendet:

```
RENAME OBJECT dbo.Customer TO NewCustomer;
```

Beim Umbenennen einer Tabelle werden alle einer Tabelle zugeordneten Objekte und Eigenschaften aktualisiert, um auf den neuen Tabellennamen zu verweisen. Beispielsweise werden die Tabellendefinitionen, Indizes, Einschränkungen und Berechtigungen aktualisiert. Ansichten werden nicht aktualisiert.

## Umbenennen externer Tabellen

Beim Umbenennen einer externen Tabelle ändert sich der Name der Tabelle in SQL Data Warehouse. Die Umbenennung wirkt sich nicht auf den Speicherort der externen Daten für die Tabelle aus.

## Ändern eines Tabellenschemas
Wenn das Schema geändert werden soll, zu dem ein Objekt gehört, ist dies mit ALTER SCHEMA möglich:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Für das Umbenennen von Tabellen ist eine exklusive Tabellensperre erforderlich.

Beachten Sie, dass eine Tabelle nicht umbenannt werden kann, während sie verwendet wird. Für das Umbenennen einer Tabelle muss die Tabelle exklusiv gesperrt werden. Wird die Tabelle verwendet, müssen Sie unter Umständen die Sitzung beenden, die die Tabelle verwendet. Zum Beenden einer Sitzung müssen Sie den [KILL](https://msdn.microsoft.com/library/ms173730.aspx)-Befehl verwenden. Lassen Sie bei Verwendung von ```KILL``` Vorsicht walten: Wenn die jeweilige Sitzung beendet wird, wird für nicht abgeschlossene Vorgänge ein Rollback durchgeführt. Sitzungen in SQL Data Warehouse verfügen über das Präfix „SID“. Sie müssen dieses Präfix und die Sitzungsnummer einfügen, wenn Sie den KILL-Befehl aufrufen. Beispiel: ```KILL 'SID1234'```. Weitere Informationen zu [Sitzungen] finden Sie im Artikel zu Verbindungen.


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md
[Sitzungen]: sql-data-warehouse-develop-connections.md

<!---HONumber=AcomDC_0323_2016-->