<properties 
	pageTitle="SFTP-Connector"
	description="Erste Schritte mit dem SFTP-Connector"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# Verwenden des SFTP-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem SFTP-Connector können Sie Daten von einem und auf einen SFTP-Server verschieben. Sie können Dateien bzw. Listendateien von einem SFTP-Server herunterladen oder auf diesen hochladen.

## Erstellen eines SFTP-Connectors für Ihre Logik-App ##
Zur Verwendung des SFTP-Connectors müssen Sie zunächst eine Instanz der SFTP-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SFTP-Connector".
3.	Konfigurieren Sie den SFTP-Connector wie folgt:
 
	![][1] - **Speicherort** – Wählen Sie den geografischen Standort, wo sollen die Connectors bereitgestellt werden - **Abonnement** - Wählen Sie ein Abonnement, möchten Sie diese Verbindung erstellt werden sollen – **Ressourcengruppe** - auswählen oder Erstellen einer Ressourcengruppe, in denen der Connector befinden sollte - **Webhosting-Plan** - auswählen oder erstellen Sie einen webhostingplan - **Pricing Tier** – Wählen Sie eine Preisstufe für den Connector - **Name** - Geben Sie einen Namen für den Connector SFTP - **Paket Einstellungen** - **Serveradresse** : Geben Sie den Namen des SFTP-Server oder die IP-Adresse - **akzeptieren alle SSH-Server HostKey** -bestimmt, ob alle Fingerabdruck des öffentlichen Hosts SSH vom Server akzeptiert werden sollen. Wenn auf "false" festgelegt ist, wird der Hostschlüssel mit dem Schlüssel, der in der Eigenschaft "SSH-Server-Host-Taste Fingerabdruck" - Übereinstimmung **SSH Server HostKey** - Geben Sie den Fingerabdruck des öffentlichen hostschlüssels für den SSH-Server - **Stammordner** : Geben Sie einen Ordnerpfad Root - **Cipher verschlüsseln** - Geben Sie die Verschlüsselung Cipher. - **Serverport** -Geben Sie die Portnummer des SFTP-Server
4. Klicken Sie auf "Erstellen". Ein neuer SFTP-Connector wird erstellt.

5. Navigieren Sie über "Durchsuchen" -> "API-Apps"-> <Name of the API App just created> zur soeben erstellten API-App. Sie werden bemerken, dass die Komponente "Sicherheit" noch nicht konfiguriert ist.

	![][2]
6. Klicken Sie auf die Komponente "Sicherheit", um die Sicherheitsoptionen (Benutzername, Kennwort, privater Schlüssel, PPK-Dateikennwort) für den SFTP-Connector zu konfigurieren. Wählen Sie auf der Registerkarte "Autorisierung" unter "Sicherheit" eine der Autorisierungsmethoden "Kennwort" oder "Privatekey" bzw. "MultiFactor" aus, und geben Sie die erforderlichen Eigenschaften an.

	![][3] ![][4] ![][5]
6. Sobald die Sicherheitskonfiguration gespeichert wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des SFTP-Connectors erstellen. 

## Verwenden des SFTP-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie jetzt den SFTP-Connector als Trigger oder Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der SFTP-Connector befindet.
 	
	![][6]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 	
	![][7]
3.	Der SFTP-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt.
 
	![][8]
4.	Sie können die SFTP-Connector-API-App im Editor bearbeiten, indem Sie auf den "SFTP-Connector" klicken.
 
	
6.	Sie können nun den SFTP-Connector im Datenfluss verwenden. Sie können die aus dem SFTP-Trigger abgerufene Datei ("TriggerOnFileAvailable") in anderen Datenflussaktionen verwenden.

	**Hinweis:** der SFTP-Trigger "TriggerOnFileAvailable" werden die abgerufene Datei nach der Verarbeitung der Datei gelöscht.

8.	Konfigurieren Sie die Eingabeeigenschaften für SFTP-Trigger wie folgt:

	- **Ordnerpfad** -Geben Sie den Pfad des Ordners von der Dateien muss Retieved sein.
	- **Der Dateityp: Text- oder Binärdaten** – Wählen Sie den Typ der Datei.
	- **Dateimaske** -Geben Sie die Dateimaske für das Abrufen von Dateien angewendet werden soll. ' *' Ruft alle Dateien im angegebenen Ordner.
	- **Dateimaske ausschließen** -Geben Sie die Dateimaske zum Ausschließen von Dateien angewendet werden soll. Wenn auch die Eigenschaft "Dateimaske" festgelegt ist, wird zuerst "Exclude File Mask" angewendet.

 
	![][9] ![][10]

7.	Auf ähnliche Weise können Sie die SFTP-Aktionen im Datenfluss verwenden. Sie können die Aktion "Datei hochladen" zum Hochladen einer Datei auf den SFTP-Server verwenden. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

	- **Inhalt** – Gibt den Inhalt der Datei an, die hochgeladen werden soll.
	- **Codierung für die Inhaltsübertragung** – Geben Sie keine oder base64 an.
	- **Dateipfad** -Geben Sie den Pfad der hochzuladenden Datei
	- **Überschreiben** – Geben Sie "true", um die Datei zu überschreiben, wenn es bereits vorhanden ist.
	- **Anfügen, wenn **-Geben Sie "true" oder "false". Wenn diese Option auf "True" festgelegt ist, werden die Daten an die Datei angefügt, wenn sie vorhanden ist. Wenn auf "False" festgelegt wurde, wird die Datei überschrieben, wenn vorhanden - **Temporären Ordner** – angegeben, wird der Adapter die Datei 'Temporären Ordnerpfad' Hochladen und nach Abschluss des Uploads wird die Datei "Ordnerpfad" verschoben. Der Pfad des temporären Ordners sollte sich auf dem gleichen physischen Datenträger befinden wie der Ordnerpfad, um sicherzustellen, dass der Verschiebevorgang atomarisch verläuft. Temporäre Ordner können nur verwendet werden, wenn die Eigenschaft "Anfügen, falls vorhanden" deaktiviert ist.

	![][11] ![][12]





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
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->