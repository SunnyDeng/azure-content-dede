<properties 
	pageTitle="Upgrade auf SQL-Datenbank V12" 
	description="Erläutert, wie von einer früheren Version von Azure SQL-Datenbank ein Upgrade auf Azure SQL-Datenbank V12 durchgeführt wird." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="09/30/2015" 
	ms.author="sstein"/>


# Upgrade auf SQL-Datenbank V12


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


SQL-Datenbank V12 ist die neueste Version der SQL-Datenbank und [viele Vorteile gegenüber der vorherigen V2-Version ](sql-database-v12-whats-new.md). In diesem Artikel ist beschrieben, wie V2-Server über das Azure-Vorschauportal auf V12 aktualisiert werden.

Im Verlauf der Aktualisierung auf SQL-Datenbank V12 müssen Sie auch [alle Web- und Business-Datenbanken auf eine neue Dienstebene aktualisieren](sql-database-upgrade-new-service-tiers.md). Die folgenden Anleitungen umfassen die Schritte zum Aktualisieren Ihrer Web- und Business-Datenbanken anhand von Tarifempfehlungen, die auf der bisherigen Nutzung Ihrer Datenbanken basieren.



1. Navigieren Sie im [Azure-Vorschauportal](http://portal.azure.com/) zu dem Server, den Sie aktualisieren möchten. Wählen Sie dazu **ALLE DURCHSUCHEN** > **SQL-Server** und dann den gewünschten Server aus.
2. Wählen Sie **Letzte Aktualisierung der SQL-Datenbank** aus, und wählen Sie dann **Upgrade für diesen Server ausführen** aus.

      ![Server upgraden][1]

## Upgrade für Ihre Web- und Business-Datenbanken durchführen

2. Führen Sie ein Upgrade für alle Web- und Business-Datenbanken durch. Wenn Ihr Server Web- oder Business-Datenbanken hat, müssen Sie für diese ein Upgrade durchführen. Um Sie beim Durchführen des Upgrades zu unterstützen, empfiehlt der SQL-Datenbank-Dienst eine geeignete Dienstebene und Leistungsstufe (Tarif) für jede Datenbank. Durch Analysieren der bisherigen Nutzung für jede Datenbank empfiehlt der Dienst einen Tarif, der am besten geeignet ist, die vorhandene Datenbankworkload auszuführen. 
    
    Wählen Sie jede zu überprüfende Datenbank aus, und wählen Sie den empfohlenen Tarif aus, auf den aktualisiert werden soll. Sie können immer die verfügbaren Tarife durchsuchen und den Tarif auswählen, der für Ihre Umgebung am besten geeignet ist.

     ![Datenbanken][2]



7. Nachdem Sie auf den vorgeschlagenen Tarif geklickt haben, wird das Blatt **Wählen Sie Ihren Tarif** angezeigt. Dort können Sie einen Tarif auswählen und dann auf die Schaltfläche **Auswählen** klicken, um zu diesem Tarif zu wechseln. Wählen Sie für jede Web- oder Business-Datenbank einen neuen Tarif aus.

    ![empfehlungen][6]


Nachdem alle Datenbanken auf dem Server qualifiziert sind, können Sie das Upgrade starten.

## Starten des Upgrades

3. Wenn alle Datenbanken auf dem Server zum Upgrade qualifiziert sind, müssen Sie in **GEBEN SIE DEN SERVERNAMEN EIN** den Servernamen eingeben, um zu bestätigen, dass Sie das Upgrade durchführen möchten. Klicken Sie dann auf **OK**. 

    ![Upgrade bestätigen][3]


4. Das Upgrade wird gestartet, und die Benachrichtigung zum Vorgangsfortschritt wird angezeigt. Der Upgradevorgang wird eingeleitet. Abhängig von den Details Ihrer speziellen Datenbanken kann das Durchführen des Upgrades auf V12 einige Zeit dauern. Während dieser Zeitspanne bleiben alle Datenbanken auf dem Server online, Server- und Datenbankverwaltungsaktionen sind jedoch eingeschränkt.

    ![Upgrade wird ausgeführt][4]

    Zum Zeitpunkt des tatsächlichen Übergangs auf die neue Leistungsstufe können die Datenbankverbindungen ggf. für einen sehr kurzen Zeitraum (normalerweise im Sekundenbereich) vorübergehend unterbrochen werden. Wenn eine Anwendung über Maßnahmen zur Behandlung vorübergehender Fehler (Wiederholungslogik) bei Verbindungsunterbrechungen verfügt, ist es ausreichend, am Ende des Upgrades Schutz vor unterbrochenen Verbindungen vorzusehen.

5. Nach Abschluss des Upgradevorgangs werden die SQL-Datenbank V12-Server-Features aktiviert.

    ![V12-aktiviert][5]


## Verwandte Links

- [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md)
- [Plan and prepare to upgrade to SQL Database V12](sql-database-v12-plan-prepare-upgrade.md) (Planen und Vorbereiten des Upgrades auf die SQL-Datenbank V12; in englischer Sprache)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO1-->