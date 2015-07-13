<properties
   pageTitle="Transaktionen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Implementieren von Transaktionen in Azure SQL Data Warehouse zum Entwickeln von Lösungen"
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

# Transaktionen in SQL Data Warehouse

Wie zu erwarten unterstützt SQL Data Warehouse alle Transaktionseigenschaften. Um allerdings eine angemessene Leistung von SQL Data Warehouse sicherzustellen, wurden einige Features im Vergleich zu SQL Server eingeschränkt. In diesem Artikel werden die Unterschiede hervorgehoben und die anderen Features aufgelistet.

## Transaktionsisolationsstufen
SQL Data Warehouse implementiert ACID-Transaktionen. Die Isolation der Transaktionsunterstützung ist jedoch beschränkt auf `READ UNCOMMITTED` und kann nicht geändert werden. Sie können eine Reihe von Codemethoden implementieren, um fehlerhafte Datenlesevorgänge zu verhindern, wenn dies ein Problem für Sie darstellt. Die am häufigsten verwendeten Methoden nutzen sowohl CTAS als auch Wechsel von Partitionstabellen (oftmals gleitendes Fenstermuster genannt), um zu verhindern, dass Benutzer Daten abrufen, die noch vorbereitet werden. Sichten, die die Daten vorab filtern, sind auch ein beliebter Ansatz.

## Transaktionsstatus
SQL Data Warehouse verwendet die XACT_STATE()-Funktion, um eine fehlgeschlagene Transaktion mit dem Wert "-2" zu melden. Dies bedeutet, dass die Transaktion fehlgeschlagen und nur für den Rollback markiert ist.

> [AZURE.NOTE]Die Verwendung von "-2" in der XACT_STATE-Funktion zum Kennzeichnen einer fehlgeschlagenen Transaktion stellt für SQL Server unterschiedliche Verhalten dar. SQL Server verwendet den Wert "-1" für eine Transaktion, für die kein Commit durchgeführt werden kann. SQL Server kann einige Fehler innerhalb einer Transaktion tolerieren, ohne als nicht commitfähig gekennzeichnet zu werden. SELECT 1/0 würde beispielsweise einen Fehler verursachen, jedoch keinen Transaktionszustand erzwingen, der keinen Commit zulässt. SQL Server lässt auch Lesevorgänge in der Transaktion zu, für die kein Commit möglich ist. Dies ist allerdings in SQLDW nicht der Fall. Bei einem Fehler innerhalb einer SQLDW-Transaktion wird automatisch der Zustand "-2" festgelegt, einschließlich SELECT 1/0-Fehlern. Es ist daher wichtig, zu überprüfen, ob in Ihrem Anwendungscode XACT_STATE() verwendet wird.

In SQL Server kann ein Codefragment angezeigt werden, das wie folgt aussieht:

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

Beachten Sie, dass die `SELECT`-Anweisung vor der `ROLLBACK`-Anweisung auftritt. Beachten Sie auch, dass die Einstellung der `@xact`-Variable DECLARE verwendet und nicht `SELECT`.

In SQL Data Warehouse müsste der Code wie folgt aussehen:

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

Beachten Sie, dass das Rollback der Transaktion vor dem Lesen der Fehlerinformationen im `CATCH`-Block erfolgen muss.

## Error_Line()-Funktion
Es ist auch erwähnenswert, dass SQL Data Warehouse die ERROR_LINE()-Funktion nicht implementiert oder unterstützt. Wenn diese in Ihrem Code enthalten ist, müssen Sie sie entfernen, um die Kompatibilität mit SQL Data Warehouse zu gewährleisten. Verwenden Sie stattdessen Abfragebezeichnungen in Ihrem Code, um entsprechende Funktionalität zu implementieren. Weitere Informationen zu dieser Funktion finden Sie im Artikel [Abfragebezeichnungen].

## Verwenden von THROW und RAISERROR
THROW ist die modernere Implementierung zum Auslösen von Ausnahmen in SQL Data Warehouse, RAISERROR wird jedoch ebenfalls unterstützt. Es gibt aber einige erwähnenswerte Unterschiede.

- Benutzerdefinierte Fehlermeldungsnummern können für THROW nicht im Bereich 100.000 bis 150.000 liegen.
- RAISERROR-Fehlermeldungen sind bei 50.000 festgelegt.
- Die Verwendung von "sys.messages" wird nicht unterstützt.

## Einschränkungen
SQL Data Warehouse verfügt über einige weitere Einschränkungen in Bezug auf Transaktionen.

Dies sind:

- Keine verteilten Transaktionen
- Keine geschachtelten Transaktionen zulässig
- Keine Speicherpunkte zulässig

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->