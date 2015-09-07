<properties
	pageTitle="Verwendung des OneDrive-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des OneDrive-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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

# Erste Schritte mit dem OneDrive-Connector und das Hinzufügen zur Logik-App
Stellen Sie eine Verbindung mit dem OneDrive her, um Dateien hochzuladen, herunterzuladen und zu löschen. Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den OneDrive-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Erstellen eines OneDrive-Connectors für Ihre Logik-App ##
Zur Verwendung des OneDrive-Connectors müssen Sie zunächst eine Instanz der OneDrive-Connector-API-App erstellen. Dies kann entweder direkt im Logik-App-Designer oder außerhalb davon erfolgen. Außerhalb des Designers erfolgt das Erstellen einer Instanz wie folgt:

1.	Öffnen Sie auf der Homepage des Azure-Portals Azure Marketplace.
2.	Suchen Sie unter „Alles“ nach „OneDrive-Connector“.
3.	Konfigurieren Sie den OneDrive-Connector wie folgt:

	![][1]
	- **Name** - Geben Sie einen Namen für den OneDrive-Connector an
	- **App Service-Plan** - Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen 
	- **Tarif** - Wählen Sie einen Tarif für den Connector aus 
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll 
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll 
	- **Standort** - Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll

4. Klicken Sie auf "Erstellen". Ein neuer OneDrive-Connector wird erstellt.
5. Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des OneDrive-Connectors erstellen.

## Verwenden des OneDrive-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den OneDrive-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der OneDrive-Connector befindet. Befolgen Sie die Anweisungen zum [Erstellen einer neuen Logik-App].

2.	Öffnen Sie "Trigger und Aktionen" innerhalb der erstellten Logik-App, um den Logik-App-Designer zu öffnen und den Ablauf zu konfigurieren.

3.	Der OneDrive-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt.

	![][2]
4.	Sie können die OneDrive-Connector-API-App im Editor bearbeiten, indem Sie auf den "OneDrive-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Microsoft-Anmeldeinformationen an (falls Sie nicht automatisch angemeldet werden). Klicken Sie auf "Ja", um den Zugriff zuzulassen.

	![][3]
	![][4]

5.	Sie können nun den OneDrive-Connector im Datenfluss verwenden. Trigger sind derzeit nicht im OneDrive-Connector verfügbar. Die verfügbaren Aktionen sind: Abrufen einer Datei, Hochladen einer Datei, Löschen einer Datei und Auflisten von Dateien.

	![][5]

6.	Für dieses Beispiel verwenden wir die Aktion "Datei hochladen". Sie können die OneDrive-Aktion "Datei hochladen" zum Hochladen einer Datei in Ihr OneDrive-Konto verwenden.

	![][6]

	Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

 - **Dateipfad** - Gibt den Dateipfad der Datei an, die hochgeladen werden soll.
 - **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll.
 - **Codierung für die Inhaltsübertragung** – Geben Sie "Keine" oder "base64" an.
 - **Überschreiben** - Legen Sie dies auf "True" fest, um die Datei ggf. zu überschreiben. Dies ist eine erweiterte Eigenschaft.

7. Nach der Konfiguration ist die Aktion "Datei hochladen" konfiguriert und kann im Datenfluss verwendet werden. Auf ähnliche Weise können auch andere Aktionen konfiguriert werden.

8. Um den Connector außerhalb einer Logik-App zu verwenden, können die vom Connector zur Verfügung gestellten REST-APIs genutzt werden. Sie können diese API-Definitionen mit "Durchsuchen" -> "API-App" -> "OneDrive-Connector" anzeigen. Klicken Sie jetzt auf die Lupe "API-Definition" im Abschnitt "Zusammenfassung", um alle APIs anzuzeigen, die von diesem Connector zur Verfügung gestellt werden.

	![][7]

9. Ausführliche Informationen zu den APIs finden sie unter [OneDrive Connector (in englischer Sprache)].

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie finden die Swagger-REST-API-Referenz unter [Connectors and API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766) (in englischer Sprache).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[Erstellen einer neuen Logik-App]: app-service-logic-create-a-logic-app.md
[OneDrive Connector (in englischer Sprache)]: https://msdn.microsoft.com/library/dn974227.aspx

<!---HONumber=August15_HO9-->