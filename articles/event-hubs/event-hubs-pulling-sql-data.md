<properties
   pageTitle="Übertragen von SQL-Daten in Azure Event Hubs mithilfe von Pull | Microsoft Azure"
   description="Übersicht über das Beispiel für den Event Hubs-Import aus SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/26/2016"
   ms.author="spyros;spyrossak" />

# Übertragen von Daten per Pull aus SQL in einen Azure Event Hub

Bei einer typischen Architektur für eine Anwendung zur Verarbeitung von Echtzeitdaten werden die Daten zuerst per Push an einen Azure Event Hub übertragen. Dabei kann es sich um ein IoT-Szenario, beispielsweise die Überwachung des Verkehrs auf verschiedenen Strecken der Autobahn, oder um ein Gaming-Szenario, beispielsweise die Überwachung der Aktionen einer Horde wilder Konkurrenten, oder um ein Unternehmensszenario handeln, z. B. die Überwachung der Gebäudebelegung. In diesen Fällen werden in der Regel Zeitreihendaten von externen Objekten erzeugt. Diese Daten müssen Sie sammeln, analysieren, speichern und entsprechend handeln, und möglicherweise haben Sie einen erheblichen Aufwand in die Infrastruktur für diese Prozesse investiert. Wie gehen Sie vor, wenn Sie Daten aus einem Element wie einer Datenbank statt aus einer Quelle von Streamingdaten importieren und in Verbindung mit Ihren anderen Streamingdaten verwenden möchten? Nehmen Sie an, Sie möchten Azure Stream Analytics, Remote Data Explorer (RDX) oder ein anderes Tool verwenden, um langsam veränderliche Daten aus Microsoft Dynamics AX oder einem benutzerdefinierten Facilities-Management-System zu analysieren und zu verarbeiten? Um dieses Problem zu lösen, haben wir ein kleines Open-Source-Cloudbeispiel geschrieben, das Sie ändern und bereitstellen können. Damit können Sie die Daten per Pullvorgang aus einer SQL-Tabelle abrufen und per Push an einen Azure Event Hub übertragen, damit sie in den nachfolgenden Analyseanwendungen als Eingabe verwendet werden können. Beachten Sie, dass dieses Szenario selten vorkommt und das Gegenteil von dem darstellt, was Sie normalerweise mit einem Event Hub durchführen. Aber wenn Sie sich in einer Situation befinden, in der diese Vorgehensweise erforderlich ist, finden Sie den entsprechenden Code im [Azure-Beispielkatalog](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).

Beachten Sie, dass dieser Code wirklich nur ein Beispiel ist. Es handelt sich **nicht** um eine Produktionsanwendung, und es wurden keine Bemühungen unternommen, sie für die Nutzung in einer solchen Umgebung geeignet zu machen. Dieser Code stellt lediglich ein Beispiel für Entwickler dar. Bevor Sie sich für eine End-to-End-Architektur entscheiden, sollten Sie Sicherheit, Leistung, Funktionalität und Kostenfaktoren in jeder Hinsicht überprüfen.

## Anwendungsstruktur

Die Anwendung ist in C# geschrieben. In der im Beispiel enthaltenen Datei „readme.md“ finden Sie alle Informationen, die Sie zum Ändern, Erstellen und Veröffentlichen der Anwendung benötigen. In den folgenden Abschnitten können Sie sich einen allgemeinen Überblick über die Funktionsweise der Anwendung verschaffen.

Zu Beginn gehen wir davon aus, dass Sie Zugriff auf eine Azure SQL-Tabelle haben. Sie müssen auch einen Azure Event Hub eingerichtet haben und die für den Zugriff erforderliche Verbindungszeichenfolge kennen.

Beim Starten der SqlToEventHub-Lösung wird eine Konfigurationsdatei (App.config) gelesen, um gemäß der Beschreibung in der Datei „readme.md“ eine Reihe von Informationen abzurufen. Diese sind mehr oder weniger selbsterklärend, z. B. der Name der Datentabelle usw., und besteht keine Notwendigkeit, die Erklärungen hier aufzuführen.

Nach dem Lesen der Konfigurationsdatei verfällt die Anwendung in eine Schleife und liest die SQL-Tabelle, überträgt Datensätze per Push an den Event Hub und wartet dann ein benutzerdefiniertes Ruheintervall ab, bevor der Vorgang von vorn beginnt. Hierbei sind einige Punkte zu beachten:

1. Die Anwendung basiert auf der Annahme, dass die SQL-Tabelle von einem externen Prozess aktualisiert wird und dass Sie nur die Updates an einen Event Hub senden möchten.
2. Die SQL-Tabelle muss über ein Feld mit einer eindeutigen und sich erhöhenden Zahl verfügen, z. B. eine Datensatznummer. Dies kann einfach ein Feld namens „Id“ oder ein anderer Wert sein, der sich beim Hinzufügen von Datensätzen zur Datenbank erhöht, z. B. „Creation\_time“ oder „Sequence\_number“. Die Anwendung erkennt und speichert den Wert des Felds bei jeder Iteration. In jedem nachfolgenden Durchlauf der Schleife fragt die Anwendung im Wesentlichen die Tabelle nach allen Datensätzen ab, in denen der Wert dieses Felds den Wert übersteigt, der beim letzten Durchlaufen der Schleife erkannt wurde. Diesen letzten Wert bezeichnen wir als „Offset“.
3. Die Anwendung erstellt beim Start eine Tabelle „TableOffsets“, um die Offsets zu speichern. Die Tabelle wird mit der Abfrage „CreateOffsetTableQuery“ erstellt, die in der Konfigurationsdatei definiert ist. 
4. Es gibt mehrere Abfragen für die Arbeit mit der Offsettabelle, die in der Konfigurationsdatei als „OffsetQuery“, „UpdateOffsetQuery“ und „InsertOffsetQuery“ definiert sind. Sie sollten diese nicht ändern.
5. Die in der Konfigurationsdatei definierte Abfrage „DataQuery“ ist die Abfrage, die ausgeführt wird, um Datensätze per Pull aus der SQL-Tabelle abzurufen. Zu Optimierungszwecken ist sie zurzeit auf die ersten 1.000 Datensätze in jedem Schleifendurchlauf eingeschränkt. Wenn der Datenbank seit der letzten Abfrage beispielsweise 25.000 Datensätze hinzugefügt wurden, kann die Ausführung der Abfrage eine Weile dauern. Durch die Begrenzung der Abfrage auf 1.000 Datensätze pro Durchlauf werden die Abfragen viel schneller ausgeführt. Durch die Auswahl der ersten 1.000 werden einfach aufeinander folgende Batches von 1.000 Datensätze per Push an den Event Hub übertragen.    

## Nächste Schritte

Zum Bereitstellen der Lösung klonen Sie die Anwendung SqlToEventHub, oder laden Sie sie herunter, bearbeiten Sie die Datei „App.config“, erstellen Sie die Anwendung, und veröffentlichen Sie sie. Nachdem Sie die Anwendung veröffentlicht haben, können Sie deren Ausführung im klassischen Azure-Portal unter Cloud Services verfolgen und die bei Ihrem Event Hub eingehenden Ereignisse überwachen. Beachten Sie, dass die Häufigkeit von zwei Dingen abhängt: der Aktualisierungshäufigkeit der SQL-Tabelle und dem Intervall, das Sie in der Konfigurationsdatei für die Anwendung angegeben haben.

<!---HONumber=AcomDC_0302_2016-->