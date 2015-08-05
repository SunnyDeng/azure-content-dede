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
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Verwenden von Azure Stream Analytics mit SQL Data Warehouse

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, hohe Verfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Die Grundlagen finden Sie unter [Einführung in Azure Stream Analytics][]. Im [Lernprogramm zu den ersten Schritten][] können Sie sich dann mit dem Erstellen einer End-to-End-Lösung mit Stream Analytics vertraut machen.

In diesem Artikel erfahren Sie, wie Sie die Azure SQL Data Warehouse-Datenbank als Ausgabesenke für Stream Analytics-Aufgaben verwenden können.

## Voraussetzungen

Führen Sie zunächst die folgenden Schritte im [Lernprogramm zu den ersten Schritten][] aus.

1. Erstellen einer Event Hub-Eingabe
2. Konfigurieren und starten der Ereignisgenerator-Anwendung
3. Bereitstellen eines Stream Analytics-Auftrags
4. Festlegen der Auftragseingabe und -abfrage

Erstellen Sie dann eine Azure SQL Data Warehouse-Datenbank.

## Festlegen der Auftragsausgabe: Azure SQL Data Warehouse-Datenbank

### Schritt 1
Klicken Sie in Ihrem Stream Analytics-Auftrag am oberen Rand der Seite auf **AUSGABE**, und klicken Sie dann auf **AUSGABE HINZUFÜGEN**.

### Schritt 2
Wählen Sie "SQL-Datenbank" aus, und klicken Sie auf "Weiter". ![][Add Output]

### Schritt 3
Geben Sie auf der nächsten Seite die folgenden Werte ein. - Ausgabealias: Geben Sie einen Anzeigenamen für die Auftragsausgabe ein. - Abonnement: - Wenn sich Ihre SQL Data Warehouse-Datenbank im gleichen Abonnement wie der Stream Analytics-Auftrag befindet, wählen Sie "SQL-Datenbank aus aktuellem Abonnement verwenden" aus. - Wenn sich die Datenbank in einem anderen Abonnement befindet, wählen Sie "SQL-Datenbank aus einem anderen Abonnement verwenden" aus. - Datenbank: Geben Sie den Namen einer Zieldatenbank an. - Servername: Geben Sie den Servernamen für die soeben festgelegte Datenbank an. Diese Eingaben können Sie im Azure-Portal vornehmen.

![][Server Name]

- Benutzername: Geben Sie den Benutzernamen eines Kontos mit Schreibberechtigungen für die Datenbank an.
- Kennwort: Geben Sie das Kennwort für das angegebene Benutzerkonto ein.
- Tabelle: Geben Sie den Namen der Zieltabelle in der Datenbank an.

![][Add Database]

### Schritt 4
Klicken Sie auf das Häkchen, um diese Auftragsausgabe hinzuzufügen und um zu überprüfen, ob Stream Analytics erfolgreich mit der Datenbank verbunden werden kann.

![][Test Connection]

Wenn die Verbindung mit der Datenbank hergestellt wird, wird unten im Portal eine entsprechende Meldung angezeigt. Sie können dann unten auf "Verbindung testen" klicken, um die Verbindung mit der Datenbank zu testen.




## Nächste Schritte
Einen Überblick über die Integration finden Sie unter [SQL Data Warehouse-Integration (Übersicht)][]. Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht für SQL Data Warehouse][].

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Einführung in Azure Stream Analytics]: ./stream-analytics-introduction/
[Lernprogramm zu den ersten Schritten]: ./articles/stream-analytics-get-started/
[Entwicklungsübersicht für SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse-Integration (Übersicht)]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO4-->