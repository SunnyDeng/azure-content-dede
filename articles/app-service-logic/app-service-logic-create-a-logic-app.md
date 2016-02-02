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
	ms.topic="get-started-article"
	ms.date="01/12/2016"
	ms.author="stepsic"/>

# Erstellen einer Logik-App zum Verbinden von SaaS-Diensten

| Kurzübersicht |
| --------------- |
| [Logik-Apps – Definitionssprache](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logik-Apps – Connector-Dokumentation](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Logik-Apps – Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=azurelogicapps) |

In diesem Thema wird veranschaulicht, wie Sie in nur wenigen Minuten in [App Services-Logik-Apps](app-service-logic-what-are-logic-apps.md) einsteigen können. Wir durchlaufen einen Workflow, mit dessen Hilfe Sie eine Gruppe von Tweets, an denen Sie interessiert sind, an einen Dropbox-Ordner übermitteln können.

Für dieses Szenario benötigen Sie Folgendes:

- Ein Azure-Abonnement
- Ein Twitter-Konto
- Ein Dropbox-Konto

<!--- TODO: Add try it now information here -->

## Erstellen von Connectors

Zunächst müssen Sie die beiden Connectors erstellen, die Sie verwenden möchten: den [Dropbox-Connector](app-service-logic-connector-dropbox.md) und den [Twitter-Connector](app-service-logic-connector-twitter.md). Aufgrund von Beschränkungen der Twitter-API müssen Sie außerdem eine kostenlose App bei Twitter registrieren. Gehen Sie so vor:

1. Melden Sie sich beim Azure-Portal an.

2. Klicken Sie auf der Startseite auf [Marketplace](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps/), und suchen Sie nach Twitter (oder [klicken Sie hier](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2/)).

3. Wählen Sie **Twitter-Connector** aus, und klicken Sie auf **Erstellen**. Eine Ansicht für alle Ihre Einstellungen wird angezeigt. Sie können als Name **Twitter-Connector** übernehmen.
4. Wählen Sie **Paketeinstellungen** – hier müssen Sie die Informationen aus der Twitter-Anwendung eingeben. Sie können eine kostenlose Anwendung über die folgenden Schritte einrichten:
	1. Wechseln Sie zur [Twitter-App-Registrierungsseite](http://apps.twitter.com).
	2. Erstellen Sie eine neue App.
	3. Benennen Sie die App und fügen Sie eine Beschreibung hinzu. Sie können eine beliebige URL für die Website eingeben und eine für den Rückruf-URL (nicht leer lassen).
	4. Kopieren Sie nach der Registrierung den **Verbraucherschlüssel** aus Twitter in das Feld **clientId** in Azure, und den **geheimen Verbraucherschlüssel** aus Twitter in das Feld **clientSecret**.
	5. Klicken Sie im Bereich "Azure" auf **OK**, um zu den anderen API-Einstellungen zurückzukehren.

5. Geben Sie in **Neuen App Service-Plan erstellen** einen Plannamen ein.

	>[AZURE.NOTE]Die Schritte in diesem Abschnitt setzen voraus, dass Sie einen neuen App Service-Plan erstellen. Wenn Sie einen vorhandenen App Service-Plan verwenden, klicken Sie auf **Vorhandenen auswählen**, wählen Ihren vorhandenen Plan aus und fahren mit dem letzten Schritt in diesem Abschnitt fort. Sie benötigen einen Plan, um all Ihre Apps zu hosten.

6.  Wählen Sie einen **Tarif** für den neuen Plan.

	>[AZURE.NOTE]Standardmäßig werden nur für Logik-Apps empfohlene Pläne angezeigt. Klicken Sie auf **Alle anzeigen**, um alle verfügbaren Pläne anzuzeigen. Bei Ausführen einer Logik-App auf der Stufe "Kostenlos" sind nur eine einmalige Ausführung pro Stunde und maximal 1000 Aktionen pro Monat möglich.

7. Erstellen Sie eine **Ressourcengruppe** für Ihren Datenfluss.

	Ressourcengruppen dienen als Container für Ihre Anwendungen. Alle Ressourcen für Ihre Anwendung befinden sich in der gleichen Ressourcengruppe.

8. Wenn Sie mehr als ein Azure-Abonnement haben, wählen Sie dasjenige, das Sie verwenden möchten.

9. Wählen Sie den **Standort** für die Ausführung Ihrer Logik-App aus.

	![Ansicht "API-App erstellen"](./media/app-service-logic-create-a-logic-app/gallery.png)

10. Klicken Sie auf **Erstellen**. Der Bereitstellungsschritt kann einige Minuten dauern.

11. Wiederholen Sie nun den Vorgang für [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2/).

## Starten der Logik-App

Nun müssen Sie eine neue Logik-App erstellen:

1. Klicken Sie am linken unteren Bildschirmrand auf **+ Neu**, erweitern Sie **Web + Mobil**, und klicken Sie dann auf **Logik-App**.

 	Die Ansicht "Logik-App erstellen" wird angezeigt, auf dem Sie zum Einstieg Grundeinstellungen angeben können.

	![Ansicht "Logik-App erstellen"](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. Geben Sie in **Name** einen aussagekräftigen Namen für Ihre Logik-App ein.

3. Wählen Sie den **App Service-Plan**, den Sie beim Erstellen Ihrer Connectors verwendet haben. Dadurch werden automatisch der Speicherort, das Abonnement und die Ressourcengruppe ausgewählt.

Die grundlegenden Einstellungen sind nun festgelegt. Klicken Sie jedoch noch nicht auf **Erstellen**. Als Nächstes fügen Sie Ihrem Workflow Trigger und Aktionen hinzu.

## Hinzufügen eines Triggers

Trigger sind die Grundlage der Ausführung Ihrer Logik-App. Als Nächstes fügen Sie einen Wiederholungstrigger hinzu, der den Workflow gemäß einem vordefinierten Zeitplan startet.

1. Klicken Sie immer noch in der Ansicht **Logik-App erstellen** auf **Trigger und Aktionen**.

	Ein Designer wird als Vollbild eingeblendet, der Ihren Datenfluss und einige Vorlagen zeigt, mit denen Sie starten können.
	
2. Für dieses Tutorial **starten wir die Erstellung von Grund auf**. Sie können stets eine Vorlage nutzen, wenn dies sinnvoll erscheint.
    
    Auf der rechten Seite finden Sie nun eine Liste aller Dienste, die Trigger haben können.

3. Klicken Sie im oberen Abschnitt auf **Wiederholung**.

	Dadurch wird ein Feld hinzugefügt, in dem Sie die Wiederholungseinstellungen angeben können.

	![Serie](./media/app-service-logic-create-a-logic-app/recurrence.png)

4.  Wählen Sie für die Wiederholung eine **Frequenz** und ein **Intervall** (z. B. einmal pro Stunde) aus, und klicken Sie dann auf das grüne Häkchen.

Nun fügen Sie dem Datenfluss eine Aktion hinzu.

## Hinzufügen einer Twitter-Aktion

Aktionen sind das, was in Ihrem Workflow stattfindet. Sie können eine beliebige Anzahl von Aktionen verwenden und so organisieren, dass Informationen von einer Aktion zur nächsten übergeben werden.

1. Klicken Sie im rechten Bereich auf **Twitter-Connector**.

2. Klicken Sie nach dem Laden auf **Autorisieren**, melden Sie sich bei Ihrem Twitter-Konto an, und klicken Sie auf **App autorisieren**.

	Dadurch wird dem Connector Zugriff auf Ihr Twitter-Konto gewährt. Eine Liste möglicher Vorgänge, die vom Twitter-Connector bereitgestellt werden, wird angezeigt.

	![Aktionen](./media/app-service-logic-create-a-logic-app/actions.png)

	> [AZURE.NOTE] Die Schaltfläche **Autorisieren** verwendet OAuth-Sicherheitseinstellungen, um mit SaaS-Diensten wie Twitter eine Verbindung herzustellen. Mehr zu OAUTH finden Sie unter [OAUTH-Sicherheit](app-service-logic-oauth-security.md).

3. Klicken Sie auf **Tweets durchsuchen**, geben Sie dann in **Abfrage eingeben** beispielsweise `#MicrosoftAzure` ein, und klicken Sie auf das grüne Häkchen.

	![Twitter-Suche](./media/app-service-logic-create-a-logic-app/twittersearch.png)

Der Twitter-Connector ist nun Teil des Workflows.

## Hinzufügen einer Dropbox-Aktion und Erstellen der App

Der letzte Schritt ist das Hinzufügen einer Aktion, die Tweets in eine Dropbox-Datei hochlädt.

1. Klicken Sie im rechten Bereich auf **Dropbox-Connector**.

2. Nachdem die Bereitstellung abgeschlossen ist, klicken Sie auf **Autorisieren**, melden Sie sich bei Ihrem Dropbox-Konto an, und klicken Sie auf **Zulassen**.

	![Autorisieren des Dropbox-Connectors](./media/app-service-logic-create-a-logic-app/authorize.png)

	Dadurch wird dem Connector Zugriff auf Ihr Dropbox-Konto gewährt. Eine Liste möglicher Vorgänge, die vom Dropbox-Connector bereitgestellt werden, wird angezeigt.

4. Klicken Sie auf **Datei hochladen**.

	Die Einstellungen des Dropbox-Connectors werden angezeigt, die Sie festlegen müssen, um die Daten aus der Twitter-Suche an Dropbox zu übergeben.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. Geben Sie in das Feld **Dateipfad** `/tweet.txt` ein.

4. Klicken Sie im Feld **Inhalt** auf die Schaltfläche `...`, und klicken Sie auf die Option **Tweet-Text**.

	Dadurch wird der Wert `@first(body('twitterconnector')).TweetText` in das Textfeld eingegeben. Dieser generierte Wert enthält die folgenden Teile:

	Inhaltsteil | Beschreibung
	------------------------------------------ | ------------
	 `@` | Gibt an, dass Sie eine Funktion statt eines tatsächlichen Werts eingeben.
	`actions('twitterconnector').outputs.body` | Ruft die Tweets ab, die von der Twitter-Connector-Abfrage zurückgegeben wurden.
	`first()` | Die Aktion "Tweets durchsuchen" gibt eine Liste zurück, aber Sie möchten nur eine Datei hochladen.
	`.TweetText` | Wählt die Tweet-Nachrichteneigenschaft aus.

5. Klicken Sie auf das grüne Häkchen, um die Connectoreinstellungen zu speichern.

5. Nun da der Entwurf fertig ist, klicken Sie links oben im Designer auf **Codeansicht**. Beachten Sie, dass dies der JSON-Code ist, der den Workflow definiert, den Sie zuvor im Designer erstellt haben. Auf diesen Code gehen wir im [nächsten Thema][Use logic app features] näher ein.

6. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **OK** und dann auf die Schaltfläche **Erstellen**.

	Dadurch wird die neue Logik-App erstellt.

## Verwalten Ihrer Logik-App nach der Erstellung

Ihre Logik-App wird nun ausgeführt. Jedes Mal, wenn der geplante Workflow ausgeführt wird, überprüft er Tweets auf das spezifische Hashtag. Wenn ein übereinstimmender Tweet gefunden wird, wird er in Ihrer Dropbox abgelegt. Schließlich wird noch gezeigt, wie Sie die App deaktivieren.

1. Klicken Sie auf der linken Seite des Bildschirms auf **Durchsuchen**, und wählen Sie **Logik-Apps** aus.

2. Klicken Sie auf die neue Logik-App, die Sie gerade erstellt haben, um den aktuellen Status und allgemeine Informationen anzuzeigen.

3. Um die neue Logik-App zu bearbeiten, klicken Sie auf **Trigger und Aktionen**.

5. Um die App zu deaktivieren, klicken Sie auf der Befehlsleiste auf **Deaktivieren**.

In weniger als 5 Minuten haben Sie eine einfache Logik-App eingerichtet, die in der Cloud ausgeführt wird. Weitere Informationen zur Verwendung von Logik-App-Features finden Sie unter [Verwenden von Logik-App-Features]. Informationen zu den Logik-App-Definitionen selbst finden Sie unter [Logik-App-Definitionen erstellen](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[Verwenden von Logik-App-Features]: app-service-logic-use-logic-app-features.md

<!---HONumber=AcomDC_0128_2016-->