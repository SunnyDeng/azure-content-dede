<properties
   pageTitle="Erste Schritte: Bereitstellen eines SQL Data Warehouse | Microsoft Azure"
   description="Stellen Sie ein SQL Data Warehouse bereit, indem Sie diese Schritte und Richtlinien befolgen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Erste Schritte: Bereitstellen von SQL Data Warehouses #

Dieser Artikel bietet einen schnellen Einstieg in die Bereitstellung eines SQL Data Warehouse in Azure. In dieser Anleitung werden die folgenden Aufgaben ausgeführt:

1. Erstellen einer neuen SQL Data Warehouse-Datenbank
2. Konfigurieren eines neuen logischen Servers
3. Festlegen einer Azure-Firewallregel, um externen Clientzugriff zu ermöglichen

## Kostenlose Azure-Testversion ##
Sie benötigen ein Azure-Abonnement, um die folgenden Aufgaben abzuschließen. Wenn Sie noch keinen Zugriff auf ein Azure-Abonnement haben, müssen Sie dies als ersten Schritt erledigen.

Sie können Zugriff auf eine [kostenlose Testversion][] erhalten, mit der Sie alle Dienste in Azure, einschließlich SQL Data Warehouse, testen können.


## Anmelden beim Azure-Portal ##

Wenn Sie ein Abonnement haben, können Sie sich beim [Azure-Portal][] anmelden. Melden Sie sich jetzt dort an.

In den nächsten Schritten setzen wir schnell einen neuen logischen Server auf und erstellen eine neue SQL Data Warehouse-Datenbank.

## Suchen des SQL Data Warehouse-Diensts

Als Erstes müssen wir den SQL Data Warehouse-Dienst im Azure-Verwaltungsportal suchen.

In der unteren linken Ecke des Azure-Verwaltungsportals befindet sich die Schaltfläche "Neu". Die Schaltfläche "Neu" ist der Ausgangspunkt zum Erstellen eines neuen Diensts in Azure.

- Klicken Sie jetzt auf die Schaltfläche "Neu".

### Daten + Speicher

Durch Klicken auf die Schaltfläche "Neu" wurden alle Dienstkategorien in Azure geöffnet. SQL Data Warehouse befindet sich in der Kategorie "Daten + Speicher".

- Klicken Sie auf "Daten + Speicher", um die von Azure angebotenen Dienste für diese Kategorie anzuzeigen.

### SQL Data Warehouse

Wie Sie sehen können, bietet Azure viele Daten- und Speichermodule. Dieser Leitfaden bezieht sich jedoch auf SQLDW.

- Fahren Sie fort, und wählen Sie "SQL Data Warehouse".

## Konfigurieren von SQL Data Warehouse

Zum Abschließen des Bereitstellungsverfahrens müssen Sie nur noch das SQL Data Warehouse konfigurieren.


### Datenbankname

Die erste Konfiguration ist die Benennung der Datenbank.



- Nennen Sie die Datenbank im Rahmen dieses Schnellstarts "MySQLDW".


> [AZURE.NOTE]Bei der Erstellung Ihrer eigenen Datenbank können Sie diese natürlich nach eigenem Bedarf benennen. Der Name muss jedoch den grundlegenden Benennungsanforderungen von Azure entsprechen.

### Leistung

Die Leistungsoption ist sehr **wichtig**. Über diesen Schieberegler kann die Leistung von SQL Data Warehouse skaliert werden. Sie können die Leistung zu einem beliebigen Zeitpunkt erhöhen oder verringern – nicht nur während der Clusterkonfiguration. Je weiter Sie den Regler nach rechts schieben, desto mehr Ressourcen stehen zur Verfügung. Wenn diese Ressourcen nicht mehr benötigt werden, können Sie den Schieberegler sofort wieder zurückschieben, um Kosten zu sparen. In SQL Data Warehouse können Sie Ihr Leistungsprofil bei Bedarf ändern, ohne den Cluster neu erstellen oder Daten verschieben zu müssen.

- Verwenden Sie den Schieberegler nun, um zu sehen, wie die Data Warehouse-Einheiten zunehmen, während Sie den Regler nachts rechts bewegen, und sich verringern, wenn Sie ihn wieder nach links schieben.

- Vor dem Beenden dieses Schritts sollten Sie sicherstellen, dass sich der Schieberegler wieder auf der linken Seite befindet. Bei der neuen Datenbank handelt es sich nur um ein kleines Data Warehouse, sodass wir nicht sehr viele Ressourcen benötigen. Bewahren Sie sich die Ressourcen für den verbleibenden Testzeitraum auf.

### Auswählen der Quelle

Diese Option bietet die Möglichkeit, mit einer leeren Datenbank zu starten. Wählen Sie die neue Datenbank als Ausgangspunkt.

> [AZURE.NOTE]Es gibt auch eine zweite Option. Es ist auch möglich, die Datenbank aus einem bereits vorhandenen Wiederherstellungspunkt zu erstellen – eine Wiederherstellungsoption.

### Logischer Server

Ihre SQL Data Warehouse-Datenbank befindet sich auf einem logischen Server. Der logische Server sorgt auf Instanzebene für eine Reihe von Datenbanken für Konfigurationskonsistenz und lagert den Dienst an ein Azure-Rechenzentrum aus.

Die festzulegenden Optionen sind: 1. Servername 2. Name des Serveradmins 3. Kennwort 4. Standort des Rechenzentrums 5. Zugriffsberechtigungen für Azure-Dienste auf den Server

Sie können diese Werte nach Bedarf festlegen. Der Servername muss eindeutig sein. Es empfiehlt sich, ein Rechenzentrum in der Nähe auszuwählen, um die Netzwerklatenz gering zu halten. SQL Data Warehouse enthält außerdem leistungsfähige Features, die andere Azure-Dienste nutzen. Es ist daher ratsam, das Kontrollkästchen für den Zugriff auf Azure-Dienste aktiviert zu lassen.

> [AZURE.NOTE]SQL Data Warehouse muss V12-Server verwenden. Stellen Sie sicher, dass diese Option auf "Ja" festgelegt ist. Azure SQL-Datenbanken und SQL Data Warehouse-Datenbanken können den logischen Server auch gemeinsam verwenden. Es muss jedoch ein V12-Server sein.

> [AZURE.NOTE]Notieren Sie den Servernamen, den Namen des Serveradmins und das Kennwort, und bewahren Sie diese sicher auf. Sie benötigen diese Informationen zum Herstellen einer Verbindung mit der SQL Data Warehouse-Datenbank.

### Ressourcengruppe
Ressourcengruppen sind Container, die zum Verwalten einer Azure-Ressourcensammlung entwickelt wurden.

Für diese Schnellstartanleitung können die Standardwerte für Ressourcengruppen beibehalten werden.

Weitere Informationen über[Ressourcengruppen]

### Abonnement
Ein einzelner Benutzer kann ein oder mehrere Azure-Abonnements haben. Wenn mehr als ein Abonnement mit Ihrem Benutzernamen verknüpft ist, können Sie wählen, welches Abonnement verwendet werden soll.

Für diesen Leitfaden reichen die Standardwerte aus.

Erstellen wir nun das SQL Data Warehouse.

## Erstellen des Data Warehouse ##
Sie müssen nur noch auf die Schaltfläche "Erstellen" klicken, um das Data Warehouse zu erstellen.

Glückwunsch! Sie haben Ihre erste SQL Data Warehouse-Datenbank erstellt.

Jetzt sollte wieder die Startseite des [Azure-Portals][] angezeigt werden. Beachten Sie, dass die SQL Data Warehouse-Datenbank auf der Seite hinzugefügt wurde


An diesem Punkt kann niemand auf die SQL Data Warehouse-Datenbank zugreifen. Aus Gründen der Sicherheit wird in der Standardeinstellung der Clientzugriff für die Datenbank noch nicht konfiguriert.

Daher wird im letzten Schritt des Bereitstellungsverfahrens der Dienst für den externen Zugriff konfiguriert.

## Konfigurieren der Azure-Firewall ##

Führen Sie zur erstmaligen Konfiguration der Azure-Firewall folgende Schritte aus:

1. Klicken Sie im linken Navigationsbereich auf "Durchsuchen".

2. Wählen Sie "SQL Server" aus.

3. Wählen Sie Ihren logischen SQL Server aus.

4. Wählen Sie "Einstellungen" aus.

5. Klicken Sie auf "Firewall".

6. Legen Sie die Firewallregel fest.

    Sie müssen an dieser Stelle einige Einstellungen vornehmen. Die sind die folgenden: - Benennen der Firewallregel - Festlegen eines IP-Adressbereichs, wenn Sie nicht über eine statische IP-Adresse verfügen

    > [AZURE.NOTE]Der Client IP-Adressbereich, den Sie einbeziehen müssen, ist Ihre externe oder öffentliche IP-Adresse. Sie können Ihre externe IP-Adresse über einige Websites wie z. B. <a href="http://www.whatismyip.com" target="_blank">www.whatismyip.com</a> herausfinden.

7. Speichern Sie Ihre Firewallregel.


Da Sie jetzt die Firewall konfiguriert haben, können Sie Verbindungen zwischen dem Desktop und dem gerade erstellten Azure SQL Data Warehouse herstellen.

## Nächste Schritte

Nachdem der SQLDW-Dienst bereitgestellt wurde, können wir jetzt zu dessen Verwendung übergehen.

In den nächsten Schritten erfahren Sie daher Folgendes: 1. [Herstellen der Verbindung und Abfragen] der SQLDW-Datenbank 2. Exportieren von Daten aus der SQLDW-Datenbank in Azure-Blob-Speicher 3. Laden weiterer Daten in die SQLDW-Datenbank


<!--Image references-->


<!-- Articles -->
[Herstellen der Verbindung und Abfragen]: ./sql-data-warehouse-get-started-connect-query/
[Ressourcengruppen]: ./azure-preview-portal-using-resource-groups/

<!--External links-->
[kostenlose Testversion]: https://azure.microsoft.com/de-de/pricing/free-trial/
[Azure-Portal]: https://portal.azure.com/
[Azure-Portals]: https://portal.azure.com/

<!---HONumber=July15_HO1-->