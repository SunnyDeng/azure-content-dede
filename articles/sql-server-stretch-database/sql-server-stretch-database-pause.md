<properties
	pageTitle="Anhalten und Fortsetzen der Datenmigration (Stretch Database) | Microsoft Azure"
	description="Erfahren Sie Einzelheiten über das Anhalten oder Fortsetzen der Datenmigration in Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Anhalten und Fortsetzen der Datenmigration (Stretch Database)

Wählen Sie zum Anhalten oder Fortsetzen der Migration von Daten in Azure für eine Tabelle in SQL Server Management Studio **Stretch** aus, und wählen Sie dann **Anhalten**, um die Datenmigration anzuhalten, oder **Fortsetzen**, um die Datenmigration fortzusetzen. Sie können zum Anhalten oder Fortsetzen der Datenmigration auch Transact-SQL verwenden.

Die Datenmigration lässt sich für einzelne Tabellen anhalten, um Probleme auf dem lokalen Server zu behandeln oder um die verfügbare Netzwerkbandbreite zu maximieren.

## Anhalten der Datenmigration

### Verwenden von SQL Server Management Studio zum Anhalten der Datenmigration

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Stretch-fähige Tabelle aus, deren Migration angehalten werden soll.

2.  Wählen Sie durch Klicken mit der rechten Maustaste **Stretch** aus, und wählen Sie dann **Anhalten** aus.

### Verwenden von Transact-SQL zum Anhalten der Datenmigration
Führen Sie den folgenden Befehl aus:

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;
GO;
```

## Fortsetzen der Datenmigration

### Verwenden von SQL Server Management Studio zum Fortsetzen der Datenmigration

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Stretch-fähige Tabelle aus, deren Migration fortgesetzt werden soll.

2.  Wählen Sie durch Klicken mit der rechten Maustaste **Stretch** aus, und wählen Sie dann **Fortsetzen** aus.

### Verwenden von Transact-SQL zum Fortsetzen der Datenmigration
Führen Sie den folgenden Befehl aus:

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
```

## Weitere Informationen
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0309_2016-->