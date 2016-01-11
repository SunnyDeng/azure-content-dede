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
   ms.date="12/18/2015"
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
* Azure Data Lake-Speicher
* HDFS-Dateien und -Verzeichnisse
* Apache Hive-Tabellen
* Teradata-Tabellen und -Sichten
* Azure Data Lake-Speicherdatei und -Verzeichnisse
* MySQL-Tabellen und -Sichten

> [AZURE.NOTE]SQL Server unterstützt außerdem Microsoft Azure SQL-Datenbank sowie Microsoft Azure SQL Data Warehouse.

<br/>

> [AZURE.NOTE]Unterstützung für SQL Server Reporting Services gilt nur für Server im einheitlichen Modus. Der SharePoint-Modus wird noch nicht unterstützt.

<br/>


## Strukturelle Metadaten
Wenn Sie eine Datenquelle registrieren, extrahiert das Registrierungstool Informationen zur Struktur des ausgewählten Objekts, die als strukturelle Metadaten bezeichnet werden.

Für alle Objekte enthalten diese strukturellen Metadaten den jeweiligen Speicherort des Objekts, damit Benutzer, die diese Daten ermitteln, mit ihrer Hilfe die Verbindung mit dem Objekt in den Clienttools ihrer Wahl herstellen können. Andere strukturelle Metadaten sind der Objektname und -typ, der Attribut-/Spaltenname und der Datentyp.

## Beschreibende Metadaten
Zusätzlich zu den aus der Datenquelle extrahierten strukturellen Metadaten extrahiert das Registrierungstool für Datenquellen auch beschreibende Metadaten. Für SQL Server Analysis Services und SQL Server Reporting Services stammen diese aus den Beschreibungseigenschaften, die von diesen Diensten verfügbar gemacht werden. Für SQL Server werden Werte extrahiert, die von der erweiterten Eigenschaft "ms\_description" bereitgestellt werden. Für Oracle Database extrahiert das Registrierungstool für Datenquellen die Spalte COMMENTS aus der Sicht ALL\_TAB\_COMMENTS.

Zusätzlich zu den aus der Datenquelle extrahierten beschreibenden Metadaten können Benutzer mit dem Registrierungstool für Datenquellen auch beschreibende Metadaten eingeben. Benutzer können Tags hinzufügen und Experten für die registrierten Objekte bestimmen. Alle diese beschreibenden Metadaten werden zusammen mit den strukturellen Metadaten in den **Azure Data Catalog**-Dienst kopiert.

## Einschließen einer Vorschau

In der Standardeinstellung werden nur Metadaten aus Datenquellen extrahiert und in den **Azure Data Catalog**-Dienst kopiert. Doch eine Datenquelle lässt sich häufig anhand eines Beispiels der darin enthaltenen Daten veranschaulichen.

Mit dem **Azure Data Catalog**-Tool zum Registrieren von Datenquellen können Benutzer eine Vorschau der Daten in Form einer Momentaufnahme jeder registrierten Tabelle oder Sicht hinzufügen. Wenn sich der Benutzer während der Registrierung für das Einschließen von Vorschauen entscheidet, fügt das Registrierungstool bis zu 20 Datensätze aus jeder Tabelle oder Sicht hinzu. Diese Momentaufnahme wird dann zusammen mit den strukturellen und beschreibenden Metadaten in den Katalog kopiert.


> [AZURE.NOTE]Für eine breite Tabellen mit einer großen Anzahl von Spalten werden möglicherweise weniger als 20 Datensätze in deren Vorschau eingefügt.


## Einschließen von Datenprofilen

Ähnlich wie ein Einschließen von Vorschauen wertvollen Kontext für Benutzer bereitstellen kann, die in **Azure Data Catalog** nach Datenquellen suchen, lässt sich auch durch Einschließen eines Datenprofils erreichen, die ermittelten Datenquellen einfacher verstehen zu können.

Mit dem **Azure Data Catalog**-Tool zum Registrieren von Datenquellen können Benutzer für jede registrierte Tabelle oder Sicht ein Datenprofil hinzufügen. Trifft der Benutzer die Wahl, während der Registrierung ein Datenprofil hinzuzufügen, fügt das Registrierungstool aggregierte Statistiken über die Daten in jeder Tabelle oder Ansicht hinzu. Hierzu gehören:

* Die Anzahl von Zeilen und die Größe der Daten im Objekt
* Das Datum der letzten Aktualisierung der Daten und das Objektschema
* Die Anzahl von Datensätzen mit Nullwerten und die Anzahl von unterschiedlichen Werten für Spalten
* Die kleinsten, größten, Durchschnitts- und Standardabweichungswerte für Spalten

Diese statistischen Daten werden dann zusammen mit den strukturellen und beschreibenden Metadaten in den Katalog kopiert.

> [AZURE.NOTE]Für Text- oder Datumsspalten werden keine Durchschnitts- und Standardabweichungswerte in deren Datenprofile eingeschlossen.

## Aktualisieren von Registrierungen

Durch das Registrieren einer Datenquelle kann diese in **Azure Data Catalog** anhand der Metadaten und der optionalen Vorschau ermittelt werden, die während der Registrierung extrahiert wurden. Wenn die Datenquelle im Katalog aktualisiert werden muss (z. B. wenn das Schema eines Objekts geändert wurde, ursprünglich ausgeschlossene Tabellen eingeschlossen werden sollen oder ein Benutzer die in der Vorschau enthaltenen Daten aktualisieren möchte), kann das Registrierungstool für Datenquellen erneut ausgeführt werden.

Beim erneuten Registrieren einer bereits registrierten Datenquelle werden neue Objekte erstellt, während vorhandene Objekte aktualisiert werden. Alle von Benutzern über das **Azure Data Catalog**-Portal bereitgestellten Metadaten bleiben erhalten.

## Zusammenfassung
Durch die Registrierung einer Datenquelle in **Azure Data Catalog** werden strukturelle und beschreibende Metadaten aus der Datenquelle in den Katalogdienst kopiert, sodass die Datenquelle leichter zu ermitteln und zu verstehen ist. Nachdem eine Datenquelle registriert wurde, kann sie im **Azure Data Catalog**-Portal mit Anmerkungen versehen, verwaltet und ermittelt werden.

<!---HONumber=AcomDC_1223_2015-->