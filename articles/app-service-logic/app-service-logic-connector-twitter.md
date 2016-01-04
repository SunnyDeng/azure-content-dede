<properties
   pageTitle="Verwendung des Twitter-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Twitter-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Erste Schritte mit dem Twitter-Connector und das Hinzufügen zur Logik-App
Verbinden Sie sich mit Ihrem Twitter-Feed, um Tweets zu posten und Tweets aus Ihrer Timeline, aus der Timeline Ihrer Freunde und die Follower Ihres Twitter-Kontos abzurufen. Connectors können in Logik-Apps als Teil eines "Workflows" verwendet werden, um Daten abzurufen, zu verarbeiten oder per Pushvorgang zu übermitteln. Wenn Sie den Twitter-Connector im Workflow verwenden, können Sie eine Vielzahl von Szenarien umsetzen. Dazu zählen z. B.:

- Abrufen neuer Tweets im Zusammenhang mit einem angegebenen Schlüsselwort oder Text. Wenn ein neuer Tweet abgerufen wird, wird eine neue Instanz Ihres Workflows ausgelöst, und die Daten werden an den nächsten Connector im Workflow übergeben. Beispielsweise können Sie einen Twitter-Connector erstellen und den Trigger "Neuer Tweet aus Suche" zum Überwachen von #peanutbutterandjelly verwenden. Bei jedem neuen Tweet für #peanutbutterandjelly wird der Workflow (d. h. die Logik-App) automatisch gestartet.
- Mithilfe der verschiedenen Aktionen, z. B. "Tweets durchsuchen", können Sie die Antwort auswählen und in Ihrem Workflow verwenden. Sie können z. B. Tweets auf den Namen Ihres Unternehmens durchsuchen. Wenn er gefunden wird, können Sie eine Logik-App zum Schreiben dieser Daten in eine SQL Server-Datenbank verwenden. Nutzen Sie anschließend die SQL Server-Daten, um zu bestimmen, was zu Ihrem Unternehmen getweetet wird. 
- Verwenden Sie alle Operatoren in der [Twitter-Suche](https://twitter.com/search). Wählen Sie den Link **Operatoren**. Der Twitter-Connector unterstützt alle aufgelisteten Operatoren.


## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Beispielsweise kann ein neuer Tweet einen Workflow oder Prozess auslösen oder starten. Ein *Aktion* ist das Ergebnis einer Aktivität. Sie können z. B. einen bestimmten Tweet suchen und, wenn er gefunden wurde, eine E-Mail senden oder eine Datenbank aktualisieren.

Der Twitter-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format.

Der Twitter-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Neuer Tweet aus Suche | <ul><li>Timeline eines Benutzer abrufen</li><li>Tweets suchen</li><li>Tweeten</li><li>Timeline zu Erwähnungen abrufen</li><li>Timeline auf Startseite abrufen</li><li>Follower abrufen</li><li>Freunde abrufen</li><li>Benutzerdetails abrufen</li><li>Tweet an Benutzer senden</li><li>Direktnachricht senden</li></ul>

Der Trigger **Neuer Tweet** wurde archiviert. Derzeit ist er noch als erweiterter Vorgang verfügbar und kann verwendet werden. Die Aktion **Retweet** wurde entfernt und wird nicht mehr unterstützt. Wenn Sie die Aktion "Retweet" verwenden, erhalten Sie zur Laufzeit einen Fehler. Entfernen Sie deshalb die Aktion "Retweet" aus Ihren Logik-Apps.


## Erstellen des Twitter-Connectors

> [AZURE.IMPORTANT]Während Sie einen Twitter-Connector erstellen, können Sie Ihre eigene App bei Twitter registrieren und die App-Schlüssel mit dem Twitter-Connector verwenden. Sie können eine Anwendung kostenlos auf [http://apps.twitter.com](http://apps.twitter.com) registrieren. Stellen Sie beim Registrieren sicher, dass Sie eine Rückruf-URL angeben. Sie können die Rückruf-URL später ändern, sobald der Twitter-Connector erstellt wurde. Sie benötigen den Twitter-API-Schlüssel und den geheimen Schlüssel zur Erstellung eines Connectors.

Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. [Optional] Erstellen Sie eine kostenlose Twitter-Anwendung unter [http://apps.twitter.com](http://apps.twitter.com).
    * Wenn Sie Ihre App registrieren, können Sie jede URL für die Website eingeben. Geben Sie eine beliebige Rückruf-URL an (lassen Sie diese Angabe nicht aus). Sie können sie später aktualisieren.
2. Wählen Sie im Azure-Startmenü **Marketplace** aus.
3. Suchen Sie den Twitter-Connector, wählen Sie den Eintrag aus, und klicken Sie dann auf **Erstellen**.
4. [Optional] Klicken Sie auf "Paketeinstellungen", und fügen Sie den "Verbraucherschlüssel" aus der Twitter-App in das Feld "clientId" ein. Fügen Sie den "geheimen Verbraucherschlüssel" aus der Twitter-App in das Feld "clientSecret" ein: ![][10]
5. Geben Sie andere erforderliche Einstellungen im Zusammenhang mit Connectorname, App Service und Ressourcengruppe ein.
6.	Klicken Sie auf **Erstellen**.

> [AZURE.NOTE]Wenn Sie Ihre Twitter-API mit der Umleitungs-URL zusätzlich absichern möchten, finden Sie die Details dazu [hier](app-service-logic-oauth-security.md).


## Verwenden des Twitter-Connectors in Ihrer Logik-App
Sobald Ihre API-App erstellt wurde, können Sie jetzt den Twitter-Connector als Trigger oder Aktion für Ihre Logik-Apps verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen Sie eine neue Logik-App oder öffnen Sie eine vorhandene Logik-App: ![][2]
2.	Öffnen Sie **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen: ![][3]
3.	Der Twitter-Connector wird auf der rechten Seite aufgeführt. Wählen Sie ihn aus, um ihn automatisch der Logik-App hinzuzufügen: ![][4]
4.	Klicken Sie auf **Autorisieren**, geben Sie Ihre Twitter-Anmeldeinformationen ein, und klicken Sie auf **App autorisieren**: ![][5]


Sie können jetzt den Twitter-Connector zum Erstellen des Workflows konfigurieren. Sie können die vom Twitter-Trigger abgerufenen Tweets in anderen Aktionen im Workflow verwenden: ![][6]

Auf ähnliche Weise können Sie die Twitter-Aktionen in Ihrem Workflow verwenden. Wählen Sie eine Twitter-Aktion aus, und konfigurieren Sie die Eingaben für die jeweilige Aktion: ![][7] ![][8]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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
[9]: ./media/app-service-logic-connector-twitter/settings.PNG
[10]: ./media/app-service-logic-connector-twitter/TwitterAPISettings.png

<!---HONumber=AcomDC_1203_2015-->