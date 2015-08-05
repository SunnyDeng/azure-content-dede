<properties
   pageTitle="SMTP-Connector-API-App"
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
   ms.date="07/01/2015"
   ms.author="andalmia"/>


# SMTP-Connector

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil eines Workflows abzurufen und zu verarbeiten.

Mit dem SMTP-Connector können Sie eine Verbindung mit einem SMTP-Server herstellen und eine Aktion zum Senden von E-Mails mit Anlagen ausführen. Mit der SMTP-Connector-Aktion "E-Mail senden" können Sie eine E-Mail an die angegebenen E-Mail-Adressen senden.

## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine *Aktion* ist das Ergebnis des Triggers. Wenn z. B. ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird, senden Sie eine E-Mail an den neuen Kunden.

Der SMTP-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Es gibt derzeit keine Trigger für diesen Connector.

Der SMTP-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Keine | Senden von E-Mail


## Erstellen des SMTP-Connectors
Ein Connector kann innerhalb einer Logik-App erstellt werden oder direkt aus dem Azure Marketplace. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus, und suchen Sie nach "SMTP-Connector".
3. **Erstellen** Sie den Connector.
4. Geben Sie den Namen, die App Service-Plan und andere Eigenschaften ein.
5. Geben Sie die folgenden Paketeinstellungen ein:

	Name | Erforderlich | Beschreibung
	--- | --- | ---
	Benutzername | Ja | Geben Sie einen Benutzernamen ein, der eine Verbindung mit dem SMTP-Server herstellen kann.
	Kennwort | Ja | Geben Sie das Kennwort für den Benutzernamen ein.
	Serveradresse | Ja | Geben Sie den SMTP-Servernamen oder die IP-Adresse ein.
	Serverport | Ja | Geben Sie die Portnummer des SMTP-Servers ein.
	SSL aktivieren | Nein | Geben Sie*true* ein, um SMTP über einen sicheren SSL/TLS-Kanal zu verwenden.

6. Klicken Sie auf **Erstellen**.

## Verwenden des SMTP-Connectors in Logik-Apps
Sobald Ihr Connector erstellt wurde, können Sie den SMTP-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen einer neuen Logik-App:

	![][2]
2.	Öffnen Sie **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen und den Workflow zu konfigurieren.

	![][3]
3.	Der SMTP-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus:

	![][4]
4.	Wählen Sie den SMTP-Connector aus, um ihn automatisch dem Workflow-Designer hinzuzufügen.

Sie können nun den SMTP-Connector zur Verwendung in Ihrem Workflow konfigurieren. Wählen Sie die Aktion **E-Mail senden** aus, und konfigurieren Sie die Eingabeeigenschaften wie folgt:

	Eigenschaft | Beschreibung
	--- | ---
	To | Geben Sie die E-Mail-Adresse des/der Empfänger/s ein. Trennen Sie mehrere E-Mail-Adressen durch ein Semikolon (;). Geben Sie z. B. Folgendes ein: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Geben Sie die E-Mail-Adresse des/der Empfänger/s in Cc ein. Trennen Sie mehrere E-Mail-Adressen durch ein Semikolon (;). Geben Sie z. B. Folgendes ein: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Geben Sie den Betreff der E-Mail ein.
	Body | Geben Sie den Text der E-Mail ein.
	Is HTML | Wenn diese Eigenschaft auf „true“ festgelegt ist, werden Textinhalte im HTML-Format gesendet.
	Bcc | Geben Sie die E-Mail-Adresse des/der Empfänger/s in Bcc ein. Trennen Sie mehrere E-Mail-Adressen durch ein Semikolon (;). Geben Sie z. B. Folgendes ein: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Geben Sie die Wichtigkeit der E-Mail ein. Es stehen folgende Optionen zur Verfügung: „Normal“, „Niedrig“ und „Hoch“.
	Attachments | Anlagen, die mit der E-Mail gesendet werden sollen. Enthält folgende Felder: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Erstellen der API-Apps mithilfe von REST-APIs. Informationen finden Sie unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=July15_HO4-->