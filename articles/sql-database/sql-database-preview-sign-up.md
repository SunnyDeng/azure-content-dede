<properties 
	pageTitle="Exemplarische Vorgehensweise: Aktivieren des aktuellen SQL-Datenbankupdates V12 (Preview)" 
	description="Beschreibt die Schritte zum Testen der Previewversion von Azure SQL-Datenbank V12 mithilfe der neuen Benutzeroberfläche im Azure-Portal." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


#Exemplarische Vorgehensweise: Aktivieren des aktuellen SQL-Datenbankupdates V12 (Preview)

In diesem Thema wird beschrieben, wie Sie die Option "Azure SQL-Datenbankupdate V12 (Preview)" aktivieren, die im Dezember 2014 erstmalig von Microsoft veröffentlicht wurde.

Bevor Sie die neueste V12-Preview testen können, benötigen Sie ein Microsoft Azure-Abonnement oder mindestens ein Abonnement für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).

Sie können die V12-Preview über die Vorschauversion des neuen Microsoft Azure-Verwaltungsportals unter [http://portal.azure.com/](http://portal.azure.com/) aktivieren. Nachdem Sie die V12-Preview für Ihr Abonnement aktiviert haben, werden die Erstellungs- und Upgradeoption für die V12-Preview im Azure-Portal verfügbar. Anschließend können Benutzer den [Erstellungs-](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) oder [Upgrade](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)-Workflow über das Server- oder Datenbankfenster initiieren.

> [AZURE.NOTE]
> Testdatabanken, Datenbankkopien oder neue Datenbanken eignen sich gut für das Upgrade auf die Preview. Upgrades für geschäftsrelevante Produktionsdatenbanken sollten wie üblich erst nach der Previewphase durchgeführt werden.

Weitere Informationen zur Preview finden Sie unter [Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


##A. Sicherheitsautorisierung

Zunächst müssen Sie sicherstellen, dass Sie über die erforderlichen Berechtigungen zum Aktivieren der V12-Preview für Ihr Abonnement verfügen. Wenn Sie versuchen, die V12-Previewoption zu aktivieren, erfolgt eine Autorisierungsprüfung, um sicherzustellen, dass Sie über ausreichende Berechtigungen innerhalb des Abonnements verfügen.

 Um die Vorschau zu testen, müssen Sie über eine der folgenden Autorisierungen verfügen:

- Besitzer des Abonnements
- Co-Administrator für das Abonnement

Weitere Informationen zu Azure-Konten finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](http://msdn.microsoft.com/library/hh531793.aspx).

##B. Schritte in der Benutzeroberfläche des Portals

In diesem Abschnitt wird eine Klickreihenfolge beschrieben, die Sie einmal in der Benutzeroberfläche des Azure-Portals ausführen können, um die V12-Previewoption zu aktivieren. Nachdem Sie die Option aktiviert haben, bleibt sie danach verfügbar.

Alle Aktivierungsszenarien basieren auf demselben Grundprinzip. Beim ersten Versuch, einen [neuen SQL-Datenbankserver zu erstellen](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/), wird ein Fenster mit dem Titel **Aktuelles Update (Preview)** eingeblendet. Dort finden Sie ein Kontrollkästchen, über das Sie Ihre Berechtigung zur Verwendung der V12-Previewversion aktivieren können. Nachdem Sie die Berechtigung aktiviert haben, wird das Kontrollkästchen nicht mehr angezeigt. Stattdessen können Sie über ein Steuerelement **Ja|Nein** angeben, ob Ihr neuer Server die V12-Preview verwenden soll. Bei Auswahl von **Nein** erstellen Sie einen V11-Server (wie durch SELECT @@VERSION; angegeben).

###B.1 Steuerelement "Ja|Nein"für die V12-Previewversion

Nachdem Sie die Berechtigung für die V12-Preview aktiviert haben, sehen Sie das Steuerelement **Ja|Nein**, das im folgenden Portalscreenshot durch einen Kreis gekennzeichnet ist.

![YesNoOptionForTheV12Preview][Image1]


##C. Nächste Schritte

Als Nächstes werden in den folgenden Themen Verwendungsmöglichkeiten für die V12-Preview erläutert.

- [Erstellen einer Datenbank im aktuellen SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [Upgrade auf das aktuelle SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> Testdatabanken, Datenbankkopien oder neue Datenbanken eignen sich gut für das Upgrade auf die Preview. Upgrades für geschäftsrelevante Produktionsdatenbanken sollten wie üblich erst nach der Previewphase durchgeführt werden.


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
 