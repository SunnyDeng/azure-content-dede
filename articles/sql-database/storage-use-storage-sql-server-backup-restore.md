<properties
	pageTitle="Gewusst wie: Verwenden von Azure Storage für die SQL Server-Sicherung und -Wiederherstellung | Azure"
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
	ms.date="07/16/2015"
	ms.author="carlrab"/>



# Verwenden von Azure-Speicher für SQL Server-Sicherung und -Wiederherstellung

## Übersicht

Die Funktion, die das Schreiben von SQL Server-Sicherungen in den Azure-Blob-Speicherdienst ermöglicht, wurde in SQL Server 2012 SP1 CU2 eingeführt. Mithilfe dieser Funktion können Sie Daten aus einer lokalen SQL Server-Datenbank oder einer SQL Server-Datenbank auf einem virtuellen Azure-Computer im Azure-Blob-Dienst sichern und daraus wiederherstellen. Die Sicherung in der Cloud bietet zahlreiche Vorteile: Verfügbarkeit, unbegrenzter georeplizierter Offsitespeicher und problemlose Migration von Daten in die und aus der Cloud. In dieser Version können Sie BACKUP- oder RESTORE-Anweisungen mit T-SQL oder SMO ausgeben.

## Vorteile der Verwendung des Azure-Blob-Diensts für SQL Server-Sicherungen

Speicherverwaltung, Risiko eines Speicherausfalls, Zugriff auf Offsitespeicher und Konfiguration von Geräten – dies sind nur einige der generellen Probleme bei Sicherungen. Bei einem auf einem virtuellen Azure-Computer ausgeführten SQL Server stellen die Konfiguration und Sicherung einer VHD oder die Konfiguration angeschlossener Laufwerke zusätzliche Herausforderungen dar. Im Folgenden werden einige der wichtigsten Vorteile aufgeführt, die die Verwendung des Azure-Blob-Speicherdiensts für SQL Server-Sicherungen bietet:

* Flexibler, zuverlässiger und unbegrenzter Offsitespeicher: Das Speichern von Sicherungen im Azure-Blob-Dienst ist eine praktische, flexible und leicht zugängliche Option für die Offsitespeicherung. Die Erstellung von Offsitespeicher für SQL Server-Sicherungen erfordert ggf. lediglich das Ändern vorhandener Skripts/Jobs. Offsitespeicher sollte normalerweise weit genug vom Standort der Produktionsdatenbank entfernt sein, um zu verhindern, dass ein einziger Notfall sich auf beide Standorte (den des Offsitespeichers und den der Produktionsdatenbank) auswirkt. Die geografische Replikation des Blob-Speichers bietet zusätzlichen Schutz im Fall eines Notfalls, der sich auf die gesamte Region auswirkt. Außerdem stehen Sicherungen überall und jederzeit zur Verfügung und können problemlos für Wiederherstellungen aufgerufen werden.
* Sicherungsarchiv: Der Azure-Blob-Speicherdienst bietet eine bessere Alternative als die sonst übliche Archivierung von Sicherungen auf Bändern. Bandspeicher muss ggf. physisch an einen Offsitestandort transportiert werden und erfordert Schutzmaßnahmen für die Medien. Die Speicherung von Sicherungen im Azure-Blob-Speicher stellt eine sofortige, hochverfügbare und dauerhafte Archivierungsoption dar.
* Kein Mehraufwand für Hardwareverwaltung: Bei Azure-Diensten fällt kein Mehraufwand für Hardwareverwaltung an. Azure-Dienste verwalten die Hardware und stellen geografische Replikation für Redundanz und Schutz vor Hardwareausfällen zur Verfügung.
* SQL Server-Instanzen, die auf einem virtuellen Windows Azure-Computer ausgeführt werden, können derzeit durch Erstellung angeschlossener Datenträger in Azure-Blob-Speicherdiensten gesichert werden. Die Anzahl der Datenträger, die an einen virtuellen Azure-Computer angeschlossen werden können, ist allerdings begrenzt. Bei einer sehr großen Instanz beträgt die maximale Anzahl 16 Datenträger, während kleinere Instanzen weniger Datenträger unterstützen. Die Beschränkung auf 16 Datenträger kann durch eine direkte Sicherung im Azure-Blob-Speicher umgangen werden.
* Darüber hinaus steht die Sicherungsdatei, die jetzt im Azure-Blob-Speicherdienst gespeichert wird, einem lokalen SQL Server oder einem anderen, auf einem virtuellen Windows Azure-Computer ausgeführten SQL Server direkt zur Verfügung, ohne dass Datenbanken angefügt/getrennt werden müssen oder die VHD heruntergeladen und angeschlossen werden muss.
* Kostenvorteile: Sie zahlen nur für den tatsächlich genutzten Dienst. Die Option kann genauso kosteneffizient wie eine Offsitesicherungs-/-archivierungslösung sein. Weitere Informationen finden Sie im [Azure-Preisrechner](http://go.microsoft.com/fwlink/?LinkId=277060 "Preisrechner") und in der [Azure-Preisübersicht](http://go.microsoft.com/fwlink/?LinkId=277059 "Preisübersicht").

Weitere Details finden Sie unter [SQL Server-Sicherung und -Wiederherstellung mit dem Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkId=271617).

In den folgenden zwei Abschnitten werden der Azure-Blob-Speicherdienst und die SQL Server-Komponenten vorgestellt, die beim Sichern im oder Wiederherstellen aus dem Azure-Blob-Speicherdienst zum Einsatz kommen. Es ist wichtig, die Komponenten zu kennen und zu wissen, wie sie interagieren, wenn Sie Daten im Azure-Blob-Speicherdienst sichern oder daraus wiederherstellen möchten.

Als Erstes muss ein Azure-Konto erstellt werden. SQL Server verwendet den Namen des Azure-Speicherkontos und dessen Zugriffsschlüssel zum Authentifizieren und um Blobs in den Speicherdienst zu schreiben bzw. daraus zu lesen. Diese Authentifizierungsdaten werden in den SQL Server-Anmeldeinformationen gespeichert, die während der Sicherungs- oder Wiederherstellungsvorgänge verwendet werden.

Eine ausführliche Anleitung zum Erstellen eines Speicherkontos und Durchführen einer einfachen Wiederherstellung finden Sie unter [Erste Schritte mit der SQL Server-Sicherung und -Wiederherstellung im Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkId=271615).

## Komponenten des Azure-Blob-Speicherdiensts

* Speicherkonto: Das Speicherkonto ist der Ausgangspunkt für alle Speicherdienste. Um auf einen Azure-Blob-Speicherdienst zuzugreifen, erstellen Sie als Erstes ein Azure-Speicherkonto. Der Name und Zugriffsschlüssel des Speicherkontos werden für die Authentifizierung beim Azure-Blob-Speicherdienst und dessen Komponenten benötigt. Weitere Informationen zum Azure-Blob-Speicherdienst finden Sie unter [Verwenden des Azure-Blob-Speicherdiensts](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).

* Container: Ein Container stellt einen Satz Blobs als Gruppe bereit und kann eine unbegrenzte Anzahl von Blobs enthalten. Um eine SQL Server-Sicherung in einen Azure-Blob-Dienst zu speichern, muss mindestens ein Stammcontainer erstellt worden sein.

* Blob: Eine Datei von beliebiger Art und Größe Es gibt zwei Arten von Blobs, die im Azure-BLOB-Speicher gespeichert werden können: Block- und Seitenblobs. Für die SQL Server-Sicherung werden Seiten-Blobs verwendet. Blobs sind über das folgende URL-Format adressierbar: `https://<storage account>.blob.core.windows.net/<container>/<blob>`. Weitere Informationen zu Seiten-Blobs finden Sie unter [Grundlegendes zu Block-BLOBs, Anhang-BLOBS und Seiten-BLOBs](http://msdn.microsoft.com/library/azure/ee691964.aspx).

## SQL Server-Komponenten

* URL: Eine URL gibt einen Uniform Resource Identifier (URI) zu einer eindeutigen Sicherungsdatei an. Die URL dient zur Angabe des Speicherorts und Namens der SQL Server-Sicherungsdatei. In dieser Implementierung ist lediglich eine URL gültig, die auf einen Seiten-Blob in einem Azure-Speicherkonto verweist. Die URL darf nicht auf ein Blob, sondern lediglich auf einen Container verweisen. Wenn das Blob nicht vorhanden ist, wird es erstellt. Bei Angabe eines vorhandenen Blobs schlägt BACKUP fehl, es sei denn, die Option > WITH FORMAT wird angegeben. Nachfolgend sehen Sie ein Beispiel für die URL zur Angabe im BACKUP-Befehl: **`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>` 
<b>Hinweis:</b> HTTPS ist nicht erforderlich, wird jedoch empfohlen. <b>Wichtig</b> Wenn Sie eine Sicherungsdatei kopieren und in den Azure-Blob-Speicherdienst hochladen, müssen Sie einen Seiten-Blob als Speichertyp verwenden, falls Sie diese Datei für Wiederherstellungen nutzen möchten. Ein RESTORE aus einem Block-Blob schlägt mit einer Fehlermeldung fehl.

* Anmeldeinformationen: Die Informationen, die zum Herstellen einer Verbindung zum Azure-Blob-Speicherdienst und zum Authentifizieren bei diesem erforderlich sind, werden als Anmeldeinformationen gespeichert. Damit SQL Server Sicherungen in einen Azure-Blob schreiben oder daraus wiederherstellen kann, müssen SQL Server-Anmeldeinformationen erstellt werden. In den Anmeldeinformationen werden der Name des Speicherkontos sowie der zugehörige Zugriffsschlüssel gespeichert. Nach Erstellung der Anmeldeinformationen müssen diese bei Ausgabe der BACKUP/RESTORE-Anweisungen in der Option WITH CREDENTIAL angegeben werden. Schritt-für-Schritt-Anweisungen zum Erstellen von SQL Server-Anmeldeinformationen finden Sie unter [Erste Schritte mit der SQL Server-Sicherung und -Wiederherstellung im Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkId=271615).

## SQL Server-Sicherung und -Wiederherstellung mit Azure-Blobs – Konzepte und Aufgaben:

**Konzepte, Überlegungen und Codebeispiele:**

[SQL Server-Sicherung und -Wiederherstellung mit dem Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkId=271617)

**Erste-Schritte-Lernprogramm:**

[Erste Schritte mit der SQL Server-Sicherung und -Wiederherstellung im Windows Azure-BLOB-Speicherdienst](http://go.microsoft.com/fwlink/?LinkID=271615 "Lernprogramm:")

**Bewährte Methoden, Problembehandlung:**

[Bewährte Methoden für die Sicherung und Wiederherstellung (Windows Azure-BLOB-Speicherdienst)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=July15_HO4-->