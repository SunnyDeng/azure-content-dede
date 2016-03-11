<properties
   pageTitle="Verwendung des POP3-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des POP3-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="sameerch"/>


# Erste Schritte mit dem POP3-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview.

Stellt eine Verbindung mit einem POP3-Server her, um E-Mails mit und ohne Anlagen abzurufen.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den POP3-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.


## Erstellen eines POP3-Connectors für Ihre Logik-App ##
Zur Verwendung des POP3-Connectors müssen Sie zunächst eine Instanz der POP3-Connector-API-App erstellen. Dies kann entweder direkt im Logik-App-Designer oder außerhalb davon erfolgen. Außerhalb des Designers erfolgt das Erstellen einer Instanz wie folgt:

1.	Öffnen Sie auf der Homepage des Azure-Portals Azure Marketplace.
2.	Suchen Sie unter „Alles“ nach „POP3-Connector“.
3.	Konfigurieren Sie den POP3-Connector wie folgt:

	![][1]
	- **Standort** – Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll
	- **Webhostingplan** – Wählen Sie einen Webhostingplan aus oder erstellen Sie einen
	- **Tarif** – Wählen Sie einen Tarif für den Connector aus
	- **Name** – Geben Sie Ihrem POP3-Connector einen Namen
	- **Paketeinstellungen**
		- **Benutzername** Geben Sie den Benutzernamen zur Verbindung mit dem POP3-Server an
		- **Kennwort** Geben Sie das Kennwort zur Verbindung mit dem POP3-Server an
		- **Serveradresse** Geben Sie den POP3-Severnamen oder die IP-Adresse an
		- **Serverport** Geben Sie die POP3-Serverportnummer an
		- **SSL aktivieren** Geben Sie "true" ein, um POP3 über einen sicheren SSL-/TLS-Kanal zu verwenden
4.	Klicken Sie auf "Erstellen". Ein neuer POP3-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des POP3-Connectors erstellen.

## Verwenden des POP3-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den POP3-Connector als Trigger für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der POP3-Connector befindet.

	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.

	![][3]
3.	Der POP3-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus.

	![][4]
4.	Sie können die POP3-Connector-API-App im Editor bearbeiten, indem Sie auf den "POP3-Connector" klicken.

5.	Sie können nun den POP3-Connector im Datenfluss verwenden. Wählen Sie den Trigger "E-Mail abrufen", und konfigurieren Sie die Häufigkeit und das Abrufintervall. Sie können nun die vom POP3-Trigger abgerufenen E-Mails in anderen Aktionen im Datenfluss verwenden.
		 

	![][5]
	
	![][6]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!----HONumber=AcomDC_0224_2016-->