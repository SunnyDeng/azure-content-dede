<properties 
	pageTitle="Erstellen eines Azure Search-Indexes im Portal" 
	description="Hinzufügen eines Indexes zu einem Azure-Suchdienst durch Ausfüllen von Felddefinitionen im Verwaltungsportal" 
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
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Erstellen eines Azure Search-Indexes im Portal

Sie können in Azure Search schnell einen Prototyp eines Indexes erstellen, indem Sie den Index im Azure-Verwaltungsportal erstellen. Diese Vorgehensweise eignet sich hervorragend für ein Testen der Machbarkeit des Konzepts, kann aber auch genutzt werden, um Schemadefinitionen und Ressourcennutzung für jeden Index anzuzeigen, der für Ihren Dienst bereitgestellt wird.

Damit Sie diese Aufgabe ausführen können, benötigen Sie einen funktionsfähigen Azure-Suchdienst. Unter [Erstellen eines Azure-Suchdiensts im Portal](search-create-service-portal.md) finden Sie Informationen, wie ein Azure-Suchdienst eingerichtet wird.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Das Dashboard können Sie u. a. auf folgende Arten finden.
	- Klicken Sie auf der Sprungleiste auf **Startseite**. Die Startseite hat eine Kachel für jeden Dienst, der zu Ihrem Abonnement gehört. Klicken Sie auf die Kachel, um das Dashboard des Diensts zu öffnen.
	- Klicken Sie auf der Sprungleiste auf **Durchsuchen** | **Filtern nach** | **Suchdienste**, um in der Liste nach Ihrem Suchdienst zu suchen. 

3. Auf dem Dashboard des Diensts sehen am oberen Rand eine Befehlsleiste, wozu auch der Befehl **Index hinzufügen** gehört. 
	
	Überprüfen Sie die Preisstufe. Wenn Sie die kostenlose Version haben, können Sie bis zu drei Indizes erstellen. Möglicherweise müssen Sie einen Index löschen, um Speicherplatz freizugeben.

     ![][1]

4. Um einen Index zu löschen, klicken Sie auf den entsprechenden Index, damit ein Blatt geöffnet wird. Klicken Sie auf **Löschen**.

     ![][2]

5. Um einen neuen Index im Portal zu erstellen, klicken Sie auf **Index hinzufügen**, und geben Sie dem Index einen Namen, z. B. *hotels*. 

	Es kann eine Minute dauern, bis der Index erstellt ist, aber wenn er fertiggestellt ist, sodass Sie mit ihm arbeiten können, wird er in der Liste "Indizes" angezeigt.

6. Klicken Sie auf *hotels*, um das Indexdefinitionsblatt zu öffnen. 

	Wenn Sie einen Index im Portal erstellen, wird ein erforderliches Feld (ID) für Sie erstellt. Dies ist das Schlüsselfeld, mit dem jedes Dokument eindeutig gekennzeichnet wird. Es gibt nur ein Feld pro Schlüssel (keine zusammengesetzten Schlüssel), und ein Schlüssel ist immer eine Zeichenfolge.

	Wenn Sie das Schlüsselfeld umbenennen möchten, müssen Sie dies nun während der Indexerstellung tun. Nachdem der Index erstellt ist, können Sie das Feld nicht mehr umbenennen.

	![][3]

7. Damit Sie den Feldnamen bearbeiten können, klicken Sie auf den Pfeil nach rechts in der Felderliste. 

8. Ersetzen Sie *id* durch *hotelId*.

9. Klicken Sie auf jedem Blatt (Felder und Index) auf **OK**, um den Index zu erstellen.

##Hinzufügen von Feldern

In Azure Search sind Indexattribute wie "searchable", "facetable" und "filterable" standardmäßig aktiviert. Wenn Sie diese Attribute festlegen, deaktivieren Sie üblicherweise Suchverhaltensweisen, die nicht sinnvoll sind (beispielsweise Sortieren oder Facettieren nach einer Beschreibung).

Das Portal ist hier anders. Im Portal sind Suchverhaltensweisen standardmäßig deaktiviert, sodass Sie alle Verhaltensweisen, die gelten sollen, Feld für Feld auswählen können.

1. Klicken Sie auf **Felder hinzufügen/bearbeiten**, um weitere Felder hinzuzufügen. In dieser Übung wird der  *hotels*-Index neu erstellt, der im Artikel [Gewusst wie: Verwenden von Fiddler mit Azure Search](Search-fiddler.md) aufgeführt ist. 

	![][4]

2. Fügen Sie Felder hinzu, und konfigurieren Sie diese, um das Schema zu vervollständigen.

	![][5]

	Referenzinformationen zu Feldnamen und -typen finden Sie unter [Benennungsregeln](https://msdn.microsoft.com/library/azure/dn857353.aspx) und [Unterstützte Datentypen](https://msdn.microsoft.com/library/azure/dn798938.aspx).

3. Klicken Sie oben auf der Seite auf **Speichern**.

  	![][6]

##Nächste Schritte

Obwohl der Index definiert ist, kann er solange nicht verwendet werden, bis Sie Dokumente geladen haben. Um dies einfach zu erreichen, führen Sie die Schritte aus, die in [Gewusst wie: Verwenden von Fiddler mit Azure Search](search-fiddler.md) unter **Laden von Dokumenten** zu finden sind. Sie können dann die restlichen Schritte in dem Artikel ausführen, um einige Abfragen auszuführen.

Sobald der grundsätzliche Index Ihren Vorstellungen entspricht, könnten Sie ein Sprachanalyseprogramm oder ein Vorschlagsprogramm hinzufügen, damit Unterstützung für mehrere Sprachen oder für Wortvorschläge hinzugefügt wird. Beide Features sind im Indexschema angegeben. Weitere Informationen finden Sie unter [Sprachunterstützung](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) und [Index erstellen](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG
[6]: ./media/search-create-index-portal/AzureSearch-PortalIndex-6.PNG

<!--HONumber=54-->