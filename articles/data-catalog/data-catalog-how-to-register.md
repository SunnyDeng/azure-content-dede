<properties
   pageTitle="Registrieren von Datenquellen"
   description="Anleitungsartikel mit Informationen zum Registrieren von Datenquellen bei Azure Data Catalog, einschließlich der extrahierten Metadatenfelder und von der Vorschauversion unterstützten Datenquellen."
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
   ms.date="08/19/2015"
   ms.author="maroche"/>


# Registrieren von Datenquellen

## Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog**, den Benutzern zu helfen, Datenquellen zu ermitteln, zu verstehen und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Der erste Schritt, dass eine Datenquelle über **Azure Data Catalog** ermittelt werden kann, besteht darin, die Datenquelle zu registrieren.
## Registrieren von Datenquellen
Die Registrierung umfasst das Extrahieren der Metadaten aus der Datenquelle und das Kopieren der Daten in den **Azure Data Catalog**-Dienst. Die Daten verbleiben, wo sie sich derzeit befinden, und unter der Kontrolle der Administratoren und Richtlinien des aktuellen Systems.

Um eine Datenquelle zu registrieren, starten Sie einfach im **Azure Data Catalog**-Portal das **Azure Data Catalog**-Tool für die Registrierung von Datenquellen. Melden Sie sich mit Ihrem Geschäfts- oder Schulkonto (den gleichen Azure Active Directory-Anmeldeinformationen wie bei der Anmeldung beim Portal) an, und wählen Sie dann die Datenquelle aus, die Sie registrieren möchten. Weitere detaillierte Informationen finden Sie im Tutorial [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md).

Nachdem die Datenquelle registriert wurde, verfolgt der Katalog ihren Speicherort nach und indiziert ihre Metadaten. Dadurch können Benutzer die Datenquelle suchen, durchsuchen und ermitteln und dann ihren Speicherort zum Herstellen einer Verbindung mit der Anwendung oder dem Tool ihrer Wahl verwenden.
## Unterstützte Datenquellen
In der aktuellen Vorschauversion unterstützt **Azure Data Catalog** die Registrierung dieser Datenquellen und Objekttypen:

* SQL Server-Datenbankmodultabellen und -sichten
* Oracle-Datenbanktabellen und -sichten
* SQL Server Analysis Services (Mehrdimensionale Dimensionen, Measures und KPIs)
* SQL Server Analysis Services – Tabellarische Tabellen
* SQL Server Reporting Services-Berichte
* Azure-Speicherblobs und -Verzeichnisse

> [AZURE.NOTE]SQL Server unterstützt außerdem Microsoft Azure SQL-Datenbank.

<br/>

> [AZURE.NOTE]Unterstützung für SQL Server Reporting Services gilt nur für Server im einheitlichen Modus. Der SharePoint-Modus wird noch nicht unterstützt.

<br/>

## Strukturelle Metadaten
Wenn Sie eine Datenquelle registrieren, extrahiert das Registrierungstool Informationen zur Struktur des ausgewählten Objekts, die als strukturelle Metadaten bezeichnet werden. Für alle Objekte enthalten diese strukturellen Metadaten den Speicherort des Objekts, damit Clienttools die Daten ermitteln können. Andere strukturelle Metadaten sind der Objektname und -typ, der Attribut-/Spaltenname und der Datentyp.
## Beschreibende Metadaten
Zusätzlich zu den aus der Datenquelle extrahierten strukturellen Metadaten extrahiert das Registrierungstool für Datenquellen auch beschreibende Metadaten. Für SQL Server Analysis Services und SQL Server Reporting Services stammen diese aus den Beschreibungseigenschaften, die von diesen Diensten verfügbar gemacht werden. Für SQL Server werden Werte extrahiert, die von der erweiterten Eigenschaft "ms\_description" bereitgestellt werden. Für Oracle Database extrahiert das Registrierungstool für Datenquellen die Spalte COMMENTS aus der Sicht ALL\_TAB\_COMMENTS.

Zusätzlich zu den aus der Datenquelle extrahierten beschreibenden Metadaten können Benutzer mit dem Registrierungstool für Datenquellen auch beschreibende Metadaten eingeben. Benutzer können Tags hinzufügen und Experten für die registrierten Objekte bestimmen. Alle diese beschreibende Metadaten werden zusammen mit den strukturellen Metadaten in den **Azure Data Catalog**-Dienst kopiert.
## Einschließen einer Vorschau
In der Standardeinstellung werden nur aus Datenquellen extrahierte Metadaten in den **Azure Data Catalog**-Dienst kopiert. Doch eine Datenquelle wird häufig anhand eines Beispiels der darin enthaltenen Daten veranschaulicht.

Das Registrierungstool für Datenquellen des **Azure Data Catalog**-Diensts ermöglicht Benutzern, eine Vorschau der Daten in Form einer Momentaufnahme jeder registrierten Tabelle oder Ansicht hinzuzufügen. Wenn sich der Benutzer während der Registrierung für das Einschließen der Vorschau entscheidet, fügt das Registrierungstool bis zu 20 Datensätze aus jeder Tabelle oder Sicht hinzu. Diese Momentaufnahme wird dann zusammen mit den strukturellen und beschreibenden Metadaten in den Katalog kopiert. Hinweis: Bei der Vorschau auf breite Tabellen mit einer großen Anzahl von Spalten werden möglicherweise weniger als 20 Datensätze gezeigt.
## Aktualisieren von Registrierungen
Durch das Registrieren einer Datenquelle kann sie in **Azure Data Catalog** mithilfe der Metadaten und optionalen Vorschau ermittelt werden, die während der Registrierung extrahiert werden. Wenn die Datenquelle im Katalog aktualisiert werden muss (z. B. wenn das Schema eines Objekts geändert wurde, ursprünglich ausgeschlossene Tabellen eingeschlossen werden sollen oder ein Benutzer die in der Vorschau enthaltenen Daten aktualisieren möchte), kann das Registrierungstool für Datenquellen erneut ausgeführt werden.

Beim erneuten Registrieren einer bereits registrierten Datenquelle werden neue Objekte erstellt, während vorhandene Objekte aktualisiert werden. Alle von Benutzern über das **Azure Data Catalog**-Portal bereitgestellten Metadaten bleiben erhalten.

## Zusammenfassung
Durch Registrieren eine Datenquelle im **Azure Data Catalog** ist es einfacher, diese Datenquelle zu ermitteln und zu verstehen, denn strukturelle und beschreibende Metadaten aus der Datenquelle werden in den Katalogdienst kopiert. Nachdem eine Datenquelle registriert wurde, kann sie im **Azure Data Catalog**-Portal mit Anmerkungen versehen, verwaltet und ermittelt werden.

<!---HONumber=August15_HO8-->