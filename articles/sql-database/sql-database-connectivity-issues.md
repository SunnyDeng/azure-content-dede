<properties 
	pageTitle="Verbindungsprobleme mit Azure SQL-Datenbank" 
	description="Identifizieren und Bestimmen von Verbindungsfehlern mit SQL-Datenbank." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="07/24/2015" 
	ms.author="sstein"/>


# Verbindungsprobleme mit SQL-Datenbanken

Dieser Artikel enthält eine Übersicht über die Verwendung von verschiedenen Möglichkeiten zum Behandeln von Problemen beim Verbinden mit Azure SQL-Datenbank.


## Ist das Verbindungsproblem anwendungsspezifisch, oder kann generell keine Verbindungen mit der Datenbank hergestellt werden?

Verwenden Sie SQL Server Management Studio oder "SQLCMD.EXE" zum Überprüfen, ob eine Verbindung mit der Datenbank hergestellt werden kann.

- Anweisungen zum Herstellen einer Verbindung mit der SQL-Datenbank mit SQL Server Management Studio (SSMS) finden Sie unter [Herstellen von Verbindungen mit einer Azure SQL-Datenbank mit SSMS](sql-database-connect-to-database.md).
- Anweisungen zum Herstellen einer Verbindung mit der SQL-Datenbank mit SQLCMD finden Sie unter [Vorgehensweise: Herstellen einer Verbindung mit Azure SQL-Datenbank über sqlcmd](https://msdn.microsoft.com/library/azure/ee336280.aspx).



## Versucht die Anwendung eine Verbindung mit einer in Azure ausgeführten SQL-Datenbank herzustellen (ist z. B. die Anwendung, die keine Verbindung mit der Datenbank herstellen kann, ein Clouddienst oder eine Web-App)?

Stellen Sie sicher, dass die Firewallregel zum Zulassen sämtlicher Azure-Dienste für den Server/die Datenbank aktiviert ist.

- Weitere Informationen zu Firewallregeln und das Aktivieren von Verbindungen aus Azure finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).



## Versucht die Anwendung aus einem privaten Netzwerk heraus eine Verbindung mit der SQL-Datenbank herzustellen?

Stellen Sie sicher, dass für den Server/die Datenbank die Firewallregeln aktiviert sind, die den Zugriff aus bestimmten Netzwerken zulassen.

- Allgemeine Informationen zu Firewallregeln finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).
- Anweisungen zum Einrichten von Firewallregeln finden Sie unter [Vorgehensweise: Konfigurieren von Firewalleinstellungen (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/azure/jj553530.aspx).


## Wenn die Firewallregeln ordnungsgemäß konfiguriert sind, gehen Sie anhand der geführten exemplarischen Vorgehensweise unter [Troubleshoot Microsoft Azure SQL Database connectivity](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1) (Behandeln von Verbindungsproblemen in Microsoft Azure SQL-Datenbank, in englischer Sprache) vor.

Der oben genannte Supportartikel bietet Hilfe bei folgenden Verbindungsproblemen mit SQL-Datenbank:

- Aufgrund der Firewalleinstellungen kann keine Verbindung zum Server hergestellt werden. 
- Der Server wurde nicht gefunden, oder es konnte nicht darauf zugegriffen werden. 
- Anmeldung auf dem Server nicht möglich 
- Fehler aufgrund von Verbindungstimeouts 
- Vorübergehende Fehler 
- Verbindungsbeendigung aufgrund des Erreichens einiger im System definierter Limits 
- Verbindungsfehler von SQL Server Management Studio (SSMS) 


## Zusätzliche Informationen

- Weitere Informationen zum Herstellen einer Verbindung mit SQL-Datenbank finden Sie unter [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx).   

- Details zu bestimmten Verbindungsfehlern finden Sie unter [Fehlermeldungen für Clientprogramme der SQL-Datenbank](sql-database-develop-error-messages.md#bkmk_connection_errors) im Abschnitt **Vorübergehende Fehler, Fehler aufgrund eines Verbindungsabbruchs**.

- Zugriff auf Ereignisdaten von Verbindungen erhalten Sie durch Abfragen von Konnektivitätsereignissen mit der Sicht [**sys.event\_log (Azure SQL-Datenbank)**](https://msdn.microsoft.com/library/dn270018.aspx).

- Zugriff auf Metriken von Datenbank-Konnektivitätsereignissen erhalten Sie durch Abfragen mit der Sicht [**sys.database\_connection\_stats (Azure SQL-Datenbank)**](https://msdn.microsoft.com/library/dn269986.aspx).

 

<!---HONumber=Oct15_HO3-->