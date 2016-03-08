<properties
	pageTitle="Erstellen einer Logik-App | Microsoft Azure"
	description="Informationen zum Erstellen einer Logik-App zum Verbinden von SaaS-Diensten"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/01/2016"
	ms.author="stepsic"/>

# Erstellen einer Logik-App zum Verbinden von SaaS-Diensten

| Kurzübersicht |
| --------------- |
| [Logik-Apps – Definitionssprache](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logik-Apps – Connector-Dokumentation](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Logik-Apps – Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=azurelogicapps) |

In diesem Thema wird veranschaulicht, wie Sie in nur wenigen Minuten in [App Services-Logik-Apps](app-service-logic-what-are-logic-apps.md) einsteigen können. Wir durchlaufen einen Workflow, mit dessen Hilfe Sie eine Gruppe von Tweets, an denen Sie interessiert sind, an ein Postfach übermitteln können.

Für dieses Szenario benötigen Sie Folgendes:

- Ein Azure-Abonnement
- Ein Twitter-Konto
- Ein Office 365-Konto

## Erstellen einer neuen Logik-App zum Empfangen von Tweets per E-Mail

1. Wählen Sie im Dashboard des Azure-Portals **Marketplace** aus. 
2. Suchen Sie unter „Alles“ nach „Logik-Apps“, und wählen Sie dann **Logik-App (Vorschau)** aus. Sie können auch **Neu**, **Web- und mobile Anwendungen** und dann **Logik-App (Vorschau)** auswählen. 
3. Geben Sie einen Namen für Ihre Logik-App ein, wählen Sie den App Service-Plan aus, und wählen Sie **Erstellen**. In diesem Schritt wird davon ausgegangen, dass Sie über einen App Service-Plan verfügen und mit den erforderlichen Eigenschaften vertraut sind. Ist dies nicht der Fall, keine Sorge: Sie können bei [Azure App Service-Pläne – Detaillierte Übersicht](azure-web-sites-web-hosting-plans-in-depth-overview.md) beginnen. 

4. Beim ersten Öffnen der Logik-App benötigen Sie einen Trigger. Suchen Sie im Triggersuchfeld nach **twitter**, und wählen Sie das Ergebnis aus.

7. Jetzt geben Sie das Schlüsselwort ein, nach dem Twitter durchsucht werden soll. ![Twitter-Suche](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Wählen Sie das Pluszeichen (+) aus, und wählen Sie dann die Option **Aktion hinzufügen** oder **Bedingung hinzufügen**: ![Plus](./media/app-service-logic-create-a-logic-app/plus.png)
6. Wenn Sie **Aktion hinzufügen** wählen, werden alle Connectors mit den jeweiligen verfügbaren Aktionen aufgeführt. Sie können dann auswählen, welcher Connector und welche Aktion Ihrer Logik-App hinzugefügt wird. Sie können z. B. **Office 365 – E-Mail senden** und weitere Office 365-Aktionen auswählen: ![Actions](./media/app-service-logic-create-a-logic-app/actions.png)

7. Nun müssen Sie die Parameter für die gewünschten E-Mails ausfüllen: ![Parameter](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Wählen Sie abschließend **Speichern**, um Ihre Logik-App online zu stellen.

## Verwalten Ihrer Logik-App nach der Erstellung

Ihre Logik-App wird nun ausgeführt. Jedes Mal, wenn der geplante Workflow ausgeführt wird, überprüft er Tweets auf das spezifische Hashtag. Wenn ein übereinstimmender Tweet gefunden wird, wird er in Ihrer Dropbox abgelegt. Schließlich wird noch gezeigt, wie Sie die App deaktivieren.

1. Klicken Sie auf der linken Seite des Bildschirms auf **Durchsuchen**, und wählen Sie **Logik-Apps** aus.

2. Klicken Sie auf die neue Logik-App, die Sie gerade erstellt haben, um den aktuellen Status und allgemeine Informationen anzuzeigen.

3. Um die neue Logik-App zu bearbeiten, klicken Sie auf **Trigger und Aktionen**.

5. Um die App zu deaktivieren, klicken Sie auf der Befehlsleiste auf **Deaktivieren**.

In weniger als 5 Minuten haben Sie eine einfache Logik-App eingerichtet, die in der Cloud ausgeführt wird. Weitere Informationen zur Verwendung von Logik-App-Features finden Sie unter [Verwenden von Logik-App-Features]. Informationen zu den Logik-App-Definitionen selbst finden Sie unter [Logik-App-Definitionen erstellen](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Verwenden von Logik-App-Features]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0302_2016-->