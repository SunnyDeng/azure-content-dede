<properties
	pageTitle="Erstellen eines Azure Search-Indexes mit dem Azure-Portal | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erstellen Sie einen Azure Search-Index im Azure-Portal."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# Erstellen eines Azure Search-Indexes im Azure-Portal
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

In diesem Artikel lernen Sie, wie Sie einen Azure Search-[Index](search-what-is-an-index.md) mithilfe des Azure-Portals erstellen.

Bevor Sie dieser Anleitung folgen und einen Index erstellen, müssen Sie einen [Azure Search-Dienst erstellen](search-create-service-portal.md).


## I. Wechseln Sie zu Ihrem Azure Search-Blatt.
1. Klicken Sie im Menü auf der linken Seite des [Azure-Portals](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) auf „Alle Ressourcen“.
2. Wählen Sie Ihren Azure Search-Dienst aus.

## II. Fügen Sie den Index hinzu, und benennen Sie ihn.
1. Klicken Sie auf die Schaltfläche „Index hinzufügen“.
2. Benennen Sie den Azure Search-Index. Da wir in dieser Anleitung einen Index für die Suche nach Hotels erstellen, haben wir unseren Index „hotels“ genannt.
  * Der Indexname muss mit einem Buchstaben beginnen und darf nur Kleinbuchstaben, Ziffern oder Bindestriche („-“) enthalten.
  * Ähnlich wie beim Dienstnamen ist auch der von Ihnen gewählte Indexname Bestandteil der Endpunkt-URL, an die Sie Ihre HTTP-Anforderungen für die Azure Search-API senden werden.
3. Klicken Sie auf den Eintrag „Felder“, um ein neues Blatt zu öffnen.

![](./media/search-create-index-portal/add-index.png)


## III. Erstellen und definieren Sie die Felder des Indexes.
1. Wenn Sie den Eintrag „Felder“ auswählen, wird ein neues Blatt mit einem Formular geöffnet, in dem Sie die Indexdefinition eingeben.
2. Fügen Sie Ihrem Index anhand des Formulars Felder hinzu.

  * Für jeden Azure Search-Index ist ein *Schlüsselfeld* vom Typ „Edm.String“ erforderlich. Dieses Schlüsselfeld wird standardmäßig mit dem Feldnamen „id“ erstellt. Wir haben es in unserem Index in „hotelId“ geändert.
  * Bestimmte Eigenschaften des Indexschemas können nur einmal festgelegt und später nicht mehr aktualisiert werden. Aus diesem Grund sind zurzeit jegliche Schemaaktualisierungen, die eine erneute Indizierung nach sich ziehen (z. B. Änderungen der Feldtypen), nach der Anfangskonfiguration nicht möglich.
  * Wir haben die Eigenschaftswerte für jedes Feld ausgehend davon ausgewählt, wie sie unserer Meinung nach in einer Anwendung verwendet werden. Berücksichtigen Sie beim Gestalten des Indexes die Benutzerfreundlichkeit und die geschäftlichen Anforderungen, da jedem Feld die [richtigen Eigenschaften](https://msdn.microsoft.com/library/azure/dn798941.aspx) zugewiesen sein müssen. Über diese Eigenschaften wird gesteuert, welche Suchfunktionen (Filtern, Facettierung, Sortieren, Volltextsuche usw.) für welche Felder gelten. Beispielsweise ist es wahrscheinlich, dass für Personen, die nach Hotels suchen, Übereinstimmungen mit Schlüsselwörtern im Beschreibungsfeld relevant sind. Daher aktivieren wir für dieses Feld die Volltextsuche, indem wir die Eigenschaft „Durchsuchbar“ festlegen.
	* Zudem können Sie für jedes Feld die [Sprachanalyse](https://msdn.microsoft.com/de-DE/library/azure/dn879793.aspx) festlegen, indem Sie am oberen Rand des Blatts auf die Registerkarte „Analyzer“ klicken. Unten sehen Sie, dass wir einen französischen Analyzer für ein Feld in unserem Index ausgewählt haben, das für französischen Text bestimmt ist.

3. Klicken Sie auf dem Blatt „Felder“ auf **OK**, um die Felddefinitionen zu bestätigen.
4. Klicken Sie auf dem Blatt „Index hinzufügen“ auf **OK**, um den soeben definierten Index zu speichern und zu erstellen.

In den folgenden Screenshots sehen Sie, wie wir die Felder für unseren Index „Hotels“ benannt und definiert haben.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## Weiter
Nach dem Erstellen eines Azure Search-Indexes können Sie [Ihre Inhalte in den Index hochladen](search-what-is-data-import.md) und mit dem Durchsuchen der Daten beginnen.

<!---HONumber=AcomDC_0316_2016-->