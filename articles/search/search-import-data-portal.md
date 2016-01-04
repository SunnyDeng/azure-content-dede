<properties
	pageTitle="Importieren von Daten in Azure Search über das Portal | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Hochladen von Daten in einen Index in Azure Search über das Portal"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="Azure Classic Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importieren von Daten in Azure Search über das Portal
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Das klassische Azure-Portal bietet den Befehl **Daten importieren** im Azure Search-Dashboard, der Sie durch die Erfassung von Daten in Azure Search begleitet. Der Befehl setzt auf der integrierten Indexerfunktion auf, die eine vorhandene Datenquelle durchforstet und Dokumente basierend auf dem in der Datenquelle gefundenen Rowset erstellt oder hochlädt.

Bei Verwenden des Assistenten besteht der Datenimport aus drei Teilen:

- einer Datenquellenverbindung
- einem Zielindex, in den Daten hochgeladen werden (der Assistent kann diesen für Sie häufig generieren)
- einem Zeitplan, der jetzt oder in regelmäßigen Abständen befolgt wird

Zum Verwenden eines Indexers oder des Befehls **Daten importieren** muss Ihre primäre Datenquelle eine der folgenden unterstützten Datenquellen sein: Azure SQL-Datenbank, relationale SQL Server-Datenbank in einer Azure-VM oder Azure DocumentDB.

Sie können Daten nur aus einer einzelnen Tabelle, Sicht oder entsprechenden Datenstruktur importieren. Sie müssen diese Datenstruktur ggf. zunächst in Ihrer Anwendungsdatenquelle erstellen, damit die richtigen Metadaten und Dateneingaben in Ihren Suchindex gelangen.

##Konfigurieren des Datenimports

1. Melden Sie sich am [klassischen Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Im Anschluss finden Sie einige Möglichkeiten, um zum Dashboard zu gelangen.
	- Klicken Sie auf der Navigationsleiste auf **Startseite**. Auf der Startseite befinden sich die Kacheln für die in Ihrem Abonnement enthaltenen Dienste. Klicken Sie auf die Kachel, um das Service-Dashboard zu öffnen.
	- Klicken Sie auf der Navigationsleiste auf **Alle durchsuchen** > **Filtern nach** > **Search-Dienste**, um Ihren Search-Dienst in der Liste zu finden.

3. Im oberen Bereich des Service-Dashboards befindet sich eine Befehlsleiste mit dem Befehl **Daten importieren**. Klicken Sie auf **Daten importieren**, um das Blatt „Daten importieren“ zu öffnen.

4. Klicken Sie auf **Mit Ihren Daten verbinden**, um eine von einem Indexer verwendete Datenquellendefinition anzugeben. Folgende Optionen sind verfügbar:
	- 	„Vorhandene Datenquelle“ bezieht sich auf eine zuvor für einen Indexer erstellte Datenquellendefinition. Wenn Sie in Ihrem Suchdienst bereits Indexer definiert haben, können Sie eine Datenquellendefinition für einen anderen Importvorgang wiederverwenden.
	- 	„Azure SQL“ wird verwendet, um eine Datenquellenverbindung mit einer SQL-­Datenbank in Azure oder SQL Server-Datenbank in einer Azure-VM anzugeben. 
	- 	„DocumentDB“ wird verwendet, um eine Datenquellenverbindung für diesen Datenquellentyp anzugeben. 

   Für „Azure SQL“ und „DocumentDB“ muss die Datenbank in Ihrem Abonnement vorhanden sein. Sie können eine Verbindungszeichenfolge einfügen, wenn Sie Ihnen bekannt ist. Oder wählen Sie eine Datenquelle, die zuvor von jemand erstellt wurde, der über Schreibrechte für Ihr Abonnement verfügt.

5. Klicken Sie auf **Zielindex anpassen**, um den Standardindex fertigzustellen.
	- **Schlüssel** ist ein Pflichtfeld. Das Feld, das Sie für den Schlüssel auswählen, muss ein Zeichenfolgenfeld mit eindeutigen Werten sein.
	- Der Feldname und -typ werden meist für Sie ausgefüllt. Sie können den Datentyp ändern.
	- Wählen Sie Attribute für die einzelnen Felder:
		- „Abrufbar“ gibt das Feld in Suchergebnissen zurück.
		- „Filterbar“ ermöglicht, dass in Filterausdrücken auf das Feld verwiesen wird.
		- „Sortierbar“ ermöglicht, dass das Feld in einer Sortierung verwendet wird.
		- „Facettierbar“ aktiviert das Feld für die Facettennavigation.
		- „Durchsuchbar“ aktiviert die Volltextsuche.
	- Klicken Sie auf die Registerkarte **Analyse**, wenn Sie eine Sprachanalyse auf Feldebene angeben möchten. Details finden Sie unter [Erstellen eines Indexes für Dokumente in mehreren Sprachen](search-language-support.md).
	- Klicken Sie auf die **Vorschläge**, wenn Sie die automatische Vervollständigung bzw. Eingabevorschläge für Abfragen aktivieren möchten.

6. Klicken Sie auf **Daten importieren**, um den Importvorgang über die Option „Jetzt ausführen“ auszuführen, oder richten Sie einen Wiederholungszeitplan ein.

Beim gerade abgeschlossenen Datenimportvorgang wird im Hintergrund ein Indexer erstellt. Sie können nun den Indexer direkt bearbeiten und seine Bestandteile ändern.
	
##Bearbeiten eines vorhandenen Indexers

Doppelklicken Sie im Dashboard des Diensts auf die Kachel „Indexer“, um eine Liste aller Indexer einzublenden, die für Ihr Abonnement erstellt wurden. Doppelklicken Sie auf einen der Indexer, um ihn auszuführen, zu bearbeiten oder zu löschen.

<!---HONumber=AcomDC_1203_2015-->