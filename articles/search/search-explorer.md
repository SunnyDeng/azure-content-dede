<properties
	pageTitle="Abfragen eines Azure Search-Index per Suchexplorer im Azure-Portal | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Der Suchexplorer ist ein Ansatz ohne Code zum Abfragen eines Azure Search-Index im Azure-Portal."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Abfragen eines Azure Search-Index per Suchexplorer im Azure-Portal
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Der **Suchexplorer** ist ein Abfragetool, das in das Azure-Portal integriert ist und zum Abfragen eines Azure Search-Index ohne Code verwendet werden kann. Das Tool kann mit allen Indizes Ihres Diensts verknüpft werden und verfügt über ein Suchfeld zum Eingeben von Suchzeichenfolgen und -ausdrücken. Die gültige Abfragesyntax wird basierend auf Ihrer Eingabe generiert. Ergebnisse werden auf der Portalseite angezeigt.

Der Suchexplorer eignet sich ideal zum Erlernen der Abfragesyntax, Ausführen gelegentlicher Ad-hoc-Abfragen oder Verfeinern eines Abfrageausdrucks, bevor Sie versuchen, ihn per Code zu beschreiben. Damit Sie dieses Tool verwenden können, müssen Sie bereits über einen Azure Search-Dienst und einen entsprechenden Index verfügen. Hilfe zu diesen Aufgaben finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md) und [Importieren von Daten in Azure Search über das Portal](search-import-data-portal.md).

## Öffnen des Suchexplorers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Im Anschluss finden Sie einige Möglichkeiten, um zum Dashboard zu gelangen.
	- Klicken Sie auf der Navigationsleiste auf **Startseite**. Auf der Startseite befinden sich die Kacheln für die in Ihrem Abonnement enthaltenen Dienste. Klicken Sie auf die Kachel, um das Service-Dashboard zu öffnen.
	- Klicken Sie auf der Navigationsleiste auf **Alle durchsuchen** > **Filtern nach** > **Search-Dienste**, um Ihren Search-Dienst in der Liste zu finden.

3. Im oberen Bereich des Service-Dashboards befindet sich auch eine Befehlsleiste für den **Suchexplorer**.

  	![][1]

4. Klicken Sie auf **Suchexplorer**, um das Blatt „Suchexplorer“ zu öffnen.
5. Legen Sie den Index und die API-Version fest. Der Suchexplorer stellt automatisch eine Verbindung mit dem ersten Index in Ihrer Indexliste her, aber Sie können auf **Index ändern** klicken, um zu einem anderen Index zu wechseln. Mit **API-Version festlegen** können Sie die allgemein verfügbaren Versionen oder die Vorschauversionen angeben. Ein Teil der Abfragesyntax ist nur als Vorschauversion verfügbar.
6. Wenn Sie die Schritte unter [Erste Schritte mit Azure Search](search-get-started-portal.md) ausgeführt haben, um einen Index basierend auf USGS-Daten (geologischer Dienst der USA) für Rhode Island zu erstellen und aufzufüllen, können Sie diesen Suchbegriff verwenden, um die Ausgabe der gleichen drei Suchergebnisse im Suchexplorer zu überprüfen: `roger williams +school -building`

Beachten Sie die Abfragesyntax, die als Antwort auf die Eingabe des Suchbegriffs automatisch generiert wird.

![][2]

## Nächste Schritte

Weitere Informationen zur Abfragesyntax und Beispiele finden Sie unter [Dokumente durchsuchen](https://msdn.microsoft.com/library/azure/dn798927.aspx) auf der MSDN-Website.

Unter den folgenden Links finden Sie Informationen zu weiteren Ansätzen ohne Code für die Erstellung oder Verwaltung eines Suchdiensts oder -index:

- [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md)
- [Importieren von Daten in Azure Search über das Portal](search-import-data-portal.md)
- [Verwalten Ihres Search-Diensts in Azure](search-manage.md)

<!--Image References-->
[1]: ./media/search-explorer/AzSearch-SearchExplorer-Btn.png
[2]: ./media/search-explorer/AzSearch-SearchExplorer-Example.png

<!---HONumber=AcomDC_0128_2016-->