<properties 
   pageTitle="Azure Service Bus-Connector-API-App" 
   description="Verwenden des Azure Service Bus-Connectors" 
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


# Verwenden des Azure Service Bus-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem Azure Service Bus-Connector können Sie Nachrichten an Warteschlangen/Themen/Abonnements senden oder daraus empfangen.

## Erstellen eines Azure Service Bus-Connectors für Ihre Logik-App ##
Zur Verwendung des Azure Service Bus-Connectors müssen Sie zunächst eine Instanz der Azure Service Bus-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option „+NEU“ unten links im Azure-Portal.
2.	Wechseln Sie zu „Web und Mobil > API-Apps“, und suchen Sie nach „Azure Service Bus-Connector“.
3.	Konfigurieren Sie den Azure Service Bus-Connector wie folgt:
 
	![][1] – **Standort** – Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll. – **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll. – **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll. – **Webhostingplan** – Wählen Sie einen Webhostingplan aus, oder erstellen Sie einen. – **Tarif** – Wählen Sie einen Tarif für den Connector aus. – **Name** – Geben Sie Ihrem Azure Service Bus-Connector einen Namen. – **Paketeinstellungen** – **Verbindungszeichenfolge** – Die Verbindungszeichenfolge für Azure Service Bus. Beispiel: Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key] - **Entitätsname** – Der Name der Warteschlange oder des Themas – **Abonnementname** – Der Name des Abonnements, aus dem Nachrichten empfangen werden

4.	Klicken Sie auf „Erstellen“. Ein neuer Azure Service Bus-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Azure Service Bus-Connectors erstellen. 

## Verwenden des Azure Service Bus-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie jetzt den Azure Service Bus-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Azure Service Bus-Connector befindet.
 
	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 
	![][3]
3.	Der Azure Service Bus-Connector wird im Katalog auf der rechten Seite im Abschnitt „API-Apps in dieser Ressourcengruppe“ angezeigt.
 
	![][4]
4. Sie können die Azure Service Bus-Connector-API-App im Editor bearbeiten, indem Sie auf „Azure Service Bus-Connector“ klicken.
 
5.	Sie können nun den Azure Service Bus-Connector im Datenfluss verwenden. Sie können die aus dem Azure Service Bus-Trigger („Nachricht verfügbar“) abgerufene Nachricht für andere Aktionen im Datenfluss verwenden.
 
	![][5] ![][6] 
6.	Auf ähnliche Weise können Sie die Azure Service Bus-Aktion „Nachricht senden“ zum Senden einer Nachricht verwenden.

	![][7] ![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=62-->