<properties urlDisplayName="How to scale" pageTitle="Skalieren eines Media Services | Azure-Dokumentation" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





#Skalieren eines Mediendienstes  

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]


Sie können Mediendienste durch Angabe der Anzahl von **On-Demand Streaming Reserved Units** und **Encoding Reserved Units** skalieren, mit denen Sie Ihr Konto bereitstellen möchten. 


<h2>Reservierte Einheiten für bedarfsgesteuertes Streaming</h2>

Die reservierten Einheiten für bedarfsgesteuertes Streaming stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und zusätzliche Funktionen, die aktuell [dynamische Paketfunktionen] umfassen(http://go.microsoft.com/fwlink/?LinkId=276874). Standardmäßig wird das bedarfsgesteuerte Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Durchsatz des bedarfsgesteuerten Streamings zu erhöhen, sollten Sie die reservierten Einheiten für bedarfsgesteuertes Streaming kaufen. 

Um die Anzahl der reservierten Einheiten für bedarfsgesteuertes Streaming zu ändern, gehen Sie folgendermaßen vor:

1. Im [Verwaltungsportal](https://manage.windowsazure.com/) klicken Sie auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.

2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. Klicken Sie anschließend auf den Streaming-Endpunkt, den Sie ändern möchten.


3. Um die Anzahl der Streaming-Einheiten anzugeben, wählen Sie die Registerkarte "SKALIERUNG" aus und verschieben anschließend den Schieberegler für die **reservierte Kapazität**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die Zuordnung neuer Einheiten für das bedarfsgesteuerte Streaming dauert ca. 20 Minuten. 

	 
	>[Azure.Note] Aktuell kann das bedarfsgesteuerte Streaming für bis zu einer Stunde deaktiviert werden, wenn Sie einen positiven Wert für die Einheiten für das bedarfsgesteuerte Streaming zurück auf null setzen.

	>[Azure.Note] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter den [Media Services-Preisangaben](http://go.microsoft.com/fwlink/?LinkId=275107).

<h2>Für das Codieren reservierte Einheiten</h2>

Die Anzahl der bereitgestellten reservierten Einheiten für das Codieren entspricht der Anzahl der Medienaufgaben, die gleichzeitig in einem angegebenen Konto verarbeitet werden können. Wenn Ihr Konto beispielsweise über fünf reservierte Einheiten verfügt, werden fünf Medienaufgaben gleichzeitig ausgeführt, so lange es auszuführende Aufgaben gibt. Die restlichen Aufgaben bleiben in der Warteschlange und werden nacheinander für die Verarbeitung aufgerufen, wenn eine aktive Aufgabe abgeschlossen wird. Wenn für ein Konto keine reservierten Einheiten bereitgestellt wurden, werden die Aufgaben nacheinander aufgerufen. In diesem Fall hängt die Wartezeit zwischen dem Abschließen einer Aufgabe und dem Start der nächsten Aufgabe von der Verfügbarkeit von Ressourcen im System ab.

Um die Anzahl der für das Codieren reservierten Einheiten zu ändern, gehen Sie folgendermaßen vor:

1. Im [Verwaltungsportal](https://manage.windowsazure.com/) klicken Sie auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.

2. Wählen Sie die Seite CODIERUNG aus. 
	
	Auf der Seite Codierung können Sie zwischen drei verschiedenen Typen von Encoding Reserved Units auswählen:  Basic, Standard und Premium (siehe unten).

	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	Sie können die Anzahl der reservierten Einheiten für den ausgewählten TYP DER RESERVIERTEN EINHEIT über den CODIERUNG-Schieberegler einstellen.  
	
	Der wichtigste Unterschied zwischen den einzelnen Typen ist die Geschwindigkeit. Eine bestimmte Codierungsaufgabe wird z. B. mit dem Typ Standard schneller ausgeführt als mit dem Typ Basic. Weitere Informationen finden Sie im Blog "Typen reservierter Codierungseinheiten" von [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

	>[Azure.Note] Der Premium-Typ für reservierte Einheiten ist in den folgenden Rechenzentren nicht verfügbar: Singapur, Hong Kong, Osaka, Peking, Shanghai.

3. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die neuen für das Codieren reservierten Einheiten werden zugewiesen, wenn Sie auf SPEICHERN klicken.

	>[Azure.Note] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet.

<h2>Öffnen eines Supporttickets</h2>


Standardmäßig kann jedes Media Services-Konto auf bis zu 25 für das Codieren und bis zu 4 für das bedarfsgesteuerte Streaming reservierte Einheiten skaliert werden. Sie können einen höheren Grenzwert durch Öffnen eines Supporttickets anfordern.

Um ein Supportticket zu öffnen, gehen Sie folgendermaßen vor: 

1. Melden Sie sich im [Verwaltungsportal] an Ihrem Azure-Konto an(http://manage.windowsazure.com).
2. Wechseln Sie zu [Support](http://www.windowsazure.com/de-de/support/contact/).
3. Klicken Sie auf "Get Support".
4. Wählen Sie Ihr Abonnement aus.
5. Wählen Sie unter Supporttyp "Technical" aus.
6. Klicken Sie auf "Create Ticket".
7. Wählen Sie "Azure Media Services" in der Produktliste auf der nächsten Seite aus.
8. Wählen Sie "Media Processing" als "Problem type" und dann "Reservation Units" als Kategorie aus.
9. Klicken Sie auf "Weiter".
10. Folgen Sie den Anweisungen auf der nächsten Seite, und geben Sie dann Details zur Anzahl der benötigten reservierten Einheiten für das Codieren oder das bedarfsgesteuerte Streaming an.
11. Klicken Sie auf "Submit", um das Ticket zu öffnen.





 





<!--HONumber=35.1-->
