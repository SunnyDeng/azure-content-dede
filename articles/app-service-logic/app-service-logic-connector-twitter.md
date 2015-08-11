<properties
   pageTitle="Verwenden des Twitter-Connectors in Microsoft Azure App Service"
   description="Verwenden der Twitter-Connector-API-App"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/14/2015"
   ms.author="sameerch"/>


# Twitter-Connector

Verbinden Sie sich mit Ihrem Twitter-Feed, um Tweets zu posten und Tweets aus Ihrer Timeline, aus der Timeline Ihrer Freunde und die Follower Ihres Twitter-Kontos abzurufen. Connectors können in Logik-Apps als Teil eines "Workflows" verwendet werden, um Daten abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Wenn Sie den Twitter-Connector im Workflow verwenden, können Sie eine Vielzahl von Szenarien umsetzen. Dazu zählen z. B.:

- Abrufen neuer Tweets im Zusammenhang mit einem angegebenen Schlüsselwort oder Text. Wenn ein neuer Tweet abgerufen wird, wird eine neue Instanz Ihres Workflows ausgelöst, und die Daten werden an den nächsten Connector im Workflow übergeben. Beispielsweise können Sie einen Twitter-Connector erstellen und den Trigger "Neuer Tweet aus Suche" zum Überwachen von #peanutbutterandjelly verwenden. Bei jedem neuen Tweet für #peanutbutterandjelly wird der Workflow (d. h. die Logik-App) automatisch gestartet.
- Mithilfe der verschiedenen Aktionen, z. B. "Tweets durchsuchen", können Sie die Antwort auswählen und in Ihrem Workflow verwenden. Sie können z. B. Tweets auf den Namen Ihres Unternehmens durchsuchen. Wenn er gefunden wird, können Sie eine Logik-App zum Schreiben dieser Daten in eine SQL Server-Datenbank verwenden. Nutzen Sie anschließend die SQL Server-Daten, um zu bestimmen, was zu Ihrem Unternehmen getweetet wird. 


## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Beispielsweise kann ein neuer Tweet einen Workflow oder Prozess auslösen oder starten. Ein *Aktion* ist das Ergebnis einer Aktivität. Sie können z. B. einen bestimmten Tweet suchen und, wenn er gefunden wurde, eine E-Mail senden oder eine Datenbank aktualisieren.

Der Twitter-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format.

Der Twitter-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Neuer Tweet aus Suche | <ul><li>Timeline eines Benutzer abrufen</li><li>Tweets suchen</li><li>Tweeten</li><li>Timeline zu Erwähnungen abrufen</li><li>Timeline auf Startseite abrufen</li><li>Follower abrufen</li><li>Freunde abrufen</li><li>Benutzerdetails abrufen</li><li>Tweet an Benutzer senden</li><li>Direktnachricht senden</li></ul>

> [AZURE.IMPORTANT]Der Trigger **Neuer Tweet** wurde archiviert. Derzeit ist er noch als erweiterter Vorgang verfügbar und kann verwendet werden. Die Aktion **Retweet** wurde entfernt und wird nicht mehr unterstützt. Wenn Sie die Aktion "Retweet" verwenden, erhalten Sie zur Laufzeit einen Fehler. Entfernen Sie deshalb die Aktion "Retweet" aus Ihren Logik-Apps.


## Erstellen des Twitter-Connectors
Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Twitter-Connector".
3. Geben Sie den Namen, den App Service-Plan und andere Eigenschaften ein:

	![][1]
4.	Klicken Sie auf **Erstellen**.


## Verwenden des Twitter-Connectors in Ihrer Logik-App
Sobald Ihre API-App erstellt wurde, können Sie jetzt den Twitter-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen einer neuen Logik-App oder Öffnen einer vorhandenen Logik-App:

	![][2]
2.	Öffnen Sie **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen:

	![][3]
3.	Der Twitter-Connector wird auf der rechten Seite aufgeführt. Wählen Sie ihn aus, um ihn automatisch Ihrer Logik-App hinzuzufügen:

	![][4]
4.	Klicken Sie auf **Autorisieren** aus, geben Sie Ihre Twitter-Anmeldeinformationen ein, und klicken Sie auf **App autorisieren**:

	![][5]


Sie können jetzt den Twitter-Connector zum Erstellen des Workflows konfigurieren. Sie können die vom Twitter-Trigger abgerufenen Tweets in anderen Aktionen im Workflow verwenden:

	![][6]

Auf ähnliche Weise können Sie die Twitter-Aktionen in Ihrem Workflow verwenden. Wählen Sie eine Twitter-Aktion aus, und konfigurieren Sie die Eingaben für die jeweilige Aktion:

	![][7]
	![][8]
## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png

<!----HONumber=July15_HO5-->