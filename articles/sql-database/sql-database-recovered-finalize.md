<properties
   pageTitle="Abschließen der wiederhergestellten Azure SQL-Datenbank"
   description="Zeitpunktwiederherstellung, Microsoft Azure SQL-Datenbank, Datenbank wiederherstellen, Azure-Verwaltungsportal, Azure-Portal"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="04/13/2015"
   ms.author="elfish"/>

# Abschließen der wiederhergestellten Azure SQL-Datenbank

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md)
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)

## Übersicht

Dieser Artikel enthält eine Aufgabenprüfliste, die Sie durchgehen müssen, bevor Sie eine neu wiederhergestellte Azure SQL-Datenbank wieder produktiv einsetzen. Diese Prüfliste gilt für Datenbanken, die aus einem Failover bei der Georeplikation, aus einer gelöschten Datenbank, mit der Zeitpunktwiederherstellung oder mit Geo-Restore wiederhergestellt wurden.

## Aktualisieren von Verbindungszeichenfolgen

Überprüfen Sie, ob die Verbindungszeichenfolgen Ihrer Anwendung auf die neu wiederhergestellte Datenbank verweisen. Aktualisieren Sie die Verbindungszeichenfolgen, wenn eine der folgenden Situationen zutrifft:

  + Die wiederhergestellte Datenbank verwendet einen anderen Namen als den Namen der Quelldatenbank
  + Die wiederhergestellte Datenbank befindet sich auf einem anderen Server als dem Quellserver

Weitere Informationen zum Ändern von Verbindungszeichenfolgen finden Sie unter [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx) und [Verbindungen mit der Azure SQL-Datenbank: Zentrale Empfehlungen](sql-database-connect-central-recommendations.md).
 
## Ändern von Firewallregeln
Überprüfen Sie die Firewallregeln auf Server- und Datenbankebene, und stellen Sie sicher, dass Verbindungen von Ihrem Clientcomputer oder Azure zum Server und der neu wiederhergestellten Datenbank aktiviert sind. Weitere Informationen finden Sie unter [Azure SQL-Datenbank-Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) und [Vorgehensweise: Konfigurieren der Firewall-Einstellungen (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Überprüfen von Server-Anmeldungen und Datenbankbenutzern

Überprüfen Sie, ob alle von der Anwendung verwendeten Anmeldungen auf dem Server vorhanden sind, der die wiederhergestellte Datenbank hostet. Erstellen Sie die fehlenden Anmeldungen erneut, und gewähren Sie ihnen entsprechende Berechtigungen auf der wiederhergestellten Datenbank. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336235.aspx).

Überprüfen Sie, ob jedem Datenbankbenutzer in der wiederhergestellten Datenbank eine gültige Server-Anmeldung zugeordnet ist. Verwenden Sie die ALTER USER-Anweisung, um einem Benutzer eine gültige Server-Anmeldung zuzuordnen. Weitere Informationen finden Sie unter [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


## Einrichten von Telemetrie-Warnungen

Überprüfen Sie, ob die vorhandenen Warnungsregel-Einstellungen der wiederhergestellten Datenbank zugeordnet sind. Aktualisieren Sie die Einstellung, wenn eine der folgenden Situationen zutrifft:

  + Die wiederhergestellte Datenbank verwendet einen anderen Namen als den Namen der Quelldatenbank
  + Die wiederhergestellte Datenbank befindet sich auf einem anderen Server als dem Quellserver

Weitere Informationen zu Datenbank-Warnungsregeln finden Sie unter [Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure](https://msdn.microsoft.com/library/azure/dn306638.aspx).


## Aktivieren der Überwachung

Wenn für den Zugriff auf die Datenbank Überwachung erforderlich ist, müssen Sie nach der Wiederherstellung der Datenbank die Überwachung aktivieren. Es ist ein guter Indikator für die Notwendigkeit von Überwachung, wenn Clientanwendungen sichere Verbindungszeichenfolgen in einem Muster von *.database.secure.windows.net verwenden. Weitere Informationen finden Sie unter [Erste Schritte mit der SQL-Datenbanküberwachung](sql-database-auditing-get-started.md). 

<!---HONumber=58--> 