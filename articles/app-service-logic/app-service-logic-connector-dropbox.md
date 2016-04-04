<properties
	pageTitle="Verwendung des Dropbox-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des Dropbox-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
	authors="anuragdalmia"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="sameerch"/>

# Erste Schritte mit dem Dropbox-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Um die Schemaversion 2015-08-01-preview aufzurufen, klicken Sie auf [Dropbox-API](../connectors/connectors-create-api-dropbox.md).

Stellen Sie eine Verbindung mit einem Dropbox-Konto her, um Dateien hoch- oder herunterzuladen. Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten abzurufen und zu verarbeiten. Sie können den Dropbox-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Trigger und Aktionen

Ein Trigger startet eine neue Instanz basierend auf einem bestimmten Ereignis wie dem Eingang einer neuen Nachricht. Eine Aktion ist das Ergebnis, wie z. B. das Hochladen der Datei in Dropbox nach dem Empfang einer neuen Nachricht.

Der Dropbox-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Der Dropbox-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Keine | <ul><li>Datei löschen</li><li>Datei abrufen</li><li>Datei hochladen</li><li>Datei auflisten</li></ul>


## Erstellen eines Dropbox-Connectors für Ihre Logik-App
Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Dropbox-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Geben Sie den Namen, den App Service-Plan und andere Eigenschaften ein:
	![][1]
	- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **App Service-Plan** – Wählen oder erstellen Sie einen Webhostingplan.
	- **Tarif** – Wählen Sie einen Tarif für den Connector.
	- **Name** – Geben Sie einen Namen für den Dropbox-Connector ein.  
4. Klicken Sie auf **Erstellen**.


## Verwenden des Dropbox-Connectors in Logik-Apps
Sobald Ihre API-App erstellt wurde, können Sie den Dropbox-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie in der Logik-App **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren: ![][3]
2.	Der Dropbox-Connector wird im Katalog aufgeführt: 
	![][4]
3.	Wählen Sie den Dropbox-Connector aus, um ihn automatisch im Designer hinzuzufügen. Klicken Sie auf **Autorisieren**, geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **Zulassen**: 
	![][5] 
	![][6] 
	![][7]

Sie können nun den Dropbox-Connector im Datenfluss verwenden. Sie können die Dropbox-Aktion "Datei hochladen" zum Hochladen einer Datei in Ihr Dropbox-Konto verwenden: 
	![][8] 
	![][9]

Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

- **Dateipfad** – Gibt den Dropbox-Zieldateipfad der Datei an, die hochgeladen werden soll. Beispiel: Photos/image.png
- **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll. Dies stammt häufig aus einem vorherigen Schritt in Ihrer Logik-App.
- **Codierung für die Inhaltsübertragung** – Geben Sie „none“ oder „base64“ an.
- **Überschreiben** - Legen Sie dies auf "True" fest, um die Datei ggf. zu überschreiben.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=AcomDC_0323_2016-->