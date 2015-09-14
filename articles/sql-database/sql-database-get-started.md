<properties
	pageTitle="Erste Schritte mit SQL-Datenbank | Microsoft Azure"
	description="Erstellen Sie Ihre erste Clouddatenbank in wenigen Minuten mit Azure SQL-Datenbank, dem relationalen Datenbankverwaltungsdienst (RDBMS) von Microsoft in der Cloud, mit dem Azure-Portal und der AdventureWorks-Beispieldatenbank."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/01/2015"
	ms.author="genemi"/>

# Erstellen einer ersten Azure SQL-Datenbank

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Dieser Artikel zeigt, wie Sie im Azure-Portal in wenigen Minuten eine Beispiel-SQL-Datenbank erstellen. Sie lernen Folgendes:

- Erstellen eines Servers zum Hosten der erstellten Datenbank und Einrichten einer Firewallregel für diesen
- Erstellen einer Datenbank aus einem AdventureWorks-Beispiel mit Daten zum Testen

Sie benötigen vor Beginn ein Azure-Konto und ein Azure-Abonnement. Falls Sie diese benötigen, können Sie sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.

## Schritt 1: Anmelden und Starten der SQL-Datenbank-Einrichtung
1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com/) an.
2. Klicken Sie auf **Neu** > **Daten + Speicher** > **SQL-Datenbank**.

	![Erstellen einer neuen SQL-Datenbank](./media/sql-database-get-started/create-db.png)
	
	Im angezeigten Blatt mit den Einstellungen für **SQL-Datenbank** legen Sie die Informationen für Server und Datenbank fest.

	![Datenbank- und Servereinstellungen für SQL-Datenbank](./media/sql-database-get-started/get-started-dbandserversettings.png)

## Schritt 2: Auswählen der Servereinstellungen
Eine SQL-­Datenbank befindet sich in Azure auf einem Datenbankserver. Ein Server kann mehrere Datenbanken hosten. Beim Einrichten einer Datenbank können Sie auch einen Server erstellen und einrichten, der als Host fungieren soll, oder Sie können eine zuvor erstellte Datenbank verwenden. Wir werden eine neue einrichten.

1. Geben Sie einen **Namen** für die Datenbank ein (wir verwenden **AdventureWorks**). Die anderen Datenbankeinstellungen werden später noch behandelt.
2. Klicken Sie unter **Server** auf **Erforderliche Einstellungen konfigurieren** und dann auf **Neuen Server erstellen**.

	![Benennen der Datenbank](./media/sql-database-get-started/name-and-newserver.png)

3. Geben Sie auf dem Blatt **Neuer Server** einen **Servernamen** ein, der in Azure eindeutig sein muss und leicht zu merken sein sollte. Sie benötigen diesen Namen später, wenn Sie eine Verbindung herstellen und mit der Datenbank arbeiten.
4. Geben Sie einen **Server admin login** ein, der leicht zu merken ist (wir verwenden **AdventureAdmin**). Geben Sie ein sicheres **Kennwort** ein, und wiederholen Sie die Eingabe in **Kennwort bestätigen**.

	![Servereinstellungen für die neue Datenbank](./media/sql-database-get-started/get-started-serversettings.png)

	 Belassen Sie für **Create V12 Server (latest update)** als Wert **Ja**, um die neuesten Features zu verwenden. Der **Standort** bestimmt die Region des Rechenzentrums, in dem der Server erstellt wird.

	>[AZURE.TIP]Erstellen Sie Datenbankserver an einem Ort in der Nähe der Anwendungen, die die Datenbank verwenden werden. Wenn Sie den Standort ändern möchten, klicken Sie einfach auf **Standort**, wählen Sie einen anderen Standort aus, und klicken Sie dann auf **OK**.

5. Klicken Sie auf **OK**, um zurück zum Blatt **SQL-Datenbank** zu wechseln.

Die Datenbank und der Server wurden bisher noch nicht erstellt. Dies erfolgt erst nach dem nächsten Schritt, in dem Sie auswählen, die Datenbank aus dem AdventureWorks-Beispiel zu erstellen, und die Einstellungen bestätigen.

## Schritt 4: Einrichten und Erstellen der Datenbank
1. Klicken Sie auf dem Blatt **SQL-Datenbank** auf **Quelle auswählen** und dann auf **Beispiel**. 

	![Erstellen einer Datenbank aus einem Beispiel](./media/sql-database-get-started/new-sample-db.png)

2. Wechseln Sie zurück zum Blatt **SQL-Datenbank**. Dort wird unter **Select sample** jetzt **AdventureWorks LT [V12]** angezeigt. Klicken Sie auf **Erstellen**, um das Erstellen von Server und Datenbank zu starten.

	![Erstellen einer Beispieldatenbank](./media/sql-database-get-started/adworks_create.png)

	>[AZURE.NOTE]Für diese schnelle Anleitung haben wir die Einstellungen für **Tarif**, **Sortierung** und **Ressourcengruppe** nicht geändert. Sie können den Tarif einer Datenbank jederzeit ändern diese schnell und ohne Ausfallzeiten zentral hoch oder herunter skalieren. Weitere Informationen finden Sie unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/) und [SQL-Datenbank-Tarife](sql-database-service-tiers.md). Die Sortierung einer Datenbank kann nach der Einrichtung nicht mehr geändert werden. Weitere Informationen zur Sortierung finden Sie unter [Sortierung und Unicode-Unterstützung](https://msdn.microsoft.com/library/ms143726.aspx). Weitere Informationen zu Azure-Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

Wechseln Sie zurück zum Azure-Startmenü, in dem eine Kachel den Fortschritt anzeigt, bis die Datenbank erstellt wurde und online ist. Sie können auch auf **Alle durchsuchen** und dann auf **SQL-Datenbanken** klicken, um zu überprüfen, ob die Datenbank online ist.
	
Glückwunsch! Sie verfügen nun über eine Datenbank, die in der Cloud ausgeführt wird. Sie sind fast fertig. Es ist noch ein wichtiger Schritt zu erledigen. Sie müssen auf dem Datenbankserver eine Regel erstellen, damit Sie Verbindungen mit der Datenbank herstellen können.

## Schritt 5: Konfigurieren der Firewall

Sie müssen eine Firewallregel auf dem Server einrichten, die Verbindungen von der IP-Adresse des Clientcomputers ermöglicht, sodass Sie mit der Datenbank arbeiten können. Damit stellen Sie nicht nur sicher, dass Sie eine Verbindung herstellen können, sondern Sie können so auch weitere Details zu den SQL-Servers in Azure erhalten.

1. Klicken Sie auf **Alle durchsuchen**, scrollen Sie nach unten, und klicken Sie auf **SQL-Server**. Klicken Sie dann in der Liste der **SQL-Server** auf den Namen des zuvor erstellten Servers.

	![Auswählen des Datenbankservers](./media/sql-database-get-started/browse_dbservers.png)

	
3. Klicken Sie auf dem auf der rechten Seite angezeigten Blatt mit den Datenbankeigenschaften auf **Einstellungen** und dann in der Liste auf **Firewall**.

	![Öffnen der Firewalleinstellungen](./media/sql-database-get-started/db_settings.png)


	In den **Firewalleinstellungen** wird die aktuelle **Client-IP-Adresse** angezeigt.

	![Aktuelle IP-Adresse](./media/sql-database-get-started/firewall_config_client_ip.png)

4. Klicken Sie auf **Add Client IP**, damit Azure eine Regel für diese IP-Adresse erstellt, und klicken Sie dann auf **Speichern**.

	![Hinzufügen der IP-Adresse](./media/sql-database-get-started/firewall_config_new_rule.png)

	>[AZURE.IMPORTANT]Die IP-Adresse kann sich von Zeit zu Zeit ändern, und Sie können dann möglicherweise nicht auf den Server zugreifen, bis Sie eine neue Firewallregel erstellt haben. Überprüfen Sie die IP-Adresse mit [Bing](http://www.bing.com/search?q=my%20ip%20address), und fügen Sie dann eine einzelne IP-Adresse oder einen Bereich von IP-Adressen hinzu. Weitere Einzelheiten finden Sie unter [Konfigurieren der Firewalleinstellungen](sql-database-configure-firewall-settings.md).

## Nächste Schritte
Nachdem Sie über eine Datenbank mit Beispieldaten verfügen, können Sie diese mit Ihren bevorzugten Tools untersuchen.

- Wenn Sie mit Transact-SQL und SQL Server Management Studio vertraut sind, informieren Sie sich über das [Herstellen von Verbindungen mit einer SQL-Datenbank und das Abfragen dieser mit SSMS](sql-database-connect-query-ssms.md).

- Wenn Sie mit Excel vertraut sind, informieren Sie sich über das [Herstellen von Verbindungen mit SQL-Datenbank mit Excel](sql-database-connect-excel.md).

- Wenn Sie mit der Codierung beginnen möchten, finden Sie weitere Informationen unter [Herstellen von Verbindungen mit einer SQL-Datenbank und das Abfragen dieser mit C#](sql-database-connect-query.md) und [Verwenden von SQL-Datenbank mit .NET (C#)](sql-database-develop-dotnet-simple.md). Unter [Schnellstart-Codebeispiele für SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md) finden Sie auch Beispiele und Anleitungen zu Node.js, Python, Ruby, Java, PHP und C++ neben C#.

- Wenn Sie lokale SQL Server-Datenbanken nach Azure verschieben möchten, finden Sie unter [Migrieren von Datenbanken zu Azure SQL-Datenbank](sql-database-cloud-migrate.md) weitere Informationen.

<!---HONumber=September15_HO1-->