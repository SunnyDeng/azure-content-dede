<properties 
	pageTitle="Erstellen Sie Ihre erste Azure-Suchanwendung in .NET" 
	description="Lernprogramm zum Erstellen einer Projektmappe mithilfe der .NET-Clientbibliothek aus dem Azure Search .NET SDK." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#Erstellen Sie Ihre erste Azure-Suchanwendung in .NET#

In diesem Lernprogramm wird eine benutzerdefinierte Websuchanwendung in Visual Studio 2013 oder höher erstellt, in der Azure Search für deren Suchfunktionalität verwendet wird. Für das Lernprogramm wird das [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) genutzt, um Klassen für die Objekte und Vorgänge zu erstellen, die im Beispiel verwendet werden.

Sie können das Beispiel aus CodePlex unter [Azure Search Demo Using USGS Data](https://azsearchdemos.codeplex.com/SourceControl/latest) herunterladen, damit Sie den Schritten in diesem Lernprogramm folgen können. In der Beispielanwendung werden Daten von [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) verwendet, die nach dem Bundesstaat Washington gefiltert sind. Mit diesen Daten wird eine Suchanwendung basierend auf Daten über Landmarkengebäude wie Krankenhäuser und Schulen sowie auf geologischen Merkmalen wie Flüsse, Seen und Gipfel erstellt.

Damit Sie dieses Beispiel ausführen können, benötigen Sie einen Azure-Suchdienst, bei dem Sie sich im [Azure-Portal](https://portal.azure.com) anmelden können. 

Sie können mit [Erstellen eines Diensts im Portal](../search-create-service-portal/) beginnen, wenn Sie Unterstützung bei der Bereitstellung des Diensts und der Überprüfung seiner Verfügbarkeit benötigen. In diesem Artikel wird auch erläutert, wie der Dienstname und die Administratorschlüssel (admin-key) zu finden sind, die in jedem Lernprogramm und jeder Projektmappe verwendet werden, die Azure Search beinhaltet.

> [AZURE.TIP] Es empfiehlt sich, NuGet-Pakete zu aktualisieren, bevor irgendwelche Projekte erstellt werden. So lassen sich Erstellungsfehler vermeiden. Klicken Sie mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**. Aktualisieren Sie alle Pakete, die in dieser Projektmappe verwendet werden.

##Erstellen des Indexes##

1. Kopieren Sie den Dienstnamen und den Administratorschlüssel (admin-key) aus dem [Azure-Portal](https://portal.azure.com), und fügen Sie Name und Schlüssel in **DataIndexer** | **App.config** ein.
1. Klicken Sie mit der rechten Maustaste auf das Projekt **DataIndexer**, um es als Startprojekt festzulegen.
1. Erstellen Sie das Projekt, und führen Sie es aus.

Sie sollten ein Konsolenfenster mit den folgenden Meldungen sehen.

![][1]

Im Portal sollten Sie den neuen Index "features" mit xx und xx sehen.  Es kann mehrere Minuten dauern, bis das Portal auf dem neuesten Stand ist. Planen Sie daher ein, den Bildschirm nach einigen Minuten zu aktualisieren, damit die Ergebnisse zu sehen sind. 

![][2]

##Erstellen der Anwendung##


1. Kopieren Sie den Dienstnamen und den Administratorschlüssel (admin-key) aus dem [Azure-Portal](https://portal.azure.com), und fügen Sie Name und Schlüssel in **SimpleSearchMVCApp** | **Web.config** ein.
1. Klicken Sie mit der rechten Maustaste auf das Projekt **SimpleSearchMVCApp**, um es als Startprojekt festzulegen.
1. Erstellen Sie das Projekt, und führen Sie es aus.

Sie sollten in Ihrem Standardbrowser eine Webseite sehen, die ein Suchfeld für das Zugreifen auf USGS-Daten enthält, die im Index in Ihrem Azure-Suchdienst gespeichert sind.

![][3]

##Suchen nach USGS-Daten##

Der USGS-Datenbestand enthält Datensätze, die für den Bundesstaat Washington gelten. Wenn Sie bei leerem Suchfeld auf **Search** klicken, erhalten Sie die obersten 50 Einträge. Dies ist das Standardverhalten. 

Durch Eingabe eines Suchbegriffs wird die Suchmaschine veranlasst, einen gezielteren Suchvorgang auszuführen. Geben Sie einen regionalen Namen ein. "Snoqualmie" ist eine Stadt in Washington. Der Name ist auch der Name eines Flusses, eines malerischen Wasserfalls, einer Passstraße und der bundesstaatlichen Parks.

![][4]

Sie könnten auch jeden der folgenden Begriffe ausprobieren:

- Seattle
- Rainier
- Seattle and Rainier
- Seattle +Rainier -Mount (ruft Ergebnisse für Landmarken auf der Rainier-Allee oder den Rainier-Club ab, die sich alle in den Stadtgrenzen von Seattle befinden).

##Analysieren des Codes##

Um sich die Grundlagen des .NET SDK zu erarbeiten, sollten Sie [Gewusst wie: Verwenden von Azure Search in .NET](../search-howto-dotnet-sdk/) lesen. Dort finden Sie Erläuterungen der am häufigsten verwendeten Klassen aus der Clientbibliothek.

Im restlichen Teil dieses Abschnitts werden einige Punkte zu jedem Projekt behandelt. Wo dies geeignet ist, werden alternative Ansätze skizziert, in denen erweiterte Funktionen verwendet werden.

**DataIndexer-Projekt**

Damit das Projekt einfach bleibt, werden die Daten in die Projektmappe eingebettet. Dazu wird eine Textdatei verwendet, die aus Daten generiert wurde, die von der [USGS-Website (United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm) stammen. 

Alternativen zum Einbetten von Daten sind [Indexer für DocumentDB](documentdb-search-indexer.md) oder [Indexer für Azure SQL-Datenbank](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). Indexer rufen Daten in Ihren Azure Search-Index ab, wodurch sich der Code, den Sie schreiben und pflegen müssen, ggf. deutlich vereinfachen kann. 

Sie können auch Daten aus einer lokalen SQL Server-Datenbank laden. [In diesem Lernprogramm](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/) wird gezeigt, wie das geht.

Das Programm **DataIndexer** enthält eine **SearchDocuments**-Methode zum Suchen nach und Filtern von Daten. Diese Methode ist als ein Überprüfungsschritt vorhanden, der die Ausgabe der Statusmeldungen im Konsolenfenster unterstützt, wobei es um diejenigen geht, die Suchergebnisse und Filterverhaltensweisen zeigen. Höchstwahrscheinlich benötigen Sie eine solche Methode nicht in Code, den Sie dafür schreiben, einen Index zu erstellen und zu laden. 

**SimpleSearchMVCApp-Projekt**

Im MVC-Projekt werden eine Ansicht und ein Controller verwendet, um Eingaben und Ausgaben zur Darstellungsschicht zu leiten. **Index.cshtml** enthält den HTML-Code, der für das Darstellen der Suchergebnisse verwendet wird. Momentan ist dies eine einfache Tabelle, in der die Daten aus dem Dataset zusammengestellt werden. Während dies sehr nützlich zur Prototyperstellung und zum Testen ist, können Sie für die Präsentation problemlos Verbesserungen vornehmen. Tipps, wie Ergebnisse in Stapeln verarbeitet und Anzahlen auf einer Seite angeordnet werden können, finden Sie unter [Seitenergebnisse und Paginierung in Azure Search](search-pagination-page-layout.md).

Verbindungen mit Azure Search sowie das Ausführen einer Abfrageanforderung sind in der Datei **FeatureSearch.cs** definiert.

Eine Schlussbemerkung: Wenn Sie noch nicht vom Wert und von der Einfachheit des .NET SDK überzeugt sind, sollten Sie die Quelldateien dieses Beispiels mit dem folgenden Beispiel vergleichen, das auf der REST-API basiert: [Azure Search Adventure Works Demo](https://azuresearchadventureworksdemo.codeplex.com/). Die in diesem Lernprogramm beschriebene .NET SDK-Version ist viel einfacher und hat weniger Codezeilen.

##Nächste Schritte##

Dies ist das erste Azure Search-Lernprogramm, das auf dem USGS-Dataset basiert. Im Laufe der Zeit wird dieses Lernprogramm erweitert und werden neue Lernprogramm erstellt, in denen die Suchfunktionen veranschaulicht werden, die Sie möglicherweise in Ihren benutzerdefinierten Lösungen verwenden möchten.

Wenn Sie bereits einige Kenntnisse zu Azure Search haben, können Sie dieses Beispiel als Ausgangspunkt für das Ausprobieren von Vorschlagsprogrammen (Wortvorschlags- oder AutoVervollständigen-Abfragen), Filtern und Facettennavigation verwenden. Sie können auch die Seite für die Suchergebnisse verbessern, indem Sie Zählungen hinzufügen und Dokumente stapelweise verarbeiten, sodass Benutzer durch die Ergebnisse blättern können.

Azure Search ist Neuland für Sie? Wir empfehlen, dass Sie weitere Lernprogramme durcharbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](http://azure.microsoft.com/documentation/services/search/), um weitere Ressourcen zu finden. Sie können auch die Links in unserer [Liste der Videos und Lernprogramme](https://msdn.microsoft.com/library/azure/dn798933.aspx) verwenden, um auf weitere Informationen zuzugreifen.

<!--Image references-->
[1]:./media/search-get-started-20150228/consolemessages.png
[2]:./media/search-get-started-20150228/portalindexstatus.png
[3]:./media/search-get-started-20150228/usgssearchbox.png
[4]:./media/search-get-started-20150228/snoqualmie.png

<!--HONumber=54-->