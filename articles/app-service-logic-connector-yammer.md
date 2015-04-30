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

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. 

Mit dem Yammer-Connector können Sie eine Verbindung mit Yammer herstellen und eine Aktion "Nachricht bereitstellen" sowie einen Trigger für den Abruf neuer Nachrichten ausführen.

## Erstellen eines Yammer-Connectors für Ihre Logik-App ##
Zur Verwendung des Yammer-Connectors müssen Sie zunächst eine Instanz der Yammer-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "Yammer-Connector".
3.	Konfigurieren Sie den Yammer-Connector wie folgt:
 
	![][1]
	- **Standort** - Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Webhosting-Plan** - Wählen oder erstellen Sie einen Webhosting-Plan.
	- **Preisstufe** - Wählen Sie eine Preisstufe für den Connector.
	- **Name** - Geben Sie einen Namen für den Yammer-Connector ein. 

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
5.	Sie können nun den Yammer-Connector im Datenfluss verwenden. Sie können die aus dem Yammer-Trigger ("Neue Nachricht") abgerufene neue Nachricht für andere Aktionen im Datenfluss verwenden. Konfigurieren Sie die Eingabeeigenschaften des Yammer-Triggers wie folgt:

	- **Gruppen-ID** - die ID der Gruppe, aus der die neue Nachricht abgerufen werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht aus dem folgenden Feed abgerufen. Die Gruppen-ID kann von der Gruppen-URL in Yammer abgerufen werden.
		Beispiel: Die Gruppen-ID in der folgenden URL lautet "5453203"
		https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
 
	![][8]
	![][9]

	

6.	Auf ähnliche Weise können Sie die Yammer-Aktion im Datenfluss verwenden, um eine Nachricht bereitzustellen, indem Sie die Aktion "Nachricht bereitstellen" auswählen. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht bereitstellen" wie folgt:
	- **Nachrichtentext** - Textinhalt der bereitzustellenden Nachricht.
	- **Gruppen-ID** - Gibt die ID der Gruppe an, in der die Nachricht bereitgestellt werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht im Feed für das gesamte Unternehmen bereitgestellt. Die Gruppen-ID kann von der Gruppen-URL in Yammer abgerufen werden. 
	Beispiel: Die Gruppen-ID in der folgenden URL lautet "5453203"
	https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Tag Users** - Ein Bereich von Netzwerkbenutzernamen, die in der Nachricht markiert werden müssen. 
	- 	**Anlagen** - Anlagen, die mit der Nachricht bereitgestellt werden sollen. Enthält die folgenden Felder:
		- Dateiname
		- Inhalt
		- Codierung für die Inhaltsübertragung
		- Inhaltstyp

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

<!--HONumber=52-->