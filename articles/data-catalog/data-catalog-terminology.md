<properties
   pageTitle="Azure Data Catalog-Terminologie"
   description="Eine Einführung in die Konzepte und Begriffe, die in der Azure Data Catalog-Dokumentation verwendet werden."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/20/2015"
   ms.author="maroche"/>

# Azure Data Catalog-Terminologie

## Katalog

Azure Data Catalog ist ein cloudbasiertes Metadatenrepository, in dem Datenquellen und Datenassets registriert werden können. Der Katalog dient als zentraler Speicherort für strukturelle Metadaten, die aus Datenquellen extrahiert werden, und für beschreibende Metadaten, die von Benutzern hinzugefügt werden.

## Datenquelle

Eine Datenquelle ist ein System oder ein Container zum Verwalten von Datenassets. Beispiele hierfür sind SQL Server-Datenbanken, Oracle-Datenbanken, SQL Server Analysis Services-Datenbanken (tabellarisch oder mehrdimensional) und SQL Server Reporting Services-Server.

## Datenasset

Datenassets sind Objekte in Datenquellen, die im Katalog registriert werden können. Beispiele hierfür sind SQL Server-Tabellen und -Sichten, Oracle-Tabellen und -Sichten, SQL Server Analysis Services-Measures, -Dimensionen und -KPIs sowie SQL Server Reporting Services-Berichte.

## Speicherort des Datenassets

Im Katalog wird der Speicherort einer Datenquelle oder eines Datenassets gespeichert, um mit einer Clientanwendung eine Verbindung zur Quelle herstellen zu können. Das Format und die Details des Speicherorts variieren je nach Typ der Datenquelle. Eine SQL Server-Tabelle kann beispielsweise anhand ihres vierteiligen Namens identifiziert werden – Servername, Datenbankname, Schemaname, Objektname –, während ein SQL Server Reporting Services-Bericht über seine URL identifiziert wird.

## Strukturelle Metadaten

Strukturelle Metadaten sind die Metadaten, die aus einer Datenquelle extrahiert werden. Hiermit wird die Struktur eines Datenassets beschrieben. Dies umfasst den Speicherort des Assets, den Objektnamen und -typ sowie weitere typspezifische Merkmale. Beispielsweise enthalten die strukturellen Metadaten für Tabellen und Sichten die Namen und Datentypen für die Spalten des Objekts.

## Beschreibende Metadaten

Beschreibende Metadaten sind Metadaten, mit denen der Zweck oder die Absicht eines Datenassets beschrieben wird. Normalerweise werden beschreibende Metadaten von Katalogbenutzern über das Azure Data Catalog-Portal hinzugefügt, aber sie können auch während der Registrierung aus der Datenquelle extrahiert werden. Mit dem Azure Data Catalog-Registrierungstool werden Beschreibungen aus der Description-Eigenschaft in SQL Server Analysis Services und SQL Server Reporting Services extrahiert – sowie aus der [erweiterten ms\_description-Eigenschaft](https://technet.microsoft.com/library/ms190243.aspx) in SQL Server-Datenbanken –, sofern diese Eigenschaften mit Werten aufgefüllt wurden.

## Zugriff anfordern

Zu den beschreibenden Metadaten eines Datenassets zählen Informationen zur Zugriffsanforderung auf das Datenasset oder die Datenquelle. Diese Informationen werden zusammen mit dem Speicherort des Datenassets angezeigt. Sie können eine oder mehrere der folgenden Optionen umfassen:

- Die E-Mail-Adresse des für das Gewähren von Zugriff auf die Datenquelle verantwortlichen Benutzers oder Teams.
- Die URL des dokumentierten Prozesses, den Benutzer ausführen müssen, um auf die Datenquelle zugreifen.
- Die URL eines Identitäts- und Zugriffsverwaltungs-Tools (wie z. B. Microsoft Identity Manager), das für den Zugriff auf die Datenquelle verwendet werden kann.
- Eine Freitext-Eintrag, der beschreibt, wie Benutzer auf die Datenquelle zugreifen können.

## Vorschau

Eine Vorschau in Azure Data Catalog ist eine Momentaufnahme von bis zu 20 Datensätzen, die während der Registrierung aus der Datenquelle extrahiert und mit den Metadaten des Datenassets im Katalog gespeichert werden können. Mit der Vorschau können Benutzer, die ein Datenasset ermitteln, die Funktion und den Zweck besser verstehen. Anders ausgedrückt: Angezeigte Beispieldaten können wertvoller als nur Spaltennamen und Datentypen sein. Eine Vorschau wird nur für Tabellen und Sichten unterstützt. Sie muss vom Benutzer während der Registrierung explizit ausgewählt werden.

## Datenprofil

Ein Datenprofil in Azure Data Catalog ist eine Momentaufnahme der Metadaten eines registrierten Datenassets auf Tabellen- und Spaltenebene, die während der Registrierung aus der Datenquelle extrahiert und mit den Metadaten des Datenassets im Katalog gespeichert werden können. Mit dem Datenprofil können Benutzer, die ein Datenasset ermitteln, die Funktion und den Zweck besser verstehen. Ähnlich wie Vorschauen müssen Datenprofile explizit vom Benutzer während der Registrierung ausgewählt werden.

> [AZURE.NOTE]Die Extraktion eines Datenprofils kann bei großen Tabellen und Sichten ein kostspieliger Vorgang sein, und zudem die für das Registrieren einer Datenquelle erforderliche Zeit erheblich erhöhen.

## Perspektive des Benutzers

In Azure Data Catalog können alle Benutzer beschreibende Metadaten für ein registriertes Datenasset angeben. Jeder Benutzer hat eine eigene Sicht auf die Daten und deren Nutzung. Beispielsweise kann ein für einen Server verantwortlicher Administrator die Details der Vereinbarung zum Servicelevel (SLA) oder Sicherungsfenster bereitstellen. Ein Data Steward kann Links zur Dokumentation für die Geschäftsprozesse angeben, die mit den Daten unterstützt werden. Ein Analyst kann eine Beschreibung mit den Angaben bereitstellen, die für andere Analysten am relevantesten sind und sehr hilfreich für Benutzer sein können, die die Daten ermitteln und verstehen müssen.

Jede dieser Perspektiven ist sehr wertvoll, und mit Azure Data Catalog kann jeder Benutzer die Informationen angeben, die für ihn aussagekräftig sind. Alle Benutzer können diese Informationen nutzen, um die Daten und ihren Zweck zu verstehen.

## Experten

Ein Experte ist ein Benutzer, der in Bezug auf ein Datenasset über eine fundierte "Expertensicht" verfügt. Alle Benutzer können sich selbst oder andere Benutzer als Experten für ein Asset hinzufügen. Die Angabe als Experte ist in Azure Data Catalog nicht mit weiteren Privilegien verbunden. Sie ermöglicht Benutzern beim Prüfen der beschreibenden Metadaten eines Assets das einfache Finden der Angaben, die voraussichtlich am nützlichsten sind.

## Besitzer

Ein Besitzer ist ein Benutzer mit zusätzlichen Berechtigungen zum Verwalten eines Datenassets in Azure Data Catalog. Benutzer können registrierte Datenassets in Besitz nehmen, und Besitzer können andere Benutzer als Mitbesitzer hinzufügen.
> [AZURE.NOTE]Diese Besitz- und Verwaltungsfunktionen sind nur in der Standard Edition von Azure Data Catalog verfügbar.

## Registrierung

Die Registrierung umfasst das Extrahieren der Metadaten eines Datenassets aus einer Datenquelle und das Kopieren in den Azure Data Catalog-Dienst. Datenassets, die registriert wurden, können dann mit Anmerkungen versehen und ermittelt werden.

## Weitere Informationen

- [Was ist Azure Data Catalog?](data-catalog-what-is-data-catalog.md) Dieser Artikel enthält einen Überblick über den Azure Data Catalog-Dienst, den damit verbundenen Nutzen sowie die unterstützten Szenarien.

- [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md) Dieser Artikel enthält ein umfassendes Lernprogramm zur Verwendung von Azure Data Catalog für die Ermittlung von Datenquellen.

<!---HONumber=Nov15_HO4-->