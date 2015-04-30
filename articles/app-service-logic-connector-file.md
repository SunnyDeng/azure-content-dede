<properties 
	pageTitle="Datei-Connector"
	description="Erste Schritte mit dem Datei-Connector"
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

# Verwenden des Datei-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. 

Mit dem Datei-Connector können Sie Dateien aus dem Dateisystem eines Hostcomputers hochladen, herunterladen oder löschen.

## Erstellen eines Datei-Connectors für Ihre Logik-App ##
Zur Verwendung des Datei-Connectors müssen Sie zunächst eine Instanz der Datei-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Navigieren Sie zu "Web und Mobile > Azure Marketplace" und suchen Sie nach "Datei-Connector".
3.	Konfigurieren Sie den Datei-Connector wie folgt:
 
	![][1]
 
	- **Name** - Geben Sie einen Namen für den Datei-Connector ein.
	- **Paketeinstellungen**
		- **Stammordner** -Geben Sie den Pfad des Stammordners auf dem Hostcomputer an. Beispiel: D:\FileConnectorTest
		- **Dienstbus-Verbindungszeichenfolge** - Geben Sie eine Dienstbus-Verbindungszeichenfolge an. Stellen Sie sicher, dass der Dienstbus-Namespace vom Typ "Standard" ist und NICHT "Basic".
	- **App Service-Plan** - Wählen oder erstellen Sie einen App Service-Plan.
	- **Preisstufe** - Wählen Sie eine Preisstufe für den Connector.
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Standort** - Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.

4. Klicken Sie auf "Erstellen". Ein neuer Datei-Connector wird erstellt.
5. Sobald die API-App-Instanz erstellt wurde, navigieren Sie zu deren Dashboard. Klicken Sie dazu auf "Durchsuchen" -> "API-Apps", und wählen Sie Ihre Datei-Connector-API-App aus.
6. Im Datei-Connector-Dashboard sehen Sie, dass der Bereich "Hybridverbindung" unter "On-Premise Setup Incomplete" angezeigt wird. Öffnen Sie zum Abschließen des Hybrid-Setups dieses Dashboard auf dem Hostcomputer, mit dessen Dateisystem eine Verbindung hergestellt werden soll. Klicken Sie anschließend auf den Bereich "Hybridverbindung". Klicken Sie auf dem geöffneten Blatt "Hybridverbindung" auf den Link "Herunterladen und konfigurieren", um den lokalen Hybrid Connection Manager zu installieren.

	![][2]

7. Hierdurch wird ein Installationsprogramm gestartet, in dem die Zeichenfolge "Relay Listen Connection String" angegeben werden muss. Kopieren Sie die Zeichenfolge "Primary Configuration String" aus dem Blatt "Hybridverbindung", und fügen Sie sie ein. Klicken Sie auf "Installieren".

	![][3]

8. Um sicherzustellen, dass die Hybridinstallation erfolgreich war, öffnen Sie erneut das Datei-Connector-Dashboard. Der Hybridverbindungsstatus sollte als "Verbunden" angezeigt werden.

	![][4]

## Verwenden des Datei-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den Datei-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der Datei-Connector befindet. Folgen Sie den Anweisungen zum [Erstellen einer neuen Logik-App].  	
	
2.	Öffnen Sie "Trigger und Aktionen" innerhalb der erstellten Logik-App, um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren.  	
	
3.	Der Datei-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.
 	
4.	Sie können die Datei-Connector-API-App im Editor bearbeiten, indem Sie auf "Datei-Connector" klicken. Der Datei-Connector macht einen Trigger und vier Aktionen verfügbar.
 
	![][5]

6.	Diese machen wiederum verschiedene Eigenschaften verfügbar. In der folgenden Abbildung sind die Eigenschaften für den Trigger und die Dateiabrufaktion aufgelistet.
 
	![][6]

7. Nachdem Sie diese konfiguriert haben, können Sie die Trigger und Aktionen im Datenfluss nutzen. Auf ähnliche Weise können auch andere Aktionen konfiguriert werden.

8. Um den Connector außerhalb einer Logik-App zu verwenden, können Sie die REST-APIs verwenden, die vom Connector verfügbar gemacht werden. Sie können diese API-Definitionen mit "Durchsuchen" -> "API-App" -> "Datei-Connector" anzeigen. Klicken Sie auf den Bereich "API-Definition" unterhalb des Abschnitts "Zusammenfassung", um alle von diesem Connector verfügbar gemachten APIs anzuzeigen.

	![][7]

9. Ausführliche Informationen zu den APIs finden sie unter [File Connector](in englischer Sprache).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Erstellen einer neuen Logik-App]: app-service-logic-create-a-logic-app.md
[File Connector]: https://msdn.microsoft.com/en-US/library/dn936296.aspx (in englischer Sprache)

<!--HONumber=52-->