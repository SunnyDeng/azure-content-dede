<properties 
	pageTitle="Skalieren eines Mediendiensts" 
	description="Erfahren Sie, wie Sie Mediendienste durch Angabe der Anzahl von On-Demand Streaming Reserved Units und Encoding Reserved Units für Ihr Konto skalieren können." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Skalieren eines Mediendiensts  

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

## Übersicht

Sie können Mediendienste skalieren, indem Sie die Anzahl **reservierter Einheiten für das Streaming ** und **reservierter Einheiten für die Codierung** angeben, die für Ihr Konto bereitgestellt werden soll. 

## Reservierte Einheiten für das Streaming

Weitere Informationen finden Sie unter [Skalieren von Streamingeinheiten](media-services-manage-origins#scale_streaming_endpoints.md).

## <a id="encoding_reserved_units"></a>Für das Codieren reservierte Einheiten

Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Codierungsaufträge verarbeitet werden. Sie können zwischen den folgenden Typen reservierter Einheiten auswählen: Basic, Standard oder Premium. Eine bestimmte Codierungsaufgabe wird z. B. mit dem Typ Standard schneller ausgeführt als mit dem Typ Basic. Weitere Informationen finden Sie im Blog "Typen reservierter Einheiten für die Codierung" von [Milan Gada](http://azure.microsoft.com/blog/author/milanga).

Zusätzlich zum Typ reservierter Einheiten können Sie angeben, dass für Ihr Konto reservierte Einheiten für die Codierung bereitgestellt werden sollen. Die Anzahl der bereitgestellten reservierten Einheiten für die Codierung bestimmt die Anzahl der Medienaufgaben, die gleichzeitig unter einem angegebenen Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf reservierte Einheiten verfügt, werden fünf Medienaufgaben gleichzeitig ausgeführt, so lange es auszuführende Aufgaben gibt. Die restlichen Aufgaben bleiben in der Warteschlange und werden nacheinander für die Verarbeitung aufgerufen, wenn eine aktive Aufgabe abgeschlossen wird. Wenn für ein Konto keine reservierten Einheiten bereitgestellt wurden, werden die Aufgaben nacheinander aufgerufen. In diesem Fall hängt die Wartezeit zwischen dem Abschließen einer Aufgabe und dem Start der nächsten Aufgabe von der Verfügbarkeit von Ressourcen im System ab.

Führen Sie folgende Schritte aus, um den Typ reservierter Einheiten und die Anzahl reservierter Einheiten für die Codierung zu ändern:

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendiensts.

2. Wählen Sie die Seite **CODIERUNG** aus. 

	Um den **TYP RESERVIERTER EINHEITEN** zu ändern, drücken Sie BASIC, STANDARD oder PREMIUM. 

	Verwenden Sie zum Ändern der Anzahl reservierter Einheiten für den ausgewählten Typ reservierter Einheiten den Schieberegler unter **CODIERUNG**. 
	
	
	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	  
	>[Azure.Note] Der Premium-Typ für reservierte Einheiten ist in den folgenden Rechenzentren nicht verfügbar: Singapur, Hongkong, Osaka, Peking, Shanghai.

3. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die neuen für das Codieren reservierten Einheiten werden zugewiesen, wenn Sie auf SPEICHERN klicken.

	>[Azure.Note] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet.

## Öffnen eines Supporttickets

Standardmäßig kann jedes Media Services-Konto auf bis zu 25 für das Codieren und bis zu 5 für das bedarfsgesteuerte Streaming reservierte Einheiten skaliert werden. Sie können einen höheren Grenzwert durch Öffnen eines Supporttickets anfordern.

Um ein Supportticket zu öffnen, gehen Sie folgendermaßen vor: 

1. Melden Sie sich im [Verwaltungsportal](http://manage.windowsazure.com) bei Ihrem Azure-Konto an.
2. Wechseln Sie zu [Support](http://azure.microsoft.com/support/contact/).
3. Klicken Sie auf "Support erhalten".
4. Wählen Sie Ihr Abonnement aus.
5. Wählen Sie unter Supporttyp "Technisch" aus.
6. Klicken Sie auf "Ticket erstellen".
7. Wählen Sie "Azure Media Services" in der Produktliste auf der nächsten Seite aus.
8. Wählen Sie "Medienverarbeitung" als "Problemtyp" und dann "Reservierungseinheiten" als Kategorie aus.
9. Klicken Sie auf "Weiter".
10. Folgen Sie den Anweisungen auf der nächsten Seite, und geben Sie dann Details zur Anzahl der benötigten reservierten Einheiten für das Codieren oder das bedarfsgesteuerte Streaming an.
11. Klicken Sie auf "Senden", um das Ticket zu öffnen.





<!--HONumber=45--> 
