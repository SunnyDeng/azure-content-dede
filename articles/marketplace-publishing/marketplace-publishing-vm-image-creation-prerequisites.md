<properties
   pageTitle="Technische Voraussetzungen für das Erstellen eines VM-Images für den Azure Marketplace | Microsoft Azure"
   description="Grundlegendes zu den Anforderungen für das Erstellen und Bereitstellen eines VM-Images im Azure Marketplace, damit dieses von anderen Benutzern erworben werden kann."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="10/09/2015"
  ms.author="hascipio; v-divte"/>

# Technische Voraussetzungen für das Erstellen eines VM-Images für den Azure Marketplace
Lesen Sie die Informationen zum Prozess vor Beginn sorgfältig durch, um nachvollziehen zu können, wo und warum die einzelnen Schritte ausgeführt werden. Bereiten Sie nach Möglichkeit Ihre Unternehmensinformationen und andere Daten vor, laden Sie die erforderlichen Tools herunter, und/oder erstellen Sie technische Komponenten, bevor Sie mit der Angebotserstellung beginnen. Diese Schritte werden in diesem Artikel erläutert.

## Herunterladen der erforderlichen Tools und Anwendungen
Sie sollten die folgenden Aufgaben ausgeführt haben, bevor Sie mit dem hier beschriebenen Verfahren beginnen:

- Installieren Sie abhängig vom Zielbetriebssystem die Azure PowerShell-Cmdlets oder die Linux-Befehlszeilenschnittstelle über die Downloadseite von Azure.
- Laden Sie den Azure-Speicher-Explorer aus CodePlex herunter.
- Laden Sie das "Certification Test Tool for Azure Certified" (in englischer Sprache) herunter:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Sie benötigen einen Windows-basierten Computer zum Ausführen des Zertifizierungstools. Wenn kein Windows-basierter Computer zur Verfügung steht, können Sie das Tool unter Verwendung eines Windows-basierten virtuellen Computers in Azure ausführen.

## Unterstützte Plattformen
Sie können Azure-basierte virtuelle Computer unter Windows oder Linux erstellen. Für einige Aufgaben im Veröffentlichungsprozess – beispielsweise für das Erstellen einer mit Azure kompatiblen VHD – werden je nach verwendetem Betriebssystem andere Tools und Schritte verwendet.

- Wenn Sie Linux verwenden, finden Sie weitere Informationen im Abschnitt "Erstellen einer mit Azure kompatiblen VHD (Linux-basiert)" im [Leitfaden zum Veröffentlichen von VM-Images](marketplace-publishing-vm-image-creation.md).
- Bei Einsatz von Windows finden Sie weitere Informationen im Abschnitt "Erstellen einer mit Azure kompatiblen VHD-Datei (Windows-basiert)" im [Leitfaden zum Veröffentlichen von VM-Images](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE]Sie benötigen Zugriff auf einen Windows-Computer, um folgende Aufgaben auszuführen:  
– Ausführen des Tools für die Zertifizierungsprüfung  
– Erstellen der VHD-SAS-URL zum Übermitteln der VHD-Zertifizierung

## Entwickeln von VHDs
Es ist möglich, Azure-VHDs in der **Cloud** oder **lokal** zu entwickeln.

- Bei der cloudbasierten Entwicklung werden alle Entwicklungsschritte remote ausgeführt, die VHDs befinden sich in Azure.
- Für die lokale Entwicklung müssen Sie eine VHD herunterladen und mithilfe der lokalen Infrastruktur entwickeln. Diese Vorgehensweise ist möglich, wird jedoch nicht empfohlen. Beachten Sie, dass bei einer lokalen Entwicklung für Windows oder SQL geeignete lokale Lizenzschlüssel vorliegen müssen. Sie können SQL Server nicht nach dem Erstellen eines virtuellen Computers einschließen oder installieren, und Ihr Angebot muss auf einem genehmigten SQL-Image aus dem Azure-Portal basieren. Wenn Sie sich für eine lokale Entwicklung entscheiden, müssen einige Schritte anders ausgeführt werden als bei der Entwicklung in der Cloud. Weitere Informationen hierzu finden Sie unter [Erstellen eines lokalen VM-Images](marketplace-publishing-vm-image-creation-on-premise.md).

## Nächste Schritte
Sie haben die Voraussetzungen überprüft und die erforderlichen Aufgaben ausgeführt und können nun mit dem Erstellen eines Angebots für Ihr VM-Image fortfahren. Ausführliche Informationen finden Sie unter [Leitfaden zum Veröffentlichen von VM-Images](marketplace-publishing-vm-image-creation.md).

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)
- [Erstellen eines virtuellen Computers unter Windows im Azure-Vorschauportal](../virtual-machines/virtual-machines-windows-tutorial/)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Nov15_HO3-->