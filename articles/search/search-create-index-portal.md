<properties
	pageTitle="Erstellen eines Azure Search-Index im Portal | Microsoft Azure"
	description="Fügen Sie Azure Search, einem in der Cloud gehosteten Suchdienst, durch Angabe von Felddefinitionen im Azure-Portal einen Index hinzu."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Erstellen eines Azure Search-Index im Azure-Portal

Sie können in Azure Search schnell einen Indexprototypen verwenden, indem Sie einen Index im Azure-Portal erstellen. Das Portal eignet sich hervorragend für Machbarkeitsstudien, kann aber auch zum Anzeigen von Schemadefinitionen und Ressourcennutzung für einen beliebigen Index verwendet werden, der für Ihren Dienst bereitgestellt ist.

Vergewissern Sie sich für diese Aufgabe, dass Sie über einen einsatzbereiten Azure Search-Dienst verfügen. Hilfe bei der Einrichtung finden Sie bei Bedarf unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Im Anschluss finden Sie einige Möglichkeiten, um zum Dashboard zu gelangen.
	- Klicken Sie auf der Navigationsleiste auf **Startseite**. Auf der Startseite befinden sich die Kacheln für die in Ihrem Abonnement enthaltenen Dienste. Klicken Sie auf die Kachel, um das Service-Dashboard zu öffnen.
	- Klicken Sie auf der Navigationsleiste auf **Alle durchsuchen** > **Filtern nach** > **Search-Dienste**, um Ihren Search-Dienst in der Liste zu finden.

3. Im oberen Bereich des Service-Dashboards befindet sich eine Befehlsleiste mit dem Befehl **Index hinzufügen**.

	Überprüfen Sie den Tarif. Bei Verwendung der kostenlosen Version stehen Ihnen bis zu drei Indizes zur Verfügung. Unter Umständen muss ein Index gelöscht werden, um Platz freizugeben.

     ![][1]

4. Klicken Sie zum Löschen eines Index auf den gewünschten Index. Daraufhin öffnet sich ein Blatt. Klicken Sie auf **Löschen**.

     ![][2]

5. Klicken Sie zum Erstellen eines neuen Index im Portal auf **Index hinzufügen**, und benennen Sie ihn (beispielsweise mit *hotels*).

	Die Indexerstellung kann einen Moment dauern. Sobald der Index verwendungsbereit ist, wird er in der Indexliste angezeigt.

6. Klicken Sie auf **hotels**, um das Blatt mit der Indexdefinition zu öffnen.

	Wenn Sie einen Index im Portal erstellen, wird automatisch ein erforderliches Feld (id) erstellt. Hierbei handelt es sich um das Schlüsselfeld, mit dem die einzelnen Dokumente eindeutig identifiziert werden. Es gibt jeweils nur ein Feld pro Schlüssel (keine zusammengesetzten Schlüssel), und es ist immer eine Zeichenfolge.

	Wenn Sie das Schlüsselfeld umbenennen möchten, müssen Sie diesen Schritt jetzt (also während der Indexerstellung) ausführen. Nach der Indexerstellung kann das Feld nicht mehr umbenannt werden.

	![][3]

7. Klicken Sie zum Bearbeiten des Feldnamens in der Feldliste auf den Pfeil nach rechts.

8. Ersetzen Sie **id** durch **hotelId**.

9. Klicken Sie auf jedem Blatt („Felder“ und „Index“) auf **OK**, um den Index zu erstellen.

## Hinzufügen von Feldern

In Azure Search sind Indexattribute wie „searchable“, „facetable“ und „filterable“ standardmäßig aktiviert. Diese Attribute werden in der Regel verwendet, um nicht sinnvolle Suchverhaltensweisen zu deaktivieren (beispielsweise die Sortierung oder Facettenerstellung auf der Grundlage einer Beschreibung).

Das Portal unterscheidet sich hier. Im Portal sind Suchverhaltensweisen standardmäßig deaktiviert, damit Sie für die einzelnen Felder jeweils alle geeigneten Verhaltensweisen auswählen können.

1. Klicken Sie auf die Option zum Hinzufügen/Bearbeiten von Feldern, um weitere Felder hinzufügen. In dieser Übung erstellen wir den im Artikel [Verwenden von Fiddler mit Azure Search](search-fiddler.md) beschriebenen Index *hotels* neu.

	![][4]

2. Fügen Sie Felder hinzu, und konfigurieren Sie sie, um das Schema zu vervollständigen.

	![][5]

	Informieren Sie sich unter [Benennungsregeln](https://msdn.microsoft.com/library/azure/dn857353.aspx) und [Unterstützte Datentypen](https://msdn.microsoft.com/library/azure/dn798938.aspx) über Feldnamen und -typen.

    Folgende Indexattribute stehen zur Verfügung:

	- **Retrievable**: Gibt an, ob ein Feld in einem Suchergebnis zurückgegeben werden kann.
	- **Filterable**: Ermöglicht die Verwendung des Felds in Abfragen vom Typ **$filter**.
	- **Sortable**: Ermöglicht die Verwendung des Felds als Sortieroption.
	- **Facetable**: Ermöglicht die Verwendung eines Felds in einer Facettennavigationsstruktur für selbstständiges Filtern. Repetitive Werte, mit denen sich mehrere Dokumente zu einer Gruppe zusammenfassen lassen (etwa mehrere Dokumente der gleichen Produkt- oder Dienstleistungskategorie), sind in der Regel am besten für die Verwendung als Facetten geeignet.
	- **Key**: Die eindeutige ID der einzelnen Dokumente, die für die Dokumentsuche verwendet wird. Jeder Index muss über einen Schlüssel verfügen. Als Schlüssel kann immer nur ein einzelnes Feld fungieren, und dieses Feld muss auf „Edm.String“ festgelegt sein.
	- **Searchable**: Gibt an, ob das Feld für die Volltextsuche verwendet werden kann.

3. Unerwünschte Felder können Sie entfernen, indem Sie mit der rechten Maustaste auf das Feld klicken und anschließend auf **Löschen** klicken.

4. Klicken Sie auf **OK**, um den soeben definierten Index zu speichern, und klicken Sie auf der Seite „Index hinzufügen“ erneut auf **OK**, um den Index zu erstellen.


## Nächste Schritte

Der Index ist nun zwar definiert, er kann aber erst nach dem Laden von Dokumenten verwendet werden. Falls Sie den zu Testzwecken verwendeten Hotelindex neu erstellen, können Sie in [Fiddler](search-fiddler.md) problemlos die geringe Anzahl von Dokumenten für diesen Index laden. Die entsprechenden Anweisungen finden Sie unter [Verwenden von Fiddler mit Azure Search](search-fiddler.md) im Abschnitt „Laden von Dokumenten“. Anschließend können Sie anhand der restlichen Schritte in diesem Artikel einige Abfragen ausführen.

Nachdem Sie sich mit dem einfachen Index vertraut gemacht haben, können Sie ggf. eine Sprachanalyse oder Vorschläge hinzufügen, um mehrere Sprachen oder Vorschläge mit automatischer Vervollständigung zu unterstützen. Beide Features werden im Indexschema angegeben. Weitere Informationen finden Sie unter [Sprachunterstützung](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) bzw. unter [Index erstellen](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG

<!---HONumber=Nov15_HO2-->