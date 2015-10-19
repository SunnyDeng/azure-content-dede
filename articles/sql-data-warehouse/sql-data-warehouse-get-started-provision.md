<properties
	pageTitle="Erstellen einer SQL Data Warehouse-Datenbank im Azure-Vorschauportal | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein Azure SQL Data Warehouse im Azure-Vorschauportal erstellen."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="barbkess"
	manager="jhubbard"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/01/2015"
   ms.author="lodipalm;barbkess"/>

# Erstellen eines SQL Data Warehouse im Azure-Vorschauportal#

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-TSQL.md)
- [PowerShell](sql-data-warehouse-get-started-create-powershell.md)

In dieser exemplarischen Vorgehensweise wird das Erstellen einer Azure SQL Data Warehouse-Datenbank veranschaulicht, was im Azure-Vorschauportal nur wenige Minuten dauert.

In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:

- Erstellen eines Servers, der die Datenbank hostet
- Erstellen einer Datenbank, die die AdventureWorksDW-Beispieldatenbank enthält

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Schritt 1: Anmelden und erste Schritte

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie auf **Neu** > **Daten + Speicher** > **SQL Data Warehouse**.

	![Erstellen eines Data Warehouse](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

1. Geben Sie auf dem Blatt "SQL Data Warehouse-Datenbank" einen Namen für Ihre Datenbank ein. In diesem Beispiel nennen wir die Datenbank "AdventureWorksDW".

    ![Eingeben eines Datenbanknamens](./media/sql-data-warehouse-get-started-provision/database-name.png)


## Schritt 2: Konfigurieren und Erstellen eines Servers
In Azure SQL-Datenbank und SQL Data Warehouse wird jede Datenbank einem Server und jeder Server einem geografischen Standort zugewiesen. Der Server wird als logischer SQL Server bezeichnet.

> [AZURE.NOTE]<a name="note"></a>Ein logischer SQL Server: >> + Bietet ein einheitliches Verfahren zum Konfigurieren mehrerer Datenbanken innerhalb des gleichen geografischen Standorts. > + Ist keine physische Hardware wie bei einem lokalen Server, der eine Datenbank hostet, sondern Teil der Dienstsoftware. Aus diesem Grund sprechen wird von einem *logischen Server*. > + Kann mehrere Datenbanken ohne Beeinträchtigung der Leistung hosten. > + Enthält im Namen ein kleingeschriebenes *s*. "SQL **s**erver" ist ein logischer Azure-Server, während "SQL **S**erver" ein Produktname ist.

1. Klicken Sie auf **Server** > **Neuen Server erstellen**. Es fallen keine Gebühren für den Server ein. Wenn Sie einen bereits vorhandenen V12-Server verwenden möchten, wählen Sie den vorhandenen Server aus, und fahren Sie mit dem nächsten Schritt fort. 

    ![Erstellen eines neuen Servers](./media/sql-data-warehouse-get-started-provision/create-server.png)

3. Geben Sie die Informationen für den neuen Server ein.
    
	- **Servername**. Geben Sie einen Namen für den logischen Server ein.
	- **Name des Serveradministrators**. Geben Sie einen Benutzernamen für das Serveradministratorkonto ein.
	- **Kennwort**. Geben Sie das Kennwort des Serveradministrators ein. 
	- **Standort**. Wählen Sie einen geografischen Standort für den Server. Um die Datenübertragungszeit zu verkürzen, empfiehlt es sich, den Server in der geografischen Nähe zu anderen Datenressourcen zu platzieren, auf die diese Datenbank zugreift.
	- **V12-Server erstellen**. "JA" ist für "SQL Data Warehouse" die einzige Option. 
	- **Azure-Diensten den Zugriff auf Server gestatten**. Diese Option ist für "SQL Data Warehouse" stets aktiviert.

    >[AZURE.NOTE]Stellen Sie sicher, dass Sie den Servernamen, den Serveradministratornamen und das Kennwort notieren und aufbewahren. Sie benötigen diese Informationen, um sich beim Server anzumelden.

1. Klicken Sie auf **OK**, um die Einstellungen zu speichern und zum Blatt "SQL Data Warehouse" zurückzukehren.

    ![Konfigurieren des neuen Servers](./media/sql-data-warehouse-get-started-provision/configure-server.png)

## Schritt 3: Konfigurieren und Erstellen einer Datenbank
Nachdem Sie Ihren Server ausgewählt haben, können Sie die Erstellung der Datenbank abschließen.
 
2. Füllen Sie auf dem Blatt **SQL Data Warehouse** die restlichen Felder aus. 

    ![Erstellen einer Datenbank](./media/sql-data-warehouse-get-started-provision/create-database.png)
    
    - **Leistung**: Es wird empfohlen, mit 400 DWUs zu beginnen. Sie können den Schieberegler nach links oder rechts bewegen, um die Leistungsstufe Ihrer Datenbank anzupassen, was sowohl jetzt als auch nach Erstellen der Datenbank möglich ist. 

        > [AZURE.NOTE]Die Leistung wird in Data Warehouse-Einheiten (Data Warehouse Units, DWUs) gemessen. Wenn Sie die Anzahl der DWUs erhöhen, erhöht SQL Data Warehouse die Computerressourcen, die für Ihre Datenbankvorgänge verfügbar sind. Wie Sie Ihren Workload ausführen, werden Sie erkennen, in welchem Verhältnis DWUs zur Workloadleistung stehen.
        > 
        > Sie können die Leistungsstufe schnell und einfach ändern, nachdem die Datenbank erstellt wurde. Wenn Sie z. B. die Datenbank nicht verwenden, ziehen Sie den Schieberegler nach links, um Kosten zu senken. Oder erhöhen Sie die Leistung, wenn mehr Ressourcen benötigt werden. Dies ist die skalierbare Leistung von SQL Data Warehouse.

    - **Quelle auswählen**. Klicken Sie auf **Quelle auswählen** > **Beispiel**. Da derzeit nur eine Beispieldatenbank verfügbar ist, füllt Azure bei Wählen von "Beispiel" automatisch die Einstellung **Beispiel auswählen** mit "AdventureWorksDW" aus.
  
        ![Auswählen eines Beispiels](./media/sql-data-warehouse-get-started-provision/select-source.png)

    - **Ressourcengruppe**. Behalten Sie die Standardwerte bei. Ressourcengruppen sind Container, die Ihnen helfen, eine Sammlung von Azure-Ressourcen zu verwalten. Weitere Informationen zu [Ressourcengruppen](../azure-portal/resource-group-portal.md).
    
    - **Abonnement**. Wählen Sie das Abonnement für die Abrechnung dieser Datenbank aus.

1. Klicken Sie auf **Erstellen**, um die SQL Data Warehouse-Datenbank zu erstellen.

1. Warten Sie einige Minuten, bis die Datenbank bereit ist. Anschließend wird Ihre Datenbank in Ihrem Dashboard angezeigt. Jetzt sollte sich nun wieder im [Azure-Vorschauportal](https://portal.azure.com) befinden. Beachten Sie, dass die SQL Data Warehouse-Datenbank auf der Seite hinzugefügt wurde.

    ![Portalansicht](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)


## Schritt 4: Konfigurieren des Serverfirewall-Zugriffs für Ihre Client-IP-Adresse
Um über Ihre aktuelle IP-Adresse auf den Server zugreifen zu können, müssen Sie Ihre Client-IP-Adresse den Firewallregeln hinzufügen. Dies wird im folgenden Schritt gezeigt.

1. Klicken Sie auf **Durchsuchen** > **SQL Server** > Server wählen> **Einstellungen** > **Firewall**.

    ![Suchen von Firewalleinstellungen](./media/sql-data-warehouse-get-started-provision/find-firewall-settings.png)

4. Klicken Sie auf **Client-IP-Adresse hinzufügen**, damit Azure eine Regel für diese IP-Adresse erstellt, und klicken Sie dann auf **Speichern**.

	![Hinzufügen der IP-Adresse](./media/sql-data-warehouse-get-started-provision/add-client-ip.png)

1. Erstellen Sie eine Firewallregel mit einem Bereich von IP-Adressen. Sie können dies jetzt oder später tun.

	>[AZURE.IMPORTANT]Die IP-Adresse kann sich von Zeit zu Zeit ändern, und Sie können dann möglicherweise nicht auf den Server zugreifen, bis Sie eine neue Firewallregel erstellt haben. Überprüfen Sie die IP-Adresse mit [Bing](http://www.bing.com/search?q=my%20ip%20address), und fügen Sie dann eine einzelne IP-Adresse oder einen Bereich von IP-Adressen hinzu. Weitere Einzelheiten finden Sie unter [Konfigurieren der Firewalleinstellungen](../sql-database/sql-database-configure-firewall-settings.md).

    Um eine Regel zu erstellen, geben Sie einen Namen und den IP-Adressbereich ein, und klicken Sie auf **Speichern**.

    ![Hinzufügen einer Firewallregel](./media/sql-data-warehouse-get-started-provision/add-rule.png)

Da Sie jetzt die Firewall konfiguriert haben, können Sie Verbindungen zwischen dem Desktop und der gerade erstellten Azure SQL Data Warehouse-Datenbank herstellen.


## Nächste Schritte

Nachdem Sie jetzt eine Beispieldatenbank für SQL Data Warehouse erstellt haben, erfahren Sie unter [Herstellen der Verbindung und Abfragen](./sql-data-warehouse-get-started-connect-query.md), wie SQL Data Warehouse verwendet wird.

>[AZURE.NOTE]Wir möchten diesen Artikel verbessern. Wenn Sie auf die Frage „War dieser Artikel hilfreich?“ mit „Nein“ antworten, bitten wir Sie, einen kurzen Vorschlag dazu einzufügen, was fehlt oder wie der Artikel zu verbessern ist. Vielen Dank im Voraus!

<!---HONumber=Oct15_HO2-->