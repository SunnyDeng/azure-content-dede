<properties
	pageTitle="Verwendung des SFTP-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des SFTP-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
	authors="anuragdalmia"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="sameerch"/>

# Erste Schritte mit dem SFTP-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Um die Schemaversion 2015-08-01-preview aufzurufen, klicken Sie auf [SFTP-API](../connectors/connectors-create-api-sftp.md).

Mit dem SFTP-Connector können Sie Daten von einem und auf einen SFTP-Server verschieben. Sie können Dateien auf einem SFTP-Server herunterladen, hochladen oder auflisten.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den SFTP-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.

## Erstellen eines SFTP-Connectors für Ihre Logik-App ##
Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "SFTP-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Konfigurieren Sie den SFTP-Connector wie folgt: ![][1]
	- **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Webhostingplan** – Wählen oder erstellen Sie einen Webhostingplan.
	- **Tarif** – Wählen Sie einen Tarif für den Connector.
	- **Name** – Geben Sie einen Namen für den SFTP-Connector ein.
	- **Paketeinstellungen**
		- **Serveradresse** – Geben Sie den SFTP-Servernamen oder die IP-Adresse an.
		- **Accept Any SSH Server HostKey** – Legt fest, ob öffentliche SSH-Hostschlüssel-Fingerabdrücke des Servers akzeptiert werden sollen. Wenn dies auf "False" festgelegt ist, wird der Hostschlüssel mit dem in der "SSH Server Host Key Finger Print"-Eigenschaft angegebenen Schlüssel abgeglichen.
		- **SSH Server HostKey** – Geben Sie den Fingerabdruck des öffentlichen Hostschlüssels für den SSH-Server an (*optional*).
		- **Stammordner** – Geben Sie den Pfad des Stammordners an. Wenn keine Eingabe erfolgt, wird als Standard das Stammverzeichnis verwendet.
		- **Encrypt Cipher** – Legen Sie das Verschlüsselungsverfahren fest (*optional*).
		- **Serverport** – Geben Sie die Portnummer des SFTP-Servers an.
4. Klicken Sie auf "Erstellen". Es wird ein neuer SFTP-Connector erstellt.

5. Navigieren Sie über "Durchsuchen" -> "API-Apps"-> <Name of the API App just created> zur soeben erstellten API-App. Sie werden bemerken, dass die Komponente "Sicherheit" noch nicht konfiguriert ist: ![][2]
6. Klicken Sie auf die Komponente "Sicherheit", um die Sicherheitsoptionen (Benutzername, Kennwort, privater Schlüssel, PPK-Dateikennwort) für den SFTP-Connector zu konfigurieren. Wählen Sie auf der Registerkarte "Autorisierung" unter "Sicherheit" eine der Autorisierungsmethoden "Kennwort" oder "Privatekey" bzw. "MultiFactor" aus, und geben Sie die erforderlichen Eigenschaften an: ![][3] ![][4] ![][5]  
6. Sobald die Sicherheitskonfiguration gespeichert wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des SFTP-Connectors erstellen.

## Verwenden des SFTP-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie jetzt den SFTP-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie die Ressourcengruppe mit dem SFTP-Connector aus: ![][6]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren: ![][7]
3.	Der SFTP-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt: ![][8]
4.	Sie können die SFTP-Connector-API-App im Editor bearbeiten, indem Sie auf den "SFTP-Connector" klicken.

5.	Sie können nun den SFTP-Connector im Datenfluss verwenden. Sie können die aus dem SFTP-Trigger abgerufene Datei ("TriggerOnFileAvailable") in anderen Datenflussaktionen verwenden.

	> [AZURE.IMPORTANT] Der SFTP-Trigger "TriggerOnFileAvailable" löscht die abgerufene Datei nach ihrer Verarbeitung.

6.	Konfigurieren Sie die Eingabeeigenschaften für SFTP-Trigger wie folgt:

	- **Ordnerpfad** – Geben Sie den Pfad des Ordners ein, aus dem die Dateien abgerufen werden sollen.
	- **Typ der Datei: Text oder binär** – Wählen Sie den Dateityp aus.
	- **Dateimaske** – Geben Sie die Dateimaske an, die für das Abrufen von Dateien angewendet werden soll. Mit "*" werden alle Dateien im angegebenen Ordner abgerufen.
	- **Dateimaske ausschließen** – Geben Sie die Dateimaske zum Ausschließen von Dateien an. Wenn auch die Eigenschaft "Dateimaske" festgelegt ist, wird zuerst "Dateimaske ausschließen" angewendet.


	![][9]  
	![][10]

7.	Auf ähnliche Weise können Sie die SFTP-Aktionen im Datenfluss verwenden. Sie können die Aktion "Datei hochladen" zum Hochladen einer Datei auf den SFTP-Server verwenden. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

	- **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll.
	- **Codierung für die Inhaltsübertragung** – Geben Sie "Keine" oder "base64" an.
	- **Dateipfad** – Geben Sie den Dateipfad der Datei an, die hochgeladen werden soll.
	- **Überschreiben** – Legen Sie dies auf "True" fest, um die Datei ggf. zu überschreiben.
	- ****Anfügen, falls vorhanden** – Legen Sie hierfür "True" oder "False" fest. Wenn diese Option auf "True" festgelegt ist, werden die Daten an die Datei angefügt, wenn sie vorhanden ist. Ist "False" angegeben, wird eine ggf. vorhandene Datei überschrieben.
	- **Temporärer Ordner** – Falls angegeben, lädt der Adapter die Datei in den "Pfad des temporären Ordners". Nach Abschluss des Uploads wird die Datei in den "Ordnerpfad" verschoben. Der Pfad des temporären Ordners sollte sich auf dem gleichen physischen Datenträger befinden wie der Ordnerpfad, um sicherzustellen, dass der Verschiebevorgang atomarisch verläuft. Temporäre Ordner können nur verwendet werden, wenn die Eigenschaft "Anfügen, falls vorhanden" deaktiviert ist.

	![][11]  
	![][12]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!---HONumber=AcomDC_0323_2016-->