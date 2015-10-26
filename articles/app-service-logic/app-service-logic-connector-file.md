<properties
	pageTitle="Verwendung des Datei-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des Datei-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="andalmia"/>

# Erste Schritte mit dem Datei-Connector und das Hinzufügen zur Logik-App
Stellen Sie eine Verbindung mit einem Dateisystem her, um z. B. Dateien auf einem Hostcomputer hochzuladen und herunterzuladen. Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten abzurufen und zu verarbeiten. Sie können den Datei-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

Der Datei-Connector verwendet den Hybrid Connection Manager für eine Hybridkonnektivität mit dem Dateisystem.

## Erstellen eines Datei-Connectors für Ihre Logik-App ##
Zur Verwendung des Datei-Connectors müssen Sie zunächst eine Instanz der Datei-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" auf der linken Seite im Azure-Portal.
2.	Navigieren Sie zu "Marketplace > API-Apps" und suchen Sie nach "Datei-Connector".
3.	Konfigurieren Sie den Datei-Connector wie folgt: ![][1]

	- **Name** – Geben Sie einen Namen für den Datei-Connector ein.
	- **Paketeinstellungen**
		- **Stammordner** - Geben Sie den Pfad des Stammordners auf dem Hostcomputer an. Z. B. D:\\FileConnectorTest
		- **Service Bus-Verbindungszeichenfolge** - Geben Sie eine Dienstbus-Verbindungszeichenfolge an. Stellen Sie sicher, dass der Service Bus-Namespace vom Typ "Standard" ist und NICHT "Basic", um Service Bus Relays zu ermöglichen. Service Bus Relay wird für die Verbindung mit dem Hybrid Connection Manager verwendet.
	- **App Service-Plan** - Wählen oder erstellen Sie einen App Service-Plan.
	- **Tarif** – Wählen Sie einen Tarif für den Connector.
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.

4. Klicken Sie auf "Erstellen". Ein neuer Datei-Connector wird erstellt.

## Hybrid Connection Manager konfigurieren ##
Sobald die API-App-Instanz erstellt wurde, navigieren Sie zu deren Dashboard. Klicken Sie dazu auf "Durchsuchen" > "API-Apps" > "Wählen Sie Ihre Datei-Connector-API-App aus". Hier muss der Hybrid Connection Manager konfiguriert werden. Weitere Informationen zum Konfigurieren des Hybrid Connection Managers und zur Problembehebung finden Sie unter [Hybrid Connection Manager verwenden].

## Verwenden des Datei-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Datei-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Datei-Connector befindet. Befolgen Sie die Anweisungen zum [Erstellen einer neuen Logik-App].

2.	Öffnen Sie "Trigger und Aktionen" innerhalb der erstellten Logik-App, um den Logik-App-Designer zu öffnen und den Ablauf zu konfigurieren.

3.	Der Datei-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt.

4.	Sie können die Datei-Connector-API-App im Editor bearbeiten, indem Sie auf "Datei-Connector" klicken. Der Datei-Connector macht einen Trigger und vier Aktionen verfügbar: ![][5]

6.	Diese machen wiederum verschiedene Eigenschaften verfügbar. In der folgenden Abbildung sind die Eigenschaften für den Trigger und die Dateiabrufaktion aufgelistet: ![][6]

7. Nachdem Sie diese konfiguriert haben, können Sie die Trigger und Aktionen im Datenfluss nutzen. Auf ähnliche Weise können auch andere Aktionen konfiguriert werden.

> [AZURE.NOTE]Der Datei-Trigger löscht die Datei, nachdem sie erfolgreich aus dem Ordner gelesen wurde.

## Datei-Connector-REST-APIs ##
Um den Connector außerhalb einer Logik-App zu verwenden, können die vom Connector zur Verfügung gestellten REST-APIs genutzt werden. Sie können diese API-Definitionen mit "Durchsuchen" -> "API-App" -> "Datei-Connector" anzeigen. Klicken Sie jetzt auf die Lupe "API-Definition" im Abschnitt "Zusammenfassung", um alle APIs anzuzeigen, die von diesem Connector zur Verfügung gestellt werden: ![][7]

Ausführliche Informationen zu den APIs finden sie unter [File Connector (in englischer Sprache)].

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Erstellen einer neuen Logik-App]: app-service-logic-create-a-logic-app.md
[File Connector (in englischer Sprache)]: https://msdn.microsoft.com/library/dn936296.aspx
[Hybrid Connection Manager verwenden]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=Oct15_HO3-->