<properties
   pageTitle="Umbenennen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Umbenennen von Objekten und Datenbanken in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Umbenennen in SQL Data Warehouse
SQL Server unterstützt die Objekt- und Datenbankumbenennung mit den gespeicherten Prozeduren „sp_rename“ bzw. „sp_renamedb“.

SQL Data Warehouse verwendet DDL-Syntax, um das gleiche Ziel zu erreichen. Die DDL-Befehle lauten RENAME OBJECT und RENAME DATABASE.

## Umbenennen von Objekten

Es ist wichtig zu verstehen, dass der Name sich nur auf das Objekt auswirkt, das umbenannt wird. Es findet keine Weitergabe der Namensänderung statt. Daher sind alle Sichten ungültig, in denen der alte Name eines Objekts verwendet wird, bis ein Objekt mit dem alten Namen erstellt wurde. Aus diesem Grund wird Ihnen `RENAME OBJECT` häufig in Paaren begegnen.

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## Umbenennen von Datenbanken

Das Umbenennen von Datenbanken ähnelt der Umbenennung von Objekten sehr.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

Bedenken Sie, dass Sie ein Objekt oder eine Datenbank nicht umbenennen können, wenn andere Benutzer damit verbunden sind oder das Objekt bzw. die Datenbank verwenden. Unter Umständen müssen Sie geöffnete Sitzungen zuerst beenden. Zum Beenden einer Sitzung müssen Sie den [KILL][]-Befehl verwenden. Verwenden Sie KILL mit Bedacht. Wenn der Befehl ausgeführt wird, wird die jeweilige Sitzung beendet, und für nicht abgeschlossene Vorgänge wird ein Rollback durchgeführt.

> [AZURE.NOTE]Sitzungen in SQL Data Warehouse verfügen über das Präfix „SID“. Sie müssen dieses Präfix und die Sitzungsnummer einfügen, wenn Sie den KILL-Befehl aufrufen. Mit KILL 'SID1234' wird beispielsweise die Sitzung 1234 beendet – sofern Sie über die richtigen Berechtigungen für die Ausführung verfügen.

## Beenden von Sitzungen
Um eine Datenbank umbenennen zu können, müssen Sie unter Umständen Sitzungen beenden, die mit SQL Data Warehouse verbunden sind. Die folgende Abfrage generiert eine eindeutige Liste mit KILL-Befehlen zum Löschen der Verbindungen (Speichern für die aktuelle Sitzung).

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
```

## Wechseln von Schemas
Wenn das Schema geändert werden soll, zu dem ein Objekt gehört, ist dies mit `ALTER SCHEMA` möglich:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/de-de/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=July15_HO1-->