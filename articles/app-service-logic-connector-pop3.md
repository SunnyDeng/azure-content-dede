<properties 
   pageTitle="POP3-Connector-API-App" 
   description="Verwenden des POP3-Connectors" 
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


# Verwenden des POP3-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. 

Mit dem POP3-Connector können Sie eine Verbindung mit einem POP3-Server herstellen und einen Trigger zum Abrufen von E-Mails mit Anlagen ausführen.

## Erstellen eines POP3-Connectors für Ihre Logik-App ##
Zur Verwendung des POP3-Connectors müssen Sie zunächst eine Instanz der POP3-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "POP3-Connector".
3.	Konfigurieren Sie den POP3-Connector wie folgt:
 
	![][1]
	- **Standort** - Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Webhostingplan** - Wählen oder erstellen Sie einen Webhostingplan.
	- **Preisstufe** - Wählen Sie eine Preisstufe für den Connector.
	- **Name** - Geben Sie einen Namen für den POP3-Connector ein.
	- **Paketeinstellungen**
		- **Benutzername** - Geben Sie den Benutzernamen für die Verbindung mit dem POP3-Server an.
		- **Kennwort** Geben Sie das Kennwort für die Verbindung mit POP3-Server an.
		- **Serveradresse** - Geben Sie den POP3-Servernamen oder die IP-Adresse an.
		- **Serverport** - Geben Sie die Portnummer des POP3-Servers an.
		- **SSL aktivieren** - Geben Sie "True" an, um POP3 über einen sicheren SSL-/TLS-Kanal zu verwenden.
4.	Klicken Sie auf "Erstellen". Ein neuer POP3-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des POP3-Connectors erstellen. 

## Verwenden des POP3-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den POP3-Connector als Trigger für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der POP3-Connector befindet.
 
	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 
	![][3]
3.	Der POP3-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus.
 
	![][4]
4.	Sie können die POP3-Connector-API-App im Editor bearbeiten, indem Sie auf den "POP3-Connector" klicken. 
	
5.	Sie können nun den POP3-Connector im Datenfluss verwenden. Wählen Sie den Trigger "Get Email", und konfigurieren Sie die Häufigkeit und das Abrufintervall. Sie können nun die vom POP3-Trigger abgerufenen E-Mails in anderen Aktionen im Datenfluss verwenden.
		 
	
	![][5]
	![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG


<!--HONumber=52-->