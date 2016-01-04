<properties 
	pageTitle="Hinzufügen von Codierungseinheiten" 
	description="Erfahren Sie, wie Sie mit .NET Codierungseinheiten hinzufügen."  
	services="media-services" 
	documentationCenter="" 
	authors="juliako,milangada,gtrifonov" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"
	ms.author="juliako"/>


#Skalieren der Codierung mit .NET SDK


> [AZURE.SELECTOR]
- [Portal](media-services-portal-encoding-units.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Übersicht

Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Codierungsaufträge verarbeitet werden. Sie können zwischen den folgenden Typen reservierter Einheiten auswählen: Basic, Standard oder Premium. Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit Standard beispielsweise schneller ausgeführt als mit dem Typ Basic. Weitere Informationen finden Sie im Blog "Encoding Reserved Unit Types" (Typen reservierter Codierungseinheiten, in englischer Sprache) von [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

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

Standardmäßig kann jedes Media Services-Konto auf bis zu 25 für das Codieren und bis zu 4 für das bedarfsgesteuerte Streaming reservierte Einheiten skaliert werden. Sie können einen höheren Grenzwert durch Öffnen eines Supporttickets anfordern.

###Öffnen eines Supporttickets

Um ein Supportticket zu öffnen, gehen Sie folgendermaßen vor:

1. Klicken Sie auf [Support erhalten](https://manage.windowsazure.com/?getsupport=true). Wenn Sie nicht angemeldet sind, werden Sie zur Eingabe Ihrer Anmeldeinformationen aufgefordert.

1. Wählen Sie Ihr Abonnement aus.
 
1. Wählen Sie unter Supporttyp „Technisch“ aus.
 
1. Klicken Sie auf „Ticket erstellen“.
 
1. Wählen Sie „Azure Media Services“ in der Produktliste auf der nächsten Seite aus.
 
1. Wählen Sie einen Problemtyp aus, der Ihr Problem beschreibt.
 
1. Klicken Sie auf „Weiter“.
 
1. Befolgen Sie die Anweisungen auf der nächsten Seite, und geben Sie dann die Details zu Ihrem Problem an.
 
1. Klicken Sie auf "Senden", um das Ticket zu öffnen.
 


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1217_2015-->