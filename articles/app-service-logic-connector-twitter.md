<properties 
   pageTitle="Twitter-Connector-API-App" 
   description="Verwenden des Twitter-Connectors" 
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
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# Verwenden des Twitter-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem Twitter-Connector können Sie Tweets posten und Tweets von Ihrer Chronik, von Freunden und Followern aus Ihrem Twitter-Konto abrufen.

- Der Twitter-Connector-Trigger ruft neue Tweets ab, die einem angegebenen Schlüsselwort zugeordnet sind. Wenn ein neuer Tweet empfangen wird, wird eine neue Instanz des Datenflusses ausgelöst, und die in der Anforderung empfangenen Daten werden zur Verarbeitung an den Datenfluss weitergegeben. 
- Mit Twitter-Connector-Aktionen können Sie "Twittern", "Tweets suchen", "Retweeten", "Benutzerchronik abrufen" und so weiter. Diese Aktionen erhalten eine Antwort und stellen diese den Aktionen im Datenfluss zur Nutzung zur Verfügung.

## Erstellen eines Twitter-Connectors für Ihre Logik-App ##
Zur Verwendung des Twitter-Connectors müssen Sie zunächst eine Instanz der Twitter-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1. Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
1. Wechseln Sie zu "API-Apps", und suchen Sie nach "Twitter-Connector".
1. Konfigurieren Sie den Twitter-Connector wie folgt:

	![][1]
4.	Klicken Sie zum Erstellen auf "OK".
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Twitter-Connectors erstellen. 
	- Die API-App-Instanz des Twitter-Connectors kann auch von der Logik-App aus erstellt werden. 
	- Öffnen Sie den Logik-App-Editor, und klicken Sie auf den Twitter-Connector, der im Katalog auf der rechten Seite zur Verfügung steht.
	- Dadurch wird eine API-App-Instanz des Twitter-Connectors in derselben Ressourcengruppe erstellt, in der die Logik-App erstellt wurde.


## Verwenden des Twitter-Connectors in Ihrer Logik-App ##
Sobald Ihre API-App erstellt wurde, können Sie jetzt den Twitter-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Twitter-Connector befindet.
 	
	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 	
	![][3]
3.	Der Twitter-Connector wird im Abschnitt "Zuletzt verwendet" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus.
 
	![][4]
4.	Sie können die Twitter-Connector-API-App im Editor ablegen, indem Sie im Katalog auf der rechten Seite unter "Zuletzt verwendet" auf "Facebook-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Twitter-Anmeldeinformationen an. Klicken Sie auf "App autorisieren".
 
	![][5]
6.	Sie können nun den Twitter-Connector im Datenfluss verwenden. Sie können nun die vom Twitter-Trigger abgerufenen Tweets in anderen Aktionen im Datenfluss verwenden.
 
	![][6]
7.	Auf ähnliche Weise können Sie die Twitter-Aktionen im Datenfluss verwenden. Wählen Sie eine Twitter-Aktion aus, und konfigurieren Sie die Eingaben für die jeweilige Aktion.

	![][7] 
	![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png

<!---HONumber=54-->