<properties
   pageTitle="Twilio-Connector-API-App"
   description="Verwenden des Twilio-Connectors"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Verwenden des Twilio-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Der Twilio-Connector ermöglicht das Senden und Empfangen von SMS über ein Twilio-Konto. Außerdem können Sie Telefonnummern und Nutzungsdaten abrufen.

## Erstellen eines Twilio-Connectors für Ihre Logik-App ##
Zur Verwendung des Twilio-Connectors müssen Sie zunächst eine Instanz der Twilio-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "API-Apps", und suchen Sie nach "Twilio-Connector".
3.	Konfigurieren Sie den Twilio-Connector wie folgt:

	![][1]
	- **Standort** - Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll
	- **Webhostingplan** - Wählen Sie einen Webhostingplan aus oder erstellen Sie einen
	- **Tarif** - Wählen Sie einen Tarif für den Connector aus
	- **Name** - Geben Sie Ihrem Twilio-Connector einen Namen
	- **Paketeinstellungen**
		- **Konto-SID** - Der eindeutige Bezeichner des Kontos. Konto-SID für Ihr Konto kann abgerufen werden aus <https://www.twilio.com/user/account/settings>
		- **Auth Token** -Authentifizierungstoken, das dem Konto zugeordnet ist. Das Autorisierungstoken für Ihr Konto kann unter <https://www.twilio.com/user/account/settings> abgerufen werden.


4.	Klicken Sie auf "Erstellen". Ein neuer Twilio-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Twilio-Connectors erstellen.

## Verwenden des Twilio-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Twilio-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Twilio-Connector befindet.

	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.

	![][3]
3.	Der Twilio-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt.

	![][4]
4. Sie können die Twilio-Connector-API-App im Editor bearbeiten, indem Sie auf "Twilio-Connector" klicken.

5.	Sie können nun den Twilio-Connector im Datenfluss verwenden. Sie können die Aktion "Nachricht senden" im Datenfluss zum Senden einer Nachricht verwenden. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht senden" wie folgt:
	- **Von-Telefonnummer** - Geben Sie eine Twilio-Telefonnummer ein, die für den zu sendenden Nachrichtentyp aktiviert ist. Mit diesem Connector funktionieren nur Telefonnummern oder Shortcodes, die von Twilio erworben wurden.
	- **Zu-Telefonnummer** - Die Zieltelefonnummer. Das akzeptierte Format lautet: + gefolgt vom Ländercode und der Telefonnummer. Beispiel: +16175551212 Wenn Sie das + weglassen, verwendet Twilio den Ländercode, den Sie für die Absendertelefonnummer in 'Von' angegeben haben.
	- **Text** - Der zu sendende Nachrichtentext.
 
	![][5]
	![][6]



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!----HONumber=July15_HO4-->