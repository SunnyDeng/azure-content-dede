<properties 
   pageTitle="Tarifempfehlungen für Azure SQL-Datenbank" 
   description="Beim Ändern von Tarifen im Azure-Portal werden Tarifempfehlungen bereitgestellt, die den am besten geeigneten Tarif für die Ausführung der Arbeitsauslastung einer vorhandenen Azure SQL-Datenbank empfehlen. Tarife beschreiben die Dienstebene und die Leistungsebene einer SQL-Datenbank." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/22/2015"
   ms.author="sstein"/>

# Tarifempfehlungen für SQL-Datenbank

 Es werden Tarifempfehlungen bereitgestellt, die die Dienstebene und die Leistungsebene empfehlen, die am besten geeignet sind, den Workload einer vorhandenen Azure SQL-Datenbank auszuführen.

> [AZURE.NOTE]Tarifempfehlungen sind nur für Web- und Business-Datenbanken sowie für Pools für elastische Datenbanken verfügbar – und sie sind nur im [Azure-Vorschauportal](https://portal.azure.com/) verfügbar.


Sie erhalten Tarifempfehlungen während der folgenden Aufgaben:

- [Ändern der Dienstebene und Leistungsstufe (Tarif) einer SQL-Datenbank](sql-database-scale-up.md)
- [Upgraden von Azure SQL-Server auf V12](sql-database-v12-upgrade.md)
- [Erstellen eines elastischen Datenbankpools](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)


## Übersicht

Der SQL-Datenbak-Dienst analysiert die aktuellen Leistungs- und Featureanforderungen durch Bewertung der historischen Ressourcennutzung für eine SQL-Datenbank. Darüber hinaus wird der minimal zulässige Tarif basierend auf der Größe der Datenbank und der aktivierten Funktionen für die [Geschäftskontinuität](https://msdn.microsoft.com/library/azure/hh852669.aspx) bestimmt.

Diese Informationen werden analysiert, und der Tarif und die Leistungsstufe, die für die Ausführung der typischen Arbeitsauslastung der Datenbank und die Aufrechterhaltung ihrer aktuellen Featuregruppe am besten geeignet sind, werden empfohlen.

- Der Dienst überprüft die historischen Daten der letzten 15 bis 30 Tage (Ressourcennutzung, Datenbankgröße und Datenbankaktivität) und führt einen Vergleich zwischen der Menge der verbrauchten Ressourcen und den tatsächlichen Einschränkungen der aktuell verfügbaren Dienstebenen und Leistungsstufen durch.
- Daten werden in Intervallen von 15 Sekunden analysiert, wobei das Resultset jedes Intervalls in die vorhandene Dienstebene und Leistungsstufe kategorisiert wird, welche für die Handhabung der Arbeitsauslastung dieses Resultsets am besten geeignet sind.
- Diese 15-Sekunden-Beispiele werden dann in die breitere Analyse von 15 bis 30 Tagen aggregiert, und die Dienstebene und Leistungsstufe, die 95 % der historischen Arbeitsauslastung optimal bewältigen können, werden empfohlen.

### Recommendations

Basierend auf Ihrer Datenbanknutzung können derzeit 2 Kategorien von Empfehlungen auftreten:


| Empfehlungen | Beschreibung |
| :--- | :--- |
| Upgrade | Führen Sie ein Upgrade auf eine neue Ebene durch. |
| Nicht verfügbar | Eine Datenbank erfordert eine minimale Arbeitsauslastung oder ungefähr 14 Tage Aktivität. Es sind nicht genügend Daten vorhanden, um eine gültige Empfehlung abzugeben. |

## Abrufen von Tarifempfehlungen

Zum Abrufen von Tarifempfehlungen wählen Sie eine vorhandene Web- oder Business-Datenbank aus, und klicken Sie dann auf die Kachel **Tarif** . (Tarifempfehlungen sind auch verfügbar, wenn Sie die [Schritte für das Upgrade auf SQL-Datenbank V12](sql-database-v12-upgrade.md) ausführen.)

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2. Klicken Sie im linken Menü auf **DURCHSUCHEN**.
3. Klicken Sie im Blatt **Durchsuchen** auf **SQL-Datenbanken**.
4. Klicken Sie im Blatt **SQL-Datenbanken** auf die Datenbank, die der Dienst analysieren soll.

    ![Datenbank auswählen][1]

5. Wählen Sie im Blatt "Datenbank" die Kachel **Tarif** aus.

    ![Preisstufe][2]


7. Nach dem Klicken auf die Kachel **Tarif** wird das Blatt **Empfohlene Tarife** angezeigt. Dort können Sie auf den empfohlenen Tarif klicken und dann auf die Schaltfläche **Auswählen**, um zu diesem Tarif zu wechseln.

    ![Anmelden für die Vorschau][4]

8. Optional können Sie auf **Nutzungsdetails anzeigen** klicken, um das Blatt **Tarifempfehlungen Details** zu öffnen. Dort können Sie den empfohlenen Tarif für die Datenbank, einen Featurevergleich zwischen aktuellen und empfohlenen Tarifen sowie ein Diagramm für die Analyse der historischen Ressourcennutzung anzeigen.

    ![Anmelden für die Vorschau][5]



## Zusammenfassung

Tarifempfehlungen bieten eine automatisierte Möglichkeit zum Erfassen von Telemetriedaten für jede SQL-Datenbank und zum Empfehlen der besten Kombination von Dienstebene/Leistungsstufe basierend auf den tatsächlichen Leistungs- und Featureanforderungen einer Datenbank. Klicken Sie auf die Kachel **Tarif** im Blatt einer Datenbank, um die Tarifempfehlungen anzuzeigen.



## Nächste Schritte

Abhängig von den Details Ihrer speziellen Datenbank erfolgt die Durchführung eines Upgrades oder Downgrades in der Regel nicht sofort. Das Verwaltungsportal stellt während des Übergangs der Datenbank auf einen neuen Tarif Benachrichtigungen bereit. Sie können den Upgradestatus jedoch auch überwachen, indem Sie die Sicht [Sys. dm\_operation\_status (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn270022.aspx) in der Master-Datenbank des SQL-Datenbankservers abfragen.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[2]: ./media/sql-database-service-tier-advisor/pricing-tier.png
[3]: ./media/sql-database-service-tier-advisor/preview-sign-up.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=Sept15_HO4-->