<properties
	pageTitle="Verwenden von Azure Storage für die SQL Server-Sicherung und -Wiederherstellung | Microsoft Azure"
	description="Sichern von SQL Server und SQL-Datenbanken in Azure Storage Erläutert die Vorteile der Sicherung von SQL-Datenbanken in Azure-Speicher und welche Komponenten von SQL Server und Azure-Speicher erforderlich sind."
	services="sql-database, virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor="tysonn"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="vm-windows-sql-server"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="carlrab"/>



# Verwenden von Azure-Speicher für SQL Server-Sicherung und -Wiederherstellung

## Übersicht

Die Funktion, die das Schreiben von SQL Server-Sicherungen in den Azure-Blob-Speicherdienst ermöglicht, wurde in SQL Server 2012 SP1 CU2 eingeführt. Mithilfe dieser Funktion können Sie Daten vom Azure Blob-Dienst mit einer lokalen SQL Server-Datenbank oder einer SQL Server-Datenbank auf einem virtuellen Azure-Computer sichern und daraus wiederherstellen. Die Sicherung in der Cloud bietet zahlreiche Vorteile: Verfügbarkeit, unbegrenzter georeplizierter Offsitespeicher und problemlose Migration von Daten in die und aus der Cloud. Sie können BACKUP- und RESTORE-Anweisungen mithilfe von T-SQL oder SMO aufrufen. Wenn Datenbankdateien in einem Azure-Blob gespeichert werden und Sie SQL Server 2016 verwenden, können Sie darüber hinaus [Dateimomentaufnahme-Sicherung](http://msdn.microsoft.com/library/mt169363.aspx) für nahezu sofortige Sicherungen und äußerst schnelle Wiederherstellung verwenden.

## Vorteile der Verwendung des Azure-Blob-Diensts für SQL Server-Sicherungen

Speicherverwaltung, Risiko eines Speicherausfalls, Zugriff auf Offsitespeicher und Konfiguration von Geräten – dies sind nur einige der generellen Probleme bei Sicherungen. Diese Probleme gelten für lokale Datenbankinstanzen und Azure Virtual Machine-Datenbankinstanzen gleichermaßen. Im Folgenden werden einige der wichtigsten Vorteile aufgeführt, die die Verwendung des Azure-Blob-Speicherdiensts für SQL Server-Sicherungen bietet:

* Flexibler, zuverlässiger und unbegrenzter Offsitespeicher: Das Speichern von Sicherungen in Azure-Blobs ist eine praktische, flexible und leicht zugängliche Option für die Offsitespeicherung. Die Erstellung von Offsitespeicher für SQL Server-Sicherungen erfordert ggf. lediglich das Ändern vorhandener Skripts/Aufträge für die Verwendung der Syntax für **SICHERUNG ÜBER URL**. Offsitespeicher sollte normalerweise weit genug vom Standort der Produktionsdatenbank entfernt sein, um zu verhindern, dass ein einziger Notfall sich auf beide Standorte (den des Offsitespeichers und den der Produktionsdatenbank) auswirkt. Die [Georeplikation des Azure-Blobs](../storage/storage-redundancy.md) bietet zusätzlichen Schutz im Fall eines Notfalls, der sich auf die gesamte Region auswirkt. 
* Sicherungsarchiv: Der Azure-Blob-Speicherdienst bietet eine bessere Alternative als die sonst übliche Archivierung von Sicherungen auf Bändern. Bandspeicher muss ggf. physisch an einen Offsitestandort transportiert werden und erfordert Schutzmaßnahmen für die Medien. Die Speicherung von Sicherungen im Azure-Blob-Speicher stellt eine sofortige, hochverfügbare und dauerhafte Archivierungsoption dar.
* Kein Mehraufwand für Hardwareverwaltung: Bei Azure-Diensten fällt kein Mehraufwand für Hardwareverwaltung an. Azure-Dienste verwalten die Hardware und stellen Georeplikation für Redundanz und Schutz vor Hardwareausfällen zur Verfügung.
* SQL Server-Instanzen, die auf einem virtuellen Azure-Computer ausgeführt werden, können derzeit durch Erstellung angeschlossener Datenträger in Azure-Blob-Speicherdiensten gesichert werden. Die Anzahl der Datenträger, die für Sicherungen an einen virtuellen Azure-Computer angeschlossen werden können, ist allerdings begrenzt. Bei einer sehr großen Instanz beträgt die maximale Anzahl 16 Datenträger, während kleinere Instanzen weniger Datenträger unterstützen. Durch die Aktivierung der direkten Sicherung in Azure-Blobs haben Sie Zugriff auf praktisch unbegrenzten Speicher.
* In Azure-Blobs gespeicherte Sicherungen sind jederzeit von überall aus erreichbar, um ganz einfach auf einem lokalen SQL Server oder einem anderen, auf einem virtuellen Azure-Computer ausgeführten SQL Server eine Wiederherstellung durchzuführen, ohne dass Datenbanken angefügt/getrennt werden müssen oder die VHD heruntergeladen und angeschlossen werden müssen.
* Kostenvorteile: Sie zahlen nur für den tatsächlich genutzten Dienst. Die Option kann genauso kosteneffizient wie eine Offsitesicherungs-/-archivierungslösung sein. Weitere Informationen finden Sie im [Azure-Preisrechner](http://go.microsoft.com/fwlink/?LinkId=277060 "Preisrechner") und in der [Azure-Preisübersicht](http://go.microsoft.com/fwlink/?LinkId=277059 "Preisübersicht").
* Verwenden von Speichermomentaufnahmen: Wenn Datenbankdateien in einem Azure-Blob gespeichert werden und Sie SQL Server 2016 verwenden, können Sie [Dateimomentaufnahme-Sicherung](http://msdn.microsoft.com/library/mt169363.aspx) für nahezu sofortige Sicherungen und äußerst schnelle Wiederherstellung verwenden.

Weitere Details finden Sie unter [SQL Server-Sicherung und -Wiederherstellung mit dem Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkId=271617).

In den folgenden zwei Abschnitten werden der Azure-Blob-Speicherdienst und die SQL Server-Komponenten vorgestellt, die beim Sichern im oder Wiederherstellen aus dem Azure-Blob-Speicherdienst zum Einsatz kommen. Es ist wichtig, die Komponenten zu kennen und zu wissen, wie sie interagieren, wenn Sie Daten im Azure-Blob-Speicherdienst sichern oder daraus wiederherstellen möchten.

Als Erstes muss ein Azure-Konto erstellt werden. Eine ausführliche Anleitung zum Erstellen eines Speicherkontos und zum Durchführen einer einfachen Wiederherstellung mit SQL Server 2014 finden Sie unter [Lernprogramm: SQL Server-Sicherung und -Wiederherstellung im Windows Azure-BLOB-Speicherdienst](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx). Eine ausführliche Anleitung zum Erstellen eines Speicherkontos und zum Durchführen einer einfachen Wiederherstellung mit SQL Server 2014 finden Sie unter [Lernprogramm: SQL Server-Datendateien im Windows Azure-Speicher](https://msdn.microsoft.com/library/dn466438.aspx).

## Komponenten des Azure-Blob-Speicherdiensts

* Speicherkonto: Das Speicherkonto ist der Ausgangspunkt für alle Speicherdienste. Um auf einen Azure-Blob-Speicherdienst zuzugreifen, erstellen Sie als Erstes ein Azure-Speicherkonto. Weitere Informationen zum Azure-Blob-Speicherdienst finden Sie unter [Verwenden des Azure-Blob-Speicherdiensts](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).

* Container: Ein Container stellt einen Satz Blobs als Gruppe bereit und kann eine unbegrenzte Anzahl von Blobs enthalten. Um eine SQL Server-Sicherung in einen Azure-Blob-Dienst zu speichern, muss mindestens ein Stammcontainer erstellt worden sein.

* Blob: Eine Datei von beliebiger Art und Größe Blobs sind über das folgende URL-Format adressierbar: `https://<storage account>.blob.core.windows.net/<container>/<blob>`. Weitere Informationen zu Seiten-Blobs finden Sie unter [Grundlegendes zu Block-BLOBs, Anhang-BLOBS und Seiten-BLOBs](http://msdn.microsoft.com/library/azure/ee691964.aspx).

## SQL Server-Komponenten

* URL: Eine URL gibt einen Uniform Resource Identifier (URI) zu einer eindeutigen Sicherungsdatei an. Die URL dient zur Angabe des Speicherorts und Namens der SQL Server-Sicherungsdatei. Die URL darf nicht auf einen Container, sondern lediglich auf ein Blob verweisen. Wenn das Blob nicht vorhanden ist, wird es erstellt. Bei Angabe eines vorhandenen Blobs schlägt BACKUP fehl, es sei denn, die Option > WITH FORMAT wird angegeben. Nachfolgend sehen Sie ein Beispiel für die URL zur Angabe im BACKUP-Befehl: ****`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Hinweis</b>: HTTPS ist nicht erforderlich, wird jedoch empfohlen. <b>Wichtig</b>: Wenn Sie eine Sicherungsdatei kopieren und in den Azure-Blob-Speicherdienst hochladen, müssen Sie einen Seitenblob als Speichertyp verwenden, falls Sie diese Datei für Wiederherstellungen nutzen möchten. Ein RESTORE aus einem Blockblob schlägt mit einer Fehlermeldung fehl.

* Anmeldeinformationen: Die Informationen, die zum Herstellen einer Verbindung zum Azure-Blob-Speicherdienst und zum Authentifizieren bei diesem erforderlich sind, werden als Anmeldeinformationen gespeichert. Damit SQL Server Sicherungen in einen Azure-Blob schreiben oder daraus wiederherstellen kann, müssen SQL Server-Anmeldeinformationen erstellt werden. Weitere Informationen finden Sie unter [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx).

## SQL Server-Sicherung und -Wiederherstellung mit Azure-Blobs – Konzepte und Aufgaben:

**Konzepte, Überlegungen und Codebeispiele:**

[SQL Server-Sicherung und -Wiederherstellung mit dem Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkId=271617)

**Erste-Schritte-Lernprogramm:**

[Lernprogramm: SQL Server-Datendateien im Windows Azure-Speicher](https://msdn.microsoft.com/library/dn466438.aspx)

**Bewährte Methoden, Problembehandlung:**

[Bewährte Methoden für die Sicherung und Wiederherstellung (Windows Azure-BLOB-Speicherdienst)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=Nov15_HO1-->