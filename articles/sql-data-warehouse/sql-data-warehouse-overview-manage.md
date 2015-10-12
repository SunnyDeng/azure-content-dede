<properties
   pageTitle="Verwaltungstools für SQL Data Warehouse | Microsoft Azure"
   description="Einführung in Verwaltungstools für SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="mausher;nicw;barbkess;JRJ@BigBangData.co.uk;"/>

# Verwaltungstools für SQL Data Warehouse
In diesem Thema werden Tools und Optionen für die Verwaltung von SQL Data Warehouse untersucht und verglichen, damit Sie das richtige Tool für Ihre Anforderungen auswählen können. Die Auswahl des richtigen Tools hängt von der Anzahl der Datenbanken, den Aufgaben und der Ausführungshäufigkeit einer Aufgabe ab.

## Azure-Portal
Das [Azure-Portal][] ist ein webbasiertes Verwaltungsportal, in dem Sie Datenbanken erstellen, aktualisieren und löschen sowie Datenbankressourcen überwachen können. Dieses Tool eignet sich hervorragend, wenn Sie gerade erst mit der Verwendung von Azure begonnen haben, nur eine kleine Anzahl von Data Warehouse-Datenbanken verwalten oder bestimmte Aktionen schnell ausführen müssen.

Das Portal enthält Metriken über die aktuellen und vergangenen DWU-Leistungseinstellungen, die verwendete Speichermenge, erfolgreiche und fehlgeschlagene SQL-Verbindungen und eine Reihe von Visualisierungen und Daten, mit denen Sie die Abfragen in Ihrer Instanz und die Details dieser Abfragen besser verstehen können.

## SQL Server Data Tools in Visual Studio	
[SQL Server Data Tools][] (SSDT) in Visual Studio sind Clienttools, die auf Ihrem Computer ausgeführt werden und es ermöglichen, Ihre Datenbank mit der Cloud zu verbinden, darin zu verwalten und zu entwickeln. Wenn Sie ein Anwendungsentwickler sind, der mit Visual Studio oder anderen integrierten Entwicklungsumgebungen (IDEs) vertraut ist, sollten Sie SSDT in Visual Studio ausprobieren.

SSDT enthält den SQL Server-Explorer, mit dem Sie SQL Data Warehouse-Datenbanken visuell darstellen, Verbindungen zu diesen herstellen und Skripts für sie ausführen können. Um eine schnelle Verbindung mit SQL Data Warehouse herzustellen, klicken Sie einfach in der Befehlsleiste auf die Schaltfläche **In Visual Studio öffnen**, während Sie die Datenbankdetails im Azure-Portal anzeigen.

Sie können die neueste Version von [SQL Server Data Tools][] (SSDT) mit Unterstützung für SQL Data Warehouse herunterladen.

## Befehlszeilentools
Eine Möglichkeit besteht darin, PowerShell- oder sqlcmd-Befehlszeilentools dazu zu verwenden, SQL Data Warehouse zu verwalten und Azure-Ressourcenbereitstellungen zu automatisieren. Diese Tools werden für die Verwaltung einer großen Anzahl von logischen Servern und die Bereitstellung von Ressourcenänderungen in einer Produktionsumgebung empfohlen, da die erforderlichen Aufgaben durch Skripts ausgeführt und automatisiert werden können.

## Nächste Schritte
Um mit der Verwendung dieser Tools zu beginnen, lesen Sie das Thema [Verbindungen][].

<!--Image references-->

<!--Article references-->
[Verbindungen]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/de-DE/library/mt204009.aspx

<!--Other web references-->
[Azure-Portal]: http://portal.azure.com/

<!---HONumber=Oct15_HO1-->