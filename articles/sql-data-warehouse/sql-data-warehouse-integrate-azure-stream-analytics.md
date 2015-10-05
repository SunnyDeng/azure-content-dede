<properties
   pageTitle="Verwenden von Azure Stream Analytics mit SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Verwendung von Azure Stream Analytics mit Azure SQL Data Warehouse für die Entwicklung von Lösungen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="sahajs;twounder"/>

# Verwenden von Azure Stream Analytics mit SQL Data Warehouse

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, hohe Verfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Die Grundlagen finden Sie unter [Einführung in Azure Stream Analytics][]. In [Erste Schritte mit Azure Stream Analytics][] können Sie sich dann mit dem Erstellen einer End-to-End-Lösung mit Stream Analytics vertraut machen.

In diesem Artikel erfahren Sie, wie Sie die Azure SQL Data Warehouse-Datenbank als Ausgabesenke für Stream Analytics-Aufgaben verwenden können.

## Voraussetzungen

Führen Sie zuerst die folgenden Schritte im Lernprogramm [Erste Schritte mit Azure Stream Analytics][] aus.

1. Erstellen einer Event Hub-Eingabe
2. Konfigurieren und starten der Ereignisgenerator-Anwendung
3. Bereitstellen eines Stream Analytics-Auftrags
4. Festlegen der Auftragseingabe und -abfrage

Erstellen Sie dann eine Azure SQL Data Warehouse-Datenbank.

## Festlegen der Auftragsausgabe: Azure SQL Data Warehouse-Datenbank

### Schritt 1

Klicken Sie in Ihrem Stream Analytics-Auftrag am oberen Rand der Seite auf **AUSGABE**, und klicken Sie dann auf **AUSGABE HINZUFÜGEN**.

### Schritt 2

Wählen Sie „SQL-Datenbank“ aus, und klicken Sie auf „Weiter“.

![][add-output]

### Schritt 3
Geben Sie auf der nächsten Seite die folgenden Werte ein:

- *Ausgabealias*: Geben Sie einen Anzeigenamen für diese Auftragsausgabe ein.
- *Abonnement*:
	- Befindet sich die SQL Data Warehouse-Datenbank im selben Abonnement wie der Stream Analytics-Auftrag, wählen Sie die Option "SQL-Datenbank aus aktuellem Abonnement verwenden" aus.
	- Wenn die Datenbank sich in einem anderen Abonnement befindet, wählen Sie "SQL-Datenbank aus einem anderen Abonnement verwenden" aus.
- *Datenbank*: Geben Sie den Namen einer Zieldatenbank an.
- *Servername*: Geben Sie den Servernamen für die Datenbank an, die Sie soeben angegeben haben. Diese Eingaben können Sie im Azure-Portal vornehmen.

![][server-name]

- *Benutzername*: Geben Sie den Benutzernamen eines Kontos mit Schreibberechtigungen für die Datenbank an.
- *Kennwort*: Geben Sie das Kennwort für das angegebene Benutzerkonto an.
- *Tabelle*: Geben Sie den Namen der Zieltabelle in der Datenbank an.

![][add-database]

### Schritt 4

Klicken Sie auf das Häkchen, um diese Auftragsausgabe hinzuzufügen und um zu überprüfen, ob Stream Analytics erfolgreich mit der Datenbank verbunden werden kann.

![][test-connection]

Wenn die Verbindung mit der Datenbank hergestellt wird, wird unten im Portal eine entsprechende Meldung angezeigt. Sie können dann unten auf "Verbindung testen" klicken, um die Verbindung mit der Datenbank zu testen.

## Nächste Schritte

Einen Überblick über die Integration finden Sie unter [SQL Data Warehouse-Integration (Übersicht)][].

Weitere Hinweise zur Entwicklung finden Sie in der [SQL Data Warehouse-Entwicklungsübersicht][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Einführung in Azure Stream Analytics]: stream-analytics-introductiond.md
[Erste Schritte mit Azure Stream Analytics]: stream-analytics-get-started.md
[SQL Data Warehouse-Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md
[SQL Data Warehouse-Integration (Übersicht)]: sql-data-warehouse-overview-integration.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=Sept15_HO4-->