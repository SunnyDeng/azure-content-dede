<properties 
	pageTitle="Kontingente und Einschränkungen für Media Services" 
	description="In diesem Thema sind die für Microsoft Azure Media Services geltenden Kontingente und Einschränkungen beschrieben." 
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
	ms.date="06/16/2015" 
	ms.author="juliako"/>


#Kontingente und Einschränkungen

In diesem Thema sind die für Microsoft Azure Media Services geltenden Kontingente und Einschränkungen beschrieben.

## Kontingente und Einschränkungen

- Maximale Anzahl der in Ihrem Media Services-Konto zulässigen Medienobjekte: 1.000.000. 

- Maximale Anzahl verketteter Aufgaben pro Auftrag: 30.

- Maximale Anzahl der in einer Aufgabe zulässigen Medienobjekte: 50 Medienobjekte pro Aufgabe.
 
- Maximale Anzahl der Medienobjekte pro Auftrag: 100.
 
- Die maximale Anzahl von Aufträgen in Ihrem Konto sollte 50.000 nicht überschreiten. Diese Zahl umfasst fertig gestellte, aktive und abgebrochene Aufträge sowie Aufträge in der Warteschlange. Gelöschte Aufträge sind nicht enthalten.
 
- Sie können die alten Aufträge mithilfe von **IJob.Delete** oder der HTTP **DELETE**-Anforderung löschen. Weitere Informationen finden Sie unter [Auftragsdatensatzlimit für Azure Media Encoder](http://blogs.msdn.com/b/randomnumber/archive/2014/05/05/job-record-limit-for-windows-azure-media-encoder.aspx) und [Verwalten von Medienobjekten](https://msdn.microsoft.com/library/azure/dn642436.aspx).
 
- Wenn Sie eine Anforderung zum Auflisten von Auftragsentitäten senden, werden maximal 1.000 Entitäten pro Anforderung zurückgegeben. Falls Sie alle gesendeten Aufträge nachverfolgen müssen, können Sie "Nach oben"/"Überspringen" wie in [OData-Systemabfrageoptionen](http://msdn.microsoft.com/library/gg309461.aspx) beschrieben verwenden.


- Einem bestimmten Medienobjekt können jeweils nicht mehr als fünf eindeutige Locators zugeordnet sein.
	
	**Hinweis**: Locators sind nicht für die Verwaltung der Zugriffssteuerung pro Benutzer konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM).

- Einem einzelnen Abonnement können jeweils nicht mehr als 25 Media Services-Konten zugeordnet sein.


- In der Standardeinstellung können Sie Ihrem Media Services-Konto bis zu fünf Livekanäle hinzufügen.

	Außerdem können Sie bis zu fünf Kanäle gleichzeitig starten. Wie Sie einen höheren Grenzwert anfordern, erfahren Sie im nachstehenden Abschnitt *Anfordern eines höheren Grenzwerts für aktualisierbare Kontingente*.

- In der Standardeinstellung können Sie einem einzelnen Kanal bis zu 50 Programme (im angehaltenen Status) hinzufügen.

	Es können maximal drei Programme im Ausführungsstatus zur gleichen Zeit ausgeführt werden. Wie Sie einen höheren Grenzwert anfordern, erfahren Sie im nachstehenden Abschnitt *Anfordern eines höheren Grenzwerts für aktualisierbare Kontingente*.

- Standardmäßig kann jedes Media Services-Konto bis zu zwei Streamingendpunkte (Ursprünge) haben.

	Außerdem können Sie über bis zu zwei Streamingendpunkte im Ausführungsstatus gleichzeitig verfügen.

	Sie können jeden Streamingendpunkt auf bis zu 10 Streamingeinheiten skalieren. Wie Sie einen höheren Grenzwert anfordern, erfahren Sie im nachstehenden Abschnitt *Anfordern eines höheren Grenzwerts für aktualisierbare Kontingente*.


- Standardmäßig kann jedes Media Services-Konto auf bis zu 25 Codierungseinheiten skaliert werden. Weitere Informationen finden Sie unter „Skalieren von Media Services“. Wie Sie einen höheren Grenzwert anfordern, erfahren Sie im Abschnitt „Anfordern eines höheren Grenzwerts für aktualisierbare Kontingente“.
	
	**Wichtig**: Erstellen Sie keine weiteren Media Services-Konten, um die Grenzwerte zu erhöhen, sondern senden Sie stattdessen ein Supportticket.


- Der Drosselungsmechanismus von Media Services schränkt die Ressourcenverwendung für Anwendungen ein, die zu viele Anforderungen an den Dienst ausgeben. Der Dienst kann den HTTP-Statuscode „Dienst nicht verfügbar (503)“ zurückgeben. Weitere Informationen finden Sie in der Beschreibung des HTTP-Statuscodes 503 unter [Azure Media Services-Fehlercodes](http://msdn.microsoft.com/library/azure/dn168949.aspx).

##<a id="request_higher_limit"></a>Anfordern eines höheren Grenzwerts für aktualisierbare Kontingente

###Aktualisierbare Kontingente

Sie können die Aktualisierung der Grenzwerte für die folgenden Kontingente anfordern, indem Sie ein Supportticket öffnen. – Codierungseinheiten

- Live-Kanäle (im angehaltenen und Ausführungsstatus)
 
- Streamingendpunkte (im angehaltenen und Ausführungsstatus)
 
- Streamingeinheiten

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
  

<!---HONumber=62-->