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

Logik-Apps können basierend auf einer Vielzahl von Datenquellen auslösen und Connectors das Abrufen und Verarbeiten von Daten im Rahmen des Datenflusses ermöglichen. 

Mit dem SFTP-Connector können Sie Daten von einem und auf einen SFTP-Server verschieben. Sie können Dateien bzw. Listendateien von einem SFTP-Server herunterladen oder auf diesen hochladen.

## Erstellen eines SFTP-Connectors für Ihre Logik-App ##
Zur Verwendung des SFTP-Connectors müssen Sie zunächst eine Instanz der SFTP-Connector-API-App erstellen. Dies kann wie folgt durchgeführt werden:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SFTP-Connector".
3.	Konfigurieren Sie den SFTP-Connector wie folgt:
 
	![][1] 
	- **Standort** - Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	- **Abonnement** - Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	- **Ressourcengruppe** - Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	- **Webhostingplan** - Wählen oder erstellen Sie einen Webhostingplan.
	- **Preisstufe** - Wählen Sie eine Preisstufe für den Connector.
	- **Name** - Geben Sie einen Namen für den SFTP-Connector ein.
	- **Paketeinstellungen**
		- **Serveradresse** - Geben Sie den SFTP-Servernamen oder die IP-Adresse an.
		- **Accept Any SSH Server HostKey** - Legt fest, ob öffentliche SSH-Hostschlüssel-Fingerabdrücke des Servers akzeptiert werden sollen. Wenn dies auf "False" festgelegt ist, wird der Hostschlüssel mit dem in der "SSH Server Host Key Finger Print"-Eigenschaft angegebenen Schlüssel abgeglichen.
		- **SSH Server HostKey** - Geben Sie den Fingerabdruck des öffentlichen Hostschlüssels für den SSH-Server an.
		- **Stammordner** - Geben Sie den Pfad des Stammordners an.
		- **Encrypt Cipher** - Legen Sie das Verschlüsselungsverfahren fest.
		- **Serverport** - Gibt die Portnummer des SFTP-Servers an.
4. Klicken Sie auf "Erstellen". Ein neuer SFTP-Connector wird erstellt.

5. Navigieren Sie über "Durchsuchen" -> "API-Apps"-> <Name der erstellten App-API> zur soeben erstellten API-App. Sie werden bemerken, dass die Komponente "Sicherheit" noch nicht konfiguriert ist. 

	![][2]
6. Klicken Sie auf die Komponente "Sicherheit", um die Sicherheitsoptionen (Benutzername, Kennwort, privater Schlüssel, PPK-Dateikennwort) für den SFTP-Connector zu konfigurieren. 
Wählen Sie auf der Registerkarte "Autorisierung" unter "Sicherheit" eine der Autorisierungsmethoden "Kennwort" oder "Privatekey" bzw. "MultiFactor" aus, und geben Sie die erforderlichen Eigenschaften an.

	![][3]
	![][4]
	![][5]
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

	**Hinweis:** Der SFTP-Trigger "TriggerOnFileAvailable" löscht die abgerufene Datei nach deren Verarbeitung.

8.	Konfigurieren Sie die Eingabeeigenschaften für SFTP-Trigger wie folgt:

	- **Ordnerpfad** - Geben Sie den Pfad des Ordners ein, aus dem die Dateien abgerufen werden sollen.
	- **The type of the file: text or binary** - Wählen Sie den Dateityp aus.
	- **Dateimaske** - Geben Sie die zum Abrufen der Dateien angewendete Dateimaske an. '*' Ruft alle Dateien im angegebenen Ordner ab.
	- **Exclude File Mask** - Geben Sie die Dateimaske zum Ausschließen von Dateien an. Wenn auch die Eigenschaft "Dateimaske" festgelegt ist, wird zuerst "Exclude File Mask" angewendet.

 
	![][9] 
	![][10]

7.	Auf ähnliche Weise können Sie die SFTP-Aktionen im Datenfluss verwenden. Sie können die Aktion "Datei hochladen" zum Hochladen einer Datei auf den SFTP-Server verwenden. Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Datei hochladen" wie folgt:

	- **Inhalt** - Gibt den Inhalt der Datei an, die hochgeladen werden soll.
	- **Codierung für die Inhaltsübertragung** - Geben Sie keine oder base64 an.
	- **Dateipfad** - Gibt den Dateipfad der Datei an, die hochgeladen werden soll.
	- **Überschreiben** - Legen Sie dies auf "True" fest, um die Datei ggf. zu überschreiben.
	- **Append If Exists** - Legen Sie hierfür "True" oder "False" fest. Wenn diese Option auf "True" festgelegt ist, werden die Daten an die Datei angefügt, wenn sie vorhanden ist. Ist "False" angegeben, wird eine ggf. vorhandene Datei überschrieben.
	- **Temporärer Ordner** - Falls angegeben, lädt der Adapter die Datei in den 'Pfad des temporären Ordners'. Nach Abschluss des Uploads wird die Datei in den 'Ordnerpfad' verschoben. Der 'Pfad des temporären Ordners' sollte sich auf dem gleichen physischen Datenträger befinden wie der 'Ordnerpfad', um sicherzustellen, dass der Verschiebevorgang atomarisch verläuft. Temporäre Ordner können nur verwendet werden, wenn die Eigenschaft 'Append If Exist' deaktiviert ist.

	![][11]
	![][12]





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


<!--HONumber=52-->