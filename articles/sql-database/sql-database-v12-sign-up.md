<properties
	pageTitle="Exemplarische Vorgehensweise: Aktivieren des aktuellen SQL-Datenbankupdates V12"
	description="Beschreibt die Schritte zum Testen der Version V12 von Azure SQL-Datenbank mithilfe der neuen Benutzeroberfläche im Microsoft Azure-Portal."
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
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="genemi"/>


# Exemplarische Vorgehensweise: Aktivieren des aktuellen SQL-Datenbankupdates V12

In diesem Thema wird beschrieben, wie Sie die Option "Azure SQL-Datenbankupdate V12" aktivieren, die im Dezember 2014 erstmalig von Microsoft veröffentlicht wurde.

Bevor Sie die neueste Version V12 testen können, benötigen Sie ein Microsoft Azure-Abonnement oder zumindest ein Abonnement für eine[ kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).

Sie können V12 über das neue Azure-Vorschauportal unter [http://portal.azure.com/](http://portal.azure.com/) aktivieren, anstatt über das [ältere Azure-Portal](http://manage.windowsazure.com/). Nachdem Sie V12 für Ihr Abonnement aktiviert haben, werden die Erstellungs- und Upgradeoptionen für V12 im Azure-Vorschauportal verfügbar. Anschließend können Benutzer den [Erstellungs](sql-database-create.md)-Workflow über das Serverblatt oder das Datenbankblatt initiieren.

> [AZURE.NOTE]Testdatenbanken, Datenbankkopien oder neue Datenbanken eignen sich gut für das Upgrade auf die Preview. Upgrades für geschäftsrelevante Produktionsdatenbanken sollten wie üblich erst nach der Preview-Phase durchgeführt werden.

Weitere Informationen zum Upgrade auf V12 finden Sie unter [Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12](sql-database-v12-plan-prepare-upgrade.md).


## A. Sicherheitsautorisierung

Zunächst müssen Sie sicherstellen, dass Sie über die erforderlichen Berechtigungen zum Aktivieren von V12 für Ihr Abonnement verfügen. Wenn Sie versuchen, die V12-Option zu aktivieren, erfolgt eine Autorisierungsprüfung, um sicherzustellen, dass Sie über ausreichende Berechtigungen innerhalb des Abonnements verfügen.

 UmV12 zu testen, müssen Sie über eine der folgenden Autorisierungen verfügen:

- Besitzer des Abonnements
- Co-Administrator für das Abonnement

Weitere Informationen zu Azure-Konten finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Schritte in der Benutzeroberfläche des Azure-Vorschauportals

In diesem Abschnitt wird eine Klickreihenfolge beschrieben, die Sie einmal in der Benutzeroberfläche des Azure-Vorschauportals ausführen können, um die V12-Option zu aktivieren. Nachdem Sie die Option aktiviert haben, bleibt sie danach verfügbar.

Alle Aktivierungsszenarien basieren auf demselben Grundprinzip. Beim ersten Versuch zum [Erstellen eines neuen SQL-Datenbankservers](sql-database-create.md) wird ein Blatt mit dem Titel **Aktuelles Update (Preview)** angezeigt. Dort finden Sie ein Kontrollkästchen, über das Sie Ihre Berechtigung zur Verwendung von Version V12 aktivieren können. Nachdem Sie die Berechtigung aktiviert haben, wird das Kontrollkästchen nicht mehr angezeigt. Stattdessen können Sie über ein Steuerelement **Ja|Nein** angeben, ob Ihr neuer Server V12 verwenden soll. Bei Auswahl von **Nein** erstellen Sie einen V11-Server (wie durch SELECT @@VERSION; angegeben).

### B.1 Steuerelement "Ja|Nein" “für Version V12

Nachdem Sie die Berechtigung für die Option " V12" aktiviert haben, sehen Sie das Steuerelement **Ja|Nein**, das im folgenden Screenshot des Azure-Vorschauportals durch einen Kreis gekennzeichnet ist.

![YesNoOptionForTheV12Preview][Image1]


## C. Nächste Schritte

Als Nächstes werden in den folgenden Themen Verwendungsmöglichkeiten für die SQL-Datenbank V12 erläutert.

- [Erstellen einer Datenbank in SQL-Datenbank-Update V12](sql-database-create.md)


<!-- References, Images. -->
[Image1]: ./media/sql-database-v12-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png

 

<!---HONumber=July15_HO4-->