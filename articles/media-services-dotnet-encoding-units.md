<properties 
	pageTitle="Hinzufügen von Codierungseinheiten" 
	description="Erfahren Sie, wie Sie mit .NET Codierungseinheiten hinzufügen.  
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




#Skalieren der Codierung mit .NET SDK

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).
  
##Übersicht

Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Codierungsaufträge verarbeitet werden. Sie können zwischen den folgenden Typen reservierter Einheiten auswählen: "Basic", "Standard" oder "Premium". Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit "Standard" beispielsweise schneller ausgeführt als mit dem Typ "Basic". Weitere Informationen finden Sie im Blog "Typen reservierter Einheiten für die Codierung" von [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

Zusätzlich zum Typ reservierter Einheiten können Sie angeben, dass für Ihr Konto reservierte Einheiten für die Codierung bereitgestellt werden sollen. Die Anzahl der bereitgestellten reservierten Einheiten für die Codierung bestimmt die Anzahl der Medienaufgaben, die gleichzeitig unter einem angegebenen Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf reservierte Einheiten verfügt, werden fünf Medienaufgaben gleichzeitig ausgeführt, so lange es auszuführende Aufgaben gibt. Die restlichen Aufgaben bleiben in der Warteschlange und werden nacheinander für die Verarbeitung aufgerufen, wenn eine aktive Aufgabe abgeschlossen wird. Wenn für ein Konto keine reservierten Einheiten bereitgestellt wurden, werden die Aufgaben nacheinander aufgerufen. In diesem Fall hängt die Wartezeit zwischen dem Abschließen einer Aufgabe und dem Start der nächsten Aufgabe von der Verfügbarkeit von Ressourcen im System ab.

Führen Sie folgende Schritte aus, um den Typ reservierter Einheiten und die Anzahl reservierter Einheiten für die Codierung mit dem .NET SDK zu ändern:

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##Öffnen eines Supporttickets

Standardmäßig kann jedes Media Services-Konto auf bis zu 25 für das Codieren und bis zu 5 für das bedarfsgesteuerte Streaming reservierte Einheiten skaliert werden. Sie können einen höheren Grenzwert durch Öffnen eines Supporttickets anfordern.

Um ein Supportticket zu öffnen, gehen Sie folgendermaßen vor: 

1. Melden Sie sich mit Ihrem Azure-Konto am [Verwaltungsportal](http://manage.windowsazure.com) an.
2. Wechseln Sie zu [Support](http://www.windowsazure.com/support/contact/).
3. Klicken Sie auf "Support erhalten".
4. Wählen Sie Ihr Abonnement aus.
5. Wählen Sie unter Supporttyp "Technisch" aus.
6. Klicken Sie auf "Ticket erstellen".
7. Wählen Sie "Azure Media Services" in der Produktliste auf der nächsten Seite aus.
8. Wählen Sie "Medienverarbeitung" als "Problemtyp" und dann "Reservierungseinheiten" als Kategorie aus.
9. Klicken Sie auf "Weiter".
10. Folgen Sie den Anweisungen auf der nächsten Seite, und geben Sie dann Details zur Anzahl der benötigten reservierten Einheiten für das Codieren oder das bedarfsgesteuerte Streaming an.
11. Klicken Sie auf "Senden", um das Ticket zu öffnen.



<!--HONumber=52-->