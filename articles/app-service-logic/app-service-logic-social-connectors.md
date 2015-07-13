<properties 
	pageTitle="Microsoft Azure-API-Apps – Connectors für soziale Netzwerke | API-Apps; Microservice" 
	description="Erfahren Sie, wie Sie Microsoft Azure-API-Apps mit Connectors für soziale Netzwerke erstellen und die API-App Ihrer App-Logik hinzufügen; Microservices" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Connectors für soziale Netzwerke in Microsoft Azure App Service


## Was ist ein API-App mit Connector für soziale Netzwerke?
Connectors für soziale Netzwerke sind "API-Apps", die eine Verbindung mit sozialen Netzwerkanwendungen herstellen und mehrere *Aktionen* für den authentifizierten Benutzer ausführen können. Die meisten Connectors können auch mit einem *Trigger* konfiguriert werden. Trigger sind Ereignisse (ähnlich wie Ereignisse in .NET Framework), die für einige Connectors konfiguriert werden können. Trigger können dann die Aktionen auslösen, die für den Connector (oder einen anderen Connector) konfiguriert wurden. Beispielsweise kann eine Instanz des Twitter-Connectors mit einem *Neue Nachricht*-Trigger konfiguriert werden, bei der der Trigger für jeden neuen Tweet definiert ist, der "@VisualStudio" erwähnt. Dieser Trigger kann dann so konfiguriert werden, dass eine *Aktion* zum Retweet des Tweets erfolgt.

Die folgenden Connectors für soziale Netzwerke sind von Microsoft verfügbar:

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

Es folgt eine Kurzbeschreibung der einzelnen Connectors für soziale Netzwerke:

<table>
<tr>
<th> Name</th>
<th> Beschreibung</th>
<th> Trigger</th>
<th>Aktionen</th>

<tr>
<td>Facebook
<td>Mithilfe eines Facebook-Connectors können Sie Nachrichten, Kommentare, Ereignisse und Status aus Ihrem Facebook-Feed veröffentlichen und lesen. Sie können auch Videos und Fotos veröffentlichten.
<td>Neuer Beitrag
<td><li>Beitrag veröffentlichen
	<li>Foto veröffentlichen 
</tr>

<tr>
<td>Twitter
<td>Mit einem Twitter-Connector können Sie verschiedene Vorgänge ausführen, wie z. B. posten, Tweets empfangen und Tweets durchsuchen. Ein Twitter-Connector ermöglicht auch, dass Sie mit dem Geschehen auf Twitter auf dem Laufenden bleiben und Details abrufen, wie z. B. zu Tweets, Zeitrahmen, Freunden und Followern.
<td>Neue Tweets
<td><li>Tweets durchsuchen
	<li>Tweet
	<li>Timeline des Benutzers abrufen
	<li>Retweet
</tr>

<tr>
<td>Chatter
<td>Ein Chatter-Connector kann verwendet werden, um Nachrichten in Ihrem Chatter-Feed zu lesen, zu veröffentlichen und zu durchsuchen.
<td>Neue Nachricht
<td><li>Nachricht veröffentlichen
<li>Nachricht durchsuchen
</tr>

<tr>
<td>Yammer
<td>Mithilfe eines Yammer-Connectors können Sie Nachrichten in Yammer-Gruppen veröffentlichen und lesen.
<td>Neue Nachrichten
<td><li>Nachricht veröffentlichen	
	<li>Nachricht aus Gruppe/Feed abrufen	
</tr>

<tr>
<td>Twilio
<td>Ein Twilio-Connector ermöglicht das Senden und Empfangen von SMS über ein Twilio-Konto. Außerdem können Sie Telefonnummern und Nutzungsdaten abrufen.
<td>N/V
<td><li>Nachrichten: senden, auflisten, abrufen und durchsuchen
	<li>Telefonnummern: kaufen, auflisten und Ortsruf- und gebührenfreie Nummern abrufen
</tr>
</table>


Nun, da Sie eine Vorstellung haben, was unsere Connectors für soziale Netzwerke leisten können, wollen wir einen Blick auf einige einfache Anwendungsfälle für diese Connectors werfen.

## Gründe für die Verwendung von Connectors

Connectors beschleunigen die Anwendungsentwicklung und ermöglichen sogar Nicht-Entwicklern das Erstellen voll funktionsfähiger Unternehmensanwendungen, und zwar ohne eine Programmiersprache lernen oder Code schreiben zu müssen.

### Überwachung von Kundenfeedback ###
Angenommen, Ihr Unternehmen hat vor Kurzem eine neue App veröffentlicht, und das Team möchte wissen, was Kunden in sozialen Medien über die App zu sagen haben. Jedes Teammitglied kann in regelmäßigen Abständen die verschiedenen Websites sozialer Medien überprüfen und raten, welche Stichworte Kunden zum Diskutieren Ihrer App ggf. verwenden. Eleganter ist allerdings das Erstellen eines Twitter-Connectors, der für eine Überwachung von Twitter auf bestimmte Hashtags, Erwähnungen und Stichworte konfiguriert wird. Sie können dann den SMTP-Connector zum Senden einer E-Mail-Nachricht mit dem Inhalt der übereinstimmenden Tweets an die Teammitglieder verwenden. Sie können ähnlich vorgehen, indem Sie Facebook und Yammer überwachen und eine SMS über einen Twilio-Connector an Ihr DevOps -Team senden, wenn der Inhalt von Facebook- oder Yammer-Beiträgen einen Hinweis auf ein möglicherweise schwerwiegendes Problem enthält, das Ihre Kunden betrifft. Sie erreichen dies alles, ohne eine einzige Codezeile schreiben zu müssen.

Lassen Sie uns anfangen.


## Erstellen eines Connectors
Connectors können im Azure-Portal erstellt werden.

### Erstellen eines Connectors für soziale Netzwerke im Microsoft Azure-Portal

1. Wählen Sie im Azure-Portal **NEU** > **Web und Mobil** > **Azure Marketplace** aus.
2. **Suchen** Sie den Connector, oder wählen Sie ihn in der Liste aus. Nach dem Auswählen wird ein neues Blatt bzw. Fenster geöffnet. Klicken Sie auf **Erstellen**. 
3. Geben Sie die folgenden Eigenschaften für den Connector ein: 
	<table>
    <tr><th>Eigenschaft</th> <th>Beschreibung</th> </tr>
    <tr><td>Name</td> <td>Geben Sie für Ihre API-App einen beliebigen Namen ein. Beispielsweise können Sie sie "RulesDiscountTaxCode" oder "APIAppValidateXML" nennen.</td> </tr>
    <tr><td>App Service-Plan</td> <td>Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.</th> </td>
    <tr><td>Preisstufe</td> <td>Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet.</td> </tr>
    <tr><td>Ressourcengruppe</td> <td>Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Unter "Verwenden von Ressourcengruppen" wird diese Eigenschaft erläutert.</td> </tr>
    <tr><td>Abonnement</td> <td>Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.</td> </tr>
    <tr><td>Standort</td> <td>Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. </td></tr>
    <tr><td>Zum Startmenü hinzufügen</td> <td>Wählen Sie diese Option aus, um die API-App Ihrem Startmenü (der Startseite) hinzufügen.</td></tr>
</table>
4. Klicken Sie auf **Erstellen**. Ihr Connector wird erstellt. Der Vorgang kann eine Weile dauern, und während der Erstellung des Connectors wird die Startseite angezeigt. Verwenden Sie das Menüelement "Benachrichtigungen" auf der linken Seite zur Überwachung des Status des Connectors.

Nun da Sie den ersten Connector erstellt haben, sollten Sie die Erstellung einer Web-, mobilen oder Logik-App angehen.


### Zugreifen auf einen Connector mit REST-APIs

[Zugreifen auf Connectors mit REST-APIs](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## Hinzufügen Ihres Connectors zu einer Anwendung 
Microsoft Azure App Service stellt verschiedene Anwendungstypen zur Verfügung, die diese Connectors verwenden können. Sie können z. B. eine *Logik*-App erstellen, indem Sie einen oder mehrere Ihrer Connectors in einer einzigen Anwendung *logisch* kombinieren.

Zum Verwenden Ihrer Connectors innerhalb Ihrer *Logik*-App wählen Sie in der Liste einen vorkonfigurierten Connector aus, fügen ihn Ihrem Entwurfsworkflow hinzu, nehmen die erforderlichen Konfigurationsänderungen vor, und schon ist er einsatzbereit.

Um diese Schritte durchführen zu können, benötigen Sie eine Web-App, mobile App oder Logik-App. Unter <> finden Sie die einzelnen Schritte. Sobald Ihre Anwendung verfügbar ist, fügen Sie die Connectors hinzu. Das geht so:

Gehen Sie folgendermaßen vor, um einen Connector einer Logik-App hinzuzufügen:

1. Wechseln Sie im Azure-Portal im Startmenü (auf der Startseite) zu **Marketplace**, und suchen Sie Ihre Logik-, mobile oder Web-App. 

	Wenn Sie eine neue Anwendung erstellen, suchen Sie nach "Logik-App", "Mobile App" oder "Web-App". Wählen Sie die App im neuen Blatt aus, und klicken Sie auf **Erstellen**. Unter [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) werden die Schritte erläutert.

2. Öffnen Sie Ihre App, und wählen Sie **Trigger und Aktionen** aus.
3. Wählen Sie im **Katalog** den Connector aus. Er wird Ihrer App hinzugefügt.
4. Konfigurieren Sie den Connector:
5. Jeder Connector hat Eigenschaften, die spezifisch für den Dienst und die Umgebung sind, mit der er eine Verbindung herstellt. Geben Sie die Details der Eigenschaften ein. Bedenken Sie, dass einige Eigenschaften optional sind.
6. Klicken Sie zum Speichern der Änderungen auf **OK**.


## Sicherheit
Connectors verwenden entweder OAuth oder Benutzernamen und Kennwörter.

## Weitere Informationen zu Logik- und Web-Apps
[Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md) | [Websites und Web-Apps in Azure App Service](../app-service-web/app-service-web-overview.md) |


## Weitere Connectors

[BizTalk-Integrationsconnectors](app-service-logic-integration-connectors.md) | [Enterprise-Connectors](app-service-logic-enterprise-connectors.md) | [Business-to-Business-Connectors](app-service-logic-b2b-connectors.md) | [Protokollconnectors](app-service-logic-protocol-connectors.md) | [App und Data Services-Connectors](app-service-logic-data-connectors.md) | [Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md)<br/><br/> [Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=62-->