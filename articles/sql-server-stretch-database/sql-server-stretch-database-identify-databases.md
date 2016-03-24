<properties
	pageTitle="Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank durch Ausführen von Stretch Database Advisor | Microsoft Azure"
	description="Erfahren Sie, wie Sie Datenbanken und Tabellen identifizieren können, die sich für Stretch-Datenbank eignen."
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

# Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank durch Ausführen von Stretch Database Advisor

Laden Sie den SQL Server 2016 Upgrade Advisor herunter, um Datenbanken und Tabellen, die sich für Stretch-Datenbank eignen, zu identifizieren, und führen Sie den Stretch Database Advisor aus. Stretch Database Advisor erkennt auch Hindernisse.

## Herunterladen und Installieren von Upgrade Advisor
Laden Sie Upgrade Advisor [hier](http://go.microsoft.com/fwlink/?LinkID=613421) herunter, und installieren Sie ihn. Dieses Tool ist nicht auf dem SQL Server-Installationsmedium enthalten.

## Ausführen von Stretch Database Advisor

1.  Führen Sie den Upgrade Advisor aus.

2.  Wählen Sie **Szenarien** und dann **STRETCH DATABASE ADVISOR AUSFÜHREN** aus.

3.  Klicken Sie auf dem Blatt **Stretch Database Advisor ausführen** auf **DATENBANK ZUM ANALYSIEREN AUSWÄHLEN**.

4.  Klicken Sie auf dem Blatt **Datenbanken auswählen** auf **SQL-INSTANZ**.

5.  Geben Sie auf dem Blatt **Mit SQL-Instanz verbinden** den Namen der SQL Server-Instanz ein. Klicken Sie auf **Verbinden**.

6.  Wählen Sie auf dem Blatt **Datenbanken auswählen** die zu analysierende Datenbanken aus. Klicken Sie dann auf **Auswählen**.

7.  Klicken Sie auf dem Blatt **Stretch Database Advisor ausführen** auf **Ausführen**. Die Analyse wird ausgeführt.

## Überprüfen der Ergebnisse

1.  Wenn die Analyse abgeschlossen ist, wählen Sie auf dem Blatt **Stretch Database Advisor** eine der analysierten Datenbanken aus, um das Blatt **Analyseergebnisse** anzuzeigen.

    Auf dem Blatt **Analyseergebnisse** werden die empfohlenen Tabellen in der ausgewählten Datenbank aufgelistet, die den standardmäßigen Empfehlungskriterien entsprechen. Passen Sie optional die Mindestgröße und die Zeilenzahl an, um die die Liste der empfohlenen Tabellen erweitert oder verringert werden soll.

2.  Wählen Sie in der Liste der empfohlenen Tabellen auf dem Blatt **Analyseergebnisse** eine der empfohlenen Tabellen aus, um das Blatt **Tabellenergebnisse** anzuzeigen.

    Das Blatt **Tabellenergebnisse** enthält Hindernisse für die ausgewählte Tabelle. Informationen zu den Hindernissen, die vom Stretch Database Advisor erkannt werden, finden Sie unter [Oberflächeneinschränkungen und Hindernisse für Stretch-Datenbank](sql-server-stretch-database-limitations.md).

3.  Wählen Sie in der Liste der Hindernisse auf dem Blatt **Tabellenergebnisse** eines der Probleme aus, um das Blatt **Regelergebnis** anzuzeigen.

    Auf dem Blatt **Regelergebnis** wird das ausgewählte Problem beschrieben, und es werden Schritte zur Behebung vorgeschlagen. Implementieren Sie die empfohlenen Maßnahmen, wenn Sie die ausgewählte Tabelle für Stretch-Datenbank konfigurieren möchten.

## Nächster Schritt
Aktivieren Sie Stretch-Datenbank.

-   Informationen zum Aktivieren von Stretch-Datenbank für eine **Datenbank** finden Sie unter [Aktivieren von Stretch-Datenbank für eine Datenbank](sql-server-stretch-database-enable-database.md).

-   Informationen zum Aktivieren von Stretch-Datenbank für eine andere **Tabelle**, bei bereits aktiviertem Stretch für die Datenbank, finden Sie unter [Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md).

## Weitere Informationen
[Oberflächeneinschränkungen und Hindernisse für Stretch-Datenbank](sql-server-stretch-database-limitations.md) [Aktivieren von Stretch-Datenbank für eine Datenbank](sql-server-stretch-database-enable-database.md) [Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0309_2016-->