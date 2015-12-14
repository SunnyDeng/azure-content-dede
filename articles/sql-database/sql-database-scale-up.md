<properties
	pageTitle="Ändern der Dienstebene und Leistungsstufe einer Azure SQL-Datenbank"
	description="In „Ändern der Dienstebene und Leistungsstufe einer Azure SQL-Datenbank“ wird beschrieben, wie Sie die Dienstebene und Leistungsstufe Ihrer SQL-Datenbank zentral hoch- oder herunterstufen. Ändern des Tarifs einer Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ändern der Dienstebene und Leistungsstufe (Tarif) einer SQL-Datenbank

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

In diesem Artikel wird beschrieben, wie Sie die Dienstebene und Leistungsstufe Ihrer SQL-Datenbank mit dem [Azure-Portal](https://portal.azure.com) ändern.

Bestimmen Sie anhand der Informationen unter [Aktualisieren von Web-/Business-SQL-Datenbanken auf die neuen Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](sql-database-upgrade-new-service-tiers.md) und [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](sql-database-service-tiers.md) die geeignete Dienstebene und Leistungsstufe für Ihre Azure SQL-Datenbank.

> [AZURE.IMPORTANT]Ein Ändern der Dienstebene und Leistungsstufe einer SQL-Datenbank ist ein Onlinevorgang. Dies bedeutet, dass die Datenbank während des gesamten Vorgangs ohne Ausfallzeit online und verfügbar bleibt.

- Für ein Downgrade einer Datenbank sollte die Datenbank kleiner als die in der Zieldienstebene maximal zulässige Größe sein. 
- Beim Aktualisieren einer Datenbank, für die die Option für [standardmäßige Georeplikation](https://msdn.microsoft.com/library/azure/dn758204.aspx) oder [aktive Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) aktiviert ist, müssen Sie vor der Aktualisierung der primären Datenbank zunächst die zugehörigen sekundären Datenbanken auf die gewünschte Leistungsstufe aktualisieren.
- Beim Downgrade von einer Premium-Dienstebene müssen Sie zuerst alle geografischen Replikationsbeziehungen beenden. Sie können die im Thema [Beenden einer fortlaufenden Kopierbeziehung](https://msdn.microsoft.com/library/azure/dn741323.aspx) beschriebenen Schritte verwenden, um den Replikationsprozess zwischen der primären und aktiven sekundären Datenbank zu beenden.
- Die Angebote des Wiederherstellungsdienstes variieren für die verschiedenen Dienstebenen. Wenn Sie ein Downgrade durchführen, verlieren Sie eventuell die Möglichkeit einer Zeitpunktwiederherstellung, oder der Aufbewahrungszeitraum für Sicherungen verkürzt sich. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Sie können innerhalb von 24 Stunden bis zu vier einzelne Datenbankänderungen (Dienstebene oder Leistungsstufen) vornehmen.
- Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.


**Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:**

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Eine Azure SQL-Datenbank. Wenn Sie nicht über eine SQL-Datenbank verfügen, können Sie die Erstellung anhand der Schritte im folgenden Artikel durchführen: [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).


## Ändern der Dienstebene und Leistungsstufe Ihrer Datenbank


Öffnen Sie das Blatt „SQL-Datenbank“ für die Datenbank, die Sie zentral hoch- oder herunterskalieren möchten:

1.	Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Datenbanken**.
2.	Klicken Sie auf die Datenbank, die Sie ändern möchten.
3.	Klicken Sie auf dem Blatt „SQL-Datenbank“ auf die Kachel **Tarif**.

    ![Preisgestaltungskachel][1]

1.  Wählen Sie eine neue Stufe aus, und klicken Sie auf **Auswählen**.

    Das Klicken auf **Auswählen** sendet eine Skalierungsanforderung zum Ändern der Datenbankebene. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Skalierungsvorgang abgeschlossen ist. Klicken Sie auf die Benachrichtigung, um Details und Status des Skalierungsvorgangs anzuzeigen.

    ![Preisstufe auswählen][2]

3.	Klicken Sie im linken Menüband auf **Benachrichtigungen**:

    ![Benachrichtigungen][3]

## Überprüfen, ob die Datenbank den ausgewählten Tarif hat

   Nach Abschluss des Skalierungsvorgangs überprüfen und bestätigen Sie, dass die Datenbank den gewünschten Tarif hat:

2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Datenbanken**.
2.	Klicken Sie auf die Datenbank, die Sie aktualisiert haben.
3.	Überprüfen Sie die Kachel **Tarif**, und vergewissern Sie sich, dass sie auf den richtigen Tarif festgelegt ist.

    ![neuer Preis][4]


## Nächste Schritte

- [Horizontal hoch- und herunterskalieren](sql-database-elastic-scale-get-started.md)
- [Herstellen einer Verbindung mit einer SQL-Datenbank und Abfragen dieser Datenbank mit SSMS](sql-database-connect-query-ssms.md)
- [Exportieren einer Azure SQL-Datenbank](sql-database-export.md)

## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [SQL-Datenbank-Dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_1203_2015-->