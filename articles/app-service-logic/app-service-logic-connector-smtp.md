<properties
   pageTitle="Verwendung des SMTP-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des SMTP-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>


# Erste Schritte mit dem SMTP-Connector und das Hinzufügen zur Logik-App
Stellen Sie eine Verbindung mit einem SMTP-Server her, und senden Sie E-Mails mit und ohne Anlagen. Mit der SMTP-Connector-Aktion "E-Mail senden" können Sie eine E-Mail an die angegebenen E-Mail-Adressen senden.

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil eines Workflows abzurufen und zu verarbeiten. Sie können den SMTP-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.


## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine *Aktion* ist das Ergebnis des Triggers. Wenn z. B. ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird, senden Sie eine E-Mail an den neuen Kunden.

Der SMTP-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Es gibt derzeit keine Trigger für diesen Connector.

Der SMTP-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Keine | Senden von E-Mail


## Erstellen des SMTP-Connectors
Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

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

> [AZURE.IMPORTANT]Einige SMTP-Server haben möglicherweise Probleme mit der Funktionsweise dieses Connectors (SendGrid und Gmail). Wenn Sie E-Mails von SendGrid senden möchten, bietet unser [GitHub-Repository](https://github.com/logicappsio/SendGridAPI) eine benutzerdefinierte API, die eine direkte Schnittstelle zu den SendGrid-APIs darstellt.

## Verwenden des SMTP-Connectors in Logik-Apps
Sobald Ihr Connector erstellt wurde, können Sie den SMTP-Connector als Aktion für Ihre Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1.	Erstellen einer neuen Logik-App: ![][2]
2.	Öffnen Sie **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen und den Workflow zu konfigurieren: ![][3]
3.	Der SMTP-Connector wird im Abschnitt "API-Apps in dieser Ressourcengruppe" im Katalog auf der rechten Seite angezeigt. Wählen Sie ihn aus: ![][4]
4.	Wählen Sie den SMTP-Connector aus, um ihn automatisch dem Workflow-Designer hinzuzufügen.

Sie können nun den SMTP-Connector zur Verwendung in Ihrem Workflow konfigurieren. Wählen Sie die Aktion **E-Mail senden** aus, und konfigurieren Sie die Eingabeeigenschaften wie folgt:

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

![][5] ![][6]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=Nov15_HO3-->