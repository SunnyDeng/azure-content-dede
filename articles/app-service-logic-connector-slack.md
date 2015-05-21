<properties 
	pageTitle="Pufferconnector"
	description="Erste Schritte mit dem Pufferconnector"
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
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# Verwenden des Pufferconnectors in Ihrer Logik-App #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit einem Pufferconnector können Sie Nachrichten in Pufferkanälen bereitstellen.

## Erstellen eines Pufferconnectors für Ihre Logik-App ##
Um den Pufferconnector zu verwenden, müssen Sie zunächst eine Instanz der Pufferconnector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mithilfe der Option "+ NEU" unten links im Azure-Portal.
2.	Navigieren Sie zu "Web und Mobile \> Azure Marketplace", und suchen Sie nach "Pufferconnector".
3.	Konfigurieren Sie den Pufferconnector wie folgt:
 
	![][1]
	- **Name** – Geben Sie einen Namen für den Pufferconnector an 
	- **App Service-Plan** – Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen 
	- **Tarif** – Wählen Sie einen Tarif für den Connector aus 
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll 
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll 
	- **Standort** – Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll

4. Klicken Sie auf "Erstellen". Ein neuer Pufferconnector wird erstellt.
5. Sobald die API-App-Instanz erstellt wurde, können Sie eine Logik-App in derselben Ressourcengruppe erstellen, die den Pufferconnector verwenden soll.

## Verwenden des Pufferconnectors in Ihrer Logik-App ##
Sobald Ihre API-App erstellt wurde, können Sie den Pufferconnector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe, die den Pufferconnector enthält. Befolgen Sie die Anweisungen zum [Erstellen einer neuen Logik-App].  	
	
2.	Öffnen Sie "Trigger und Aktionen" innerhalb der erstellten Logik-App, um den Logik-App-Designer zu öffnen und den Ablauf zu konfigurieren.
	
3.	Der Pufferconnector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt.
 
	![][2]
4.	Die Pufferconnector-API-App kann durch Klicken auf den "Pufferconnector" in den Editor gezogen werden. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Microsoft-Anmeldeinformationen an \(falls Sie nicht automatisch angemeldet werden\). Melden Sie sich durch Befolgen der Schritte bei Ihrem Pufferkonto an. Am Ende werden Sie aufgefordert, dem Connector die Zugriffsberechtigung für Ihr Pufferkonto zu erteilen. Klicken Sie auf "Autorisieren".
 
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	Jetzt können den Pufferconnector im Ablauf verwenden. Trigger sind derzeit nicht im Pufferconnector verfügbar. Die verfügbaren Aktionen sind – Nachricht bereitstellen
 
	![][7]

6.	Sehen wir uns die Benutzeroberfläche von "Nachricht bereitstellen" an. Sie können diese Aktion zum Bereitstellen einer Nachricht für einen beliebigen Pufferkanal verwenden.
 
	![][8]

	Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht bereitstellen" wie folgt:

 - **Text** – Geben Sie den Text der bereitzustellenden Nachricht ein.
 - **Kanalname** – Geben Sie den Pufferkanal an, in den diese Nachricht hochgeladen werden soll. Wenn dieser nicht angegeben wird, wird die Nachricht in \#general bereitgestellt.

 	**Erweiterte Eigenschaften**
 	- **Bot-Benutzername** - Name des Bots, der für diese Nachricht verwendet werden soll. Die Nachricht wird als "Bot" bereitgestellt, wenn diese Angabe nicht gemacht wird. 
 	- **Symbol-URL** - URL zu einem Bild, das als Symbol für diese Nachricht verwendet werden soll
 	- **Emoticon** - Emoticon, das als Symbol für diese Nachricht verwendet werden soll. Setzt die Symbol-URL außer Kraft.
 

7. Um den Connector außerhalb einer Logik-App zu verwenden, können die vom Connector zur Verfügung gestellten REST-APIs genutzt werden. Sie können diese API-Definitionen mit "Durchsuchen" -\> "API-App" -\> "Pufferconnector" anzeigen. Klicken Sie jetzt auf die Lupe "API-Definition" im Abschnitt "Zusammenfassung", um alle APIs anzuzeigen, die von diesem Connector zur Verfügung gestellt werden.

	![][9]

9. Details zu den APIs finden Sie auch unter [Puffer-API-Definition].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!-- Links -->
[Erstellen einer neuen Logik-App]: app-service-logic-create-a-logic-app.md
[Puffer-API-Definition]: https://msdn.microsoft.com/de-de/library/dn708020.aspx
<!--HONumber=52-->
