<properties
   pageTitle="Verwendung des Chatter-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Chatter-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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


# Erste Schritte mit dem Chatter-Connector und das Hinzufügen zur Logik-App 
Sie stellen eine Verbindung mit Chatter her und veröffentlichen eine Nachricht oder durchsuchen einen Feed. Sie können z. B. einen Chatter-Feed durchsuchen, und sobald Sie etwas Bestimmtes finden, diese Chatter-Nachricht an die Gruppe "Vertrieb" senden.

Sie können den Chatter-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Trigger und Aktionen

Ein Trigger startet eine neue Instanz basierend auf einem bestimmten Ereignis wie dem Eingang einer neuen Chatter-Nachricht. Eine Aktion ist das Ergebnis einer Aktion, wie z. B. nach dem Empfang einer Chatter-Nachricht das Senden der Nachricht an eine andere Chatter-Gruppe oder Social Media-Website wie Facebook oder Twitter.

Der Chatter-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Der Chatter-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Neue Nachricht | <ul><li>Nachricht senden</li><li>Durchsuchen</li></ul>


## Erstellen des Chatter-Connectors für Ihre Logik-App
Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach Chatter-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Geben Sie den Namen, den App Service-Plan und andere Eigenschaften ein: ![][1]  
	- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Webhostingplan** – Wählen oder erstellen Sie einen Webhostingplan.
	- **Tarif** – Wählen Sie einen Tarif für den Connector.
	- **Name** – Geben Sie einen Namen für den Chatter-Connector ein.

4. Klicken Sie auf **Erstellen**.


## Verwenden des Chatter-Connectors in Logik-Apps
Sobald Ihre API-App erstellt wurde, können Sie den Chatter-Connector als Trigger oder Aktion in Ihrer Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1. Öffnen Sie In Ihrer Logik-Apps **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.

2. Der Chatter-Connector wird im Katalog aufgeführt: ![][4]
3. Wählen Sie den Chatter-Connector aus, um ihn automatisch im Designer hinzuzufügen. Klicken Sie auf **Autorisieren**, geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **Zulassen**: ![][5]![][6]![][7]

Sie können nun den Chatter-Connector im Datenfluss verwenden. Sie können die aus dem Chatter-Trigger („Neue Nachricht“) abgerufene neue Nachricht für andere Aktionen im Datenfluss verwenden. Konfigurieren Sie die Eingabeeigenschaften für den Chatter-Trigger wie folgt:

**Gruppen-ID** – Geben Sie die ID der Gruppe ein, aus der die neue Nachricht abgerufen werden soll. Wenn die Gruppen-ID nicht angegeben wird, werden neue Nachrichten aus dem Feed des Benutzers abgerufen: ![][8]![][9]


Auf ähnliche Weise können Sie die Chatter-Aktion im Datenfluss verwenden, um eine Nachricht bereitzustellen, indem Sie die Aktion „Nachricht bereitstellen“ auswählen. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht bereitstellen" wie folgt: – **Nachrichtentext** – Textinhalt der bereitzustellenden Nachricht – **Gruppen-ID** – Geben Sie die ID der Gruppe an, in der die Nachricht bereitgestellt werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht im Feed des Benutzers bereitgestellt. – **Dateiname** – Name der Datei, die dieser Nachricht angefügt werden soll – **Inhaltsdaten** – Inhaltsdaten der Anlage – **Inhaltstyp** – Inhaltstyp der Anlage – **Content Transfer Encoding** – Codierung für die Inhaltsübertragung der Nachricht ("none"|"base64") - **Erwähnungen** – Bereich von Benutzernamen, die in der Nachricht markiert werden sollen – **Hashtags** – Bereich von Hashtags, die mit der Nachricht bereitgestellt werden

![][10] ![][11]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=AcomDC_1203_2015-->