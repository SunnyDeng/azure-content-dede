<properties
	pageTitle="Verwenden des Dropbox-Connectors in Logik-Apps in Azure App Service"
	description="Erste Schritte mit dem Dropbox-Connector in einer Logik-App in App Service"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="sameerch"/>

# Dropbox-Connector

Mit dem Dropbox-Connector können Sie Dateien in ein Dropbox-Konto hochladen bzw. aus diesem herunterladen.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

## Trigger und Aktionen

Ein Trigger startet eine neue Instanz basierend auf einem bestimmten Ereignis wie dem Eingang einer neuen Nachricht. Eine Aktion ist das Ergebnis, wie z. B. das Hochladen der Datei in Dropbox nach dem Empfang einer neuen Nachricht.

Der Dropbox-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Der Dropbox-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Keine | <ul><li>Datei löschen</li><li>Datei abrufen</li><li>Datei hochladen</li><li>Datei auflisten</li></ul>


## Erstellen eines Dropbox-Connectors für Ihre Logik-App
Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1.	Öffnen Sie den Azure Marketplace mit der Option „+NEU“ unten links im Azure-Portal.
2.	Navigieren Sie zu „Web und Mobil --> Azure Marketplace“, und suchen Sie nach „Dropbox-Connector“.
3.	Konfigurieren Sie den Dropbox-Connector wie folgt:
 
	![][1]
	- **Standort** - Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll.
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **App Service-Plan** - Wählen Sie einen Webhostingplan aus, oder erstellen Sie einen.
	- **Tarif** - Wählen Sie einen Tarif für den Connector aus.
	- **Name** - Geben Sie Ihrem Dropbox-Connector einen Namen.
4. Klicken Sie auf „Erstellen“. Ein neuer Dropbox-Connector wird erstellt.

## Verwenden des Dropbox-Connectors in Logik-Apps
Sobald Ihre API-App erstellt wurde, können Sie den Dropbox-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Dropbox-Connector befindet.

	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.

	![][3]
3.	Der Dropbox-Connector wird im Katalog auf der rechten Seite im Abschnitt „API-Apps in dieser Ressourcengruppe“ angezeigt.

	![][4]
4.	Sie können die Dropbox-Connector-API-App im Editor bearbeiten, indem Sie auf „Dropbox-Connector“ klicken. Klicken Sie auf die Schaltfläche „Autorisieren“. Geben Sie Ihre Dropbox-Anmeldeinformationen ein. Klicken Sie auf „Zulassen“.
 
	![][5]
	![][6]
	![][7]
6.	Sie können nun den Dropbox-Connector im Datenfluss verwenden. Sie können die Dropbox-Aktion „Datei hochladen“ zum Hochladen einer Datei in Ihr Dropbox-Konto verwenden.
 
	![][8]
	![][9]

Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

- **Dateipfad** – Gibt den Dropbox-Zieldateipfad der Datei an, die hochgeladen werden soll. Beispiel: Photos/image.png
- **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll. Dies stammt häufig aus einem vorherigen Schritt in Ihrer Logik-App.
- **Codierung für die Inhaltsübertragung** – Geben Sie „none“ oder „base64“ an.
- **Überschreiben** - Legen Sie dies auf "True" fest, um die Datei ggf. zu überschreiben.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

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

<!----HONumber=August15_HO8-->