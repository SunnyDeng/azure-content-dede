<properties
   pageTitle="Verwendung des SalesForce-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des SalesForce-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="sameerch"/>


# Erste Schritte mit dem SalesForce-Connector und das Hinzufügen zur Logik-App
Stellen Sie eine Verbindung mit SalesForce her, und erstellen und ändern Sie Entitäten wie Konten, Leads usw. Im Folgenden werden die typischen Integrationsszenarios aufgeführt, bei denen SalesForce verwendet wird.

- Kontosynchronisierung zwischen Salesforce und ERP-Systemen wie SAP und QuickBooks
- Datenfluss von der Bestellung bis zur Bezahlung von Salesforce bis zu ERP-Systemen

Im Rahmen der Connector-Paketeinstellungen kann der Benutzer Entitäten angeben, die der Connector verwalten kann. Aktionen, Eingabeparameter und Ausgabeparameter werden daraufhin dynamisch aufgefüllt. Im Folgenden werden die verschiedenen im SalesForce-Connector verfügbaren Aktionen aufgeführt.

- Entität erstellen – Verwenden Sie diese Aktion zum Erstellen einer neuen Salesforce-Entität wie z. B. Kunde, Anfrage oder ein benutzerdefiniertes Objekt.
- Entität aktualisieren – Verwenden Sie diese Aktion zum Aktualisieren einer vorhandenen Salesforce-Entität
- Upsert auf Entität anwenden – Verwenden Sie diese Aktion zum Aktualisieren einer vorhandenen Salesforce-Entität oder zum Erstellen einer neuen, falls sie nicht vorhanden ist
- Entität löschen – Verwenden Sie diese Aktion zum Löschen einer vorhandenen Salesforce-Entität
- Abfrage ausführen – Mit dieser Aktion können Sie eine SELECT-Abfrage ausführen, die in der Salesforce Object Query Language (SOQL) geschrieben ist

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Sie können den SalesForce-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten.


## Erstellen einer SalesForce-Connector-API-App
1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten rechts im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "SalesForce".
3.	Konfigurieren Sie den SalesForce-Connector, indem Sie die Informationen für den Hostingplan und die Ressourcengruppe angeben und den Namen der API-App auswählen: ![][15]
4. Konfigurieren Sie die SalesForce-Entitäten, die Sie lesen/schreiben möchten, in den "Paketeinstellungen".

Mit diesen Informationen können Sie jetzt eine SalesForce-Connector-API-App erstellen.


## Erstellen einer Logik-App
Lassen Sie uns eine einfache Logik-App erstellen, die einen Kunden in Salesforce erstellt und die Details zur Rechnungsadresse für denselben Kunden aktualisiert.

1.	Melden Sie sich beim Azure-Portal an, und klicken Sie auf "Neu -> Web und mobil -> Logik-App": ![][1]

2.	Stellen Sie auf der Seite "Logik-App erstellen" die erforderlichen Details wie Name, App Service-Plan und Standort bereit: ![][2]

3.	Klicken Sie auf "Trigger und Aktionen". Der Logik-App-Editor wird geöffnet: ![][3]

4.	Wählen Sie "Diese Logik manuell ausführen", sodass diese Logik-App nur manuell aufgerufen werden kann: ![][4]

5.	Erweitern Sie "API-Apps in dieser Ressourcengruppe" im Katalog, um alle verfügbaren API-Apps anzuzeigen. Wählen Sie im Katalog "SalesForce" aus, damit der "SalesForce-Connector" dem Datenfluss hinzugefügt wird: ![][5]

8.	Um die Logik-App für den Zugriff auf Ihr SalesForce-Konto zu autorisieren, klicken Sie auf "Autorisieren", und geben Sie die SalesForce-Anmeldeinformationen an: ![][6]

9.	Sie werden zur SalesForce-Anmeldeseite umgeleitet und können sich mit Ihren SalesForce-Anmeldeinformationen authentifizieren: ![][7] ![][8]

10.	Sobald die Autorisierung abgeschlossen ist, werden alle Aktionen angezeigt: ![][9]

11.	Wählen Sie die Aktion "Konto erstellen" aus, und die Eingabeparameter werden angezeigt: ![][10]

12.	Geben Sie "Kontoname" an, und klicken Sie auf ✓: ![][11]

13.	Wählen Sie im Katalog im Abschnitt "Zuletzt verwendet" den Eintrag "Salesforce-Connector". Eine neue "Salesforce"-Aktion wird hinzugefügt.

14.	Wählen Sie "Konto aktualisieren" aus der Liste der Aktionen, und die Eingabeparameter der Aktion "Konto aktualisieren" werden angezeigt: ![][12]

15.	Klicken Sie neben "Datensatz-ID" auf "+", um den ID-Wert aus der Ausgabe der Aktion "Konto erstellen" auszuwählen: ![][13]

16.	Geben Sie Werte für die Rechnungsadresse an (Straße, Stadt, Bundesland und Postleitzahl), und klicken Sie auf ✓: ![][14]

17. Klicken Sie im Bildschirm des Logik-App-Editors auf "OK", und klicken Sie dann auf "Erstellen". Es dauert ungefähr 30 Sekunden, bis die Erstellung abgeschlossen ist.

18. Sehen Sie sich die neu erstellte Logik-App an, und klicken Sie auf "Ausführen", um eine Ausführung zu initiieren.

19. Sie können überprüfen, ob ein neues Konto mit dem Namen "Contoso" in Ihrem SalesForce-Konto erstellt wird.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie finden die Swagger-REST-API-Referenz unter [Connectors and API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766) (in englischer Sprache).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png

<!---HONumber=August15_HO9-->