<properties
   pageTitle="Verwendung des Yammer-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des Yammer-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="sameerch"/>


# Verwenden des Yammer-Connectors in Logik-Apps #
Stellen Sie eine Verbindung mit Yammer her, um Nachrichten zu veröffentlichen und mit einem Trigger neue Nachrichten abzurufen.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

## Erstellen eines Yammer-Connectors für Ihre Logik-App ##
Zur Verwendung des Yammer-Connectors müssen Sie zunächst eine Instanz der Yammer-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Yammer-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Konfigurieren Sie den Yammer-Connector wie folgt: ![][1]

	- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **App Service-Plan** – Wählen oder erstellen Sie einen Webhostingplan.
	- **Tarif** – Wählen Sie einen Tarif für den Connector.
	- **Name** – Geben Sie einen Namen für den Yammer-Connector ein.

4. Klicken Sie auf "Erstellen". Es wird ein neuer Yammer-Connector erstellt.
5. Sobald die API-App-Instanz erstellt wurde, können Sie eine Logik-App zur Verwendung des Yammer-Connectors erstellen.

## Verwenden des Yammer-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Yammer-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App: ![][2]

2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren: ![][3]

3.	Der Yammer-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt: ![][4]

4. Sie können die Yammer-Connector-API-App im Editor bearbeiten, indem Sie auf den "Yammer-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Yammer-Anmeldeinformationen an. Klicken Sie auf "Zulassen": ![][5] ![][6] ![][7]

Sie können nun den Yammer-Connector im Datenfluss verwenden.

## Verwenden des Yammer-Connectors als Trigger

Sie können die aus dem Yammer-Trigger ("Neue Nachricht") abgerufene neue Nachricht für andere Aktionen im Datenfluss verwenden. Konfigurieren Sie die Eingabeeigenschaften des Yammer-Triggers wie folgt:

- **Gruppen-ID** - die ID der Gruppe, aus der die neue Nachricht abgerufen werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht aus dem folgenden Feed abgerufen. Die Gruppen-ID kann aus der Gruppen-URL in Yammer abgerufen werden.
		
	Beispiel: Die Gruppen-ID in der folgenden URL lautet "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## Verwenden Sie den Yammer-Connector zum Bereitstellen einer Nachricht

Sie können den Yammer-Connector auch als Aktion in Ihren Logik-Apps verwenden. Zunächst geben Sie einen Trigger für Ihre Logik-App an oder Sie aktivieren "Diese Logik manuell ausführen" (siehe unten). Fügen Sie den Yammer-Connector hinzu, autorisieren Sie ihn nach Bedarf, und wählen Sie die Aktion "Nachricht bereitstellen". Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht bereitstellen" wie folgt:

- **Nachrichtentext** - Textinhalt der bereitzustellenden Nachricht.
- **Gruppen-ID** - Gibt die ID der Gruppe an, in der die neue Nachricht bereitgestellt werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht im Feed für das gesamte Unternehmen bereitgestellt. Die Gruppen-ID kann aus der Gruppen-URL in Yammer abgerufen werden.  

	Beispiel: Die Gruppen-ID in der folgenden URL lautet "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**Tag Users** – Ein Bereich von Netzwerkbenutzernamen, die in der Nachricht markiert werden müssen: ![][10] ![][11]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie finden die Swagger-REST-API-Referenz unter [Connectors and API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766) (in englischer Sprache).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=August15_HO9-->