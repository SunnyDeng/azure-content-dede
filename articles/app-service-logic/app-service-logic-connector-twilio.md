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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Verwenden des Twilio-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Der Twilio-Connector ermöglicht das Senden und Empfangen von SMS über ein Twilio-Konto. Außerdem können Sie Telefonnummern und Nutzungsdaten abrufen.

## Erstellen eines Twilio-Connectors für Ihre Logik-App ##
Zur Verwendung des Twilio-Connectors müssen Sie zunächst eine Instanz der Twilio-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Navigieren Sie zu "API-Apps" und suchen Sie nach "Twilio-Connector".
3.	Konfigurieren Sie den Twilio-Connector wie folgt:
 
	![][1] - **Speicherort** – Wählen Sie den geografischen Standort, wo sollen die Connectors bereitgestellt werden - **Abonnement** - Wählen Sie ein Abonnement, möchten Sie diese Verbindung erstellt werden sollen – **Ressourcengruppe** - auswählen oder Erstellen einer Ressourcengruppe, in denen der Connector befinden sollte - **Webhosting-Plan** - auswählen oder erstellen Sie einen webhostingplan - **Pricing Tier** – Wählen Sie eine Preisstufe für den Connector - **Name** - Geben Sie einen Namen für Ihr Twilio-Connector - **Paket Einstellungen** - **Hauptkonto-SID** -den eindeutigen Bezeichner des Kontos. Hauptkonto-SID für Ihr Konto aus abgerufen werden kann <https://www.twilio.com/user/account/settings> - **Authentifizierungstoken** -Authentifizierungstoken, die dem Konto zugeordnet. Autorisierungstoken für Ihr Konto kann abgerufen werden <https://www.twilio.com/user/account/settings>


4.	Klicken Sie auf "Erstellen". Ein neuer Twilio-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Twilio-Connectors erstellen. 

## Verwenden des Twilio-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Twilio-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Twilio-Connector befindet.
 
	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 
	![][3]
3.	Der Twilio-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.
 
	![][4]
4. Sie können die Twilio-Connector-API-App im Editor bearbeiten, indem Sie auf "Twilio-Connector" klicken.
 
5.	Sie können nun den Twilio-Connector im Datenfluss verwenden. Sie können die Aktion "Nachricht senden" im Datenfluss zum Senden einer Nachricht verwenden. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht senden" wie folgt:
	- **Aus der Telefonnummer** – Geben Sie eine Twilio-Telefonnummer für den Typ der zu sendende Nachricht aktiviert. Mit diesem Connector funktionieren nur Telefonnummern oder Shortcodes, die von Twilio erworben wurden.
	- **Telefonnummer** -Ziel-Telefonnummer. Das Format akzeptiert wird: +, gefolgt von den Ländercode, und klicken Sie dann auf die Telefonnummer. Beispiel: +16175551212 Wenn Sie weglassen der + Twilio die Landeskennzahl, die Sie eingegeben, in der 'From' Anzahl haben verwenden.
	- **Text** -der Text der Nachricht gesendet werden soll.
 
	![][5] ![][6]



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->