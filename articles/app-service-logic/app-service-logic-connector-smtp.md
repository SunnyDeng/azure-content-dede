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
 
	![][1] – **Standort** – Wählen Sie den geografischen Standort, an dem der Connector bereitgestellt werden soll – **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll – **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll – **Webhostingplan** – Wählen Sie einen Webhostingplan aus oder erstellen Sie einen – **Tarif** – Wählen Sie einen Tarif für den Connector aus – **Name** – Geben Sie Ihrem SMTP-Connector einen Namen – **Paketeinstellungen** – **Benutzername** Geben Sie den Benutzernamen zur Verbindung mit dem SMTP-Server an – **Kennwort** Geben Sie das Kennwort zur Verbindung mit dem SMTP-Server an – **Serveradresse** Geben Sie den SMTP-Severnamen oder die IP-Adresse an – **Serverport** Geben Sie die SMTP-Serverportnummer an – **SSL aktivieren** Geben Sie "true" ein, um SMTP über einen sicheren SSL-/TLS-Kanal zu verwenden
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
	- **An** – Fügen Sie hier die Empfänger-E-Mail-Adressen ein. Trennen Sie mehrere E-Mail-Adressen mithilfe eines Semikolons (;). Beispiel: recipient1@domain.com;recipient2@domain.com.
	- **Cc** – Dies sind die E-Mail-Adressen der Cc-Empfänger. Trennen Sie mehrere E-Mail-Adressen mithilfe eines Semikolons (;). Beispiel: recipient1@domain.com;recipient2@domain.com.
	- **Betreff** – Dies ist das Betreff der E-Mail.
	- **Text** – Dies ist der Text der E-Mail.
	- **Is HTML** – Wenn diese Eigenschaft auf "True" festgelegt ist, wird der E-Mail-Text als HTML gesendet.
	- **Bcc** – Dies sind die E-Mail-Adressen der Empfänger für Blindkopien (Bcc). Trennen Sie mehrere E-Mail-Adressen mithilfe eines Semikolons (;). Beispiel: recipient1@domain.com;recipient2@domain.com.
	- **Priorität** – Legt die E-Mail-Priorität fest. Zur Verfügung stehen die Optionen: Normal, Niedrig, Hoch.
	- **Anlagen** – Anlagen, die mit der E-Mail gesendet werden. Enthält die folgenden Felder:
		- Inhalt (Zeichenfolge)
		- Codierung für die Inhaltsübertragung (Enum) ("keine" | "base64")
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
 

<!---HONumber=62-->