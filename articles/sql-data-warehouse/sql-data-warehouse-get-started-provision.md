<properties
	pageTitle="Erstellen einer SQL Data Warehouse-Datenbank im Azure-Vorschauportal | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein Azure SQL Data Warehouse im Azure-Vorschauportal erstellen."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2015"
   ms.author="lodipalm;barbkess"/>

# Erstellen eines SQL Data Warehouse im Azure-Vorschauportal#

In diesem Tutorial erfahren Sie, wie einfach es ist, in wenigen Minuten ein Azure SQL Data Warehouse im Azure-Vorschauportal zu erstellen.

In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:

- Erstellen einer neuen SQL Data Warehouse-Datenbank
- Erstellen eines Servers für die Datenbank
- Laden von AdventureWorksDW in die neue Datenbank

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Anmelden und Suchen von SQL Data Warehouse

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie im Menü "Hub" auf **Neu** > **Daten und Speicher** > **SQL Data Warehouse**.

	![Erstellen eines Data Warehouse](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

## Konfigurieren von Leistungs- und anderen grundlegenden Einstellungen

Füllen Sie im Bereich **SQL Data Warehouse** die folgenden Felder aus. **Server** und **Quelle** werden in den nächsten Abschnitten konfiguriert.

1. **Datenbankname**: Geben Sie einen Namen für die SQL Data Warehouse-Datenbank ein.

2. **Leistung**: Sie können während der Bereitstellung die Leistung anpassen, mit der die Instanz gestartet wird. Es wird empfohlen, mit 400 DWU zu beginnen, da hierdurch mehr MPP-Vorteile von SQL Data Warehouse zur Geltung kommen.
     
    ![Name und DWU](./media/sql-data-warehouse-get-started-provision/name-and-dwu.png)

    > [AZURE.NOTE]Die Leistung wird in Data Warehouse-Einheiten (Data Warehouse Units, DWUs) gemessen. Wenn Sie die Anzahl der DWUs erhöhen, erhöht SQL Data Warehouse die Computerressourcen, die für die Data Warehouse-Datenbankvorgänge verfügbar sind.

	> [AZURE.NOTE]Sie können die Leistungsstufe schnell und einfach ändern, nachdem die Datenbank erstellt wurde. Wenn Sie z. B. die Datenbank nicht verwenden, ziehen Sie den Schieberegler nach links, um Kosten zu senken. Oder erhöhen Sie die Leistung, wenn mehr Ressourcen benötigt werden. Dies ist die skalierbare Leistung von SQL Data Warehouse.
	

2. **Ressourcengruppe**. Behalten Sie die Standardwerte bei. Ressourcengruppen sind Container, die Ihnen helfen, eine Sammlung von Azure-Ressourcen zu verwalten. Erfahren Sie mehr über [Ressourcengruppen](../azure-portal/resource-group-portal.md).
3. **Abonnement**. Wählen Sie das Abonnement für die Abrechnung dieser Datenbank aus.


## Konfigurieren eines logischen Servers

3. Klicken Sie auf **Server** > **Neuen Server erstellen**. Dadurch wird ein logischer Server erstellt, dem die Datenbank zugeordnet wird. Wenn Sie einen bereits vorhandenen V12-Server verwenden möchten, wählen Sie den vorhandenen Server aus, und fahren Sie mit dem nächsten Abschnitt fort.

    ![Erstellen eines neuen Servers](./media/sql-data-warehouse-get-started-provision/create-new-server.png)

    >[AZURE.NOTE]In SQL Data Warehouse und einer SQL-Datenbank bietet ein Server eine einheitliche Methode zum Konfigurieren von cloudbasierten Datenbanken. Zwar ist in Azure ein Server an ein einzelnes Rechenzentrum gebunden, er ist jedoch keine physische Hardware wie ein Server für eine lokale Instanz von SQL Server. Stattdessen ist er Teil der Dienstsoftware. Aus diesem Grund wird er als logischer Server bezeichnet. Beachten Sie, dass im Unterschied zur realen Welt durch die Ausführung von Datenbanken und Data Warehouses auf demselben Server keine gegenseitige Beeinträchtigung der Leistung erfolgt.

1. Geben Sie im Fenster **Neuer Server** die erforderlichen Informationen ein.

    Stellen Sie sicher, dass Sie den Servernamen, den Administratornamen und das Kennwort notieren und aufbewahren. Sie benötigen diese Informationen, um sich beim Server anzumelden. – **Servername**. Geben Sie einen Namen für den logischen Server ein. – **Name des Serveradmins**. Geben Sie einen Benutzernamen für das Serveradministratorkonto ein. – **Kennwort**. Geben Sie das Administratorkennwort für den Server ein. - **Standort**. Wählen Sie einen geografischen Standort aus, der sich in Ihrer Nähe oder in der Nähe anderer Azure-Ressourcen befindet. Dies reduziert die Netzwerklatenz, da sich alle Datenbanken und Ressourcen, die zum logischen Server gehören, physisch in derselben Region befinden.

    ![Konfigurieren des neuen Servers](./media/sql-data-warehouse-get-started-provision/configure-new-server.png)

1. Klicken Sie auf **OK** um die Serverkonfigurationseinstellungen zu speichern.

## Laden der Beispieldatenbank

1. Wählen Sie **Quelle** > **Beispiel** aus, um die neue Datenbank mit der AdventureWorksDW-Beispieldatenbank zu initialisieren. 

    ![Erstellen eines Data Warehouse](./media/sql-data-warehouse-get-started-provision/create-data-warehouse.png)

## Abschließen der Datenbankerstellung

1. Klicken Sie auf **Erstellen**, um die SQL Data Warehouse-Datenbank zu erstellen. 

1. Jetzt müssen Sie lediglich einige Minuten warten. Abschließend wird die Beispieldatenbank auf der Startseite angezeigt.

    ![Portal-Ansicht von SQL Data Warehouse](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

## Nächste Schritte

Nachdem Sie jetzt eine Beispieldatenbank für SQL Data Warehouse erstellt haben, erfahren Sie in der nächsten exemplarischen Vorgehensweise, wie SQL Data Warehouse verwendet wird.

- [Herstellen der Verbindung und Abfragen](./sql-data-warehouse-get-started-connect-query.md)

	> [AZURE.NOTE]Wir möchten diesen Artikel verbessern. Wenn Sie auf die Frage „War dieser Artikel hilfreich?“ mit „Nein“ antworten, bitten wir Sie, einen kurzen Vorschlag dazu einzufügen, was fehlt oder wie der Artikel zu verbessern ist. Vielen Dank im Voraus!

<!---HONumber=Oct15_HO1-->