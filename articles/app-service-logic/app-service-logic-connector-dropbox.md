<properties
	pageTitle="Dropbox-Connector"
	description="Erste Schritte mit dem Dropbox-Connector"
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
	ms.date="07/02/2015"
	ms.author="sameerch"/>

# Verwenden des Dropbox-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem Dropbox-Connector können Sie Dateien in ein Dropbox-Konto hochladen bzw. aus diesem herunterladen.

## Erstellen eines Dropbox-Connectors für Ihre Logik-App ##
Zur Verwendung des Dropbox-Connectors müssen Sie zunächst eine Instanz der Dropbox-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

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
5. Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Dropbox-Connectors erstellen.

## Verwenden des Dropbox-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Dropbox-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

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

Konfigurieren Sie die Eingabeeigenschaften für die Aktion „Datei hochladen“ wie folgt:

- **Dateipfad** – Gibt den Dropbox-Zieldateipfad der Datei an, die hochgeladen werden soll. Beispiel: Photos/image.png
- **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll. Dies stammt häufig aus einem vorherigen Schritt in Ihrer Logik-App.
- **Codierung für die Inhaltsübertragung** – Geben Sie „none“ oder „base64“ an.
- **Überschreiben** – Legen Sie hierfür „True“ fest, um die Datei ggf. zu überschreiben.


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

<!----HONumber=July15_HO4-->