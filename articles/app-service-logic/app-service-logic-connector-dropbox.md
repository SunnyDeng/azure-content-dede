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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# Verwenden des Dropbox-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem Dropbox-Connector können Sie Dateien in ein Dropbox-Konto hochladen bzw. aus diesem herunterladen.

## Erstellen eines Dropbox-Connectors für Ihre Logik-App ##
Zur Verwendung des Dropbox-Connectors müssen Sie zunächst eine Instanz der Dropbox-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Navigieren Sie zu "--> Web- und Mobile Azure Marketplace", und suchen Sie nach "Dropbox-Connector".
3.	Konfigurieren Sie den Dropbox-Connector wie folgt:
 
	![][1] - **Speicherort** – Wählen Sie den geografischen Standort, wo sollen die Connectors bereitgestellt werden - **Abonnement** - Wählen Sie ein Abonnement, möchten Sie diese Verbindung erstellt werden sollen – **Ressourcengruppe** – auswählen oder Erstellen einer Ressourcengruppe, in denen der Connector befinden sollte - **App Dienstplan** - auswählen oder erstellen Sie einen webhostingplan - **Preise Ebene** – Wählen Sie eine Preisstufe für den Connector - **Name** -Geben Sie einen Namen für den Dropbox Connector
4. Klicken Sie auf "Erstellen". Ein neuer Dropbox-Connector wird erstellt.
5. Sobald die Dropbox-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Dropbox-Connectors erstellen.

## Verwenden des Dropbox-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Dropbox-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Dropbox-Connector befindet.
 	
	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 	
	![][3]
3.	Der Dropbox-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.
 
	![][4]
4.	Sie können die Dropbox-Connector-API-App im Editor bearbeiten, indem Sie auf "Dropbox-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Dropbox-Anmeldeinformationen ein. Klicken Sie auf "Zulassen".
 
	![][5] ![][6] ![][7]
6.	Sie können nun den Dropbox-Connector im Datenfluss verwenden. Sie können die Dropbox-Aktion "Datei hochladen" zum Hochladen einer Datei in Ihr Dropbox-Konto verwenden.
 
	![][8] ![][9]

Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

- **Dateipfad** -Geben Sie den Ziel-Dropbox-Dateipfad der Datei hochgeladen werden. Beispiel: Photos/image.png
- **Inhalt** -gibt den Inhalt der Datei hochgeladen werden. Dies wird häufig von einem vorherigen Schritt in Ihrer App Logik stammen.
- **Transfer Inhaltscodierung** -keine angeben oder base64.
- **Überschreiben** – Geben Sie "true", um die Datei zu überschreiben, wenn sie bereits vorhanden ist.


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
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->