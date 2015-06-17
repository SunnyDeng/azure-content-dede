<properties 
   pageTitle="SMTP-Connector-API-Anwendung" 
   description="Verwenden des SMTP-Connectors" 
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


# Verwenden des SMTP-Connectors in Logik-Apps #

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.

Mit dem SMTP-Connector können Sie eine Verbindung mit einem SMTP-Server herstellen und eine Aktion zum Senden von E-Mails mit Anlagen ausführen. Mit der SMTP-Connector-Aktion "E-Mail senden" können Sie eine E-Mail an die angegebenen E-Mail-Adressen senden.

## Erstellen eines SMTP-Connectors für Ihre Logik-App ##
Zur Verwendung des SMTP-Connectors müssen Sie zunächst eine Instanz der SMTP-Connector-API-App erstellen. Gehen Sie dazu folgendermaßen vor:

1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten links im Azure-Portal.
2.	Navigieren Sie zu "Web und Mobile > Azure Marketplace" und suchen Sie nach "SMTP-Connector".
3.	Konfigurieren Sie den SMTP-Connector wie folgt:
 
	![][1] - **Speicherort** – Wählen Sie den geografischen Standort, wo sollen die Connectors bereitgestellt werden - **Abonnement** - Wählen Sie ein Abonnement, möchten Sie diese Verbindung erstellt werden sollen – **Ressourcengruppe** - auswählen oder Erstellen einer Ressourcengruppe, in denen der Connector befinden sollte - **Webhosting-Plan** - auswählen oder erstellen Sie einen webhostingplan - **Pricing Tier** – Wählen Sie eine Preisstufe für den Connector - **Name** - Geben Sie einen Namen für den SMTP-Connector - **Paket Einstellungen** - **Benutzernamen** Geben Sie den Benutzernamen für die Verbindung zum SMTP-Server - **Kennwort** Geben Sie das Kennwort für die Verbindung zum SMTP-Server - **-Serveradresse** Geben Sie den SMTP-Servernamen oder die IP-Adresse - **-Serverport** Geben Sie die Portnummer des SMTP-Server - **SSL aktivieren** Geben Sie True, um SMTP verwenden, über einen sicheren SSL/TLS-Kanal
4.	Klicken Sie auf "Erstellen". Ein neuer SMTP-Connector wird erstellt.
5.	Sobald die API-App-Instanz erstellt wurde, können Sie in derselben Ressourcengruppe eine Logik-App zur Verwendung des SMTP-Connectors erstellen. 

## Verwenden des SMTP-Connectors in Logik-Apps ##
Sobald Ihre API-App erstellt wurde, können Sie den SMTP-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie hierzu wie folgt vor:

1.	Erstellen Sie eine neue Logik-App, und wählen Sie dieselbe Ressourcengruppe aus, in der sich der SMTP-Connector befindet.
 
	![][2]
2.	Öffnen Sie "Trigger und Aktionen", um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. 
 
	![][3]
3.	Der SMTP-Connector wird im Abschnitt "API Apps in this resource group" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus.
 
	![][4]
4.	Sie können die SMTP-Connector-API-App im Editor bearbeiten, indem Sie auf den "SMTP-Connector" klicken. 
	
7.	Sie können nun den SMTP-Connector im Datenfluss verwenden. Wählen Sie die Aktion "E-Mail senden" aus, und konfigurieren Sie die Eingabeeigenschaften wie folgt:
	-  – Dies ist die e-Mail-Adresse der Empfänger. Trennen Sie mehrere E-Mail-Adressen mithilfe eines Semikolons (;). Beispiel: recipient1@domain.com;recipient2@domain.com.
	- **Cc** – Dies ist die e-Mail-Adresse der Cc-Empfänger. Trennen Sie mehrere E-Mail-Adressen mithilfe eines Semikolons (;). Beispiel: recipient1@domain.com;recipient2@domain.com.
	- **Betreff** – Dies ist der Betreff der e-Mail.
	- **Body** – Dies ist der Textkörper der e-Mail.
	- **HTML-Format** – Wenn diese Eigenschaft festgelegt ist, auf "true", den Inhalt des Textkörpers werden im HTML-Format gesendet werden.
	- **Bcc** – Dies ist die e-Mail-Adresse der Empfänger für Blindkopie erhalten. Trennen Sie mehrere E-Mail-Adressen mithilfe eines Semikolons (;). Beispiel: recipient1@domain.com;recipient2@domain.com.
	- **Bedeutung** – Dies ist die Wichtigkeit der e-Mail. Zur Verfügung stehen die Optionen: Normal, Niedrig, Hoch.
	- **Anlagen** -Anlagen mit der e-Mail gesendet werden. Enthält die folgenden Felder:
		- Inhalt (Zeichenfolge)
		- Codierung (Enum) zur Übertragung von Inhalten ("none" | " Base64 ")
		- Inhaltstyp (Zeichenfolge)
		- Inhalts-ID (Zeichenfolge)
		- Dateiname (Zeichenfolge)
	 
	
	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->