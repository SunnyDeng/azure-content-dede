<properties 
	pageTitle="OneDrive-Connector"
	description="Erste Schritte mit dem OneDrive-Connector"
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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# Mithilfe des OneDrive-Connectors in Ihrer app Logik #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem OneDrive-Connector können Sie Dateien in ein OneDrive-Konto hochladen bzw. aus diesem herunterladen oder löschen.

## Erstellen eines OneDrive-Connectors für Ihre Logik-App ##
Zur Verwendung des OneDrive-Connectors müssen Sie zunächst eine Instanz der OneDrive-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Navigieren Sie zu "Web und Mobile > Azure Marketplace" und suchen Sie nach "OneDrive-Connector".
3.	Konfigurieren Sie den OneDrive-Connector wie folgt:
 
	![][1] - **Name** – Geben Sie einen Namen für den OneDrive Connector - **App Dienstplan** - auswählen oder erstellen Sie einen Plan für die App-Service - **Preise Ebene** – Wählen Sie eine Preisstufe für den Connector - **Ressourcengruppe** – auswählen oder Erstellen einer Ressourcengruppe, in denen der Connector befinden sollte - **Abonnement** - Wählen Sie ein Abonnement, möchten Sie diese Verbindung erstellt werden sollen – **Speicherort** -Wählen Sie den geografischen Standort, wo sollen die Connectors bereitgestellt werden,

4. Klicken Sie auf "Erstellen". Ein neuer OneDrive-Connector wird erstellt.
5. Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des OneDrive-Connectors erstellen.

## Verwenden des OneDrive-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den OneDrive-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der OneDrive-Connector befindet. Befolgen Sie die Anweisungen zum [Erstellen einer neuen Logik-App].  	
	
2.	Öffnen Sie "Trigger und Aktionen" innerhalb der erstellten Logik-App, um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.
	
3.	Der OneDrive-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.
 
	![][2]
4.	Sie können die OneDrive-Connector-API-App im Editor bearbeiten, indem Sie auf den "OneDrive-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Microsoft-Anmeldeinformationen an (falls nicht automatisch angemeldet). Klicken Sie auf "Ja", um den Zugriff zuzulassen.
 
	![][3] ![][4]
	
5.	Sie können nun den OneDrive-Connector im Datenfluss verwenden. Trigger sind derzeit nicht im OneDrive-Connector verfügbar. Die verfügbaren Aktionen sind: Abrufen einer Datei, Hochladen einer Datei, Löschen einer Datei und Auflisten von Dateien.
 
	![][5]

6.	Für dieses Beispiel verwenden eine Aktion "Datei hochladen". Sie können die OneDrive-Aktion "Datei hochladen" zum Hochladen einer Datei in Ihr OneDrive-Konto verwenden.
 
	![][6]

	Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

 - **Dateipfad** -Geben Sie den Pfad der hochzuladenden Datei
 - **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll.
 - **Codierung für die Inhaltsübertragung** – Geben Sie keine oder base64 an.
 - **Überschreiben** – Geben Sie "true", um die Datei zu überschreiben, wenn sie bereits vorhanden ist. Dies ist eine erweiterte Eigenschaft.

7. Nach der Konfiguration ist die Aktion "Datei hochladen" konfiguriert und kann im Datenfluss verwendet werden. Auf ähnliche Weise können auch andere Aktionen konfiguriert werden.

8. Um den Connector außerhalb einer Logik-App zu verwenden, können Sie die REST-APIs verwenden, die vom Connector verfügbar gemacht werden. Sie können diese API-Definitionen mit "Durchsuchen" -> "API-App" -> "OneDrive-Connector" anzeigen. Klicken Sie auf den Bereich "API-Definition" unterhalb des Abschnitts "Zusammenfassung", um alle von diesem Connector verfügbar gemachten APIs anzuzeigen.

	![][7]

9. Details der APIs finden Sie unter [OneDrive-API-Definition].

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
[OneDrive-API-Definition]: https://msdn.microsoft.com/de-de/library/dn974227.aspx

<!---HONumber=GIT-SubDir_Tue_AM_dede-->