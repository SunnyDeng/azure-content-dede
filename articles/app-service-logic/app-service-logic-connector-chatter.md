<properties
   pageTitle="Chatter-Connector-API-App"
   description="Verwenden des Chatter-Connectors"
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


# Verwenden des Chatter-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem Chatter-Connector können Sie eine Verbindung mit Chatter herstellen und verschiedene Aktionen, wie z. B. Bereitstellen einer Nachricht und Suchen, sowie einen Trigger ausführen, um neue Nachrichten abzurufen.

## Erstellen eines Chatter-Connectors für Ihre Logik-App ##
Zur Verwendung des Chatter-Connectors müssen Sie zunächst eine Instanz der Chatter-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "Chatter-Connector".
3.	Konfigurieren Sie den Chatter-Connector wie folgt:

	![][1] - **Speicherort** – Wählen Sie den geografischen Standort, wo sollen die Connectors bereitgestellt werden - **Abonnement** - Wählen Sie ein Abonnement, möchten Sie diese Verbindung erstellt werden sollen – **Ressourcengruppe** – auswählen oder Erstellen einer Ressourcengruppe, in denen der Connector befinden sollte - **Webhosting-Plan** - auswählen oder erstellen Sie einen webhostingplan - **Preise Ebene** – Wählen Sie eine Preisstufe für den Connector - **Name** -Geben Sie einen Namen für den Connector Geschwätz

4.	Klicken Sie auf "Erstellen". Ein neuer Chatter-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des Chatter-Connectors erstellen.

## Verwenden des Chatter-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie jetzt den Chatter-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Chatter-Connector befindet.

	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.

3.	Der Chatter-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.

	![][4]
4. Sie können die Chatter-Connector-API-App im Editor bearbeiten, indem Sie auf "Chatter-Connector" klicken. Klicken Sie auf die Schaltfläche "Autorisieren". Geben Sie Ihre Anmeldeinformationen ein. Klicken Sie auf "Zulassen".

	![][5] ![][6] ![][7]
5.	Sie können nun den Chatter-Connector im Datenfluss verwenden. Sie können die aus dem Chatter-Trigger ("Neue Nachricht") abgerufene neue Nachricht für andere Aktionen im Datenfluss verwenden. Konfigurieren Sie die Eingabeeigenschaften für den Chatter-Trigger wie folgt:
	- **Gruppen-ID** -Geben Sie die ID der Gruppe aus der neuen Nachricht abgerufen werden soll. Wenn die Gruppen-ID nicht angegeben wird, werden neue Nachricht aus dem Benutzer-Feed abgerufen.

  ![][8] ![][9]

6. Auf ähnliche Weise können Sie die Chatter-Aktion im Datenfluss verwenden, um eine Nachricht bereitzustellen, indem Sie die Aktion "Nachricht bereitstellen" auswählen. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht bereitstellen" wie folgt:
	- **Meldungstext** -Text-Inhalt der Nachricht bereitgestellt wird
	- **Gruppen-ID** -Geben Sie die ID der Gruppe auf das neue Nachricht gebucht werden soll. Wenn die Gruppen-ID nicht angegeben wird, wird die Nachricht im Benutzer-Feed bereitgestellt.
	- 	**Dateinamen** -Name der Datei, die mit dieser Nachricht angefügt werden
	- 	**Inhaltsdaten** -Inhaltsdaten der Anlage
	- 	**Inhaltstyp** -Inhaltstyp der Anlage
	- 	**Transfer Inhaltscodierung** -Transfer Inhaltscodierung der Anlage ("none" | " Base64 ")
	- 	**Erwähnt** -Array des Benutzers den Namen an, der in dieser Nachricht markiert werden
	- 	**Hashtags** -Array von Hashtags entlang der Nachricht bereitgestellt wird

	![][10] ![][11]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->