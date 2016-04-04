<properties
   pageTitle="Verwendung des Twilio-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Twilio-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="sameerch"/>


# Erste Schritte mit dem Twilio-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Um die Schemaversion 2015-08-01-preview aufzurufen, klicken Sie auf [Twilio-API](../connectors/connectors-create-api-twilio.md).

Stellen Sie eine Verbindung mit Ihrem Twilio-Konto her, um SMS-Nachrichten zu senden und zu empfangen. Außerdem können Sie Telefonnummern und Nutzungsdaten abrufen. Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den Twilio-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Erstellen eines Twilio-Connectors für Ihre Logik-App ##
Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Twilio-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Konfigurieren Sie den Twilio-Connector wie folgt: ![][1]  
	- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Webhostingplan** – Wählen oder erstellen Sie einen Webhostingplan.
	- **Tarif** – Wählen Sie einen Tarif für den Connector.
	- **Name** – Geben Sie einen Namen für den Twilio-Connector ein.
	- **Paketeinstellungen**
		- **Konto-SID** – Der eindeutige Bezeichner des Kontos. Die Konto-SID für Ihr Konto kann unter <https://www.twilio.com/user/account/settings> abgerufen werden.
		- **Auth Token** – Autorisierungstoken, das dem Konto zugeordnet ist. Das Autorisierungstoken für Ihr Konto kann unter <https://www.twilio.com/user/account/settings> abgerufen werden.


4.	Klicken Sie auf "Erstellen". Es wird ein neuer Twilio-Connector erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie eine Logik-App zur Verwendung des Twilio-Connectors erstellen.

## Verwenden des Twilio-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Twilio-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie die Ressourcengruppe mit dem Twilio-Connector aus: ![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren: ![][3]
3.	Der Twilio-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt: ![][4]
4. Sie können die Twilio-Connector-API-App im Editor bearbeiten, indem Sie auf "Twilio-Connector" klicken.

5.	Sie können nun den Twilio-Connector im Datenfluss verwenden. Sie können die Aktion "Nachricht senden" im Datenfluss zum Senden einer Nachricht verwenden. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht senden" wie folgt:
	- **Von-Telefonnummer** - Geben Sie eine Twilio-Telefonnummer ein, die für den zu sendenden Nachrichtentyp aktiviert ist. Mit diesem Connector funktionieren nur Telefonnummern oder Shortcodes, die von Twilio erworben wurden.
	- **Zu-Telefonnummer** - Die Zieltelefonnummer. Das akzeptierte Format lautet: + gefolgt vom Ländercode und der Telefonnummer. Beispiel: +16175551212 Wenn Sie das + weglassen, verwendet Twilio den Ländercode, den Sie für die Absendertelefonnummer in 'Von' angegeben haben.
	- **Text** - Der zu sendende Nachrichtentext.

	![][5] ![][6]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=AcomDC_0323_2016-->