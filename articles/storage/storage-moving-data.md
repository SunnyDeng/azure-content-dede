<properties
	pageTitle="Verschieben von Daten in und aus Azure Storage | Microsoft Azure"
	description="Dieser Artikel bietet eine Übersicht über die verschiedenen Methoden zum Verschieben von Daten in und aus Azure Storage."
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="micurd"/>

# Verschieben von Daten in und aus Azure Storage

Wenn Sie lokale Daten in Azure Storage verschieben möchten (oder umgekehrt), steht Ihnen eine Vielzahl von Methoden zur Verfügung. Es hängt vom jeweiligen Szenario ab, welche Methode für Sie am besten geeignet ist. Dieser Artikel bietet eine kurze Übersicht über die verschiedenen Szenarien und die jeweiligen Angebote.

## Erstellen von Anwendungen

Wenn Sie eine Anwendung erstellen, ist das Entwickeln mit der REST-API oder einer unserer vielen Clientbibliotheken eine hervorragende Möglichkeit zum Verschieben von Daten in und aus Azure Storage.

Azure Storage bietet umfangreiche Clientbibliotheken für .NET, iOS, Java, Android, die universelle Windows-Plattform (UWP), Xamarin, C++, Node.JS, PHP, Ruby und Python. Die Clientbibliotheken bieten erweiterte Funktionen, beispielsweise Wiederholungslogik, Protokollierung und parallele Uploads. Die Entwicklung kann direkt mit der REST-API erfolgen. Diese API lässt sich mithilfe jeder Sprache aufrufen, die HTTP/HTTPS-Anforderungen verarbeitet.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](storage-dotnet-how-to-use-blobs.md).

## Schnelles Anzeigen/Interagieren mit den Daten

Wenn Sie Ihre Azure Storage-Daten auf einfache Weise anzeigen und gleichzeitig die Möglichkeit haben möchten, Ihre Daten hoch- und herunterzuladen, sollten Sie einen Azure-Speicher-Explorer verwenden.

In der Liste der [Azure-Speicher-Explorer](storage-explorers.md) finden Sie weitere Informationen.

## Systemadministration

Wenn Sie ein Befehlszeilenprogramm benötigen oder mit einem solchen Programm besser vertraut sind (z. B. Systemadministratoren), stehen Ihnen u. a. die folgenden Optionen zur Auswahl:

### AzCopy

AzCopy ist ein Windows-Befehlszeilenprogramm, mit dem sehr effizient Daten in und aus Azure Storage kopiert werden können. Sie können auch Daten innerhalb eines Speicherkontos oder zwischen Speicherkonten kopieren.

Weitere Informationen finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md).

### Microsoft Azure PowerShell

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Diensten in Azure bereitstellt. Bei diesem Modul handelt es sich um eine aufgabenbasierte Befehlszeilenshell und Skriptsprache, die speziell für die Systemverwaltung entwickelt wurde.

Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md).

### Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit Azure-Diensten bereit. Die Azure-Befehlszeilenschnittstelle ist unter Windows, OS X und Linux verfügbar.

Weitere Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher](storage-azure-cli.md).

## Verschieben großer Datenmengen in einem langsamen Netzwerk

Eines der größten Probleme beim Verschieben großer Datenmengen ist die Zeit für die Übertragung. Wenn Sie Daten in bzw. aus Azure Storage übertragen möchten, ohne die Kosten für Netzwerke bedenken oder Code schreiben zu müssen, ist Azure Import/Export eine geeignete Lösung.

Weitere Informationen finden Sie unter [Azure Import/Export](storage-import-export-service.md).

## Sichern der Daten

Wenn Sie einfach Ihre Daten in Azure Storage sichern müssen, ist Azure Backup die beste Wahl. Dies ist eine leistungsfähige Lösung zum Sichern von lokalen Daten und Azure-VMs.

Weitere Informationen finden Sie unter [Azure Backup](../backup/backup-introduction-to-azure-backup.md).

## Zugreifen auf Ihre Daten lokal und über die Cloud

Wenn Sie eine Lösung für den Zugriff auf Ihre Daten lokal und über die Cloud benötigen, dann sollten Sie StorSimple, die hybride Cloudspeicherlösung von Azure, in Betracht ziehen. Diese Lösung besteht aus einem physischen StorSimple-Gerät, auf dem auf intelligente Weise häufig verwendete Daten auf SSDs, gelegentlich verwendete Daten auf HDDs und inaktive/Backup-/Archivdaten in Azure Storage gespeichert werden.

Weitere Informationen finden Sie unter [StorSimple](../storsimple/storsimple-overview.md).

## Wiederherstellen der Daten

Wenn Sie über lokale Workloads und Anwendungen verfügen, benötigen Sie eine Lösung, die Geschäftskontinuität im Falle eines Notfalls ermöglicht. Azure Site Recovery steuert Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server. Replizierte Daten werden in Azure Storage gespeichert, sodass Sie kein sekundäres lokales Rechenzentrum benötigen.

Weitere Informationen finden Sie unter [Azure Site Recovery](../site-recovery/site-recovery-overview.md).

<!---HONumber=AcomDC_0323_2016-->