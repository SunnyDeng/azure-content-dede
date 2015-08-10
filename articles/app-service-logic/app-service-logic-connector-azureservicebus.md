<properties
   pageTitle="Verwenden des Azure Service Bus-Connectors in Azure App Service"
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
   ms.date="06/29/2015"
   ms.author="andalmia"/>


# Azure Service Bus-Connector

Mit dem Azure Service Bus-Connector können Sie Nachrichten aus Service Bus-Entitäten wie Warteschlangen und Themen senden und Nachrichten von Service Bus-Entitäten wie Warteschlangen und Abonnements empfangen.

## Trigger und Aktionen
Trigger sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine Aktion ist das Ergebnis des Triggers. Senden Sie z. B. eine Warnung oder eine Nachricht, wenn ein Auftrag oder eine neue Nachricht in eine Warteschlange gestellt wird.

Der Azure Service Bus-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format.

Der Azure Service Bus-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Nachricht verfügbar | Nachricht senden

## Den Azure Service Bus-Connector erstellen
Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus, und suchen Sie nach "Azure Service Bus-Connector".
3. Geben Sie den Namen, App Service-Plan und andere Eigenschaften ein: <br/> ![][1]

4. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Beschreibung
--- | ---
Verbindungszeichenfolge | Die Verbindungszeichenfolge für Azure Service Bus. Geben Sie z. B. Folgendes ein: *Endpoint=sb://\[Namespace\].servicebus.windows.net;SharedAccessKeyName=\[Name\];SharedAccessKey=\[Schlüssel\]*.
Entitätsname | Geben Sie den Namen der Warteschlange oder des Themas ein.
Abonnementname | Geben Sie den Namen des Abonnements ein, von dem Nachrichten empfangen werden sollen.

5. Klicken Sie auf **Erstellen**.

Nachdem der Connector erstellt wurde, können Sie ihn einer Logik-App in derselben Ressourcengruppe hinzufügen.

## Verwenden des Service Bus-Connectors in Logik-Apps
Sobald Ihr Connector erstellt wurde, können Sie jetzt den Azure Service Bus-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Azure Service Bus-Connector befindet: <br/> ![][2]

2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Workflow zu konfigurieren: <br/> ![][3]

3. Der Azure Service Bus-Connector wird im Katalog auf der rechten Seite im Abschnitt "API-Apps in dieser Ressourcengruppe" angezeigt: <br/> ![][4]

4. Sie können den Azure Service Bus-Connector im Editor bearbeiten, indem Sie auf "Azure Service Bus-Connector" klicken.

5.	Sie können nun den Azure Service Bus-Connector im Workflow verwenden. Sie können die aus dem Azure Service Bus-Trigger \("Nachricht verfügbar"\) abgerufene Nachricht für andere Aktionen im Workflow verwenden: <br/> ![][5] <br/> ![][6]

Sie können auch die Azure-Service Bus-Aktion "Nachricht senden" verwenden: <br/> ![][7] <br/> ![][8]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=July15_HO5-->