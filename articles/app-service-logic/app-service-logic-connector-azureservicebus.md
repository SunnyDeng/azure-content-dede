<properties
   pageTitle="Verwenden des Azure Service Bus-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Azure Service Bus-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="rajram"/>


# Erste Schritte mit dem Azure Service Bus-Connector und das Hinzufügen zur Logik-App 
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Um die Schemaversion 2015-08-01-preview aufzurufen, klicken Sie auf [Service Bus-API](../connectors/connectors-create-api-servicebus.md).

Stellen Sie Verbindungen mit Azure Service Bus her, um Nachrichten an Warteschlangen und Themen zu senden und um Nachrichten aus Warteschlangen und Abonnements zu empfangen. Connectors werden als Teil eines "Workflows" in Logik-Apps verwendet.

## Trigger und Aktionen
Trigger sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine Aktion ist das Ergebnis des Triggers. Senden Sie z. B. eine Warnung oder eine Nachricht, wenn ein Auftrag oder eine neue Nachricht in eine Warteschlange gestellt wird.

Der Azure Service Bus-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format.

Der Azure Service Bus-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Nachricht verfügbar | Nachricht senden

## Den Azure Service Bus-Connector erstellen
Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Azure Service Bus-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Geben Sie den Namen, den App Service-Plan und andere Eigenschaften ein:  
	![][1]

4. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Beschreibung
--- | ---
Verbindungszeichenfolge | Die Verbindungszeichenfolge für Azure Service Bus. Geben Sie z. B. Folgendes ein: *Endpoint=sb://[Namespace].servicebus.windows.net;SharedAccessKeyName=[Name];SharedAccessKey=[Schlüssel]*.
Entitätsname | Geben Sie den Namen der Warteschlange oder des Themas ein.
Abonnementname | Geben Sie den Namen des Abonnements ein, von dem Nachrichten empfangen werden sollen.

5. Klicken Sie auf **Erstellen**.

## Verwenden des Service Bus-Connectors in Logik-Apps
Sobald Ihr Connector erstellt wurde, können Sie jetzt den Azure Service Bus-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Azure Service Bus-Connector befindet:  
	![][2]

2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Workflow zu konfigurieren:  
	![][3]

3. Der Azure Service Bus-Connector wird im Katalog auf der rechten Seite im Abschnitt "API-Apps in dieser Ressourcengruppe" angezeigt:  
	![][4]

4. Sie können den Azure Service Bus-Connector im Editor bearbeiten, indem Sie auf "Azure Service Bus-Connector" klicken.

5.	Sie können nun den Azure Service Bus-Connector im Workflow verwenden. Sie können die aus dem Azure Service Bus-Trigger ("Nachricht verfügbar") abgerufene Nachricht für andere Aktionen im Workflow verwenden:  
	![][5]  

	![][6]

Sie können auch die Azure-Service Bus-Aktion "Nachricht senden" verwenden:  
![][7]

![][8]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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

<!---HONumber=AcomDC_0323_2016-->