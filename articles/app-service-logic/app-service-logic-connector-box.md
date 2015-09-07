<properties
   pageTitle="Verwendung des Box-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des Box-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="andalmia"/>

# Erste Schritte mit dem Box-Connector und das Hinzufügen zur Logik-App 
Stellen Sie eine Verbindung mit Box her, um z. B. Dateien abzurufen, hochzuladen, zu löschen. Connectors werden als Teil eines "Workflows" in Logik-Apps verwendet.

Es gibt Situationen, in denen Sie möglicherweise mit Box arbeiten müssen, um Daten sicher mit anderen Personen zu teilen – auch außerhalb Ihrer Firewall. Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten.


## Trigger und Aktionen
Die Box-Katalog-App bietet Ihnen Aktionen als Mechanismen für die Interaktion mit Box:

**Aktionen**: Über die Aktionen können Sie vordefinierte Aktionen für das Box-Konto ausführen, das mit der Logik-App konfiguriert wurde. Im Folgenden sind die Aktionen aufgeführt, die über ein Box-Konto mit dem Box-Connector durchgeführt werden können:

a. *Dateien auflisten*: Dieser Vorgang gibt die Informationen aller Dateien in einem Ordner zurück. Liste der Parameter, die für die Aktion erforderlich sind:

Parametername | Beschreibung | Erforderlich
--- | --- | ---
Ordnerpfad | Pfad des Ordners zur Liste. | Ja

> [AZURE.NOTE]Hierdurch werden keine Dateiinhalte zurückgegeben.

b. *Datei abrufen:* Dieser Vorgang ruft eine Datei mit ihren Inhalten und Eigenschaften ab. Liste der Parameter, die für die Aktion erforderlich sind:

Parametername | Beschreibung | Erforderlich
--- | --- | ---
Dateipfad | Pfad des Ordners, in dem sich die Datei befindet. | Ja
Dateityp | Gibt an, ob die Datei im Text- oder im Binärformat vorliegt. | Nein

> [AZURE.NOTE]Bei diesem Vorgang wird die Datei nach dem Lesen nicht gelöscht.


c. *Datei hochladen*: Wie der Name schon sagt, wird die Datei durch diese Aktion in ein Box-Konto hochgeladen. Wenn die Datei bereits vorhanden ist, wird sie nicht überschrieben, und ein Fehler wird ausgelöst. Liste der Parameter, die für die Aktion erforderlich sind:

Parametername | Beschreibung | Erforderlich
--- | --- | ---
Dateipfad | Pfad zur Datei. | Ja
Inhalt der Datei | Dateiinhalt, der hochgeladen werden soll. | Ja
Codierung für die Inhaltsübertragung | Codierungstyp des Inhalts: "Base64" oder "None". | 

d. *Datei löschen*: Diese Aktion löscht die angegebene Datei aus einem Ordner. Wenn die Datei/der Ordner nicht gefunden wird, wird eine Ausnahme ausgelöst. Liste der Parameter, die für die Aktion erforderlich sind:

Parametername | Beschreibung | Erforderlich
--- | --- | ---
Dateipfad | Vollständiger Dateipfad einschließlich Ordnern. | Ja


## Erstellen des Box-Connectors für Ihre Logik-App

Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Suchen Sie nach "Box-Connector", wählen Sie ihn aus, und klicken Sie dann auf **Erstellen**.
3. Geben Sie den Namen, den App Service-Plan und andere Eigenschaften ein: !
	[][1]
4. Klicken Sie auf **Erstellen**.


## Verwenden des Box-Connectors in Ihrer Logik-App

Sobald Ihre API-App erstellt wurde, können Sie den Box-Connector als Aktion in Ihrer Logik-App verwenden. Gehen Sie dazu folgendermaßen vor:

1. Öffnen Sie in der Logik-App **Trigger und Aktionen**, um den Logik-Apps-Designer zu öffnen und den Datenfluss zu konfigurieren. Der Box-Connector wird im Katalog aufgeführt. Wählen Sie ihn aus, um ihn automatisch dem Logik-Apps-Designer hinzuzufügen:

	> [AZURE.NOTE]Wenn der Box-Connector am Anfang der Logik-App aktiviert ist, fungiert dieser wie ein Trigger. Andernfalls könnte Aktionen auf das Box-Konto über den Connector angewendet werden. Der Box-Connector verfügte über keine Trigger, als dieser Artikel verfasst wurde.

2. Authentifizieren und autorisieren Sie Logik-Apps, um Vorgänge in Ihrem Namen durchzuführen. Wählen Sie **Autorisieren** für den Box-Connector aus: 
	![][2]

3. Geben Sie die Anmeldeinformationen des Box-Kontos an, mit dem Sie die Vorgänge ausführen möchten: 
	![][3]

4. Erteilen Sie Logik-Apps Zugriff auf das Konto, um Vorgänge in Ihrem Namen auszuführen: 
	![][4]

5. Die Liste der Aktionen wird angezeigt, und Sie können die entsprechenden Vorgänge auswählen, die Sie ausführen möchten: 
	![][5]

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=August15_HO9-->