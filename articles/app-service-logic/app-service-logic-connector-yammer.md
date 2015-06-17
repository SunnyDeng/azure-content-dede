<properties 
   pageTitle="Yammer-Connector-API-App" 
   description="Verwenden des Yammer-Connectors" 
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


# Verwenden des Yammer-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem Yammer-Connector können Sie eine Verbindung mit Yammer herstellen und eine Aktion "Nachricht bereitstellen" sowie einen Trigger für den Abruf neuer Nachrichten ausführen.

## Erstellen eines Yammer-Connectors für Ihre Logik-App ##
Zur Verwendung des Yammer-Connectors müssen Sie zunächst eine Instanz der Yammer-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "Yammer-Connector".
3.	Konfigurieren Sie den Yammer-Connector wie folgt:
 
 ![][1]
 
	- **Location** - choose the geographic location where you would like the connector to be deployed
	- **Subscription** - choose a subscription you want this connector to be created in
	- **Resource group** - select or create a resource group where the connector should reside
	- **App Service plan** - select or create a web hosting plan
	- **Pricing tier** - choose a pricing tier for the connector
	- **Name** - give a name for your Yammer Connector 

4.	Klicken Sie auf "Erstellen". Ein neuer Yammer-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Yammer-Connectors erstellen. 

## Verwenden des Yammer-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Yammer-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Yammer-Connector befindet.
 
![][2]
 
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 
![][3]
 
3.	Der Yammer-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.
 
![][4]
 
4. Sie können die Yammer-Connector-API-App im Editor bearbeiten, indem Sie auf "Yammer-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Yammer-Anmeldeinformationen an. Klicken Sie auf "Zulassen".

![][5]
 
![][6]
 
![][7]
 
Sie können nun den Yammer-Connector im Datenfluss verwenden.

## Verwenden Sie den Connector Yammer als Trigger.

1.	 Sie können die aus dem Yammer-Trigger ("Neue Nachricht") abgerufene neue Nachricht für andere Aktionen im Datenfluss verwenden. Konfigurieren Sie die Eingabeeigenschaften des Yammer-Triggers wie folgt:

	- **Gruppen-ID** -die ID der Gruppe, aus dem die neue Nachricht abgerufen werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht aus dem folgenden Feed abgerufen. Die Gruppen-ID kann von der Gruppen-URL in Yammer abgerufen werden. Beispiel: Gruppen-ID in der folgenden URL ist "5453203" https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203

	![][8]
 
	![][9]

## Verwenden Sie den Yammer-Connector zum Übermitteln einer Nachricht

6.	Sie können auch den Connector Yammer als Aktion in Ihren apps Logik verwenden. Zunächst geben Sie einen Trigger für Ihre Anwendung Logik oder überprüfen "Diese Logik manuell ausführen" (siehe unten). Den Yammer-Connector hinzufügen, nach Bedarf zu autorisieren, und wählen Sie die Aktion "Post-Nachricht". Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht bereitstellen" wie folgt:

	- **Meldungstext** -Text-Inhalt der Nachricht bereitgestellt wird
	- **Gruppen-ID** -Geben Sie die ID der Gruppe auf das neue Nachricht gebucht werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht im Feed für das gesamte Unternehmen bereitgestellt. Die Gruppen-ID kann von der Gruppen-URL in Yammer abgerufen werden. Beispiel: Gruppen-ID in der folgenden URL ist "5453203" https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Tag Benutzer** -Array von Netzwerk-Benutzernamen, der in der Nachricht markiert werden muss. 

	![][10]
 
	![][11]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=GIT-SubDir_Tue_AM_dede-->